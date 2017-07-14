
Skapa en [API-app](../articles/app-service-api/app-service-api-apps-why-best-platform.md) i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/appservice/web#create). 

Webbappen ger dig ett lagringsutrymme för ditt API och du får en URL så att du kan visa den distribuerade appen.

Ersätt *\<app_name>* med ett unikt namn i följande kommando. Om `<app_name>` inte är unikt får du ett felmeddelande om att webbplatsen med det angivna namnet <app_name> redan finns. Standardwebbadressen för webbappen är `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

När webbappen har skapats visar Azure CLI information liknande den i följande exempel:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "<app_name>.azurewebsites.net",
    "<app_name>.scm.azurewebsites.net"
  ],
  "gatewaySiteName": null,
  "hostNameSslStates": [
    {
      "hostType": "Standard",
      "name": "<app_name>.azurewebsites.net",
      "sslState": "Disabled",
      "thumbprint": null,
      "toUpdate": null,
      "virtualIp": null
    }
    < JSON data removed for brevity. >
}
```