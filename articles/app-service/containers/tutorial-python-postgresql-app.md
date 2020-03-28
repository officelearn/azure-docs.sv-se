---
title: 'Handledning: Linux Python app med Postgre'
description: Lär dig hur du får en Linux Python-app att fungera i Azure App Service, med anslutning till en PostgreSQL-databas i Azure. Django används i den här guiden.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77523951"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Självstudiekurs: Kör en Python-webbapp (Django) med PostgreSQL i Azure App Service

[Azure App Service](app-service-linux-intro.md) tillhandahåller en mycket skalbar, självkorrigering webbhotell. Den här självstudien visar hur du ansluter en datadriven Python Django-webbapp till en Azure-databas för PostgreSQL-databas och distribuerar och kör appen på Azure App Service.

![Python Django webbapp i Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Azure-databas för PostgreSQL-databas och ansluta en webbapp till den
> * Distribuera webbappen till Azure App Service
> * Visa diagnostikloggar
> * Hantera webbappen i Azure-portalen

Du kan följa stegen i den här artikeln på macOS, Linux eller Windows. Stegen är liknande i de flesta fall, även om skillnaderna inte beskrivs i den här självstudien. De flesta av exemplen nedan använder ett `bash` terminalfönster på Linux. 

## <a name="prerequisites"></a>Krav

Innan du startar den här självstudien:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installera [Git](https://git-scm.com/).
- Installera [Python 3](https://www.python.org/downloads/).
- Installera och kör [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testa PostgreSQL-installationen och skapa en databas

Anslut först till din lokala PostgreSQL-server och skapa en databas: 

I ett lokalt terminalfönster kör du `psql` för att ansluta till den `postgres` lokala PostgreSQL-servern som den inbyggda användaren.

```bash
sudo su - postgres
psql
```
eller
```PowerShell
psql -U postgres
```

Om anslutningen lyckas körs PostgreSQL-databasen. Om inte, kontrollerar du att den lokala PostgreSQL-databasen har startats genom att följa anvisningarna för ditt operativsystem i [Hämtningar – PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skapa en ny databas som heter *pollsdb*och konfigurera en databasanvändare med namnet *Manager* med lösenord *supersecretpass:*

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Skapa och kör den lokala Python-appen

Konfigurera och kör sedan exempelappen Python Django.

Den [djangoapp](https://github.com/Azure-Samples/djangoapp) prov förvaret innehåller datadrivna [Django](https://www.djangoproject.com/) opinionsundersökningar app du får genom att följa [Skriva din första Django app](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django dokumentation.

### <a name="clone-the-sample-app"></a>Klona exempelappen

I ett terminalfönster kör du följande kommandon för att klona exempelappdatabasen och ändra till den nya arbetskatalogen:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Konfigurera den virtuella Python-miljön

Skapa och aktivera en virtuell Python-miljö för att köra din app.

```bash
python3 -m venv venv
source venv/bin/activate
```
eller
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

I `venv` miljön kör *du env.sh* eller *env.ps1* för att ange de miljövariabler som *azuresite/settings.py* ska använda för databasanslutningsinställningarna.

```bash
source ./env.sh
```
eller
```PowerShell
.\env.ps1
```

Installera de nödvändiga paketen från *requirements.txt*, kör [Django-migreringar](https://docs.djangoproject.com/en/2.1/topics/migrations/)och [skapa en administratörsanvändare:](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user)

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Kör webbappen

När du har skapat administratörsanvändaren kör du Django-servern.

```bash
python manage.py runserver
```

När Django-webbappen är fulladdad returneras ungefär följande meddelande:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Gå till *\/http: /localhost:8000* i en webbläsare. Du bör se meddelandet **Inga omröstningar är tillgängliga**. 

Gå till *\/http: /localhost:8000/admin* och logga in med administratörsanvändaren som du skapade i det sista steget. Välj **Lägg till** **bredvid Frågor**och skapa en omröstningsfråga med några alternativ.

![Kör Python Django-appen i App Services lokalt](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Gå till *\/http: /localhost:8000* igen för att se enkäten frågan och svara på frågan. Den lokala Django exempel ansökan skriver och lagrar användardata till den lokala PostgreSQL databasen.

Om du vill stoppa Django-servern skriver du Ctrl+C i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

De flesta av de återstående stegen i den här artikeln använder Azure CLI-kommandon i Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Skapa och ansluta till Azure Database för PostgreSQL

I det här avsnittet skapar du en Azure-databas för PostgreSQL-server och databas och ansluter din webbapp till den. När du distribuerar din webbapp till Azure App Service använder appen den här molndatabasen. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du kan skapa en ny resursgrupp för din Azure-databas för PostgreSQL-server eller använda en befintlig resursgrupp. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Du skapar en PostgreSQL-server med kommandot [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) i Cloud Shell.

> [!NOTE]
> Innan du skapar en Azure-databas för PostgreSQL-server kontrollerar du vilken [beräkningsgenerering](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) som är tillgänglig i din region. Om din region inte stöder Gen4-maskinvara ändrar *du --sku-name* på följande kommandorad till ett värde som stöds i din region, till exempel Gen5. 

I följande kommando * \<ersätter du postgresql-namn>* med ett unikt servernamn. Servernamnet är en del av din PostgreSQL-slutpunkt *https://\<postgresql-namn>.postgres.database.azure.com*, så namnet måste vara unikt på alla servrar i Azure. 

Ersätt * \<resursgruppsnamn>* och * \<region>* med namnet och regionen för den resursgrupp som du vill använda. För * \<administratörs-användarnamn>* och * \<admin-lösenord>*, skapa användaruppgifter för databasadministratörskontot. Kom ihåg * \<admin-användarnamn>* och * \<admin-lösenord>* att använda senare för att logga in på PostgreSQL-servern och databaser.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

När Azure Database for PostgreSQL-servern skapas returnerar Azure CLI JSON-koden som följande exempel:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Skapa brandväggsregler för Azure Database för PostgreSQL-server

Kör kommandot [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) för att tillåta åtkomst till databasen från Azure-resurser. Ersätt * \<postgresql-namn>* och * \<resourcegroup-name>* platshållare med dina värden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Med föregående inställning kan nätverksanslutningar från alla IP-adresser i Azure-nätverket. För produktionsanvändning, försök att konfigurera de mest restriktiva brandväggsreglerna som är möjliga genom [att endast tillåta utgående IP-adresser som appen använder](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Kör `firewall-rule create` kommandot igen om du vill tillåta åtkomst från den lokala datorn. Ersätt * \<din IP-adress>* med [din lokala IPv4 IP-adress](https://www.whatsmyip.org/). Ersätt * \<postgresql-namn>* och * \<resourcegroup-namn>* platshållare med dina egna värden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Skapa och ansluta till Azure Database för PostgreSQL-databas

Anslut till din Azure-databas för PostgreSQL-server genom att köra följande kommando. Använd ditt eget * \<postgresql-namn>* och * \<admin-användarnamn>* och logga in med lösenordet du skapade.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Precis som du gjorde på din lokala PostgreSQL-server skapar du en databas och användare i Azure Database för PostgreSQL-server:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Det är en bra idé att skapa databasanvändare med begränsad behörighet för specifika appar i stället för att använda administratörsanvändaren. Användaren `manager` har full behörighet *only* till `pollsdb` endast databasen.

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testa appanslutning till Azure PostgreSQL-databasen

Redigera filen lokala *env.sh* eller *env.ps1* för att peka på din PostgreSQL-databas i molnet genom att ersätta * \<postgresql-namn>* med azure-databasen för PostgreSQL-servernamn.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
eller
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

I `venv` miljön i det lokala terminalfönstret kör du den redigerade *env.sh* eller *env.ps1*. 
```bash
source ./env.sh
```
eller
```PowerShell
.\env.ps1
```

Kör Django-migreringen till Azure-databasen och skapa en administratörsanvändare.

```bash
python manage.py migrate
python manage.py createsuperuser
```

När administratörsanvändaren har skapats kör du Django-servern.

```bash
python manage.py runserver
```

I en webbläsare går du till *\/http: /localhost:8000*, och du bör se meddelandet **Inga omröstningar är tillgängliga** igen. 

Gå till *\/http: /localhost:8000/admin*, logga in med administratörsanvändaren som du skapade och skapa en omröstningsfråga som tidigare.

![Kör Python Django-appen i App Services lokalt](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Gå till *\/http: /localhost:8000* igen och se enkätfrågan visas. Din app skriver nu data till Azure Database för PostgreSQL-databas.

Om du vill stoppa Django-servern skriver du Ctrl+C i terminalen.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Distribuera webbappen till Azure App Service

I det här steget distribuerar du Azure Database för PostgreSQL-databasansluten Python-app till Azure App Service.

### <a name="configure-repository"></a>Konfigurera lagringsplats

Eftersom den här självstudien använder ett Django-exempel måste du ändra och lägga till några inställningar i filen *djangoapp/azuresite/settings.py* för att fungera med Azure App Service. 

1. Django verifierar `HTTP_HOST`-huvudet i inkommande begäranden. För att din Django-webbapp ska fungera i App Service måste du lägga till appens fullständigt kvalificerade domännamn i de tillåtna värdarna. 
   
   Redigera *azuresite/settings.py* om `ALLOWED_HOSTS` du vill ändra raden enligt följande:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django stöder inte [att betjäna statiska filer i produktion](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). För den här självstudien använder du [WhiteNoise](https://whitenoise.evans.io/en/stable/) för att aktivera servering av filerna. WhiteNoise-paketet har redan installerats med *requirements.txt*. 
   
   Om du vill konfigurera Django så att du använder WhiteNoise hittar `whitenoise.middleware.WhiteNoiseMiddleware` du `MIDDLEWARE` inställningen i *azuresite/settings.py*och lägger till i listan strax efter `django.middleware.security.SecurityMiddleware` raden. Inställningen `MIDDLEWARE` bör se ut så här:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. Lägg till följande rader i slutet av *azuresite/settings.py:*
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Mer information om hur du konfigurerar WhiteNoise finns i [WhiteNoise-dokumentationen](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Avsnittet med databasinställningar följer redan bästa praxis för säkerhet vid användning av miljövariabler. Fullständiga distributionsrekommendationer finns i Checklista för [Django-dokumentation: distribution](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Genomför dina ändringar i din gaffel i *djangoapp-arkivet:*

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Skapa apptjänstplan

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Skapa en webbapp

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Tidigare i den här självstudien definierade du miljövariabler för att ansluta till PostgreSQL-databasen.

I Azure App Service anger du miljövariabler som *appinställningar*med kommandot [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)

I Azure Cloud Shell kör du följande kommando för att ange information om databasanslutning som appinställningar. Ersätt * \<>för appnamn, * * \<>* och * \<postgresql-namn>* med dina egna värden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Information om hur koden kommer åt dessa appinställningar finns i [Access-miljövariabler](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

App Service-distributionsservern ser *requirements.txt* i lagringsplatsens rot och kör Python-pakethantering automatiskt efter `git push`.

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen med URL *\//\<http: appnamn>.azurewebsites.net*. Det tar lite tid att starta, eftersom behållaren måste hämtas och köras när appen begärs för första gången. Om sidan uppnår sin tidsgräns eller visar ett felmeddelande, kan du vänta några minuter och sedan uppdatera sidan.

Du bör se de omröstningsfrågor som du skapade tidigare. 

App Service identifierar ett Django-projekt i *wsgi.py* din databas genom att leta efter `manage.py startproject` en wsgi.py fil i varje underkatalog, vilket skapar som standard. När App Service hittar filen läses Django-webbappen in. Mer information om hur App Service läser in Python-appar finns i avsnittet om att [konfigurera inbyggd Python-avbildning](how-to-configure-python.md).

Gå till *\//\<http: app-name>.azurewebsites.net/admin* och logga in med administratörsanvändaren som du skapade. Om du vill kan du skapa fler omröstningsfrågor.

![Kör Python Django-appen i App Services i Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Grattis!** Du kör en Python-webbapp (Django) i Azure App Service för Linux.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure-portalen

Sök efter och välj appen du skapade i [Azure-portalen.](https://portal.azure.com)

![Navigera till python Django-appen i Azure-portalen](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsuppgifter som bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din Python Django-app på översiktssidan i Azure-portalen](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Gå till nästa självstudiekurs om du vill lära dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudiekurs: Mappa anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla in andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera Python-appen](how-to-configure-python.md)
