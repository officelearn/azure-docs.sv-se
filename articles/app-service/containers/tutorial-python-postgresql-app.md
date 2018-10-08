---
title: Skapa en Python- och PostgreSQL-webbapp i Azure App Service | Microsoft Docs
description: Lär dig hur du kör en datadriven Python-app i Azure med anslutning till en PostgreSQL-databas.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435810"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Skapa en Docker Python- och PostgreSQL-webbapp i Azure

Med [App Service on Linux](app-service-linux-intro.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här självstudien beskriver hur du skapar en datadriven Python-webbapp med PostgreSQL som serverdel för databasen. När du är klar har du ett Python Flask-program som körs i en Docker-container i App Service på Linux.

![Docker Python Flask-app i App Service i Linux](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Python-app till PostgreSQL
> * distribuera appen till Azure
> * Visa diagnostikloggar
> * uppdatera datamodellen och distribuera om appen
> * hantera appen i Azure-portalen.

Du kan följa stegen i den här artikeln i macOS. Instruktionerna för Linux och Windows är desamma i de flesta fall, men skillnaderna anges inte i den här självstudien.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

1. [Installera Git](https://git-scm.com/)
1. [Installera Python](https://www.python.org/downloads/)
1. [Installera och kör PostgreSQL](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Testa en lokal PostgreSQL-installation och skapa en databas

I ett lokalt terminalfönster kör du `psql` för att ansluta till din lokala PostgreSQL-server.

```bash
sudo -u postgres psql postgres
```

Om du får ett felmeddelande liknande `unknown user: postgres`, kan din PostgreSQL-installation vara konfigurerad med ditt inloggade användarnamn. Prova med nedanstående kommando i stället.

```bash
psql postgres
```

Om anslutningen lyckas körs PostgreSQL-databasen. Om inte, kontrollerar du att den lokala PostgreSQL-databasen har startats genom att följa anvisningarna för ditt operativsystem i [Hämtningar – PostgreSQL Core Distribution](https://www.postgresql.org/download/).

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

Kör följande kommandon för att klona exempellagringsplatsen.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

Den här exempellagringsplatsen innehåller en [Flask](http://flask.pocoo.org/)-app.

### <a name="run-the-app-locally"></a>Köra appen lokalt

Installera de nödvändiga paketen och starta programmet.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
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

![Python Flask-appen körs lokalt](./media/tutorial-python-postgresql-app/local-app.png)

Flask-exempelappen lagrar användardata i databasen. Om du lyckas registrera en användare skriver appen data till den lokala PostgreSQL-databasen.

Du kan när som helst stoppa Flask-servern genom att skriva Ctrl+C i terminalen.

## <a name="create-a-production-postgresql-database"></a>Skapa en PostgreSQL-produktionsdatabas

I det här steget skapar du en PostgreSQL-databas i Azure. När appen har distribuerats till Azure används den här molndatabasen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en PostgreSQL-server med kommandot [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) i Cloud Shell.

I följande exempelkommando ersätter du *\<postgresql_name>* med ett unikt servernamn och ersätter *\<admin_username>* och *\<admin_password>* med de önskade autentiseringsuppgifterna. Autentiseringsuppgifterna är till databasadministratörens konto. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<postgresql_name>.postgres.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
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
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Kom ihåg \<admin_username > och \<admin_password > till senare. Du behöver dem för att kunna logga in på Postgre-servern och databaserna.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Skapa brandväggsregler för PostgreSQL-servern

I Cloud Shell kör du följande Azure CLI-kommandon för att tillåta åtkomst till databasen från Azure-resurser.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Den här inställningen tillåter nätverksanslutningar från alla IP-adresser i Azure-nätverket. För användning i produktion kan du försöka konfigurera de mest restriktiva brandväggsreglerna [med hjälp av endast de utgående IP-adresserna som din app använder](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

I Cloud Shell kör du kommandot igen för att tillåta åtkomst från den lokala datorn genom att ersätta *\<your_ip_address>* med [din lokala IPv4 IP-adress](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Ansluta Python-app till produktionsdatabasen

I det här steget ansluter du din Flask-exempelapp till Azure Database för den PostgreSQL-server du skapade.

### <a name="create-empty-database-and-user-access"></a>Skapa tom databas och användaråtkomst

Anslut till databasen genom att köra kommandot nedan i det lokala terminalfönstret. När du tillfrågas om ditt administratörslösenord använder du samma lösenord som du angav i [Skapa en Azure Database för PostgreSQL-server](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Precis som i din lokala Postgres-server, skapar du databasen och användaren i Azure Postgres-servern.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

> [!NOTE]
> Det kan vara klokt att skapa databasanvändare med begränsad behörighet för vissa program, i stället för att använda administratörsanvändaren. I det här exemplet har `manager`-användaren _endast_ fullständig behörighet till `eventregistration`-databasen.

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

![Python Flask-appen körs lokalt](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget distribuerar du det Postgres-anslutna Python-programmet till Azure App Service.

### <a name="configure-repository"></a>Konfigurera lagringsplats

Motorn för Git-distribution i App Service anropar `pip`-automatisering när det finns en _application.py_ i lagringsplatsens rot. I den här självstudien låter du distributionsmotorn köra automatiseringen åt dig. I det lokala terminalfönstret går du till roten för lagringsplatsen, skapar en dummy för _application.py_ och sparar sedan ändringarna.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Skapa en App Service-plan 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Tidigare i den här självstudien definierade du miljövariabler för att ansluta till PostgreSQL-databasen.

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell.

I följande exempel anges anslutningsinformation för databasen som appinställningar. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

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

### <a name="configure-entry-point"></a>Konfigurera startpunkt

Som standard söker den inbyggda avbildningen efter en _wsgi.py_ eller _application.py_ i rotkatalogen som startpunkt, men din startpunkt är _app/app.py_. Den _application.py_ som du lade till tidigare är tom och utför inte någonting.

I Cloud Shell kör du kommandot [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set) för att ange ett anpassat startskript.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

`--startup-file`-parametern använder ett anpassat kommando eller sökvägen till filen med det anpassade kommandot. Ditt anpassade kommando bör ha följande format:

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

I det anpassade kommandot krävs `--chdir` om din startpunkt inte finns i rotkatalogen och `<subdirectory>` är underkatalogen. `<module>` är namnet på _.py_-filen och `<variable>` är variabeln i modulen som motsvarar din webbapp.

### <a name="browse-to-the-azure-web-app"></a>Bläddra till Azure-webbappen

Bläddra till den distribuerade webbappen. Den tar lite tid att starta eftersom containern måste laddas ned och köras när appen begärs för första gången. Om sidan uppnår sin tidsgräns eller visar ett felmeddelande, kan du vänta några minuter och sedan uppdatera sidan.

```bash
http://<app_name>.azurewebsites.net
```

Du kan se tidigare registrerade gäster som sparades i Azure-produktionsdatabasen i föregående steg.

![Python Flask-program som körs i Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Grattis!** Du kör en Python-app i App Service för Linux.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

Eftersom Python-appen körs i en container, kan du komma åt konsolloggarna som genereras från containern med App Service i Linux. Gå till den här URL:en för att hitta loggarna:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Du bör se två JSON-objekt, var och en med en `href`-egenskap. En `href` pekar på Docker-konsolloggarna (slutar med `_docker.log`), och en annan `href` pekar på de konsolloggar som genererades inifrån Python-containern. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Kopiera det önskade `href`-värdet i ett webbläsarfönster för att gå till loggarna. Loggarna strömmas inte, så det kan uppstå en fördröjning. Uppdatera webbläsarsidan för att se nya loggar.

## <a name="update-data-model-and-redeploy"></a>Uppdatera datamodell och distribuera om

I det här steget lägger du till antalet deltagare i varje händelseregistrering genom att uppdatera `Guest`-modellen. Distribuera sedan uppdateringen till Azure igen.

I det lokala terminalfönstret checkar du ut filerna från förgreningen `modelChange` med följande git-kommando:

```bash
git checkout origin/modelChange -- .
```

I den här utcheckningen har det redan gjorts nödvändiga ändringar i modell, vyer och kontrollanter. Den innehåller också en databasmigrering som genererats via *alembic* (`flask db migrate`). Du kan se alla ändringar som gjorts med hjälp av följande git-kommando:

```bash
git diff master origin/modelChange
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

![Containerbaserade Python Flask-program i Python körs lokalt](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Publicera ändringar till Azure

I det lokala terminalfönstret sparar du alla ändringar i Git och skickar sedan kodändringarna till Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Gå till Azure-webbappen och prova att använda de nya funktionerna igen. Kom ihåg att uppdatera sidan.

```bash
http://<app_name>.azurewebsites.net
```

![Docker Python Flask-app i Azure App Service](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Hantera webbappen i Azure-portalen

Gå till [Azure Portal](https://portal.azure.com) för att se den webbapp du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på namnet på din Azure-webbapp.

![Navigera till webbappen på Azure Portal](./media/tutorial-python-postgresql-app/app-resource.png)

Som standard visar portalen dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Python-app till PostgreSQL
> * distribuera appen till Azure
> * Visa diagnostikloggar
> * uppdatera datamodellen och distribuera om appen
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till webbappen.

> [!div class="nextstepaction"]
> [Konfigurera inbyggd Python-avbildning](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service-web-tutorial-custom-domain.md)

