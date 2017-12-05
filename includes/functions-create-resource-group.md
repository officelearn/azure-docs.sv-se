## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk behållare som Azure-resurser (t.ex. funktionsappar, databaser och lagringskonton) distribueras och hanteras i.

I följande exempel skapas en resursgrupp med namnet `myResourceGroup`.  
Om du inte använder molnet Shell, logga in först använda `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig. Om du vill se alla stöds platser för App Service-planer, kör den [az apptjänst lista-platser](/cli/azure/appservice#az_appservice_list_locations) kommando.