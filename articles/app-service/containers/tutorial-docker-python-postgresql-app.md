---
title: Skapa en Python- och PostgreSQL-webbapp i Azure App Service | Microsoft Docs
description: Lär dig hur du kör en datadriven Python-app i Azure med anslutning till en PostgreSQL-databas.
services: app-service\web
documentationcenter: python
author: berndverst
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 20b549914daf71c0d23235b5c20ebb6f14367471
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172042"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Skapa en Docker Python- och PostgreSQL-webbapp i Azure

Med Web App for Containers får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. Den här självstudien beskriver hur du skapar en datadriven Python-webbapp med PostgreSQL som serverdel för databasen. När du är klar har du en Python Flask-app som körs i en Docker-container på [App Service på Linux](app-service-linux-intro.md).

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

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testa en lokal PostgreSQL-installation och skapa en databas

I ett lokalt terminalfönster kör du `psql` för att ansluta till din lokala PostgreSQL-server.

```bash
sudo -u postgres psql
```

Om anslutningen lyckas körs PostgreSQL-databasen. Om inte, kontrollerar du att den lokala PostgreSQL-databasen har startats genom att följa stegen i [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skapa en databas med namnet *eventregistration* och konfigurera en separat databasanvändare med namnet *manager* och lösenordet *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten. 

<a name="step2"></a>

## <a name="create-local-python-app"></a>Skapa en lokal Python-app

I det här steget konfigurerar du det lokala Python Flask-projektet.

### <a name="clone-the-sample-app"></a>Klona exempelappen

Öppna terminalfönstret och `CD` till en arbetskatalog.

Kör följande kommandon för att klona exempellagringsplatsen och tå till versionen *0.1-initialapp*.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

Den här exempellagringsplatsen innehåller en [Flask](http://flask.pocoo.org/)-app. 

### <a name="run-the-app-locally"></a>Köra appen lokalt

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

Skapa en PostgreSQL-server med kommandot [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) i Cloud Shell.

I följande exempelkommando ersätter du *\<postgresql_name>* med ett unikt servernamn och ersätter *\<admin_username>* och *\<admin_password>* med de önskade autentiseringsuppgifterna. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<postgresql_name>.postgres.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure. Autentiseringsuppgifterna är för databasadministratörens användarkonto. 

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name GP_Gen4_2
```

När den logiska Azure Database for PostgreSQL-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "<admin_username>",
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

### <a name="create-a-firewall-rule-for-the-postgresql-server"></a>Skapa en brandväggsregel för PostgreSQL-servern

I Cloud Shell kör du följande Azure CLI-kommando för att tillåta åtkomst till databasen från alla IP-adresser. När både start-IP och slut-IP har angetts till `0.0.0.0` öppnas brandväggen endast för andra Azure-resurser. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

> [!TIP] 
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

I Cloud Shell kör du kommandot igen för att tillåta åtkomst till databasen från den lokala datorn genom att ersätta *\<you_ip_address>* med [din lokala IPv4-adress](https://whatismyipaddress.com/). 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<you_ip_address> --end-ip-address=<you_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Ansluta Python-app till produktionsdatabasen

I det här steget ansluter du din Flask-exempelapp till Azure Database för den PostgreSQL-server du skapade.

### <a name="create-empty-database-and-user-access"></a>Skapa tom databas och användaråtkomst

I Cloud Shell ansluter du till databasen genom att köra `psql`. När du tillfrågas om ditt administratörslösenord använder du samma lösenord som du angav i [Skapa en Azure Database för PostgreSQL-server](#create-an-azure-database-for-postgresql-server).

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

### <a name="test-app-connectivity-to-production-database"></a>Testa appens anslutning till produktionsdatabasen

Väl tillbaka i det lokala terminalfönstret kör du följande kommandon för att köra Flask-databasmigrering och Flask-servern.

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

![Python Flask-appen körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app.png)

## <a name="upload-app-to-a-container-registry"></a>Ladda upp app till ett containerregister

I det här steget skapar du en Docker-avbildning och laddar upp den till Azure Container Registry. Du kan även använda populära register som Docker Hub.

### <a name="build-the-docker-image-and-test-it"></a>Skapa Docker-avbildningen och testa den

I det lokala terminalfönstret skapar du Docker-avbildningen.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker visar en bekräftelse på att den har skapat avbildningen.

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

Kör avbildningen lokalt i en Docker-container. Följande kommando anger miljövariabelfilen och mappar Flask-standardporten 5000 till den lokala porten 5000.

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

![Containerbaserade Python Flask-program i Python körs lokalt](./media/tutorial-docker-python-postgresql-app/local-docker.png)

Nu när du har verifierat att behållaren fungerar lokalt tar du bort _db.env_. I Azure App Service använder du appinställningar för att definiera miljövariablerna.  

### <a name="create-an-azure-container-registry"></a>Skapa ett Azure Container Registry

I Cloud Shell skapar du ett register i Azure Container Registry med följande kommando. Ersätt *\<registry_name>* med ett unikt registernamn.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

### <a name="retrieve-registry-credentials"></a>Hämta autentiseringsuppgifter för registret

I Cloud Shell kör du följande kommandon för att hämta autentiseringsuppgifter för registret. Du behöver dem för att push-överföra och hämta avbildningarna.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

I utdata ser du två lösenord. Anteckna användarnamnet (vilket är registrets namn som standard) och det första lösenordet.

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

### <a name="upload-docker-image-to-registry"></a>Ladda upp Docker-avbildningen till registret

Från det lokala terminalfönstret loggar du in på ditt nya register med `docker`. När du blir uppmanad anger du det lösenord du hämtade.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Push-överför Docker-avbildningen till registret.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="create-web-app-with-uploaded-image"></a>Skapa webbapp med uppladdad bild

I det här steget skapar du en app i Azure App Service och konfigurerar den att använda den uppladdade Docker-avbildningen i Azure Container Registry.

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

I Cloud Shell skapar du en webbapp i App Service-planen *myAppServicePlan* med kommandot [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create).

I följande kommando ska du ersätta platshållaren *\<app_name>* med ett unikt appnamn. Det här namnet är en del av standard-DNS-webbplats för webbappen. Därför måste namnet vara unikt i förhållande till alla appar i Azure App Service.

```azurecli-interactive
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

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Tidigare i den här självstudien definierade du miljövariabler för att ansluta till PostgreSQL-databasen.

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set).

I följande exempel anges anslutningsinformation för databasen som appinställningar. Den använder även variabeln *WEBSITES_PORT* till containerporten 5000, vilket gör att containern tar emot HTTP-trafik på port 80.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" WEBSITES_PORT=5000
```

### <a name="configure-custom-container-deployment"></a>Konfigurera distribution av anpassad container

Trots att du redan har angett namnet på containeravbildningen behöver du fortfarande ange URL för det anpassade registret och autentiseringsuppgifterna. I Cloud Shell kör du kommandot [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set).

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

I Cloud Shell startar du om appen. Om du startar om garanteras att alla inställningar tillämpas och att den senaste containern hämtas från registret.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen 

Bläddra till den distribuerade webbappen. 

```bash 
http://<app_name>.azurewebsites.net 
```

> [!NOTE]
> Webbappen tar lite tid att starta eftersom containern måste laddas ned och köras när appen begärs för första gången. Om du först ser ett fel efter en lång tid kan du bara uppdatera sidan.

Du kan se tidigare registrerade gäster som sparades i Azure-produktionsdatabasen i föregående steg.

![Containerbaserade Python Flask-program i Python körs lokalt](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**Grattis!** Du kör en Python-app i Web App for Containers.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodell och distribuera om

I det här steget lägger du till antalet deltagare i varje händelseregistrering genom att uppdatera `Guest`-modellen.

I det lokala terminalfönstret checkar du ut versionen *0.2-migration* med följande git-kommando:

```bash
git checkout tags/0.2-migration
```

I den här versionen har det redan gjorts nödvändiga ändringar i modell, vyer och kontrollanter. Den innehåller också en databasmigrering som genererats via *alembic* (`flask db migrate`). Du kan se alla ändringar som gjorts via följande git-kommando:

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>Testa ändringarna lokalt

I det lokala terminalfönstret kör du följande kommandon för att testa ändringarna lokalt genom att köra Flask-servern.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Gå till http://localhost:5000 i webbläsaren för att se ändringarna. Skapa en testregistrering.

![Containerbaserade Python Flask-program i Python körs lokalt](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I det lokala terminalfönstret skapar du den nya Docker-avbildningen och push-överför den till registret.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

I Cloud Shell startar du om appen för att se till att den senaste containern hämtas från registret.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
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
