# Warner Barbearia — Sistema com Firebase

Sistema de agendamento com banco de dados real. Os horários são salvos no Firebase e ficam bloqueados para todos os clientes automaticamente.

---

## Arquivos

```
warner-barbearia/
├── index.html   → Site do cliente (agendamento)
├── admin.html   → Painel do barbeiro
└── README.md
```

---

## Configuração do Firebase (passo a passo)

### 1. Criar o projeto

1. Acesse [console.firebase.google.com](https://console.firebase.google.com)
2. Clique em **"Adicionar projeto"**
3. Nome: `warner-barbearia` → Continuar → Criar projeto

### 2. Ativar o Firestore

1. No menu lateral: **Firestore Database**
2. Clique em **"Criar banco de dados"**
3. Selecione **"Começar no modo de produção"** → Escolha a região `southamerica-east1 (São Paulo)` → Ativar

### 3. Configurar as Regras do Firestore

No Firestore, clique na aba **"Regras"** e cole:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /agendamentos/{id} {
      // Clientes podem criar e ler
      allow read: if true;
      allow create: if true;
      // Apenas atualizar status (para o admin) — sem deletar
      allow update: if resource.data.keys().hasOnly(['status']) == false
                    || request.resource.data.diff(resource.data).affectedKeys().hasOnly(['status']);
      allow delete: if false;
    }
  }
}
```

Clique em **Publicar**.

### 4. Criar os índices do Firestore

Para que as queries funcionem, você precisa criar um índice composto. No Firestore:

1. Clique em **"Índices"** → **"Adicionar índice"**
2. Coleção: `agendamentos`
3. Campos:
   - `dateKey` — Crescente
   - `barbeiro` — Crescente
   - `status` — Crescente
4. Clique em **Criar índice** e aguarde alguns minutos

Repita para o índice do painel admin:
- `dateKey` — Crescente
- `horario` — Crescente

### 5. Pegar as credenciais do Firebase

1. Nas configurações do projeto (ícone de engrenagem) → **"Configurações do projeto"**
2. Role até **"Seus aplicativos"** → clique em **"</>  Web"**
3. Nome do app: `warner-site` → Registrar
4. Copie o objeto `firebaseConfig` que aparecer:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "warner-barbearia.firebaseapp.com",
  projectId: "warner-barbearia",
  storageBucket: "warner-barbearia.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123..."
};
```

### 6. Colar as credenciais nos arquivos

Abra **index.html** e **admin.html** e substitua `"COLE_AQUI"` pelos valores do seu `firebaseConfig`. Faça isso nos dois arquivos.

---

## Hospedagem no GitHub Pages (grátis)

1. Crie um repositório no GitHub: `warner-barbearia`
2. Faça upload de `index.html` e `admin.html`
3. **Settings → Pages → Branch: main → Save**
4. Site disponível em: `https://seu-usuario.github.io/warner-barbearia`
5. Admin disponível em: `https://seu-usuario.github.io/warner-barbearia/admin.html`

---

## Senha do painel admin

Por padrão a senha é `warner2025`. Para mudar, abra `admin.html` e altere esta linha:

```js
const ADMIN_PASSWORD = 'warner2025'; // ← MUDE AQUI
```

---

## O que o sistema faz

### Site do cliente (`index.html`)
- Fluxo de 5 etapas: serviço → barbeiro → data/hora → dados → confirmação
- Horários ocupados são carregados do Firebase **em tempo real**
- Se dois clientes abrirem o mesmo horário ao mesmo tempo, o segundo verá o horário bloqueado automaticamente
- Ao confirmar, salva no Firestore e abre o WhatsApp do Warner com mensagem pronta
- Mathias não tem sistema de hora marcada (exibe aviso de ordem de chegada)

### Painel admin (`admin.html`)
- Login por senha
- Navegação por dia (setas ou calendário)
- Lista de agendamentos em **tempo real** — atualiza sozinho quando alguém agenda
- Cards de estatísticas: total, confirmados, faturamento previsto, próximo horário
- Ações: marcar como **Concluído** ou **Cancelar** (libera o horário para novos clientes)
- Exportar CSV do dia

---

## Como editar preços, horários e serviços

### Mudar preços
Em `index.html`, nos cards de serviço:
```html
<div class="svc-card" data-svc="Corte Simples" data-price="40" ...>
  <div class="svc-price">R$40<span> /sessão</span></div>
```

### Mudar horários de funcionamento
Função `getSlots` no `index.html`:
```js
function getSlots(date) {
  const sat = date.getDay() === 6;
  const s = sat ? 8 : 9;   // início: sábado = 8h, dias úteis = 9h
  const e = sat ? 16 : 19; // fim: sábado = 16h, dias úteis = 19h
  ...
}
```

### Mudar dias de funcionamento
Função `isOpen`:
```js
function isOpen(date) {
  const w = date.getDay();
  return w >= 2 && w <= 6; // terça (2) até sábado (6)
}
```

---

## Limites gratuitos do Firebase

O plano gratuito (Spark) do Firebase suporta:
- **50.000 leituras/dia**
- **20.000 escritas/dia**
- **1 GB de armazenamento**

Para uma barbearia pequena com ~10-20 agendamentos por dia, o plano gratuito é mais que suficiente por anos.

---

Feito para a Warner Barbearia — Nova Friburgo, RJ
