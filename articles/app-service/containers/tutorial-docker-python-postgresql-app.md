---
title: Skapa en Docker Python och PostgreSQL-webbapp i Azure | Microsoft Docs
description: "Lär dig hur du hämtar en Docker Python-app som arbetar i Azure, med anslutning till en PostgreSQL-databas."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2017
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 0bd4f390e4507fccd1ca564c48c0f321412e229d
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Skapa en Docker Python och PostgreSQL-webbapp i Azure

Web App för behållare ger en mycket skalbar, automatisk uppdatering värdtjänst. Den här kursen visar hur du skapar en grundläggande Docker Python-webbapp i Azure. Du kan ansluta den här appen till en PostgreSQL-databas. När du är klar har du en Python Flask-program som körs i en dockerbehållare på [Apptjänst i Linux](app-service-linux-intro.md).

![Docker Python Flask-app i App Service på Linux](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

Du kan följa stegen nedan på macOS. Instruktioner för Linux och Windows är samma i de flesta fall, men skillnaderna beskrivs inte i den här kursen.
 
## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera Python](https://www.python.org/downloads/)
1. [Installera och köra PostgreSQL](https://www.postgresql.org/download/)
1. [Installera Docker Community Edition](https://www.docker.com/community-edition)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testa installationen av lokala PostgreSQL och skapa en databas

Öppna fönstret terminal och kör `psql postgres` att ansluta till den lokala PostgreSQL-servern.

```bash
psql postgres
```

Om anslutningen lyckas körs PostgreSQL-databas. Om inte, kontrollera att den lokala PostgresQL-databasen har startats genom att följa stegen i [hämtas - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skapa en databas som heter *eventregistration* och ställa in en separat databasanvändare med namnet *manager* med lösenord *supersecretpass*.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
Typen *\q* avsluta PostgreSQL-klienten. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Skapa lokala Python Flask-program

I det här steget kan du ställa in det lokala Python Flask-projektet.

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet

Öppna fönstret terminal och `CD` till en arbetskatalog.

Kör följande kommandon för att klona lagringsplatsen exempel och gå till den *0.1 initialapp* versionen.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Det här exemplet databasen innehåller en [Flask](http://flask.pocoo.org/) program. 

### <a name="run-the-application"></a>Köra programmet

> [!NOTE] 
> I ett senare steg förenklar processen genom att skapa en dockerbehållare som ska användas med produktionsdatabasen.

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

När appen har lästs in helt, se något som liknar följande meddelande:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Gå till `http://127.0.0.1:5000` i en webbläsare. Klicka på **registrera!** och skapa en testanvändare.

![Python Flask-program som körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app.png)

Exempelprogrammet Flask lagrar användardata i databasen. Om du lyckas vid registrering av en användare kan skriver din app data till den lokala PostgreSQL-databasen.

Om du vill stoppa Flask-servern när som helst, skriver du Ctrl + C i terminalen. 

## <a name="create-a-production-postgresql-database"></a>Skapa en PostgreSQL-databas för produktion

I det här steget skapar du en PostgreSQL-databas i Azure. När appen har distribuerats till Azure, använder den här databasen i molnet.

### <a name="log-in-to-azure"></a>Logga in på Azure

Du kommer nu att använda Azure CLI 2.0 för att skapa resurser som behövs för att vara värd för programmet Python i webbprogram för behållare.  Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/?view=azure-cli-latest#az_login) och följ anvisningarna på skärmen.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [resursgrupp](../../azure-resource-manager/resource-group-overview.md) med [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [Resource group intro](../../../includes/resource-group.md)]

I följande exempel skapas en resursgrupp i USA, västra region:

```azurecli-interactive
az group create --name myResourceGroup --location "West US"
```

Använd den [az apptjänst lista-platser](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) Azure CLI-kommando för att lista över tillgängliga platser.

### <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en PostgreSQL-server med den [az postgres servern skapa](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) kommando.

I följande kommando i stället använda ett unikt namn för den  *\<postgresql_name >* platshållare och en användare namn för den  *\<admin_username >* platshållare. Namnet på server som används som en del av din PostgreSQL-slutpunkt (`https://<postgresql_name>.postgres.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure. Användarnamnet är för inledande databasen administratörsanvändarkontot. Du uppmanas att välja ett lösenord för den här användaren.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>
```

När Azure-databas för PostgreSQL server skapas, visar Azure CLI information liknar följande exempel:

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

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>Skapa en brandväggsregel för Azure-databas för PostgreSQL-server

Kör följande Azure CLI-kommando för att tillåta åtkomst till databasen från alla IP-adresser.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Azure CLI bekräftar det att skapandet brandväggsregel med utdata som liknar följande exempel:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

## <a name="connect-your-python-flask-application-to-the-database"></a>Ansluta Python Flask programmet till databasen

Anslut Python Flask exempelprogrammet till Azure-databasen för PostgreSQL-server som du skapade i det här steget.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>Skapa en tom databas och skapa en ny databasanvändare för programmet

Skapa en databasanvändare med åtkomst till en enskild databas. Du kan använda dessa autentiseringsuppgifter för att undvika att programmet fullständig åtkomst till servern.

Ansluta till databasen (uppmanas du admin-lösenordet).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Skapa en databas och en användare från PostgreSQL CLI.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Typen *\q* avsluta PostgreSQL-klienten.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Testa programmet lokalt mot Azure PostgreSQL-databas

Gå tillbaka nu till den *app* mapp för den klonade Github-lagringsplatsen, du kan köra Python Flask-programmet genom att uppdatera databasen miljövariabler.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

När appen har lästs in helt, se något som liknar följande meddelande:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Navigera till http://127.0.0.1:5000 i en webbläsare. Klicka på **registrera!** och skapa en test-registrering. Du nu skriva data till databasen i Azure.

![Python Flask-program som körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Kör programmet från en Docker-behållare

Skapa Docker behållaren image.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker visas en bekräftelse som har skapats i behållaren.

```bash
Successfully built 7548f983a36b
```

Lägg till miljövariabler för databasen i en miljö variabelfil *db.env*. Appen ansluter till Azure-databasen för PostgreSQL-produktionsdatabas.

```text
DBHOST="<postgresql_name>.postgres.database.azure.com"
DBUSER="manager@<postgresql_name>"
DBNAME="eventregistration"
DBPASS="supersecretpass"
```

Kör appen i Docker-behållaren. Följande kommando anger variabeln filen miljö och mappar Flask standardporten 5000 till lokal port 5000.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

Utdata liknar den du såg tidigare. Inledande Databasmigrering inte längre behöver utföras och ignoreras därför.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

Databasen innehåller redan den registrering som du skapade tidigare.

![Docker behållaren-baserade Python Flask program som körs lokalt](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Överför dockerbehållare till en behållare registret

I det här steget överför Docker-behållare till en behållare för registret. Använd Azure Container registret, men du kan också använda andra populära dem som Docker Hub.

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I följande kommando för att skapa en behållare registernyckel ersätter  *\<registry_name >* med en unik Azure-behållaren registret valfritt namn.

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

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Hämta registret autentiseringsuppgifter för push-installation och använda Docker-avbildningar

Aktivera adminläge först om du vill visa registret autentiseringsuppgifter.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

Du ser två lösenord. Anteckna användarnamnet och lösenordet första.

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

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Ladda upp din dockerbehållare till registret för Azure-behållare

```bash
docker login <registry_name>.azurecr.io -u <registry_name> -p "<registry_password>"
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Distribuera Docker Python Flask-programmet till Azure

I det här steget kan distribuera du Docker behållaren-baserade Python Flask programmet till Azure App Service.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

Skapa en App Service-plan med kommandot [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create).

[!INCLUDE [app-service-plan](../../../includes/app-service-plan-linux.md)]

I följande exempel skapas en Linux-baserade programtjänstplan med namnet *myAppServicePlan* med S1 prissättning tjänstnivån:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

När programtjänstplanen har skapats visas Azure CLI information liknar följande exempel:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
  "kind": "linux",
  "location": "West US",
  "maximumNumberOfWorkers": 10,
  "name": "myAppServicePlan",
  "numberOfSites": 0,
  "perSiteScaling": false,
  "provisioningState": "Succeeded",
  "reserved": true,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capabilities": null,
    "capacity": 1,
    "family": "S",
    "locations": null,
    "name": "S1",
    "size": "S1",
    "skuCapacity": null,
    "tier": "Standard"
  },
  "status": "Ready",
  "subscription": "00000000-0000-0000-0000-000000000000",
  "tags": null,
  "targetWorkerCount": 0,
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="create-a-web-app"></a>Skapa en webbapp

Skapa en webbapp i den *myAppServicePlan* App Service-plan med de [az webapp skapa](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) kommando.

Webbappen ger dig en värd med utrymme för att distribuera din kod och ger en URL som du kan visa det distribuerade programmet. Använd för att skapa webbprogrammet.

I följande kommando och ersätter den  *\<appnamn >* med ett unikt appnamn. Det här namnet är en del av standard-URL för webbprogram, så namnet måste vara unikt över alla program i Azure App Service.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
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

### <a name="configure-the-database-environment-variables"></a>Konfigurera databasen miljövariabler

Tidigare i självstudierna definierat du miljövariabler för att ansluta till PostgreSQL-databasen.

I App Service som du anger miljövariabler som _appinställningar_ med hjälp av den [az webapp appsettings konfigurationsuppsättning](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) kommando.

I följande exempel anger anslutningsinformation för databasen som app-inställningar. Dessutom används den *PORT* variabel till karta PORT 5000 från din Dockerbehållare tar emot HTTP-trafik på PORT 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Konfigurera Docker behållare distribution

Apptjänst kan automatiskt hämta och kör en dockerbehållare.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-custom-image-name "<registry_name>.azurecr.io/flask-postgresql-sample" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Starta om appen när du uppdaterar dockerbehållare eller ändra inställningarna. Starta om säkerställer att alla inställningar tillämpas och senaste behållaren hämtas från registret.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen 

Bläddra till den distribuerade webbappens med hjälp av webbläsaren. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> Webbprogrammet tar längre tid att läsa in eftersom behållaren har laddats ned och startats när konfigurationen av behållaren har ändrats.

Du kan se tidigare registrerad gäster som lagras i Azure produktionsdatabasen i föregående steg.

![Docker behållaren-baserade Python Flask program som körs lokalt](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Grattis!** Du kör en Docker behållare-baserade Python Flask-app i Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodellen och distribuera om

I det här steget kan du lägga till antalet deltagare att varje Händelseregistrering genom att uppdatera modellen gäst.

Kolla in den *0,2 migrering* släpptes med följande git-kommando:

```bash
git checkout tags/0.2-migration
```

Den här versionen redan gjort nödvändiga ändringar i vyer, domänkontrollanter och modell. Den innehåller också en Databasmigrering som skapats *alembic* (`flask db migrate`). Du kan se alla ändringar som gjorts via följande git-kommando:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

Kör följande kommandon för att testa ändringarna lokalt genom att köra flask-server.

Mac / Linux:
```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Navigera till http://127.0.0.1:5000 i din webbläsare att visa ändringarna. Skapa en test-registrering.

![Docker behållaren-baserade Python Flask program som körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicera ändringar i Azure

Skapa den nya docker-avbildningen, push-installera den behållaren registret och starta om appen.

```bash
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Gå till Azure webbapp och försök igen ut de nya funktionerna. Skapa en annan Händelseregistrering.

```bash 
http://<app_name>.azurewebsites.net 
```

![Docker Python Flask-app i Azure Apptjänst](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Hantera Azure-webbapp

Gå till den [Azure-portalen](https://portal.azure.com) att se att webbappen som du skapade.

Klicka på **Apptjänster** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-docker-python-postgresql-app/app-resource.png)

Som standard visas på portalen ditt webbprogram **översikt** sidan. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. Flikar till vänster på sidan Visa sidorna annan konfiguration som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa kurs att lära dig hur du mappar en anpassad DNS-namn till ditt webbprogram.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)
