---
title: 'Handledning: Distribuera Python (Django) med Postgres'
description: Lär dig hur du skapar en Python-app med en PostgreSQL-databas och distribuerar den till Azure App Service på Linux. Självstudien använder en Django exempel app för demonstration.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085764"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Självstudiekurs: Distribuera en Python-webbapp (Django) med PostgreSQL i Azure App Service

Den här självstudien visar hur du distribuerar en datadriven Python-webbapp (Django) till [Azure App Service](app-service-linux-intro.md) och ansluter den till en Azure-databas för PostgreSQL-databas. App Service erbjuder en mycket skalbar, självkorrigering webbhotell.

![Distribuera Python Django-webbapp till Azure App Service](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en Azure-databas för PostgreSQL-databas
> * Distribuera kod till Azure App Service och ansluta till Postgres
> * Uppdatera koden och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure-portalen

Du kan följa stegen i den här artikeln på macOS, Linux eller Windows.

## <a name="install-dependencies"></a>Installera beroenden

Innan du startar den här självstudien:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Installera [Azure CLI](/cli/azure/install-azure-cli).
- Installera [Git](https://git-scm.com/).
- Installera [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Klona exempelappen

I ett terminalfönster kör du följande kommandon för att klona exempelappdatabasen och ändra till databasroten:

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Den djangoapp prov förvaret innehåller datadrivna [Django](https://www.djangoproject.com/) opinionsundersökningar app du får genom att följa [Skriva din första Django app](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django dokumentation. Det finns här för din bekvämlighet.

## <a name="prepare-app-for-app-service"></a>Förbereda appen för App Service

Liksom många Python-webbramverk kräver Django [vissa ändringar innan de kan köras i en produktionsserver](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/), och det är inte annorlunda med App Service. Du måste ändra och lägga till några inställningar i standardfilen *azuresite/settings.py* så att appen fungerar när den har distribuerats till App Service. 

Ta en titt på *azuresite/production.py*, vilket gör den nödvändiga konfigurationen för App Service. Kortfattat gör det följande:

- Ärva alla inställningar från *azuresite/settings.py*.
- Lägg till det fullständigt kvalificerade domännamnet för App Service-appen i de tillåtna värdarna. 
- Använd [WhiteNoise](https://whitenoise.evans.io/en/stable/) för att aktivera betjänande statiska filer i produktion, eftersom Django som standard inte tjänar statiska filer i produktionen. WhiteNoise-paketet ingår redan i *requirements.txt*.
- Lägg till konfiguration för PostgreSQL-databas. Som standard använder Django Sqlite3 som databas, men den är inte lämplig för produktionsappar. [Psycopg2-binära](https://pypi.org/project/psycopg2-binary/) paketet ingår redan i *requirements.txt*.
- Postgres-konfigurationen använder miljövariabler. Senare får du reda på hur du ställer in miljövariabler i App Service.

*azuresite/production.py* ingår i databasen för enkelhetens skull, men den används ännu inte av appen. För att se till att dess inställningar används i App Service måste du konfigurera två filer, *manage.py* och *azuresite/wsgi.py*, för att komma åt den.

- Ändra följande rad *i manage.py:*

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

    Du anger miljövariabeln `DJANGO_ENV` senare när du konfigurerar appen App Service.

- I *azuresite/wsgi.py*gör du samma ändring som ovan.

    I App Service använder du *manage.py* för att köra databasmigreringar och App Service använder *azuresite/wsgi.py* för att köra din Django-app i produktion. Den här ändringen i båda filerna säkerställer att produktionsinställningarna används i båda fallen.

## <a name="sign-in-to-azure-cli"></a>Logga in på Azure CLI

Du bör redan ha Azure CLI installerat. [Med Azure CLI](/cli/azure/what-is-azure-cli) kan du arbeta med Azure-resurser från kommandoradsterminalen. 

Om du vill logga [`az login`](/cli/azure/reference-index#az-login) in på Azure kör du kommandot:

```azurecli
az login
```

Följ instruktionerna i terminalen för att logga in på ditt Azure-konto. När du är klar visas dina prenumerationer i JSON-format i terminalutdata.

## <a name="create-postgres-database-in-azure"></a>Skapa Postgres-databas i Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

I det här avsnittet skapar du en Azure-databas för PostgreSQL-server och databas. Starta genom att `db-up` installera tillägget med följande kommando:

```azurecli
az extension add --name db-up
```

Skapa Postgres-databasen i [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure med kommandot, som visas i följande exempel. Ersätt * \<postgresql-namn>* med ett *unikt* namn (serverslutpunkten är *https://\<postgresql-namn>.postgres.database.azure.com*). För * \<admin-användarnamn>* och * \<admin-lösenord>* anger du autentiseringsuppgifter för att skapa en administratörsanvändare för den här Postgres-servern.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Det här kommandot kan ta ett tag eftersom det gör följande:

- Skapar en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) som heter `myResourceGroup`, om den inte finns. Varje Azure-resurs måste finnas i en av dessa. `--resource-group` är valfritt.
- Skapar en Postgres-server med den administrativa användaren.
- Skapar en `pollsdb` databas.
- Ger åtkomst från din lokala IP-adress.
- Tillåter åtkomst från Azure-tjänster.
- Skapa en databasanvändare med `pollsdb` åtkomst till databasen.

Du kan göra alla steg `az postgres` separat `psql`med `az postgres up` andra kommandon och , men gör dem alla i ett steg för dig.

När kommandot är klart hittar du de `Ran Database Query:`utdatarader som finns med . De visar databasanvändaren som har skapats `root` för `Pollsdb1`dig, med användarnamn och lösenord . Du använder dem senare för att ansluta appen till databasen.

<!-- not all locations support az postgres up -->
> [!TIP]
> Om du vill ange platsen för Postgres-servern tar du med argumentet `--location <location-name>`, där `<location_name>` är en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Du kan få de regioner som [`az account list-locations`](/cli/azure/account#az-account-list-locations) är tillgängliga för din prenumeration med kommandot.

## <a name="deploy-the-app-service-app"></a>Distribuera apptjänstappen

I det här avsnittet skapar du apptjänstappen. Du ansluter den här appen till den Postgres-databas som du skapade och distribuerar koden.

### <a name="create-the-app-service-app"></a>Skapa apptjänstappen

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Kontrollera att du är tillbaka i`djangoapp`databasroten ( ), eftersom appen kommer att distribueras från den här katalogen.

Skapa en App Service-app med [`az webapp up`](/cli/azure/webapp#az-webapp-up) kommandot, som visas i följande exempel. Ersätt * \<appnamn>* med ett *unikt* namn (serverslutpunkten är *https://\<appnamn>.azurewebsites.net*). Tillåtna tecken för `A` - `Z` `0` - `9` * \<appnamn>* är `-`, och .

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Det här kommandot kan ta ett tag eftersom det gör följande:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Genererar en [resursgrupp](../../azure-resource-manager/management/overview.md#terminology) automatiskt.
- Skapar [apptjänstplanen](../overview-hosting-plans.md) *myAppServicePlan* på den grundläggande prisnivån (B1), om den inte finns. `--plan`och `--sku` är valfria.
- Skapar apptjänstappen om den inte finns.
- Aktiverar standardloggning för appen, om den inte redan är aktiverad.
- Laddar upp databasen med ZIP-distribution med byggautomation aktiverad.

När distributionen är klar visas en JSON-utdata som följande:

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

Kopiera värdet för * \<app-resursgrupp>*. Du behöver den för att konfigurera appen senare. 

> [!TIP]
> Du kan använda samma kommando senare för att distribuera eventuella ändringar och omedelbart aktivera diagnostikloggar med:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Exempelkoden distribueras nu, men appen ansluter inte till Postgres-databasen i Azure ännu. Du gör det här sen.

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

När du kör appen lokalt kan du ställa in miljövariablerna i terminalsessionen. I App Service gör du det med *appinställningar*, med hjälp av kommandot [az webapp config appsettings set.](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set)

Kör följande kommando för att ange information om databasanslutning som appinställningar. Ersätt * \<appnamn>, * * \<app-resursgrupp>* och * \<postgresql-namn>* med dina egna värden. Kom ihåg att `root` `Pollsdb1` användarautentiseringsuppgifterna `az postgres up`och har skapats åt dig av .

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

Information om hur koden kommer åt dessa appinställningar finns i [Access-miljövariabler](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Köra migreringar av databaser

Om du vill köra databasmigreringar i App Service öppnar du en SSH-session i webbläsaren genom att navigera till *https://\<appnamn>.scm.azurewebsites.net/webssh/host:*

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

I SSH-sessionen kör du följande kommandon:

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

Bläddra till den distribuerade appen med URL *\//\<http: appnamn>.azurewebsites.net* i en webbläsare. Du bör se meddelandet **Inga omröstningar är tillgängliga**. 

Bläddra till *\//\<http: appnamn>.azurewebsites.net/admin* och logga in med administratörsanvändaren som du skapade i det sista steget. Välj **Lägg till** **bredvid Frågor**och skapa en omröstningsfråga med några alternativ.

Bläddra till den distribuerade appen med URL *\//\<http: appnamn>.azurewebsites.net/admin*och skapa några omröstningsfrågor. Du kan se frågorna på *\//\<http: app-name>.azurewebsites.net/*. 

![Kör Python Django-appen i App Services i Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Bläddra till den distribuerade appen med URL *\//\<http: app-namn>.azurewebsites.net* igen för att se enkätfrågan och svara på frågan.

App Service identifierar ett Django-projekt i *wsgi.py* din databas genom att leta efter `manage.py startproject` en wsgi.py fil i varje underkatalog, vilket skapar som standard. När App Service hittar filen läses Django-webbappen in. Mer information om hur App Service läser in Python-appar finns i avsnittet om att [konfigurera inbyggd Python-avbildning](how-to-configure-python.md).

**Grattis!** Du kör en Python-webbapp (Django) i Azure App Service för Linux.

## <a name="develop-app-locally-and-redeploy"></a>Utveckla appen lokalt och omfördela

I det här avsnittet utvecklar du din app i din lokala miljö och distribuerar om koden till App Service.

### <a name="set-up-locally-and-run"></a>Konfigurera lokalt och kör

Om du vill konfigurera den lokala utvecklingsmiljön och köra exempelappen för första gången kör du följande kommandon:

# <a name="bash"></a>[Bash](#tab/bash)

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

# <a name="cmd"></a>[Cmd](#tab/cmd)

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

När Django-webbappen är fulladdad returneras ungefär följande meddelande:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

Gå till *\/http: /localhost:8000* i en webbläsare. Du bör se meddelandet **Inga omröstningar är tillgängliga**. 

Gå till *\/http: /localhost:8000/admin* och logga in med administratörsanvändaren som du skapade i det sista steget. Välj **Lägg till** **bredvid Frågor**och skapa en omröstningsfråga med några alternativ.

![Kör Python Django-appen i App Services lokalt](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Gå till *\/http: /localhost:8000* igen för att se enkäten frågan och svara på frågan. Det lokala Django-exempelprogrammet skriver och lagrar användardata till en lokal Sqlite3-databas, så du behöver inte oroa dig för att förstöra produktionsdatabasen. Om du vill att utvecklingsmiljön matchar Azure-miljön bör du överväga att använda en Postgres-databas lokalt i stället.

Om du vill stoppa Django-servern skriver du Ctrl+C.

### <a name="update-the-app"></a>Uppdatera appen

Bara för att se hur det fungerar `polls/models.py`att göra appuppdateringar, göra en liten förändring i . Hitta raden:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

Och ändra det till:

```python
choice_text = models.CharField(max_length=100)
```

Genom att ändra datamodellen måste du skapa en ny Django-migrering. Gör det med följande kommando:

```
python manage.py makemigrations
```

Du kan testa dina ändringar lokalt genom att köra migreringar, köra utvecklingsservern och navigera till *\/http: /localhost:8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Distribuera om kod till Azure

Om du vill distribuera om ändringarna kör du följande kommando från databasroten:

```azurecli
az webapp up --name <app-name>
```

App Service upptäcker att appen finns och bara distribuerar koden.

### <a name="rerun-migrations-in-azure"></a>Köra migreringar igen i Azure

Eftersom du har gjort ändringar i datamodellen måste du köra om databasmigreringar i App Service. Öppna en SSH-session i webbläsaren genom att navigera till *https://\<appnamn>.scm.azurewebsites.net/webssh/host*. Kör följande kommandon:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Granska appen i produktion

Bläddra till *\//\<http: app-namn>.azurewebsites.net* och se de ändringar som körs live i produktionen. 

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

Du kan komma åt konsolloggarna som genereras inifrån behållaren.

> [!TIP]
> `az webapp up`aktiverar standardloggningen åt dig. Av prestandaskäl inaktiverar loggningen sig själv efter en viss tid, men slås på varje gång du kör `az webapp up` igen. Om du vill aktivera den manuellt kör du följande kommando:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Kör följande Azure CLI-kommando för att se loggströmmen:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan också granska loggfilerna `https://<app-name>.scm.azurewebsites.net/api/logs/docker`från webbläsaren på .

Om du vill stoppa loggströmning när som helst skriver du `Ctrl` + `C`.

## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure-portalen

Sök efter och välj appen du skapade i [Azure-portalen.](https://portal.azure.com)

![Navigera till python Django-appen i Azure-portalen](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Portalen visar som standard dina webbappar på sidan **Översikt**. På den här sidan får du en översikt över hur det går för appen. Här kan du också utföra grundläggande hanteringsuppgifter som bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din Python Django-app på översiktssidan i Azure-portalen](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte förväntar dig att behöva dessa resurser i framtiden tar du bort resursgrupperna genom att köra följande kommandon:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig:

> [!div class="checklist"]
> * Skapa en Azure-databas för PostgreSQL-databas
> * Distribuera kod till Azure App Service och ansluta till Postgres
> * Uppdatera koden och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure-portalen

Gå till nästa självstudiekurs om du vill lära dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudiekurs: Mappa anpassat DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller kolla in andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera Python-appen](how-to-configure-python.md)
