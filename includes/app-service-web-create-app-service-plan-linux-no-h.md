Skapa en apptjänstplan i resursgruppen med molnet Shell, den [az programtjänstplan skapa](/cli/azure/appservice/plan#create) kommando.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

I följande exempel skapas en apptjänstplan med namnet `myAppServicePlan` i den **Standard** prisnivån (`--sku S1`) och i en Linux-behållare (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

När App Service-planen har skapats visas information av Azure CLI. Informationen ser ut ungefär som i följande exempel:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```