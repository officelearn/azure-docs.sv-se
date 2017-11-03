I Cloud Shell skapar du en [webbapp](../articles/app-service/app-service-web-overview.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp#create). 

Webbappen ger dig ett lagringsutrymme för din kod och du får en URL så att du kan visa den distribuerade appen.

Ersätt *\<app_name>* med ett unikt namn (giltiga tecken är `a-z`, `0-9` och `-`) i följande kommando. Om `<app_name>` inte är unikt får du ett felmeddelande om att webbplatsen med det angivna namnet <app_name> redan finns. Standardwebbadressen för webbappen är `https://<app_name>.azurewebsites.net`. 

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

Gå till webbplatsen för att se webbappen du precis skapade.

```bash
http://<app_name>.azurewebsites.net
```
