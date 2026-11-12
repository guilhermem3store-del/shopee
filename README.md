# Agente de anúncios Shopee

Compara os anúncios ativos do Mercado Livre com a Shopee, calcula preço com custo do Bling
e cria na Shopee os SKUs que faltam (por enquanto, prefixos ISA e YMA).

## Subir no GitHub e no Render
1. Crie um repositório **privado** e envie estes arquivos (o `.env` nunca vai para o GitHub).
2. No Render: New > Blueprint (usa o `render.yaml`) ou Web Service manual com
   `gunicorn app:app --workers 1 --threads 4 --timeout 120`.
3. Crie um Postgres e coloque a URL em `DATABASE_URL`.
4. Preencha as variáveis de ambiente (veja `.env.example`).
5. Cadastre estes endereços de retorno nos apps de cada plataforma:
   - Mercado Livre: `APP_BASE_URL/callback/ml`
   - Bling: `APP_BASE_URL/bling/callback`
   - Shopee: `APP_BASE_URL/callback/shopee` (quando a conta for aprovada)
6. Abra o painel, entre em **Conexões** e autorize Mercado Livre e Bling.

## Como o vínculo com o Bling funciona
Ao criar o anúncio na Shopee, o app coloca o ID do anúncio no "ID na loja" do produto no Bling
(carrinho verde), na loja "Shopee - Store". Não existe sincronização agendada: o estoque e o
preço passam a ser atualizados pelo próprio Bling. O botão "Vincular pendentes no Bling"
só repete o vínculo dos que falharam.

Para achar o ID da loja "Shopee - Store": depois de criá-la no Bling, abra
`/api/bling-vinculo` no painel (ex.: `/api/bling-vinculo?sku=ISA0001`) e copie o ID dela.
Coloque em **Regras de preço** ou na variável `BLING_LOJA_ID`.

## Enquanto a Shopee está em análise
- Deixe `DRY_RUN=1`. A análise roda só com ML + Bling (sem Shopee conectada, o app considera
  que nenhum SKU existe lá) e o envio apenas simula.
- Quando aprovar: preencha `SHOPEE_PARTNER_ID/KEY`, conecte a Shopee, rode a análise de novo
  (agora ela compara com a Shopee de verdade), defina categoria/atributos e só então `DRY_RUN=0`.

## Para expandir
Em **Regras de preço > Prefixos de SKU**, acrescente os próximos prefixos separados por vírgula.
