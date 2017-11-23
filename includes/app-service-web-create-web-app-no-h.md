I Cloud Shell skapar du en [webbapp](../articles/app-service/app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#create). 

I följande exempel ersätter  *\<appnamn >* med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9`, och `-`). 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

När webbappen har skapats visar Azure CLI information liknande den i följande exempel:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Du har skapat en tom webbapp med git-distribution som är aktiverad.

> [!NOTE]
> URL för Git-fjärråtkomstprincipen visas i den `deploymentLocalGitUrl` egenskap med formatet `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Spara den här URL: en som du behöver senare.
>

Bläddra till den nya webbappen.

```bash
http://<app_name>.azurewebsites.net
```
