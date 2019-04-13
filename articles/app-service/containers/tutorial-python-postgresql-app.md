---
title: Python (Django) med PostgreSQL på Linux – Azure Apptjänst | Microsoft Docs
description: Lär dig hur du kör en datadriven Python-app i Azure med anslutning till en PostgreSQL-databas. Django används i självstudien.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: beverst;cephalin
ms.custom: seodec18
ms.openlocfilehash: f82cccb66c0aae93afe19259393f094d0627c801
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59546429"
---
# <a name="build-a-python-and-postgresql-app-in-azure-app-service"></a>Skapa en Python- och PostgreSQL-app i Azure App Service

Med [App Service on Linux](app-service-linux-intro.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här självstudien beskriver hur du skapar en datadriven Python-app med hjälp av PostgreSQL som serverdel för databasen. När du är klar har du ett Django-program som körs i App Service i Linux.

![Python Django-app i App Service i Linux](./media/tutorial-python-postgresql-app/django-admin-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Python-app till PostgreSQL
> * distribuera appen till Azure
> * Visa diagnostikloggar
> * hantera appen i Azure-portalen.

> [!NOTE]
> Innan du skapar en Azure Database for PostgreSQL kontrollerar du [vilken beräkningsgeneration som är tillgänglig i din region](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Du kan följa stegen i den här artikeln i macOS. Instruktionerna för Linux och Windows är desamma i de flesta fall, men skillnaderna anges inte i den här självstudien.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här självstudien behöver du:

1. [Installera Git](https://git-scm.com/)
2. [Installera Python](https://www.python.org/downloads/)
3. [Installera och kör PostgreSQL](https://www.postgresql.org/download/)

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

Skapa en databas med namnet *pollsdb* och konfigurera en separat databasanvändare med namnet *manager* och lösenordet *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Skapa en lokal Python-app

I det här steget konfigurerar du det lokala Python Django-projektet.

### <a name="clone-the-sample-app"></a>Klona exempelappen

Öppna terminalfönstret och `CD` till en arbetskatalog.

Kör följande kommandon för att klona exempellagringsplatsen.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Den här exempellagringsplatsen innehåller ett [Django](https://www.djangoproject.com/)-program. Det är samma datadrivna app som du kan få genom att följa [komma igång-självstudien i Django-dokumentationen](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Den här självstudien lär dig inte Django, men den visar hur du tar distribution och körning av en Django-app (eller en annan datadriven Python-app) till App Service.

### <a name="configure-environment"></a>Konfigurera miljön

Skapa en virtuell Python-miljö och använd ett skript för att konfigurera inställningarna för databasanslutning.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

De miljövariabler som definieras i *env.sh* och *env.ps1* används i _azuresite/settings.py_ för att definiera databasinställningarna.

### <a name="run-app-locally"></a>Kör appen lokalt

Installera de nödvändiga paketen, [kör Django-migreringar](https://docs.djangoproject.com/en/2.1/topics/migrations/) och [skapa en administratörsanvändare](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

När administratörsanvändaren har skapats kör du Django-servern.

```bash
python manage.py runserver
```

När appen har lästs in helt ser du något som liknar följande meddelande:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Gå till `http://localhost:8000` i en webbläsare. Du bör se meddelandet `No polls are available.`. 

Gå till `http://localhost:8000/admin` och logga in med den administratörsanvändare som du skapade i föregående steg. Klicka på **Lägg till** intill **Frågor** och skapa en omröstningsfråga med vissa alternativ.

![Python Django-program som körs lokalt](./media/tutorial-python-postgresql-app/django-admin-local.png)

Gå till `http://localhost:8000` igen och se den omröstningsfråga som visas.

Django-exempelprogrammet lagrar användardata i databasen. Om du lyckas lägga till en omröstningsfråga skriver appen data till den lokala PostgreSQL-databasen.

Du kan när som helst stoppa Django-servern genom att skriva Ctrl+C i terminalen.

## <a name="create-a-production-postgresql-database"></a>Skapa en PostgreSQL-produktionsdatabas

I det här steget skapar du en PostgreSQL-databas i Azure. När appen har distribuerats till Azure används den här molndatabasen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Skapa en PostgreSQL-server med kommandot [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) i Cloud Shell.

I följande exempel-kommando ersätter  *\<postgresql-name >* med ett unikt namn och Ersätt  *\<administratörens användarnamn >* och  *\<adminlösenord >* med de önskade användarautentiseringsuppgifterna. Autentiseringsuppgifterna är till databasadministratörens konto. Det här servernamnet används som en del av PostgreSQL-slutpunkten (`https://<postgresql-name>.postgres.database.azure.com`), så namnet måste vara unikt för alla servrar i Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

När den logiska Azure Database for PostgreSQL-servern har skapats visar Azure CLI information som liknar följande exempel:

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
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
> Kom ihåg \<administratörens användarnamn > och \<adminlösenord > till senare. Du behöver dem för att kunna logga in på Postgre-servern och databaserna.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Skapa brandväggsregler för PostgreSQL-servern

I Cloud Shell kör du följande Azure CLI-kommandon för att tillåta åtkomst till databasen från Azure-resurser.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Den här inställningen tillåter nätverksanslutningar från alla IP-adresser i Azure-nätverket. För användning i produktion kan du försöka konfigurera de mest restriktiva brandväggsreglerna [med hjälp av endast de utgående IP-adresserna som din app använder](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

I Cloud Shell kör du kommandot igen för att tillåta åtkomst från din lokala dator genom att ersätta  *\<din IP-adress >* med [din lokala IPv4-adress](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Ansluta Python-app till produktionsdatabasen

I det här steget ansluter du din Django-exempelapp till den Azure Database for PostgreSQL-server som du skapade.

### <a name="create-empty-database-and-user-access"></a>Skapa tom databas och användaråtkomst

I Cloud Shell ansluter du till databasen genom att köra kommandot nedan. När du tillfrågas om ditt administratörslösenord använder du samma lösenord som du angav i [Skapa en Azure Database för PostgreSQL-server](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Precis som i din lokala Postgres-server, skapar du databasen och användaren i Azure Postgres-servern.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

> [!NOTE]
> Det kan vara klokt att skapa databasanvändare med begränsad behörighet för vissa program, i stället för att använda administratörsanvändaren. I det här exemplet har `manager`-användaren _endast_ fullständig behörighet till `pollsdb`-databasen.

### <a name="test-app-connectivity-to-production-database"></a>Testa appens anslutning till produktionsdatabasen

I det lokala terminalfönstret ändrar du databasens miljövariabler (som du konfigurerade tidigare genom att köra *env.sh* eller *env.ps1*):

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Kör Django-migrering till Azure-databasen och skapa en administratörsanvändare.

```bash
python manage.py migrate
python manage.py createsuperuser
```

När administratörsanvändaren har skapats kör du Django-servern.

```bash
python manage.py runserver
```

Gå till `http://localhost:8000` igen. Du bör se meddelandet `No polls are available.` igen. 

Gå till `http://localhost:8000/admin` och logga in med den administratörsanvändare som du skapade och skapa en omröstningsfråga som tidigare.

![Python Django-program som körs lokalt](./media/tutorial-python-postgresql-app/django-admin-local.png)

Gå till `http://localhost:8000` igen och se den omröstningsfråga som visas. Appen skriver nu data till databasen i Azure.

## <a name="deploy-to-azure"></a>Distribuera till Azure

I det här steget distribuerar du det Postgres-anslutna Python-programmet till Azure App Service.

### <a name="configure-repository"></a>Konfigurera lagringsplats

Django verifierar `HTTP_HOST`-huvudet i inkommande begäranden. För att Django-appen ska fungera i App Service behöver du lägga till appens fullständigt kvalificerade domännamn till de tillåtna värdarna. Öppna _azuresite/settings.py_ och leta upp inställningen `ALLOWED_HOSTS`. Ändra raden till:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Django har inte stöd för att [behandla statiska filer i produktion](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), så du behöver aktivera det manuellt. För den här självstudien använder du [WhiteNoise](https://whitenoise.evans.io/en/stable/). WhiteNoise-paketet ingår redan i _requirements.txt_. Du behöver bara konfigurera Django till att använda det. 

I _azuresite/settings.py_ letar du upp inställningen `MIDDLEWARE` och lägger till mellanprogrammet `whitenoise.middleware.WhiteNoiseMiddleware` i listan precis nedanför mellanprogrammet `django.middleware.security.SecurityMiddleware`. Inställningen `MIDDLEWARE` bör se ut så här:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

I slutet av _azuresite/settings.py_ lägger du till följande rader.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Mer information om hur du konfigurerar WhiteNoise finns i [dokumentation för WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Avsnittet med databasinställningar följer redan bästa praxis för säkerhet vid användning av miljövariabler. De fullständiga distributionsrekommendationerna finns i avsnittet med [en checklista för distribution i dokumentationen om Django](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Checka in ändringarna i databasen.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Konfigurera distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Skapa apptjänstplan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-web-app"></a>Skapa webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Tidigare i den här självstudien definierade du miljövariabler för att ansluta till PostgreSQL-databasen.

I App Service ställer du in miljövariabler som _appinställningar_ med kommandot [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell.

I följande exempel anges anslutningsinformation för databasen som appinställningar. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Information om hur dessa inställningar som används i koden finns [Åtkomstmiljövariabler](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

App Service-distributionsservern ser _requirements.txt_ i lagringsplatsens rot och kör Python-pakethantering automatiskt efter `git push`.

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen. Den tar lite tid att starta eftersom containern måste laddas ned och köras när appen begärs för första gången. Om sidan uppnår sin tidsgräns eller visar ett felmeddelande, kan du vänta några minuter och sedan uppdatera sidan.

```bash
http://<app-name>.azurewebsites.net
```

Du bör se den omröstningsfråga som du skapade tidigare. 

App Service upptäcker ett Django-projekt i din lagringsplats genom att söka efter en _wsgi.py_ i varje underkatalog, som skapas av `manage.py startproject` som standard. När den hittar filen läser den in Django-appen. Mer information om hur App Service läser in Python-appar finns i avsnittet om att [konfigurera inbyggd Python-avbildning](how-to-configure-python.md).

Gå till `<app-name>.azurewebsites.net` och logga in med samma administratörsanvändare som du skapade. Om du vill kan du prova att skapa några fler omröstningsfrågor.

![Python Django-program som körs lokalt](./media/tutorial-python-postgresql-app/django-admin-azure.png)

**Grattis!** Du kör en Python-app i App Service för Linux.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Hantera appen i Azure-portalen

Gå till [Azure-portalen](https://portal.azure.com) om du vill se den app som du skapade.

Klicka på **App Services** på menyn till vänster och klicka sedan på din Azure-apps namn.

![Portalnavigering till Azure-app](./media/tutorial-python-postgresql-app/app-resource.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![App Service-sidan på Azure Portal](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en PostgreSQL-databas i Azure
> * Ansluta en Python-app till PostgreSQL
> * distribuera appen till Azure
> * Visa diagnostikloggar
> * hantera appen i Azure-portalen.

Gå vidare till nästa självstudie för att läsa hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Självstudier: Mappa anpassad DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller titta på andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera Python-app](how-to-configure-python.md)