# Meu Eu Histórias

Livros infantis personalizados por IA: a criança vira a heroína da própria história, com o rosto, o nome e o mundo favorito dela. Entrega em PDF pelo WhatsApp e e-mail em até 2 horas.

> **"A história onde seu filho é o herói."**

![Logo](assets/logo.webp)

## Protótipo

`index.html` é um protótipo navegável do fluxo de compra no celular, em um único arquivo sem dependências. Abra direto no navegador ou publique no GitHub Pages.

Fluxo (8 passos): **idade → tema → lição → estilo de desenho → protagonista → fotos → personagens e dedicatória → capa grátis → pagamento**.

O envio de fotos, a ilustração da capa e o comparativo foto × personagem são simulados.

## Modelo de negócio

| Item | Decisão |
|---|---|
| Produto | Livro digital em PDF, 30 páginas, 14 ilustrações |
| Preço | R$ 49,90 no cartão (até 12x) · R$ 44,90 no Pix |
| Adicionais no checkout | Versão para colorir + R$ 9,90 · Narração em áudio + R$ 9,90 · Entrega prioritária (30 min) + R$ 4,90 · Impresso capa dura + R$ 129,90 |
| Brinde e garantia | Certificado de Pequeno Leitor com o nome da criança · **Garantia Lumi: não ficou parecido, refazemos grátis** (o direito de arrependimento de 7 dias do CDC fica descrito nos termos, sem destaque) |
| Conversão | Prévia grátis da capa (com marca d'água) antes do pagamento |
| Canal | Instagram + Meta Ads; depois cupons em lote para B2B (escolas, buffets, empresas) |
| Prazo | Até 2 horas |

### Custo por livro (estimativa, out/2026)

| Item | R$ |
|---|---|
| Geração por IA (~21 imagens + texto) | 5 – 16 |
| Gateway de pagamento | 0,50 – 2,50 |
| Impostos (Simples ~6%) | ~3 |
| **Sobra antes do anúncio** | **~28 – 41** |
| CAC Meta (a validar) | 15 – 30 |

Os adicionais digitais (colorir, áudio, prioridade) custam quase nada para produzir. Se metade dos clientes levar ao menos um, o ticket médio sobe ~R$ 8 a 10, e é isso que ajuda a pagar o CAC.

### Concorrência observada (out/2026)

- **Kidoo** (kidoo.kids): R$ 49,90, com foto, ~130 temas, 8 passos, cupons em lote para B2B.
- **Historinha Personalizada**: R$ 14,90 sem foto (só características), lucro em adicionais (colorir, prioridade, histórias extras), bônus e urgência agressiva com cronômetros. Eles podem prometer "risco zero" porque, sem foto, o livro custa centavos. Com o rosto, cada livro custa ~R$ 7, então refazer é melhor que devolver. Não copiar a urgência falsa: ela queima a marca e arrisca problema com o CDC e com as políticas da Meta.

**Critério de corte:** depois de R$ 2 mil em anúncio, se o CAC estiver acima de R$ 40, pausar e trocar o criativo ou a oferta.

### Investimento para validar

~R$ 6 a 7 mil, sendo ~R$ 5 mil em tráfego. Reservar ~R$ 15 mil de capital de giro para escalar, porque o cartão parcelado cai depois e a Meta cobra antes.

## Decisões técnicas

- **Imagem:** Gemini. O 3 Pro Image fica com a character sheet e a capa, e o 2.5 Flash Image com as cenas (~R$ 7 por livro). A Batch API dá 50% de desconto.
- **Abstração `ImageProvider`:** permite trocar para o gpt-image-2 sem refatorar. A escolha final se decide pelo teste de semelhança facial.
- **Stack:** Laravel 13 + Livewire + Pest, filas (SQS/Horizon), S3 e AWS.
- **100% automático:** o QA por visão dá nota a cada imagem contra a character sheet e regera sozinho se ela ficar abaixo do mínimo. O bot de WhatsApp faz o suporte.
- **Fotos:** duas (rosto frontal e corpo inteiro). HEIC e fotos na nuvem são convertidas no servidor. Há aviso para quem chega pelo navegador interno do Instagram.
- **LGPD:** consentimento do responsável, uso só para o livro e exclusão automática das fotos após a entrega, com log.

Detalhes do pipeline: [`docs/claude-code.md`](docs/claude-code.md).

## Marca

- **Mascote:** Lumi, uma estrelinha-lanterna lendo um livro
- **Paleta:** azul-noite `#1E2A5A` · amarelo-estrela `#F9C23C` · coral `#F47B63` · creme `#FDF7EA`
- **Tipografia do protótipo:** Baloo 2 (títulos) + Nunito (texto)
- **Domínio:** `meueuhistorias.com.br` (livre em 07/10/2026). `meueu.com.br` já tem dono.
- **Pendente:** busca no INPI, classes 16 e 41

## Próximos passos

1. Teste de rosto: 3 livros-teste no Gemini e no GPT
2. Registrar o domínio e o @ do Instagram; protocolar a marca no INPI
3. MVP: wizard, prévia, checkout, QA automático e entrega
4. Termos LGPD com revisão jurídica
5. 15 a 20 posts no Instagram antes de ligar o anúncio
6. Validação: R$ 5 mil em 30 dias, com o CAC como métrica principal
