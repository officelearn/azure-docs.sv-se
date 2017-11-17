## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden, tar du bort resursgruppen genom att köra följande kommando i molnet Shell:

```azurecli-interactive
az group delete --name myResourceGroup
```

Det här kommandot kan ta någon minut att köra.