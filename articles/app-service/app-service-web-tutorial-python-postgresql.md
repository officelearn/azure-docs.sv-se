---
title: Skapa en Python- och PostgreSQL-webbapp i Azure | Microsoft Docs
description: Lär dig hur du får igång en Python-app som fungerar i Azure med anslutning till en PostgreSQL-databas.
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: e342a10c2f3b6c32d8d0bc727bf3325c26fb53d6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>Självstudie: Skapa en Python- och PostgreSQL-webbapp i Azure

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Om du vill distribuera en app till App Service i _Linux_ kan du läsa [Skapa en Docker Python- och PostgreSQL-webbapp i Azure](./containers/tutorial-docker-python-postgresql-app.md).
>

Med [Azure App Service](app-service-web-overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här kursen visar hur du skapar en grundläggande Python-webbapp i Azure. Du ansluter den här appen till en PostgreSQL-databas. När du är klar har du en Python Flask-app som körs i App Service.

![Python Flask-app i App Service i Linux](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Anslut en Python-app till MySQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * hantera appen i Azure-portalen.

Du kan följa stegen i den här självstudiekursen i macOS. Instruktionerna för Linux och Windows är desamma i de flesta fall, men skillnaderna anges inte i den här självstudien.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera Python](https://www.python.org/downloads/)
1. [Installera och kör PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testa en lokal PostgreSQL-installation och skapa en databas

Öppna terminalfönstret och kör `psql` för att ansluta till din lokala PostgreSQL-server.

```bash
sudo -u postgres psql
```

Om anslutningen lyckas körs PostgreSQL-databasen. Om inte, kontrollerar du att den lokala PostgreSQL-databasen har startats genom att följa stegen i [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skapa en databas med namnet *eventregistration* och konfigurera en separat databasanvändare med namnet *manager* och lösenordet *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Skriv `\q` om du vill avsluta PostgreSQL-klienten. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Skapa en lokal Python Flask-app

I det här steget konfigurerar du det lokala Python Flask-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna terminalfönstret och `CD` till en arbetskatalog.

Kör följande kommandon för att klona exempellagringsplatsen och återställ sedan till genomförandet (commit) för den ursprungliga appen (före `modelChange`).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

Den här exempellagringsplatsen innehåller en [Flask](http://flask.pocoo.org/)-app. 

### <a name="run-the-application"></a>Köra programmet

Installera de nödvändiga paketen och starta programmet.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

När appen har lästs in helt ser du något som liknar följande meddelande:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Gå till `http://localhost:5000` i en webbläsare. Klicka på **Registrera!** och skapa en testanvändare.

![Python Flask-appen körs lokalt](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Flask-exempelappen lagrar användardata i databasen. Om du lyckas registrera en användare skriver appen data till den lokala PostgreSQL-databasen.

Du kan när som helst stoppa Flask-servern genom att skriva Ctrl+C i terminalen. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Skapa PostgreSQL i Azure

I det här steget skapar du en PostgreSQL-databas i Azure. När appen har distribuerats till Azure används den här molndatabasen.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>Skapa en PostgreSQL-server

Skapa en PostgreSQL-server med kommandot [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

I följande kommando byter du ut platshållaren *\<postgresql_name>* mot ett unikt servernamn, platshållaren *\<admin_username>* mot ett användarnamn och platshållaren *\<admin_password>* mot ett lösenord. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<postgresql_name>.postgres.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name mydemoserver --location "West Europe" --admin-user <admin_username> --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

När den logiska Azure Database for PostgreSQL-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "additionalProperties": {},
  "administratorLogin": "<my_admin_username>",
  "earliestRestoreDate": "2018-04-19T22:51:05.340000+00:00",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westeurope",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "additionalProperties": {},
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

Kör följande Azure CLI-kommando för att tillåta åtkomst till databasen från alla IP-adresser. När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0 --name AllowAzureIPs
```

Azure CLI bekräftar skapande av brandväggsregeln med utdata som liknar följande:

```json
{
  "additionalProperties": {},
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
 "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](app-service-ip-addresses.md#find-outbound-ips).
>

### <a name="create-a-production-database-and-user"></a>Skapa en produktionsdatabas och en användare

Skapa en databasanvändare med åtkomst enbart till en enda databas. Du använder de här autentiseringsuppgifterna för att undvika att ge programmet fullständig åtkomst till servern.

Anslut till databasen (du uppmanas att ange administratörslösenordet).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <admin_username>@<postgresql_name> postgres
```

Skapa databasen och användaren från PostgreSQL CLI.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

### <a name="test-app-locally-with-azure-postgresql"></a>Testa appen lokalt med Azure PostgreSQL

När du nu går tillbaka till *appmappen* för den klonade Github-lagringsplatsen kan du köra Python Flask-appen genom att uppdatera databasens miljövariabler.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

När appen har lästs in helt ser du något som liknar följande meddelande:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Gå till http://localhost:5000 i en webbläsare. Klicka på **Registrera!** och skapa en testregistrering. Nu skriver du data till databasen i Azure.

![Python Flask-appen körs lokalt](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget distribuerar du din PostgreSQL-anslutna Python-app till Azure App Service.

Git-lagringsplatsen innehåller redan följande filer som behövs för att köra Flask-webbappen i App Service:

- `.deployment`: anger det anpassade distributionsskriptet som ska köras.
- `deploy.cmd`: distributionsskriptet. Det är där `pip install` körs.
- `web.config`: anger startpunktsskriptet som ska köras i en `httpPlatformHandler` i IIS.
- `run_waitress_server.py`: startpunktsskriptet. Startar Flask-webbappen på en [`waitress`](https://docs.pylonsproject.org/projects/waitress)-server.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Installera Python

I det här steget installerar du Python 3.6.2 med [platstillägg](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) i App Service. Du använder autentiseringsuppgifterna som du konfigurerade i [Konfigurera en distributionsanvändare](#configure-a-deployment-user) för att autentisera mot REST-slutpunkten.

I Cloud Shell kör du nästa kommando för att hämta paketinformationen för Python 3.6.2. Ersätt *\<deployment_user>* med distributionsanvändarnamnet du angav och *\<app_name>* med ditt appnamn. Ange distributionslösenordet som du konfigurerade när du blir uppmanad att göra det.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

I Cloud Shell kör du nästa kommando för att installera Python-paketet. Ersätt *\<deployment_user>* med distributionsanvändarnamnet du angav och *\<app_name>* med ditt appnamn. Ange distributionslösenordet som du konfigurerade när du blir uppmanad att göra det.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

I utdata från kommandot kan du se att Python är installerat på sökvägen `D:\home\python362x86\python.exe`.

### <a name="configure-database-settings"></a>Konfigurera databasinställningarna

Tidigare i den här självstudien definierade du miljövariabler för att ansluta till PostgreSQL-databasen.

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

I följande exempel anges anslutningsinformation för databasen som appinställningar. Ersätt *\<app_name>* med ditt appnamn och *\<postgresql_name>* med ditt PostgreSQL-servernamn.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen 

Bläddra till den distribuerade webbappen via webbläsaren. 

```bash 
http://<app_name>.azurewebsites.net 
```

Du kan se tidigare registrerade gäster som sparades i Azure-produktionsdatabasen i föregående steg.

![Python Flask-appen körs lokalt](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Grattis!** Du kör en Python Flask-app i Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodell och distribuera om

I det här steget lägger du till antalet deltagare i varje händelseregistrering genom att uppdatera `Guest`-modellen.

Checka ut filerna som taggats med `modelChange`:

```bash
git checkout modelChange -- *
```

I den här versionen har det redan gjorts nödvändiga ändringar i vyer, domänkontrollanter och modell. Den innehåller också en databasmigrering som genererats via *alembic* (`flask db migrate`). Du kan se ändringarna i alla filer i [GitHub commit view](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e) (genomförandevyn).

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Kör Flask-servern med följande kommandon för att testa ändringarna lokalt.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Gå till http://localhost:5000 i webbläsaren för att se ändringarna. Skapa en testregistrering.

![Python Flask-appen körs lokalt](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I det lokala terminalfönstret sparar du alla ändringar i Git och skickar sedan kodändringarna till Azure.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Gå till Azure-webbappen och prova att använda de nya funktionerna igen. Skapa ytterligare en händelseregistrering.

```bash 
http://<app_name>.azurewebsites.net 
```

![Python Flask-app i Azure App Service](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Hantera din Azure-webbapp

Gå till [Azure Portal](https://portal.azure.com) för att se den webbapp du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Som standard visar portalen dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](app-service-web-tutorial-custom-domain.md)
