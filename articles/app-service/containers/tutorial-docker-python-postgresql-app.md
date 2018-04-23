---
title: Skapa en Docker Python- och PostgreSQL-webbapp i Azure | Microsoft Docs
description: Lär dig hur du får igång en Docker Python-app som fungerar i Azure med anslutning till en PostgreSQL-databas.
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 673564ef7b5ff02a3809154a4dcf1669c2ed798b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Skapa en Docker Python- och PostgreSQL-webbapp i Azure

Med Web App for Containers får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. Den här kursen visar hur du skapar en grundläggande Docker Python-webbapp i Azure. Du ansluter den här appen till en PostgreSQL-databas. När du är klar har du en Python Flask-app som körs i en Docker-behållare på [App Service på Linux](app-service-linux-intro.md).

![Docker Python Flask-app i App Service i Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Python-app till PostgreSQL
> * distribuera appen till Azure
> * uppdatera datamodellen och distribuera om appen
> * hantera appen i Azure-portalen.

Du kan följa stegen i den här artikeln i macOS. Instruktionerna för Linux och Windows är desamma i de flesta fall, men skillnaderna anges inte i den här självstudien.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera Python](https://www.python.org/downloads/)
1. [Installera och kör PostgreSQL](https://www.postgresql.org/download/)
1. [Installera Docker Community Edition](https://www.docker.com/community-edition)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testa lokal PostgreSQL-installation och skapa en databas

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

Kör följande kommandon för att klona exempellagringsplatsen och tå till versionen *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Den här exempellagringsplatsen innehåller en [Flask](http://flask.pocoo.org/)-app. 

### <a name="run-the-application"></a>Köra programmet

> [!NOTE] 
> I ett senare steg förenklar du processen genom att skapa en Docker-behållare som ska användas med produktionsdatabasen.

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

![Python Flask-appen körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask-exempelappen lagrar användardata i databasen. Om du lyckas registrera en användare skriver appen data till den lokala PostgreSQL-databasen.

Du kan när som helst stoppa Flask-servern genom att skriva Ctrl+C i terminalen. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>Skapa en PostgreSQL-produktionsdatabas

I det här steget skapar du en PostgreSQL-databas i Azure. När appen har distribuerats till Azure används den här molndatabasen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en PostgreSQL-server med kommandot [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create).

I följande kommando ersätter du ett unikt servernamn för platshållaren *\<postgresql_name>* och ett användarnamn för platshållaren *\<admin_username>*. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<postgresql_name>.postgres.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure. Användarnamnet är för den initiala databasadministratörens användarkonto. Du ombeds välja ett lösenord för användaren.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

När den logiska Azure Database for PostgreSQL-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Skapa en brandväggsregel för Azure Database for PostgreSQL-server

Kör följande Azure CLI-kommando för att tillåta åtkomst till databasen från alla IP-adresser. När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

Azure CLI bekräftar skapande av brandväggsregeln med utdata som liknar följande:

```json
{
  "endIpAddress": "0.0.0.0",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAzureIPs",
  "name": "AllowAzureIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Anslut ditt Python Flask-program till databasen

I det här steget ansluter du ditt Python Flask-exempelprogram till Azure Database för den PostgreSQL-server du skapade.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Skapa en tom databas och konfigurera en ny databasprogramanvändare

Skapa en databasanvändare med åtkomst enbart till en enda databas. Du använder de här autentiseringsuppgifterna för att undvika att ge programmet fullständig åtkomst till servern.

Anslut till databasen (du uppmanas att ange administratörslösenordet).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Skapa databasen och användaren från PostgreSQL CLI.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Testa programmet lokalt mot Azure PostgreSQL-databasen

När du nu går tillbaka till *appmappen* för den klonade Github-lagringsplatsen kan du köra Python Flask-programmet genom att uppdatera databasens miljövariabler.

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

![Python Flask-programmet körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Köra programmet från en Docker-behållare

Bygga Docker-behållaravbildningen.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

I Docker visas en bekräftelse på att behållaren har skapats.

```bash
Successfully built 7548f983a36b
```

I lagringsplatsens rot lägger du till en miljövariabelfil som heter _db.env_, och lägger sedan till följande databasmiljövariabler. Appen ansluter till Azure Database for PostgreSQL-produktionsdatabasen.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Kör appen från Docker-behållaren. Följande kommando anger miljövariabelfilen och mappar Flask-standardporten 5000 till den lokala porten 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Resultatet liknar det du såg tidigare. Men initial databasmigrering behöver inte längre utföras och hoppas därför över.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Databasen innehåller redan registreringen du skapade tidigare.

![Behållarbaserade Python Flask-program i Python körs lokalt](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Ladda upp Docker-behållaren till ett behållarregister

I det här steget laddar du upp Docker-behållaren till ett behållarregister. Du kan använda Azure Container Registry men även populära alternativ som Docker Hub.

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I följande kommando för att skapa ett behållarregister ersätter du *\<registry_name>* med ett unikt Azure Container Registry-namn som du väljer själv.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

Resultat

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Hämta autentiseringsuppgifterna för registret för att push-överföra och hämta Docker-avbildningar

Aktivera administratörsläget innan du visar registrets autentiseringsuppgifter.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Du ser två lösenord. Anteckna användarnamnet och det första lösenordet.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Ladda upp din Docker-behållare till Azure Container Registry

Logga in på ditt register. När du blir uppmanad anger du det lösenord du hämtade.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Skicka dockeravbildningen till registret.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Distribuera Docker Python Flask-programmet till Azure

I det här steget distribuerar du ditt Docker-behållarbaserade Python Flask-program till Azure App Service.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en webbapp i *myAppServicePlan* App Service-planen med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

Med webbappen får du ett utrymme för att distribuera vår kod och en URL så att du kan visa den distribuerade appen. Använd det för att skapa webbappen.

I följande kommando ska du ersätta platshållaren *\<app_name>* med ett unikt appnamn. Det här namnet är en del av standard-DNS-webbplats för webbappen. Därför måste namnet vara unikt i förhållande till alla appar i Azure App Service.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
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
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>Konfigurera databasens miljövariabler

Tidigare i den här självstudien definierade du miljövariabler för att ansluta till PostgreSQL-databasen.

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

I följande exempel anges anslutningsinformation för databasen som appinställningar. Den använder även *PORT*-variabeln för att mappa PORT 5000 från Docker-behållaren för att ta emot HTTP-trafik på PORT 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Konfigurera distribution av Docker-behållare

AppService kan automatiskt ladda ned och köra en Docker-behållare.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Starta om appen när du uppdaterar Docker-behållaren eller ändra inställningarna. Om du startar om garanteras att alla inställningar tillämpas och att den senaste behållaren hämtas från registret.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen 

Bläddra till den distribuerade webbappen via webbläsaren. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Det tar längre tid att läsa in webbappen eftersom behållaren måste laddas ned och startas när konfigurationen av behållaren har ändrats.

Du kan se tidigare registrerade gäster som sparades i Azure-produktionsdatabasen i föregående steg.

![Behållarbaserade Python Flask-program i Python körs lokalt](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Grattis!** Du kör en Docker-behållarbaserad Python Flask-app i Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodell och distribuera om

I det här steget lägger du till antalet deltagare i varje händelseregistrering genom att uppdatera gästmodellen.

Kolla in *0.2-migration*-versionen med följande git-kommando:

```bash
git checkout tags/0.2-migration
```

I den här versionen har det redan gjorts nödvändiga ändringar i vyer, domänkontrollanter och modell. Den innehåller också en databasmigrering som genererats via *alembic* (`flask db migrate`). Du kan se alla ändringar som gjorts via följande git-kommando:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Kör Flask-servern med följande kommandon för att testa ändringarna lokalt.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Gå till http://localhost:5000 i webbläsaren för att se ändringarna. Skapa en testregistrering.

![Behållarbaserade Python Flask-program i Python körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

Skapa den nya docker-avbildningen, push-överför den till behållarregistret och starta om appen.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Gå till Azure-webbappen och prova att använda de nya funktionerna igen. Skapa ytterligare en händelseregistrering.

```bash 
http://<app_name>.azurewebsites.net 
```

![Docker Python Flask-app i Azure App Service](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Hantera din Azure-webbapp

Gå till [Azure Portal](https://portal.azure.com) för att se den webbapp du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Som standard visar portalen dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
