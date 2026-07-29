# Spool — como ativar a sincronização na nuvem

Isto liga o Spool a uma base de dados gratuita da Google (Firebase), para que os
seus dados apareçam iguais em qualquer telemóvel ou computador onde iniciar
sessão com o mesmo e-mail. Leva uns 5 minutos a configurar, uma única vez.

Sem fazer isto, o app continua a funcionar normalmente — só que os dados ficam
guardados apenas no aparelho onde os inseriu.


## Passo 1 — Criar o projeto Firebase (grátis)

1. Aceda a https://console.firebase.google.com e inicie sessão com uma conta Google.
2. Clique em "Criar projeto" (Add project).
3. Dê um nome (ex: "spool-erp") e avance. Pode desativar o Google Analytics
   (não é necessário) e clicar em "Criar projeto".


## Passo 2 — Ativar a Base de Dados (Firestore)

1. No menu à esquerda, vá a "Compilação" (Build) → "Firestore Database".
2. Clique em "Criar base de dados" (Create database).
3. Escolha "Iniciar em modo de produção" (Start in production mode).
4. Escolha uma localização (ex: "eur3 (europe-west)", a mais próxima de Portugal)
   e clique em "Ativar" (Enable).


## Passo 3 — Configurar as regras de segurança

1. Ainda na Firestore Database, clique no separador "Regras" (Rules).
2. Apague o conteúdo e cole exatamente isto:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/appdata/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

3. Clique em "Publicar" (Publish).

Isto garante que cada pessoa só consegue ler/escrever os seus próprios dados —
ninguém mais tem acesso, nem sequer outros utilizadores do mesmo projeto Firebase.


## Passo 4 — Ativar o login por e-mail

1. No menu à esquerda, vá a "Compilação" (Build) → "Authentication".
2. Clique em "Começar" (Get started).
3. Na lista de métodos de login, clique em "E-mail/Password".
4. Ative a primeira opção ("E-mail/Password") e clique em "Guardar" (Save).


## Passo 5 — Obter a configuração e colar no Spool

1. Clique no ícone de engrenagem ⚙️ ao lado de "Visão geral do projeto" (no
   topo do menu esquerdo) → "Definições do projeto" (Project settings).
2. Desça até "Os seus apps" (Your apps) e clique no ícone `</>` (Web).
3. Dê um nome à app (ex: "spool-web") e clique em "Registar app" (Register app).
   Não precisa de configurar hosting nem mais nada.
4. Vai aparecer um bloco de código com algo parecido com isto:

```js
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "spool-erp.firebaseapp.com",
  projectId: "spool-erp",
  storageBucket: "spool-erp.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abcdef"
};
```

5. Copie **apenas o conteúdo entre chavetas `{ }`** (ou o objeto todo, tanto faz).
6. No Spool, vá à aba **Config → Sincronização na Nuvem → "Configurar
   sincronização na nuvem"**, cole esse conteúdo na caixa de texto, e clique em
   "Guardar e continuar".


## Passo 6 — Criar a sua conta

1. Ainda na mesma secção, escolha "Criar conta", introduza o seu e-mail e uma
   password (mínimo 6 caracteres), e clique em "Criar conta".
2. Pronto — o Spool está agora sincronizado. Repita o Passo 6 (desta vez
   escolhendo "Iniciar sessão") em qualquer outro telemóvel/computador onde
   queira aceder aos mesmos dados, usando a configuração do Passo 5 (pode
   copiá-la de um aparelho para o outro, ou repetir o Passo 5 lendo a mesma
   configuração no Firebase Console).


## Notas importantes

- **É grátis** para o volume de dados de um negócio pequeno como este (o
  plano gratuito do Firebase — "Spark" — inclui muito mais do que o
  necessário aqui).
- Se perder a password, use o link "Esqueci a password" no ecrã de login —
  a Firebase envia um e-mail de recuperação.
- Os dados continuam também a ficar guardados localmente no aparelho (como
  cópia rápida/offline); a nuvem é a fonte de verdade quando há sessão iniciada.
- Pode a qualquer momento clicar em "Usar outra configuração" no ecrã de
  login para desligar a nuvem e voltar a usar o Spool apenas neste aparelho.
