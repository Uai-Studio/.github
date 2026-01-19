# MANUAL OPERACIONAL — PLATAFORMA DE JOGOS

## STACK DEFINIDA

* Frontend: React + Next.js (TypeScript)
* Backend (dados/auth): Supabase (Postgres)
* Hosting frontend: Cloudflare Pages
* Builds WebGL: Cloudflare R2
* CI/CD: GitHub Actions
* Organização: 1 repositório por jogo + 1 repositório da plataforma

---

## FLUXOGRAMA GERAL

[DEV LOCAL]
↓
[REPO DO JOGO (UNITY)]
↓ (build WebGL local)
[webgl.zip]
↓ (GitHub Release)
[GITHUB ACTION]
↓
[CLOUDFLARE R2]
↓ (URL pública)
[SUPABASE — tabela releases]
↓
[PLATAFORMA NEXT.JS]
↓
[USUÁRIO FINAL — JOGAR NO BROWSER]

---

## FLUXOGRAMA DA PLATAFORMA

[DEV]
↓
[Repo game-platform]
↓ (push/merge)
[Cloudflare Pages]
↓
[Produção — domínio público]

---

## 1. CRIAÇÃO DO REPOSITÓRIO DA PLATAFORMA

1. Criar repositório GitHub: `game-platform`
2. Clonar repositório localmente
3. Inicializar Next.js
4. Commit inicial
5. Push para `main`

---

## 2. CONFIGURAÇÃO DO SUPABASE

1. Criar projeto no Supabase
2. Copiar:

   * Project URL
   * anon public key
3. Criar tabelas:

   * games
   * releases
4. Ativar RLS nas tabelas
5. Criar policies de SELECT público para jogos publicados

---

## 3. CONFIGURAÇÃO DO FRONTEND

1. Instalar `@supabase/supabase-js`
2. Criar client Supabase
3. Criar páginas:

   * /games
   * /games/[slug]
   * /play/[slug]
4. Buscar dados via Supabase
5. Commit e push

---

## 4. DEPLOY DA PLATAFORMA

1. Cloudflare Pages → Create Application
2. Conectar repositório `game-platform`
3. Definir build:

   * Build: `npm run build`
   * Framework: Next.js
4. Configurar variáveis de ambiente:

   * NEXT_PUBLIC_SUPABASE_URL
   * NEXT_PUBLIC_SUPABASE_ANON_KEY
5. Deploy automático

---

## 5. CRIAÇÃO DO REPOSITÓRIO DO JOGO

1. Criar repositório GitHub: `game-<slug>`
2. Definir `slug` do jogo
3. Configurar secrets do repositório

Secrets obrigatórios:

* R2_ACCOUNT_ID
* R2_ACCESS_KEY_ID
* R2_SECRET_ACCESS_KEY
* R2_BUCKET
* GAME_SLUG
* PUBLIC_BASE_URL

---

## 6. PUBLICAÇÃO DE UM JOGO (UNITY WEBGL)

1. Gerar build WebGL local no Unity
2. Garantir `index.html` na raiz
3. Zipar conteúdo → `webgl.zip`
4. Criar GitHub Release

   * Tag: `vX.Y.Z`
   * Anexar `webgl.zip`
5. Publicar release

---

## 7. GITHUB ACTION (REPO DO JOGO)

Trigger:

* Release published

Processo:

1. Baixar `webgl.zip`
2. Descompactar
3. Upload para R2:

   * /<slug>/<version>/
4. Gerar URL pública final

---

## 8. REGISTRO NO SUPABASE

1. Inserir jogo em `games`

   * slug
   * title
   * is_published = true

2. Inserir release em `releases`

   * game_id
   * version
   * webgl_url

---

## 9. ACESSO FINAL

URL catálogo:

* /games

URL jogar:

* /play/<slug>

---

## CHECKLIST FINAL

[ ] Plataforma publicada
[ ] Supabase configurado
[ ] Bucket R2 ativo
[ ] Repo do jogo com Action
[ ] Release publicada
[ ] Registro no Supabase criado
[ ] Jogo acessível no browser

---

## PADRÕES OBRIGATÓRIOS

* Versionamento: `vX.Y.Z`
* 1 repo por jogo
* Build sempre versionado
* Nunca sobrescrever builds antigos
* Publicação somente via Release
