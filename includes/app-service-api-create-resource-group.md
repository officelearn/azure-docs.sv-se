Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Om du vill se tillgängliga platser kör du kommandot `az appservice list-locations`. Du skapar vanligtvis resurser i en region nära dig.
