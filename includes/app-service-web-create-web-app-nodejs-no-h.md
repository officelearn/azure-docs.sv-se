Skapa en webbapp i molnet Shell, den `myAppServicePlan` App Service-plan med den [az webapp skapa](/cli/azure/webapp#create) kommandot. Glöm inte att ersätta `<app_name>` med ett unikt appnamn.

Körningsfel i följande kommando anges till `NODE|6.9`. Om du vill se alla stöds körningar kör [az webapp lista-körningar](/cli/azure/webapp#list-runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
```

När webbappen har skapats visas Azure CLI utdata liknar följande exempel:

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
