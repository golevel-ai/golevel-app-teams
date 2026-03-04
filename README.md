# GoLevel — Aplicativo Microsoft Teams

Aplicativo Microsoft Teams que permite acessar o [GoLevel](https://admin.golevel.com.br) diretamente dentro do Teams como uma aba pessoal, com login SSO nativo da Microsoft.

## Estrutura

| Arquivo | Descrição |
|---------|-----------|
| `manifest.json` | Definição e configuração do app Teams |
| `color.png` | Ícone colorido 192×192px |
| `outline.png` | Ícone outline 32×32px (fundo transparente, contorno branco) |

## Configuração SSO (Azure AD)

Antes de gerar o pacote, configure o SSO no Azure AD:

1. Registrar o aplicativo no [Azure Portal](https://portal.azure.com) → **Azure Active Directory** → **App registrations**
2. Copiar o **Application (client) ID**
3. No `manifest.json`, substituir `{{AZURE_APP_CLIENT_ID}}` pelo ID real
4. Configurar **Redirect URI**: `https://admin.golevel.com.br/auth/teams/callback`
5. Em **API permissions**, adicionar: `User.Read`, `openid`, `profile`, `email`

## Como instalar

### 1. Gerar o pacote

```bash
zip golevel-teams-app.zip manifest.json color.png outline.png
```

### 2. Upload no Teams

1. Abrir **Microsoft Teams**
2. Ir em **Apps** → **Gerenciar seus apps**
3. Clicar em **Fazer upload de um app** → **Fazer upload de um app personalizado**
4. Selecionar o arquivo `golevel-teams-app.zip`
5. Confirmar a instalação

### 3. Via Developer Portal (alternativa)

1. Acessar [dev.teams.microsoft.com](https://dev.teams.microsoft.com)
2. Clicar em **Apps** → **Import app**
3. Selecionar o arquivo `golevel-teams-app.zip`
4. Revisar configurações e publicar

## Publicação para a organização

O administrador do Microsoft 365 pode aprovar e distribuir o app para todos os usuários:

1. Acessar o **Teams Admin Center**
2. Ir em **Teams apps** → **Manage apps**
3. Fazer upload do pacote
4. Aprovar e definir políticas de permissão

## Pré-requisitos

- Conta Microsoft Teams com permissão para sideloading de apps
- O site `admin.golevel.com.br` deve estar acessível via HTTPS
- O site deve permitir carregamento em iframe (sem bloqueio via `X-Frame-Options`)
- Aplicativo registrado no Azure AD para SSO

## Troubleshooting

| Problema | Solução |
|----------|---------|
| App não carrega o site | Verificar headers `X-Frame-Options` e `Content-Security-Policy` no servidor |
| Erro ao fazer upload | Verificar se o `.zip` contém exatamente os 3 arquivos na raiz |
| Tela em branco | Verificar se `admin.golevel.com.br` está online e acessível |
| SSO não funciona | Verificar configuração do Azure AD e se o `client_id` está correto no manifest |
