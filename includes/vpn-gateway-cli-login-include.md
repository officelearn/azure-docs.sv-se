Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen. Mer information om att logga in finns i [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Om du har fler än en Azure-prenumeration anger du prenumerationerna för kontot.

```azurecli
az account list --all
```

Ange den prenumeration som du vill använda.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```