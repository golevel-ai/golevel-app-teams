# Ajustes Necessários no Backend — Integração Microsoft Teams

## Contexto

O Hablas foi integrado como aplicativo no Microsoft Teams (Personal Tab). O app carrega `app.hablas.chat` dentro de um iframe no cliente Teams.

**Problema**: ao trocar de aba no Teams e voltar, o usuário precisa logar novamente.

**Causa**: configurações de segurança do servidor impedem o funcionamento correto dentro de iframes de terceiros.

---

## 3 Ajustes Obrigatórios

### 1. Remover bloqueio de iframe

**Problema atual**: o header `X-Frame-Options: SAMEORIGIN` impede o carregamento dentro do Teams.

**No Nginx** (`nginx.conf` ou arquivo de configuração do site):

```nginx
# REMOVER esta linha:
# add_header X-Frame-Options "SAMEORIGIN";

# ADICIONAR:
add_header Content-Security-Policy "frame-ancestors 'self' https://*.teams.microsoft.com https://teams.microsoft.com https://teams.live.com https://enterprise-261.hablas.chat";
```

**No Node.js/Express** (se o header estiver sendo definido no código):

```javascript
app.use((req, res, next) => {
  res.removeHeader('X-Frame-Options');
  res.setHeader(
    'Content-Security-Policy',
    "frame-ancestors 'self' https://*.teams.microsoft.com https://teams.microsoft.com https://teams.live.com"
  );
  next();
});
```

---

### 2. Ajustar CORS para permitir credenciais

**Problema atual**: `access-control-allow-origin: *` não permite envio de cookies em requests cross-origin.

**No Node.js/Express:**

```javascript
const cors = require('cors');

app.use(cors({
  origin: [
    'https://teams.microsoft.com',
    'https://teams.live.com',
    'https://app.hablas.chat'
  ],
  credentials: true
}));
```

---

### 3. Cookies com atributos para funcionar em iframe

**Problema atual**: cookies de sessão são bloqueados em contexto de terceiros.

**No código que define os cookies de autenticação:**

```javascript
res.cookie('nome_do_cookie', valor, {
  sameSite: 'none',
  secure: true,
  httpOnly: true
});
```

Se usa `express-session`:

```javascript
app.use(session({
  secret: 'seu-secret',
  cookie: {
    sameSite: 'none',
    secure: true,
    httpOnly: true
  }
}));
```

---

## Resumo

| Ajuste | Onde | O que fazer |
|--------|------|-------------|
| iframe | Nginx ou Express | Remover `X-Frame-Options` e adicionar `frame-ancestors` no CSP |
| CORS | Express | Trocar `origin: *` por lista de origens + `credentials: true` |
| Cookies | Express | Adicionar `sameSite: 'none'` e `secure: true` |

## Prioridade

**Alta** — sem esses ajustes, o app no Teams pede login toda vez que o usuário troca de aba.
