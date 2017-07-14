Konfigurera lokal Git-distribution till API-appen med kommandot [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).   

Med App Service kan du distribuera innehåll till en webbplats på flera sätt, t.ex. med FTP, lokala Git, GitHub, Visual Studio Team Services och Bitbucket. I den här snabbstarten ska vi distribuera med hjälp av lokala Git. Det betyder att du distribuerar innehållet med hjälp av ett Git-kommando som skickar data från en lokal databas till en databas i Azure.  

Använd skriptet nedan för att ange autentiseringsuppgifter för distributionen på kontonivån som du använder vid push-överföring av koden och se till att ta med dina egna värden för användarnamn och lösenordet.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
