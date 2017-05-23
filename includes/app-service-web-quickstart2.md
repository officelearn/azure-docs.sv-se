Använd kommandot [az appservice web create](/cli/azure/appservice/web#create) för att skapa webbappen. I följande kommando använder du ett unikt appnamn där du ser platshållaren `<app_name>`. `<app_name>` används i DNS-standardwebbplatsen för webbappen. Om `<app_name>` inte är unikt får du ett felmeddelande om att webbplatsen med det angivna namnet <appens_namn> redan finns.

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan quickStartPlan
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

Visa webbappen som du nyss skapat genom att gå till webbplatsen (`http://<app_name>.azurewebsites.net`).

![app-service-web-service-created](../articles/app-service-web/media/app-service-web-get-started-nodejs-poc/app-service-web-service-created.png)


## <a name="configure-local-git-deployment"></a>Konfigurera den lokala Git-distributionen

Med App Service kan du distribuera innehåll till en webbplats på flera sätt, t.ex. med FTP, lokala Git, GitHub, Visual Studio Team Services och Bitbucket. 

I den här snabbstarten ska vi distribuera med hjälp av lokala Git. Det betyder att du distribuerar innehållet med hjälp av ett Git-kommando som skickar data från en lokal databas till en databas i Azure. 

Konfigurera åtkomsten för lokala Git till webbappen med kommandot [az appservice web source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).

```azurecli
az appservice web source-control config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Kommandot returnerar utdata med följande format:

```
https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
```

Spara URI:n som visas. Du ska använda den i nästa steg. `<username>` är [distributionsanvändaren](#configure-a-deployment-user) som du skapade i ett tidigare steg.

## <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Lägg till en Azure-fjärrdatabas till din lokala Git-databas.

```bash
git remote add azure <URI from previous step>
```

Skicka till Azure-fjärrdatabasen för att distribuera appen. Du uppmanas att ange lösenordet som du skapade tidigare när du skapade distributionsanvändaren. Se till att du anger det lösenord som du skapade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user), inte lösenordet du använde när du loggade in på Azure Portal.

```azurecli
git push azure master
```

Föregående kommando visar information liknande den i följande exempel:

```bash
Counting objects: 13, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (11/11), done.
Writing objects: 100% (13/13), 2.07 KiB | 0 bytes/s, done.
Total 13 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cc39b1e4cb'.
remote: Generating deployment script.
remote: Generating deployment script for Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

### <a name="browse-to-the-app"></a>Bläddra till appen


Bläddra till den distribuerade appen:

```
http://<app_name>.azurewebsites.net
```

