# Cómo colocar un token de acceso para que lo use deno

Para configurar un token de acceso en Deno Deploy, necesitas crear un token y exportarlo como variable de entorno.[^1]

## Pasos para configurar el token

### 1. Crear un Access Token

Primero, ve al dashboard de Deno Deploy y crea un token de acceso:[^3]

1. Accede a tu cuenta de Deno Deploy
2. Ve a la sección de **Access Tokens** en el dashboard
3. Haz clic en "New Access Token" o "Create Access Token"
4. Dale un nombre descriptivo (ej: "WSL Development")
5. Copia el token generado (solo se mostrará una vez)

### 2. Exportar el token como variable de entorno

Dependiendo de tu sistema operativo, usa uno de estos comandos:[^1]

**En Linux/WSL2:**

```bash
export DENO_DEPLOY_TOKEN=tu_token_aqui
```

**En Windows PowerShell:**

```powershell
$env:DENO_DEPLOY_TOKEN = 'tu_token_aqui'
```

**En Windows CMD:**

```cmd
set DENO_DEPLOY_TOKEN=tu_token_aqui
```

### 3. Hacer el token permanente (opcional)

Para que el token esté disponible en cada sesión, agrégalo a tu archivo de configuración de shell:[^1]

**En WSL2/Linux (bash):**

```bash
echo 'export DENO_DEPLOY_TOKEN=tu_token_aqui' >> ~/.bashrc
source ~/.bashrc
```

**En WSL2/Linux (zsh):**

```bash
echo 'export DENO_DEPLOY_TOKEN=tu_token_aqui' >> ~/.zshrc
source ~/.zshrc
```

### 4. Ejecutar el deploy

Ahora puedes ejecutar tu comando normalmente:[^4]

```bash
deno deploy --tunnel index.js
```

El CLI de Deno Deploy detectará automáticamente la variable de entorno `DENO_DEPLOY_TOKEN` y la usará en lugar de intentar guardar credenciales en el keychain.[^1]

<span style="display:none">[^9]</span>
<break />

<div align="center">⁂</div>

[^1]: https://bejamas.com/hub/hosting/deno-deploy

[^2]: https://docs.deno.com/subhosting/api/authentication/

[^3]: https://docs.doppler.com/docs/deno-deploy

[^4]: https://docs.deno.com/runtime/reference/cli/deploy/

[^5]: https://github.com/denoland/deno/issues/5239

[^6]: https://stackoverflow.com/questions/69458798/how-do-you-get-deno-to-use-the-deno-auth-tokens-environment-variable

[^7]: https://docs.deno.com/deploy/classic/deployctl/

[^8]: https://docs.deno.com/deploy/reference/cloud_connections/

[^9]: https://github.com/denoland/deploy_feedback/issues/531

[^10]: https://docs.restate.dev/services/deploy/deno-deploy

