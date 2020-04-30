---
title: 'Självstudie: Distribuera python (django) med postgres'
description: Lär dig hur du skapar en python-app med en PostgreSQL-databas och distribuerar den till Azure App Service i Linux. I självstudien används en django exempel-app för demonstration.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085764"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Självstudie: Distribuera en python-webbapp (django) med PostgreSQL i Azure App Service

Den här självstudien visar hur du distribuerar en datadrived python-webbapp (django) för att [Azure App Service](app-service-linux-intro.md) och ansluta den till en Azure Database for PostgreSQL-databas. App Service ger en mycket skalbar och automatisk korrigering av webb värd tjänst.

![Distribuera python django-webbappen till Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL databas
> * Distribuera kod till Azure App Service och ansluta till postgres
> * Uppdatera din kod och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure Portal

Du kan följa stegen i den här artikeln på macOS, Linux eller Windows.

## <a name="install-dependencies"></a>Installera beroenden

Innan du börjar den här självstudien:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installera [Azure CLI](/cli/azure/install-azure-cli).
- Installera [git](https://git-scm.com/).
- Installera [python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Klona exempelappen

I terminalfönstret kör du följande kommandon för att klona exempel App-lagringsplatsen och ändra till rot databasen:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Exempel lagrings platsen för djangoapp innehåller den data drivna [django](https://www.djangoproject.com/) avsöknings appen som du får genom att följa de [första django-apparna](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django-dokumentationen. Den tillhandahålls här för din bekvämlighet.

## <a name="prepare-app-for-app-service"></a>Förbered app för App Service

Precis som många python-webbramverk [kräver django vissa ändringar innan de kan köras på en produktions server](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/), och det är ingen annan med App Service. Du måste ändra och lägga till vissa inställningar i standard *azuresite/Settings. py-* filen så att appen fungerar när den har distribuerats till App Service. 

Ta en titt på *azuresite/Production. py*, som gör den nödvändiga konfigurationen för App Service. En kort stund är följande:

- Ärv alla inställningar från *azuresite/Settings. py*.
- Lägg till det fullständigt kvalificerade domän namnet för App Service-appen till de tillåtna värdarna. 
- Använd [WhiteNoise](https://whitenoise.evans.io/en/stable/) för att aktivera betjäning av statiska filer i produktion, eftersom django som standard inte hanterar statiska filer i produktionen. WhiteNoise-paketet ingår redan i *requirements.txt*.
- Lägg till konfiguration för PostgreSQL Database. Som standard använder django sqlite3 som databas, men det är inte lämpligt för produktion av appar. Det [psycopg2-binära](https://pypi.org/project/psycopg2-binary/) paketet ingår redan i *Requirements. txt*.
- Postgres-konfigurationen använder miljövariabler. Senare får du lära dig hur du ställer in miljövariabler i App Service.

*azuresite/Product. py* ingår i lagrings platsen, men den används ännu inte av appen. För att se till att inställningarna används i App Service måste du konfigurera två filer, *Manage.py* och *azuresite/grundläggande wsgi. py*, för att få åtkomst till den.

- I *Manage.py*ändrar du följande rad:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    Till följande kod:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Du ställer in miljövariabeln `DJANGO_ENV` senare när du konfigurerar din app service-app.

- I *azuresite/grundläggande wsgi. py*gör du samma ändring som ovan.

    I App Service använder du *Manage.py* för att köra migrering av databasen och App Service använder *azuresite/grundläggande wsgi. py* för att köra din django-app i produktion. Den här ändringen i båda filerna säkerställer att produktions inställningarna används i båda fallen.

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Du bör redan ha Azure CLI installerat. Med [Azure CLI](/cli/azure/what-is-azure-cli) kan du arbeta med Azure-resurser från kommando rads terminalen. 

Logga in på Azure genom att köra [`az login`](/cli/azure/reference-index#az-login) kommandot:

```azurecli
az login
```

Följ anvisningarna i terminalen för att logga in på ditt Azure-konto. När du är klar visas dina prenumerationer i JSON-format i terminalfönstret.

## <a name="create-postgres-database-in-azure"></a>Skapa postgres-databas i Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

I det här avsnittet skapar du en Azure Database for PostgreSQL-Server och-databas. Starta genom att installera `db-up` tillägget med följande kommando:

```azurecli
az extension add --name db-up
```

Skapa postgres-databasen i Azure med [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommandot, som du ser i följande exempel. Ersätt * \<postgresql-Name>* med ett *unikt* namn (Server slut punkten är *https://\<postgresql-Name>. postgres.Database.Azure.com*). För * \<admin-username>* och * \<Admin-Password>* anger du autentiseringsuppgifter för att skapa en administratörs användare för den här postgres-servern.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Det här kommandot kan ta en stund eftersom det gör följande:

- Skapar en [resurs grupp](../../azure-resource-manager/management/overview.md#terminology) med `myResourceGroup`namnet, om den inte finns. Varje Azure-resurs måste vara i någon av dessa. `--resource-group` är valfritt.
- Skapar en postgres-server med den administrativa användaren.
- Skapar en `pollsdb` databas.
- Tillåter åtkomst från den lokala IP-adressen.
- Tillåter åtkomst från Azure-tjänster.
- Skapa en databas användare med åtkomst till `pollsdb` databasen.

Du kan utföra alla steg separat med andra `az postgres` kommandon och `psql`, men `az postgres up` alla är i ett steg åt dig.

När kommandot har slutförts söker du efter de utmatnings rader `Ran Database Query:`som ska visas. De visar databas användaren som skapas åt dig, med användar namn `root` och lösen ord. `Pollsdb1` Du kommer att använda dem senare för att ansluta din app till-databasen.

<!-- not all locations support az postgres up -->
> [!TIP]
> Ange platsen för din postgres-Server genom att inkludera argumentet `--location <location-name>`, där `<location_name>` är en av Azure- [regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Du kan hämta de regioner som är tillgängliga för din prenumeration [`az account list-locations`](/cli/azure/account#az-account-list-locations) med kommandot.

## <a name="deploy-the-app-service-app"></a>Distribuera App Service-appen

I det här avsnittet skapar du App Service-appen. Du ansluter den här appen till postgres-databasen som du skapade och distribuerar din kod.

### <a name="create-the-app-service-app"></a>Skapa appen App Service

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Se till att du är tillbaka i databas roten (`djangoapp`), eftersom appen ska distribueras från den här katalogen.

Skapa en App Service-app med [`az webapp up`](/cli/azure/webapp#az-webapp-up) kommandot, som visas i följande exempel. Ersätt * \<App-Name->* med ett *unikt* namn (Server slut punkten *är\<https://App-Name>. azurewebsites.net*). Tillåtna tecken för * \<App-Name>* är `A` - `Z`, `0` - `9`och `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Det här kommandot kan ta en stund eftersom det gör följande:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Genererar automatiskt en [resurs grupp](../../azure-resource-manager/management/overview.md#terminology) .
- Skapar [App Service plan](../overview-hosting-plans.md) *MyAppServicePlan* i den grundläggande pris nivån (B1), om den inte finns. `--plan`och `--sku` är valfria.
- Skapar App Service-appen om den inte finns.
- Aktiverar standard loggning för appen om den inte redan är aktive rad.
- Laddar upp lagrings platsen med hjälp av ZIP-distribution med build Automation aktiverat.

När distributionen är klar visas ett JSON-utdata som liknar följande:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Kopiera värdet för * \<app-Resource-Group->*. Du behöver den för att konfigurera appen senare. 

> [!TIP]
> Du kan använda samma kommando senare för att distribuera ändringar och omedelbart aktivera diagnostikloggar med:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Exempel koden distribueras nu, men appen ansluter inte till postgres-databasen i Azure ännu. Du kommer att göra detta härnäst.

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

När du kör din app lokalt kan du ställa in miljövariablerna i termin Alans sessionen. I App Service du göra det med *appinställningar*med hjälp av kommandot [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

Kör följande kommando för att ange anslutnings information för databasen som appinställningar. Ersätt * \<App-Name>*, * \<app-Resource-Group>* och * \<postgresql-Name>* med dina egna värden. Kom ihåg att användarautentiseringsuppgifter `root` och `Pollsdb1` har skapats åt dig av. `az postgres up`

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Information om hur koden kommer åt dessa appdata finns i [Access Environment-variabler](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Köra databas migreringar

Om du vill köra databas migreringar i App Service öppnar du en SSH-session i webbläsaren genom att gå till *https://\<App-name>. scm.azurewebsites.net/webssh/Host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Kör följande kommandon i SSH-sessionen:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Bläddra till Azure-appen

Bläddra till den distribuerade appen med URL *http:\//\<App-Name>. azurewebsites.net* i en webbläsare. Du bör se meddelandet **inga avsökningar är tillgängliga**. 

Bläddra till *http:\//\<App-Name>. azurewebsites.net/admin* och logga in med administratörs användaren som du skapade i det senaste steget. Välj **Lägg till** bredvid **frågor**och skapa en avsöknings fråga med några val.

Bläddra till den distribuerade appen med URL *http:\//\<App-Name>. azurewebsites.net/admin*och skapa några avsöknings frågor. Du kan se frågorna på *http:\//\<App-Name>. azurewebsites.net/*. 

![Kör python django-appen i App Services i Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Bläddra till den distribuerade appen med URL *http:\//\<App-Name>. azurewebsites.net* igen för att se röstnings frågan och besvara frågan.

App Service identifierar ett django-projekt i din lagrings plats genom att söka efter en *wsgi.py* -fil i `manage.py startproject` varje under katalog som skapas som standard. När App Service hittar filen laddas django-webbappen. Mer information om hur App Service läser in Python-appar finns i avsnittet om att [konfigurera inbyggd Python-avbildning](how-to-configure-python.md).

**Grattis!** Du kör en python-webbapp (django) i Azure App Service för Linux.

## <a name="develop-app-locally-and-redeploy"></a>Utveckla appen lokalt och distribuera om

I det här avsnittet utvecklar du din app i din lokala miljö och distribuerar om koden till App Service.

### <a name="set-up-locally-and-run"></a>Konfigurera lokalt och kör

Om du vill konfigurera din lokala utvecklings miljö och köra exempel appen för första gången kör du följande kommandon:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[KOMMANDOT](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

När django-webbappen är fullständigt inläst returneras något liknande följande meddelande:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Gå till *http:\//localhost: 8000* i en webbläsare. Du bör se meddelandet **inga avsökningar är tillgängliga**. 

Gå till *http:\//localhost: 8000/admin* och logga in med administratörs användaren som du skapade i det senaste steget. Välj **Lägg till** bredvid **frågor**och skapa en avsöknings fråga med några val.

![Kör python django-appen i App Services lokalt](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Gå till *http:\//localhost: 8000* igen för att se röstnings frågan och besvara frågan. Det lokala django-exempelprogrammet skriver och lagrar användar data till en lokal sqlite3-databas, så du behöver inte bekymra dig om att skapa en produktions databas. Om du vill att utvecklings miljön ska matcha Azure-miljön kan du använda en postgres-databas lokalt i stället.

Om du vill stoppa django-servern skriver du Ctrl + C.

### <a name="update-the-app"></a>Uppdatera appen

Om du bara vill se hur du gör uppdateringar av appen kan du göra `polls/models.py`en liten ändring i. Hitta raden:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

Och ändra den till:

```python
choice_text = models.CharField(max_length=100)
```

Genom att ändra data modellen måste du skapa en ny django-migrering. Gör det med följande kommando:

```
python manage.py makemigrations
```

Du kan testa dina ändringar lokalt genom att köra migreringar, köra utvecklings servern och navigera till *http:\//localhost: 8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Distribuera om koden till Azure

Kör följande kommando från databas roten för att distribuera om ändringarna:

```azurecli
az webapp up --name <app-name>
```

App Service upptäcker att appen finns och bara distribuerar koden.

### <a name="rerun-migrations-in-azure"></a>Kör om migreringar i Azure

Eftersom du har gjort ändringar i data modellen måste du köra om migreringen av databasen i App Service. Öppna en SSH-session i webbläsaren genom att gå till *https://\<app-name>. scm.azurewebsites.net/webssh/Host*. Kör följande kommandon:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Granska appen i produktion

Bläddra till *http:\//\<App-Name>. azurewebsites.net* och se ändringarna som körs Live i produktion. 

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

Du kan komma åt konsol loggarna som genereras inifrån behållaren.

> [!TIP]
> `az webapp up`aktiverar standard loggning för dig. Av prestanda skäl stänger den här loggningen av sig själv efter en stund, men inaktive ras varje `az webapp up` gång du kör igen. Kör följande kommando för att aktivera det manuellt:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Kör följande Azure CLI-kommando för att se logg strömmen:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Om du vill stoppa logg strömningen när som `Ctrl` + `C`helst skriver du.

## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure Portal

Sök efter och välj den app som du skapade i [Azure Portal](https://portal.azure.com).

![Navigera till din python django-app i Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din python django-app på sidan Översikt i Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs grupperna genom att köra följande kommandon:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig:

> [!div class="checklist"]
> * Skapa en Azure Database for PostgreSQL databas
> * Distribuera kod till Azure App Service och ansluta till postgres
> * Uppdatera din kod och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure Portal

Gå till nästa självstudie om du vill lära dig att mappa ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla ut andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera python-app](how-to-configure-python.md)
