---
title: 'Självstudie: Linux python-app med Postgre'
description: Lär dig hur du skaffar en Linux python-app som fungerar i Azure App Service, med anslutning till en PostgreSQL-databas i Azure. Django används i den här självstudien.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523951"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Självstudie: köra en python-webbapp (django) med PostgreSQL i Azure App Service

Med [Azure App Service](app-service-linux-intro.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet. Den här självstudien visar hur du ansluter en datadrived python django-webbapp till en Azure Database for PostgreSQL-databas och distribuerar och kör appen på Azure App Service.

![Python django-webbapp i Azure App Service](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL-databas och Anslut en webbapp till den
> * Distribuera webbappen till Azure App Service
> * Visa diagnostikloggar
> * Hantera webbappen i Azure Portal

Du kan följa stegen i den här artikeln på macOS, Linux eller Windows. Stegen är liknande i de flesta fall, även om skillnaderna inte beskrivs i den här självstudien. De flesta av exemplen nedan använder ett `bash` terminalfönster i Linux. 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installera [Git](https://git-scm.com/).
- Installera [python 3](https://www.python.org/downloads/).
- Installera och kör [postgresql](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Testa PostgreSQL-installation och skapa en databas

Anslut först till din lokala PostgreSQL-Server och skapa en databas: 

I ett lokalt terminalfönster kör `psql` för att ansluta till din lokala PostgreSQL-server som den inbyggda `postgres` användaren.

```bash
sudo su - postgres
psql
```
eller
```PowerShell
psql -U postgres
```

Om anslutningen lyckas körs PostgreSQL-databasen. Om inte, kontrollerar du att den lokala PostgreSQL-databasen har startats genom att följa anvisningarna för ditt operativsystem i [Hämtningar – PostgreSQL Core Distribution](https://www.postgresql.org/download/).

Skapa en ny databas med namnet *pollsdb*och konfigurera en databas användare med namnet *Manager* med lösen ordet *supersecretpass*:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Skapa och kör den lokala python-appen

Sedan ställer du in och kör exempel på python django-webbappen.

Exempel lagrings platsen för [djangoapp](https://github.com/Azure-Samples/djangoapp) innehåller den data drivna [django](https://www.djangoproject.com/) avsöknings appen som du får genom att följa de [första django-apparna](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django-dokumentationen.

### <a name="clone-the-sample-app"></a>Klona exempelappen

I terminalfönstret kör du följande kommandon för att klona exempel App-lagringsplatsen och ändra till den nya arbets katalogen:

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Konfigurera den virtuella python-miljön

Skapa och aktivera en virtuell python-miljö för att köra din app.

```bash
python3 -m venv venv
source venv/bin/activate
```
eller
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

I `venv`s miljön kör du *ENV.sh* eller *ps1* för att ange de miljövariabler som *azuresite/Settings. py* ska använda för databas anslutnings inställningarna.

```bash
source ./env.sh
```
eller
```PowerShell
.\env.ps1
```

Installera de nödvändiga paketen från *Requirements. txt*, kör [django-migreringar](https://docs.djangoproject.com/en/2.1/topics/migrations/)och [skapa en administratörs användare](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user):

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Kör webbappen

När du har skapat administratörs användaren kör du django-servern.

```bash
python manage.py runserver
```

När django-webbappen är fullständigt inläst returneras något liknande följande meddelande:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Gå till *http:\//localhost: 8000* i en webbläsare. Du bör se meddelandet **inga avsökningar är tillgängliga**. 

Gå till *http:\//localhost: 8000/admin* och logga in med administratörs användaren som du skapade i det senaste steget. Välj **Lägg till** bredvid **frågor**och skapa en avsöknings fråga med några val.

![Kör python django-appen i App Services lokalt](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Gå till *http:\//localhost: 8000* igen för att se avsöknings frågan och besvara frågan. Det lokala django-exempel programmet skriver och lagrar användar data till den lokala PostgreSQL-databasen.

Om du vill stoppa django-servern skriver du Ctrl + C i terminalen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

De flesta av de återstående stegen i den här artikeln använder Azure CLI-kommandon i Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Skapa och Anslut till Azure Database for PostgreSQL

I det här avsnittet skapar du en Azure Database for PostgreSQL-Server och-databas och ansluter din webbapp till den. När du distribuerar din webbapp till Azure App Service använder appen den här moln databasen. 

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du kan skapa en ny resurs grupp för din Azure Database for PostgreSQL Server eller använda en befintlig resurs grupp. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Skapa en Azure Database för PostgreSQL-server

Du skapar en PostgreSQL-server med kommandot [AZ postgres Server Create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) i Cloud Shell.

> [!NOTE]
> Innan du skapar en Azure Database for PostgreSQL-Server bör du kontrol lera vilken [beräknings generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) som är tillgänglig i din region. Om din region inte stöder Gen4 maskin vara, ändra *--SKU-namn* i följande kommando rad till ett värde som stöds i din region, till exempel Gen5. 

I följande kommando ersätter du *\<postgresql-name >* med ett unikt server namn. Server namnet är en del av PostgreSQL-slutpunkten *https://\<postgresql-name >. postgres. Database. Azure. com*, så namnet måste vara unikt för alla servrar i Azure. 

Ersätt *\<resourcegroup-name >* och *\<region >* med namnet och regionen för den resurs grupp som du vill använda. För *\<admin-username >* och *\<admin-Password >* skapar du användarautentiseringsuppgifter för databas administratörs kontot. Kom ihåg *\<admin-username >* och *\<admin-Password >* att använda senare för att logga in på postgresql-servern och databaser.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

När Azure Database for PostgreSQL-servern skapas returnerar Azure CLI JSON-kod som i följande exempel:

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

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Skapa brand Väggs regler för Azure Database for PostgreSQL-servern

Kör kommandot [AZ postgres Server Firewall-Rule Create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create) för att tillåta åtkomst till databasen från Azure-resurser. Ersätt *\<postgresql-name >* och *\<resourcegroup-namn >* plats hållare med dina värden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Med den föregående inställningen tillåts nätverks anslutningar från alla IP-adresser i Azure-nätverket. Vid produktions användning kan du försöka konfigurera de mest restriktiva brand Väggs reglerna genom [att bara tillåta de utgående IP-adresser som appen använder](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Kör kommandot `firewall-rule create` igen för att tillåta åtkomst från den lokala datorn. Ersätt *\<IP-adressen >* med [din lokala IPv4 IP-adress](https://www.whatsmyip.org/). Ersätt *\<postgresql-name >* och *\<resourcegroup >* plats hållarna med dina egna värden.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Skapa och Anslut till den Azure Database for PostgreSQL databasen

Anslut till din Azure Database for PostgreSQL-Server genom att köra följande kommando. Använd ditt eget *\<postgresql-namn >* och *\<admin-username >* och logga in med det lösen ord som du skapade.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Precis som du gjorde i din lokala PostgreSQL-Server skapar du en databas och en användare på den Azure Database for PostgreSQL servern:

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Det är en bra idé att skapa databas användare med begränsade behörigheter för vissa appar, i stället för att använda administratörs användaren. `manager` användaren har fullständig *behörighet till `pollsdb`* -databasen.

Skriv `\q` om du vill avsluta PostgreSQL-klienten.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Testa appens anslutning till Azure PostgreSQL-databasen

Redigera din lokala *ENV.sh* -eller *ps1* -fil så att den pekar på till din moln PostgreSQL databas genom att ersätta *\<PostgreSQL-namn >* med Azure Database for postgresql server namnet.

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

I `venv` miljön i det lokala terminalfönstret kör du den redigerade *ENV.sh* eller *kuvert. ps1*. 
```bash
source ./env.sh
```
eller
```PowerShell
.\env.ps1
```

Kör django-migrering till Azure-databasen och skapa en administratörs användare.

```bash
python manage.py migrate
python manage.py createsuperuser
```

När administratörsanvändaren har skapats kör du Django-servern.

```bash
python manage.py runserver
```

I en webbläsare går du till *http:\//localhost: 8000*, och du bör se meddelandet **inga avsökningar är tillgängliga** igen. 

Gå till *http:\//localhost: 8000/admin*, logga in med administratörs användaren som du skapade och skapa en omröstnings fråga som tidigare.

![Kör python django-appen i App Services lokalt](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Gå till *http:\//localhost: 8000* igen och se den avsöknings fråga som visas. Din app skriver nu data till Azure Database for PostgreSQL databasen.

Om du vill stoppa django-servern skriver du Ctrl + C i terminalen.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Distribuera webbappen till Azure App Service

I det här steget distribuerar du den Azure Database for PostgreSQL Database-anslutna python-appen till Azure App Service.

### <a name="configure-repository"></a>Konfigurera lagringsplats

Eftersom den här självstudien använder ett django-exempel måste du ändra och lägga till vissa inställningar i *djangoapp/azuresite/Settings. py-* filen för att arbeta med Azure App Service. 

1. Django verifierar `HTTP_HOST`-huvudet i inkommande begäranden. För att django-webbappen ska fungera i App Service måste du lägga till appens fullständigt kvalificerade domän namn i de tillåtna värdarna. 
   
   Redigera *azuresite/Settings. py* för att ändra `ALLOWED_HOSTS` raden enligt följande:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django stöder inte [betjäning av statiska filer i produktion](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). I den här självstudien använder du [WhiteNoise](https://whitenoise.evans.io/en/stable/) för att aktivera att betjäna filerna. WhiteNoise-paketet installerades redan med *kraven. txt*. 
   
   Om du vill konfigurera django för att använda WhiteNoise, i *azuresite/Settings. py*, letar du upp `MIDDLEWARE`-inställningen och lägger till `whitenoise.middleware.WhiteNoiseMiddleware` i listan, precis efter `django.middleware.security.SecurityMiddleware` raden. Inställningen `MIDDLEWARE` bör se ut så här:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. I slutet av *azuresite/Settings. py*lägger du till följande rader:
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   Mer information om hur du konfigurerar WhiteNoise finns i [WhiteNoise-dokumentationen](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Avsnittet med databasinställningar följer redan bästa praxis för säkerhet vid användning av miljövariabler. Fullständiga distributions rekommendationer finns i [django-dokumentation: check lista för distribution](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Genomför ändringarna i din förgrening av *djangoapp* -lagringsplatsen:

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

I Azure App Service ställer du in miljövariabler som *appinställningar*med kommandot [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) .

Kör följande kommando i Azure Cloud Shell för att ange databas anslutnings information som appinställningar. Ersätt *\<app-name >* , *\<resourcegroup-name >* och *\<postgresql-Name >* med dina egna värden.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Information om hur koden kommer åt dessa appdata finns i [Access Environment-variabler](how-to-configure-python.md#access-environment-variables).

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

Bläddra till den distribuerade appen med URL *http:\//\<app-name >. azurewebsites. net*. Det tar lite tid att starta, eftersom behållaren måste laddas ned och köras när appen begärs för första gången. Om sidan uppnår sin tidsgräns eller visar ett felmeddelande, kan du vänta några minuter och sedan uppdatera sidan.

Du bör se de avsöknings frågor som du skapade tidigare. 

App Service identifierar ett django-projekt i din lagrings plats genom att söka efter en *wsgi.py* -fil i varje under katalog som `manage.py startproject` skapas som standard. När App Service hittar filen laddas django-webbappen. Mer information om hur App Service läser in Python-appar finns i avsnittet om att [konfigurera inbyggd Python-avbildning](how-to-configure-python.md).

Gå till *http:\//\<app-name >. azurewebsites. net/admin* och logga in med administratörs användaren som du skapade. Om du vill kan du skapa några fler avsöknings frågor.

![Kör python django-appen i App Services i Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Grattis!** Du kör en python-webbapp (django) i Azure App Service för Linux.

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure Portal

Sök efter och välj den app som du skapade i [Azure Portal](https://portal.azure.com).

![Navigera till din python django-app i Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din python django-app på sidan Översikt i Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

Gå till nästa självstudie om du vill lära dig att mappa ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera python-app](how-to-configure-python.md)
