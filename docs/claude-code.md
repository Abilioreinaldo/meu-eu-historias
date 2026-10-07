# Instruções para o Claude Code — Meu Eu Histórias

Cole os blocos em ordem. O bloco 1 é o MVP; o bloco 2 torna o pipeline autônomo; o bloco 3 alinha o wizard ao protótipo (`index.html`).

## 0 · Comece pelo teste de rosto

```
Antes de qualquer tela, crie um comando artisan `book:generate {json}` que gera um livro completo a partir de um JSON de exemplo (nome, idade, tema, lição, estilo, 2 fotos, personagens).
Rode o mesmo JSON com GeminiImageProvider e OpenAiImageProvider e salve os PDFs lado a lado em storage/app/tests/ para comparar semelhança facial.
```

## 1 · MVP

```
Crie um MVP de livro infantil personalizado por IA: "Meu Eu Histórias".
Stack: Laravel 13 + Livewire + Pest, filas (SQS/Horizon), S3, deploy AWS.

Fluxo:
1. Wizard Livewire (8 passos): idade → tema → lição → estilo de desenho → protagonista (nome, gênero, personalidade) → fotos (rosto frontal + corpo inteiro, roupa da foto ou do tema, consentimento LGPD) → personagens extras (até 4, incl. pets, foto opcional) e dedicatória → prévia da capa → checkout.
2. Checkout Pix/cartão (Mercado Pago ou Asaas) com webhook. Pix R$ 44,90, cartão R$ 49,90 em até 12x, upsell impresso + R$ 129,90.
3. Job pipeline após pagamento:
   a. LLM gera roteiro de 14 cenas adequado à faixa etária, tema e lição (JSON: texto da página + prompt de cena).
   b. Gera "character sheet" do protagonista a partir das fotos e características.
   c. Gera 14 ilustrações usando a character sheet como referência (consistência).
   d. Monta PDF 30 páginas A5 300dpi (capa, dedicatória, páginas, contracapa com a Lumi). Texto sobreposto no PDF, nunca renderizado pela IA.
   e. Envia por e-mail + WhatsApp (API oficial), link S3 assinado.
4. Painel admin: pedidos, status do job, regenerar página, reembolso.
5. LGPD: checkbox de consentimento do responsável, fotos apagadas após a entrega (job agendado + log de exclusão), política de privacidade.
6. Temas e lições como config (slug, categoria, faixa etária, arco narrativo, paleta visual).
7. Abstração ImageProvider/TextProvider para trocar modelo sem refatorar. Padrão: Gemini 3 Pro Image para character sheet e capa, Gemini 2.5 Flash Image para cenas, Batch API quando o prazo permitir.
8. Eventos de conversão para Meta Pixel + CAPI server-side.
```

## 2 · Pipeline 100% autônomo

```
Torne o pipeline 100% autônomo, sem intervenção humana por pedido:

1. QA automático (QualityGate service):
   - Após cada imagem, chame Gemini (visão) com: character sheet + imagem gerada.
   - Retorne JSON: {face_similarity 0-10, anatomy_ok, unwanted_text, safety_ok, age_appropriate}.
   - Aprova se face_similarity >= 7 e demais true. Senão regenera (máx 3).
   - Após 3 falhas: troca para modelo Pro naquela cena; se ainda falhar, entrega com a melhor nota e loga alerta.
2. Validação do upload: Gemini verifica se cada foto tem 1 criança nítida e sozinha; rejeita com mensagem amigável no wizard.
3. Prévia pré-pagamento: gera só a capa com marca d'água (Flash) após o wizard; pagamento libera o livro completo.
4. Temas: comando artisan + job mensal que gera ThemeTemplate via LLM (arco narrativo, faixa etária, paleta, 14 cenas-base) e um job sazonal que ativa temas por data.
5. Suporte: bot WhatsApp (API oficial) com LLM + tools: consultar pedido, reenviar PDF, regenerar página N (máx 2 por pedido), abrir reembolso.
6. Criativos: job que monta carrossel/vídeo "foto → personagem" apenas de pedidos com opt-in de uso de imagem.
7. Observabilidade: dashboard com custo por livro, taxa de regeneração, nota média de QA, tempo de entrega. Alerta se custo/livro > R$ 12 ou entrega > 2h.
8. Retry/idempotência em todos os jobs; dead-letter queue com reprocessamento automático.
```

## 3 · Upload de fotos robusto (aprendizados do mercado)

```
No passo de fotos do wizard:
1. Detecte o navegador interno do Instagram/Facebook (user agent FBAN/FBAV/Instagram). Mostre aviso para abrir no Safari/Chrome, com botão "Copiar link" e o progresso salvo (rascunho do pedido por token na URL).
2. Aceite HEIC/HEIF e converta no servidor para JPEG (libheif/Imagick). Nunca peça ao cliente para converter.
3. Arquivo vazio ou inacessível (foto só na nuvem): mensagem orientando a baixar para o aparelho ou tirar uma nova pela câmera.
4. Rejeite foto abaixo de 512px no menor lado, com mensagem de "foto pequena demais, pode sair embaçada".
5. Recorte do rosto com círculo arrastável antes do envio.
6. Depois do upload do rosto, gere um rascunho rápido do personagem (Flash) e mostre comparativo foto × personagem com divisor arrastável e opção "trocar foto".
7. Dicas fixas: rosto de frente e nítido, só a criança, boa luz sem flash, sem óculos escuros ou boné.
```

## 4 · Cupons em lote (B2B)

```
Crie a venda de cupons em lote: empresa/escola compra N cupons, recebe links únicos (ou códigos para /resgatar). Quem resgata passa pelo wizard sem pagar. Painel do comprador mostra cupons usados e livros gerados.
```
