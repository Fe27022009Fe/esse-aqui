# BABY — Quiz ao vivo

Plataforma de quiz multiplayer em tempo real, inspirada na experiência do
Kahoot, construída a partir do `Quiz Baby.html` original (12 perguntas sobre
a música "Baby", de Justin Bieber feat. Ludacris — todas preservadas).

## Stack

- **Next.js 14** (App Router) + **TypeScript** — front-end e rotas de API
- **Supabase** (Postgres + Realtime) — banco de dados e sincronização ao vivo
- **Vercel** — hospedagem

## Rodando localmente

```bash
npm install
cp .env.example .env.local   # preencha com as chaves do seu projeto Supabase
npm run dev
```

Antes de rodar, execute `supabase/schema.sql` no SQL Editor do seu projeto
Supabase (veja `DEPLOY.md` para o passo a passo completo, do zero).

Abra http://localhost:3000 — `/host` para criar uma partida, `/play/CODIGO`
para entrar como jogador.

## Estrutura

```
app/
  page.tsx                 → tela inicial
  host/page.tsx            → painel do apresentador/administrador
  play/[code]/page.tsx     → tela do jogador
  api/rooms/...            → toda a lógica de servidor (validação, pontuação,
                              ações administrativas) — nada de sensível roda
                              no navegador
src/
  lib/                     → cliente Supabase, tipos, regras de pontuação
  components/              → Timer, Confetti, Leaderboard, Podium
supabase/schema.sql        → esquema completo do banco (tabelas + RLS + seed
                              das 12 perguntas originais)
DEPLOY.md                  → como publicar do zero (Supabase + Vercel)
```

## Segurança

- A `service_role key` do Supabase só é usada dentro das rotas de API (no
  servidor) e nunca é enviada ao navegador.
- A resposta certa de cada pergunta nunca trafega para o navegador antes do
  momento de revelar — o front-end recebe apenas o texto da pergunta e as
  alternativas embaralhadas.
- Toda pontuação é calculada no servidor a partir do horário real de início
  da pergunta, não pelo relógio do navegador do jogador.
- Uma trava no banco (`unique (room_id, player_id, question_cycle)`) impede
  respostas duplicadas na mesma pergunta.
