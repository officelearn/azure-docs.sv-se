Konfigurera lokal Git-distribution till webbappen med kommandot [az webapp deployment source config-local-git](/cli/azure/webapp/deployment/source#config-local-git).

Med App Service kan du distribuera innehåll till en webbplats på flera sätt, t.ex. med FTP, lokala Git, GitHub, Visual Studio Team Services och Bitbucket. I den här snabbstarten ska vi distribuera med hjälp av lokala Git. Det betyder att du distribuerar innehållet med hjälp av ett Git-kommando som skickar data från en lokal databas till en databas i Azure. 

Ersätt *\<app_name>* med din webbapps namn i följande kommando.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Kommandot returnerar utdata med följande format:

```bash
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

`<username>` är [distributionsanvändaren](#configure-a-deployment-user) som du skapade i ett tidigare steg.

Kopiera den URI som visas. Du ska använda den i nästa steg.
