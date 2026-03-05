# Configuração do Azure Portal para SSO no Teams

## 1. App Registration

No [Azure Portal](https://portal.azure.com) → **Microsoft Entra ID** → **App registrations** → selecionar o app `c4cc93b9-e10c-43b3-9a94-f273d7c9a49c`.

## 2. Expose an API

1. Ir em **Expose an API**
2. Definir **Application ID URI**: `api://admin.golevel.com.br/c4cc93b9-e10c-43b3-9a94-f273d7c9a49c`
3. Adicionar scope **access_as_user**:
   - Admin consent display name: `Access GoLevel as user`
   - Admin consent description: `Allow Teams to access GoLevel on behalf of the user`
   - State: **Enabled**

## 3. Authorized Client Applications

Adicionar os client IDs do Microsoft Teams como clientes autorizados para o scope `access_as_user`:

| Cliente | Client ID |
|---------|-----------|
| Teams Desktop / Mobile | `1fec8e78-bce4-4aaf-ab1b-5451cc387264` |
| Teams Web | `5e3ce6c0-2b1f-4285-8d4b-75ee78787346` |
| Office Web | `4765445b-32c6-49b0-83e6-1d93765276ca` |
| Outlook Desktop | `d3590ed6-52b3-4102-aeff-aad2292ab01c` |

## 4. Redirect URIs

Em **Authentication** → **Platform configurations** → **Web**, adicionar:

```
https://hznoefdjdgtvrldajcdf.supabase.co/auth/v1/callback
https://admin.golevel.com.br/teams/auth-callback.html
```

## 5. API Permissions

Verificar que tem:
- `User.Read` (delegated)
- `openid` (delegated)
- `profile` (delegated)
- `email` (delegated)

## 6. Implicit Grant

Em **Authentication** → marcar:
- ✅ **ID tokens**
- ✅ **Access tokens**
