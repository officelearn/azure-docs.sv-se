Använd Azure CLI för att hämta URL:en för fjärrdistribution för din API-app. Ersätt *\<app_name>* med din webbapps namn i följande kommando.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Konfigurera din lokala Git-distribution för att kunna push-överföra till en fjärrplats.

```bash
git remote add azure <URI from previous step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen. Du uppmanas att ange lösenordet som du skapade tidigare när du skapade distributionsanvändaren. Se till att du anger det lösenord som du skapade tidigare i snabbstarten, inte lösenordet som du använde när du loggade in på Azure Portal.

```bash
git push azure master
```
