# Warner Barbearia — Site de Agendamento

Site de agendamento completo para a Warner Barbearia, feito em HTML, CSS e JavaScript puro. Sem dependências externas, sem servidor necessário.

## Como colocar no ar (grátis)

### Opção 1 — GitHub Pages (recomendado)
1. Crie um repositório no GitHub (ex: `warner-barbearia`)
2. Faça upload do arquivo `index.html`
3. Vá em **Settings → Pages → Branch: main → Save**
4. O site fica disponível em: `https://seu-usuario.github.io/warner-barbearia`

### Opção 2 — Netlify (mais fácil ainda)
1. Acesse [netlify.com](https://netlify.com)
2. Arraste a pasta com o `index.html` direto na tela
3. Pronto — link gerado na hora

---

## Como editar

Abra o `index.html` em qualquer editor de texto (Notepad, VS Code, etc).

### Mudar preços
Procure os cards de serviço e altere o `data-price` e o texto:
```html
<div class="svc-card" data-svc="Corte Simples" data-price="40" ...>
  <div class="svc-price">R$40<span> /sessão</span></div>
```

### Mudar horários de funcionamento
No JavaScript, procure a função `getSlots`:
```js
function getSlots(date) {
  const sat = date.getDay() === 6;
  const s = sat ? 8 : 9;   // hora de início (sábado : dias úteis)
  const e = sat ? 16 : 19; // hora de fim
  ...
}
```

### Mudar o WhatsApp do Warner
Procure e substitua `5522981362541` pelo número correto (com DDI 55 e DDD, sem espaços):
```html
href="https://wa.me/5522981362541"
```

### Adicionar/remover serviços
Copie um bloco `.svc-card` e ajuste os dados:
```html
<div class="svc-card" data-svc="NOME DO SERVIÇO" data-price="VALOR" onclick="selectService(this)">
  <div class="svc-name">Nome do Serviço</div>
  <div class="svc-dur">60 minutos</div>
  <div class="svc-price">R$XX<span> /sessão</span></div>
</div>
```

### Mudar dias de funcionamento
Na função `isOpen`, os números representam os dias da semana (0=Dom, 1=Seg, 2=Ter, 3=Qua, 4=Qui, 5=Sex, 6=Sáb):
```js
function isOpen(d) {
  const w = d.getDay();
  return w >= 2 && w <= 6; // terça (2) até sábado (6)
}
```

---

## Funcionamento

- O cliente escolhe o **serviço → barbeiro → data → horário → nome e WhatsApp**
- Se escolher o **Mathias**, o sistema informa que é por ordem de chegada (sem calendário)
- Ao confirmar, o site abre o **WhatsApp do Warner** com a mensagem já preenchida
- Os horários marcados ficam bloqueados enquanto a página estiver aberta na mesma sessão

> **Limitação atual:** os agendamentos não são salvos em banco de dados. Se a página for recarregada, os horários voltam a aparecer disponíveis. Para resolver isso no futuro, é possível integrar com Google Sheets ou Firebase (gratuito).

---

## Estrutura do projeto

```
warner-barbearia/
└── index.html   ← tudo em um arquivo só
```

---

Feito com ❤ para a Warner Barbearia — Nova Friburgo, RJ
