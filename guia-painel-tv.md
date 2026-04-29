# Site gratuito para exibir imagem/vídeo em TV com painel de administração

## Objetivo
Criar um site para **digital signage** (painel de mídia) que:
- rode no navegador da TV em tela cheia;
- permita trocar conteúdo remotamente por uma área administrativa;
- use hospedagem gratuita;
- seja o mais compatível possível com TVs de diferentes fabricantes.

## Arquitetura recomendada (simples e gratuita)

### 1) Front-end público (tela da TV)
- Página web estática (HTML + CSS + JS).
- Exibe **1 mídia por vez** (imagem ou vídeo).
- Faz polling periódico (ex.: a cada 15s) para buscar configuração atual no backend.
- Possui botão/atalho para entrar em fullscreen quando suportado.

### 2) Backend + banco + auth
Use **Supabase** (plano grátis):
- Banco Postgres para guardar estado da mídia atual.
- Storage para armazenar arquivos (imagem/vídeo).
- Auth para login da administração.
- API REST automática para leitura/escrita.

### 3) Painel admin
- Página web separada (`/admin`) com login.
- Upload de imagem/vídeo.
- Seleção de mídia ativa.
- Opções como volume, mute, duração (no caso de imagem), fallback.

### 4) Hospedagem
- **Cloudflare Pages** ou **Netlify** (gratuito) para hospedar front-end e admin.
- Domínio grátis inicial com subdomínio da plataforma.

## Compatibilidade com TVs (ponto crítico)

Compatibilidade 100% “qualquer fabricante” não existe, mas dá para atingir um nível alto com estas regras:

1. Use codecs universais:
   - Vídeo: `MP4 (H.264 + AAC)`.
   - Imagem: `JPG` ou `PNG`.
2. Evite formatos pesados/especiais (HEVC, AV1, WebM em TVs antigas).
3. Resolução recomendada:
   - Full HD (1920x1080) como padrão.
4. Auto-play em TV pode variar:
   - Vídeo deve iniciar mutado para maximizar chance de autoplay.
5. Navegação simples:
   - Zero dependência de teclado/mouse após abrir a URL.
6. Fallback:
   - Se vídeo falhar, mostrar imagem fallback automaticamente.

## Fluxo de uso

1. Você abre na TV: `https://seu-projeto.pages.dev/tv`.
2. A tela carrega a mídia ativa do banco.
3. Do celular/notebook, você entra em `https://seu-projeto.pages.dev/admin`.
4. Faz login, envia novo arquivo e clica em “Ativar”.
5. Em até alguns segundos, a TV troca automaticamente.

## Modelo de dados mínimo

Tabela `screen_config`:
- `id` (uuid)
- `active_media_url` (text)
- `media_type` (`image` | `video`)
- `updated_at` (timestamp)
- `fallback_image_url` (text, opcional)

Tabela `media_assets`:
- `id` (uuid)
- `file_url` (text)
- `media_type` (`image` | `video`)
- `created_at` (timestamp)
- `created_by` (uuid)

## Segurança mínima
- Admin protegido por login.
- Storage com política de escrita apenas para usuários autenticados.
- Leitura pública somente dos arquivos que devem ir para TV.

## MVP em 1 dia

1. Criar projeto no Supabase.
2. Criar tabelas e bucket de storage.
3. Criar página `/tv` (player em tela cheia).
4. Criar página `/admin` (login + upload + ativar mídia).
5. Publicar no Cloudflare Pages.
6. Testar em 2 TVs diferentes.

## Melhorias futuras
- Playlist com rotação automática.
- Agendamento por horário/dia.
- Código PIN para pareamento por TV.
- Status online/offline da TV.
- Cache offline (PWA) para não ficar tela preta sem internet.

## Stack sugerida
- Front-end: HTML/CSS/JS puro (máxima compatibilidade) ou React/Vite.
- Backend/BaaS: Supabase.
- Hosting: Cloudflare Pages.

## Estimativa de custo
- Começo: **R$ 0** (planos gratuitos).
- Quando crescer: migrar para plano pago conforme armazenamento/banda.
