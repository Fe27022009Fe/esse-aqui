# Como publicar o BABY — Quiz (passo a passo, do zero)

Este guia assume que você nunca usou Supabase nem Vercel. Leva cerca de 15–20
minutos. Você vai precisar de uma conta de e-mail e, de preferência, uma
conta no GitHub (gratuita) para facilitar o deploy na Vercel.

## Parte 1 — Colocar o código no GitHub

1. Crie uma conta em https://github.com (se ainda não tiver).
2. Clique em **New repository**, dê um nome (ex.: `quiz-baby`) e crie o
   repositório (pode ser privado).
3. Na página do repositório, clique em **uploading an existing file** e
   arraste TODOS os arquivos e pastas deste projeto (mantendo a estrutura de
   pastas) — ou, se preferir usar o terminal:
   ```bash
   cd quiz-baby
   git init
   git add .
   git commit -m "Quiz Baby inicial"
   git branch -M main
   git remote add origin https://github.com/SEU-USUARIO/quiz-baby.git
   git push -u origin main
   ```

## Parte 2 — Criar o banco de dados (Supabase)

1. Acesse https://supabase.com e clique em **Start your project** / **Sign up**.
   Crie a conta com seu e-mail (ou com o GitHub).
2. Clique em **New project**.
   - Escolha um nome, ex.: `quiz-baby`.
   - Defina uma senha de banco de dados (guarde-a, mas ela não será usada
     diretamente pelo app).
   - Escolha a região mais próxima de você (ex.: South America / São Paulo,
     se disponível).
   - Clique em **Create new project** e aguarde 1–2 minutos.
3. No menu lateral, clique em **SQL Editor** → **New query**.
4. Abra o arquivo `supabase/schema.sql` deste projeto, copie TODO o
   conteúdo, cole no editor e clique em **Run**. Isso cria todas as tabelas
   e já insere as 12 perguntas originais do quiz.
5. No menu lateral, clique em **Project Settings** (ícone de engrenagem) →
   **API**. Você vai precisar de três valores nesta tela:
   - **Project URL** → vai virar `NEXT_PUBLIC_SUPABASE_URL`
   - **anon public** key → vai virar `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - **service_role** key (clique em "Reveal") → vai virar
     `SUPABASE_SERVICE_ROLE_KEY` — **esta chave é secreta, nunca a compartilhe
     nem a coloque em um arquivo público**.
6. Por padrão, o Supabase já habilita o Realtime para as tabelas. Se quiser
   conferir: menu **Database** → **Replication** → confirme que `rooms` e
   `players` estão marcadas para replicação (o script já deixa isso
   funcionando na maioria dos planos; se não aparecer marcado, marque e
   salve).

## Parte 3 — Publicar o site (Vercel)

1. Acesse https://vercel.com e crie uma conta (recomendado: **Continue with
   GitHub**, assim o import fica automático).
2. Clique em **Add New...** → **Project**.
3. Selecione o repositório `quiz-baby` que você criou na Parte 1 e clique em
   **Import**.
4. Na tela de configuração, abra **Environment Variables** e adicione as
   quatro variáveis abaixo (os valores vêm da Parte 2 e de uma senha que
   você escolher):

   | Nome                            | Valor                                   |
   |----------------------------------|------------------------------------------|
   | `NEXT_PUBLIC_SUPABASE_URL`       | Project URL do Supabase                   |
   | `NEXT_PUBLIC_SUPABASE_ANON_KEY`  | anon public key do Supabase               |
   | `SUPABASE_SERVICE_ROLE_KEY`      | service_role key do Supabase (secreta)    |
   | `ADMIN_PASSWORD`                 | uma senha forte, escolhida por você       |

5. Clique em **Deploy**. Em 1–2 minutos a Vercel mostra "Congratulations" com
   uma URL pública, do tipo `https://quiz-baby-xxxx.vercel.app`.
6. Pronto — essa URL já é pública e qualquer pessoa pode acessá-la, pelo
   celular ou computador.

## Parte 4 — Testar

1. Abra a URL pública em uma aba → `/host` → digite a senha de administrador
   (a que você colocou em `ADMIN_PASSWORD`) → **Criar partida**. Anote o
   código de 5 caracteres que aparece.
2. Abra a mesma URL em outra aba (ou no celular, ou peça para um amigo
   acessar) → **Entrar com código** → digite o código e um apelido.
3. Na aba do apresentador, clique em **Iniciar partida**. As perguntas devem
   aparecer simultaneamente nas duas telas.
4. Percorra o checklist de testes abaixo.

## Checklist de testes antes de divulgar o link

- [ ] Criar uma partida e gerar o código
- [ ] Dois ou mais participantes diferentes entram com o mesmo código
- [ ] Apresentador inicia a partida
- [ ] Respostas são enviadas e bloqueadas após a primeira escolha
- [ ] Cronômetro conta corretamente e o tempo esgotado conta como erro
- [ ] Pontuação é calculada (base + velocidade + sequência) e some no ranking
- [ ] Ranking atualiza em tempo real para todos
- [ ] Painel do administrador: renomear, silenciar e remover participante
- [ ] Painel do administrador: ajustar pontos manualmente
- [ ] Painel do administrador: pular pergunta, voltar pergunta, pausar/retomar
- [ ] Encerramento mostra o pódio e as estatísticas
- [ ] Fechar a aba e reabrir com o mesmo código mantém o jogador (reconexão)
- [ ] Testar em um celular e em um computador ao mesmo tempo

## Perguntas frequentes

**Preciso pagar alguma coisa?**
Não. Os planos gratuitos do Supabase e da Vercel são suficientes para uma
partida com um grupo de amigos, sala de aula ou evento pequeno/médio.

**Posso trocar as perguntas depois?**
Sim — edite diretamente a tabela `questions` pelo **Table Editor** do
Supabase (menu lateral). As mudanças valem para as próximas partidas.

**Esqueci a senha do administrador.**
Troque o valor de `ADMIN_PASSWORD` nas variáveis de ambiente do projeto na
Vercel (Project → Settings → Environment Variables) e clique em "Redeploy".

**O código some/expira?**
Não expira sozinho. Cada sala fica no banco até você apagá-la manualmente
(Table Editor → tabela `rooms`). Para uma nova partida, basta criar outra
sala — ela recebe um código novo.
