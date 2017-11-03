## <a name="clean-up-deployment"></a>Rensa distribution 

Efter skriptexempel har körts, användas Följ kommandot för att ta bort resursgruppen, Azure Redis-Cache-instans och eventuella relaterade resurser i resursgruppen.

```azurecli
az group delete --name contosoGroup
```