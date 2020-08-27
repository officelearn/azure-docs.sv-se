---
title: 'Självstudie: Distribuera en python django-app med postgres'
description: Skapa en python-webbapp med en PostgreSQL-databas och distribuera den till Azure. I självstudien används django-ramverket och appen finns på Azure App Service på Linux.
ms.devlang: python
ms.topic: tutorial
ms.date: 07/22/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
- devx-track-python
- devx-track-azurecli
ms.openlocfilehash: 368a87d1054e4a5ad12fa1e8c78bcde39f76ee63
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959416"
---
# <a name="tutorial-deploy-a-django-web-app-with-postgresql-in-azure-app-service"></a>Själv studie kurs: Distribuera en django-webbapp med PostgreSQL i Azure App Service

Den här självstudien visar hur du distribuerar en data driven python [django](https://www.djangoproject.com/) -webbapp för att [Azure App Service](overview.md) och ansluta den till en Azure Database för postgres-databas. App Service ger en mycket skalbar och automatisk korrigering av webb värd tjänst.

I den här självstudien använder du Azure CLI för att utföra följande uppgifter:

> [!div class="checklist"]
> * Konfigurera din första miljö med python och Azure CLI
> * Skapa en Azure Database for PostgreSQL databas
> * Distribuera kod till Azure App Service och ansluta till postgres
> * Uppdatera din kod och distribuera om
> * Visa diagnostikloggar
> * Hantera webbappen i Azure Portal

Du kan också använda [Azure Portal versionen av den här själv studie kursen](/azure/developer/python/tutorial-python-postgresql-app-portal).


## <a name="set-up-your-initial-environment"></a>Konfigurera din inledande miljö

1. Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installera <a href="https://www.python.org/downloads/" target="_blank">Python 3,6 eller högre</a>.
1. Installera <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 eller högre, med vilken du kör kommandon i alla gränssnitt för att etablera och konfigurera Azure-resurser.

Öppna ett terminalfönster och kontrol lera att python-versionen är 3,6 eller högre:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Kontrol lera att din Azure CLI-version är 2.0.80 eller högre:

```azurecli
az --version
```

Logga sedan in på Azure via CLI:

```azurecli
az login
```

Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter. När kommandot har slutförts visas JSON-utdata som innehåller information om dina prenumerationer.

När du har loggat in kan du köra Azure-kommandon med Azure CLI för att arbeta med resurser i din prenumeration.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clone-or-download-the-sample-app"></a>Klona eller hämta exempel appen

# <a name="git-clone"></a>[Git-klon](#tab/clone)

Klona exempel lagrings platsen:

```terminal
git clone https://github.com/Azure-Samples/djangoapp
```

Gå sedan till mappen:

```terminal
cd djangoapp
```

# <a name="download"></a>[Ladda ned](#tab/download)

Besök [https://github.com/Azure-Samples/djangoapp](https://github.com/Azure-Samples/djangoapp) , Välj **klona**och välj sedan **Hämta zip**. 

Packa upp ZIP-filen i en mapp med namnet *djangoapp*. 

Öppna sedan ett terminalfönster i den *djangoapp* -mappen.

---

Djangoapp-exemplet innehåller den data drivna django avsöknings appen som du får genom att följa de [första django-apparna](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) i Django-dokumentationen. Den färdiga appen tillhandahålls här för din bekvämlighet.

Exemplet ändras också till att köras i en produktions miljö som App Service:

- Produktions inställningarna finns i filen *azuresite/productation. py* . Utvecklings information finns i *azuresite/Settings. py*.
- Appen använder produktions inställningar när `DJANGO_ENV` miljövariabeln är inställd på "produktion". Du skapar den här miljövariabeln senare i självstudien tillsammans med andra som används för PostgreSQL-databas konfigurationen.

Dessa ändringar är specifika för att konfigurera django för att köras i alla produktions miljöer och är inte särskilt App Service. Mer information finns i [Check lista för django-distribution](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="create-postgres-database-in-azure"></a>Skapa postgres-databas i Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](../postgresql/concepts-pricing-tiers.md#compute-generations-and-vcores) is available in your region. -->

Installera `db-up` tillägget för Azure CLI:

```azurecli
az extension add --name db-up
```

Om `az` kommandot inte känns igen kontrollerar du att du har installerat Azure CLI enligt beskrivningen i [Konfigurera din inledande miljö](#set-up-your-initial-environment).

Skapa sedan postgres-databasen i Azure med [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) kommandot:

```azurecli
az postgres up --resource-group DjangoPostgres-tutorial-rg --location westus2 --sku-name B_Gen5_1 --server-name <postgre-server-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

- Ersätt *\<postgres-server-name>* med ett namn som är unikt för alla Azure (Server slut punkten är `https://\<postgres-server-name>.postgres.database.azure.com` ). Ett utmärkt mönster är att använda en kombination av ditt företags namn och ett annat unikt värde.
- För *\<admin-username>* och *\<admin-password>* anger du autentiseringsuppgifter för att skapa en administratörs användare för den här postgres-servern.
- [Pris nivån](../postgresql/concepts-pricing-tiers.md) B_Gen5_1 (Basic, Gen5, 1 kärna) som används här är den minst dyra. För produktions databaser utelämnar du `--sku-name` argumentet för att använda GP_Gen5_2-nivån (generell användning, generation 5, 2 kärnor) i stället.

Detta kommando utför följande åtgärder, vilket kan ta några minuter:

- Skapa en [resurs grupp](../azure-resource-manager/management/overview.md#terminology) med namnet `DjangoPostgres-tutorial-rg` , om den inte redan finns.
- Skapa en postgres-Server.
- Skapa ett standard administratörs konto med ett unikt användar namn och lösen ord. (Om du vill ange dina egna autentiseringsuppgifter använder `--admin-user` du `--admin-password` argumenten och med `az postgres up` kommandot.)
- Skapa en `pollsdb` databas.
- Aktivera åtkomst från din lokala IP-adress.
- Aktivera åtkomst från Azure-tjänster.
- Skapa en databas användare med åtkomst till `pollsdb` databasen.

Du kan utföra alla steg separat med andra `az postgres` och `psql` -kommandon, men `az postgres up` gör alla steg tillsammans.

När kommandot har slutförts, matar den ut ett JSON-objekt som innehåller olika anslutnings strängar för databasen tillsammans med Server-URL, ett genererat användar namn (t. ex. " joyfulKoala@msdocs-djangodb-12345 ") och ett GUID-lösenord. Kopiera användar namnet och lösen ordet till en tillfällig textfil när du behöver dem senare i den här självstudien.

<!-- not all locations support az postgres up -->
> [!TIP]
> `-l <location-name>`, kan ställas in på en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/regions/). Du kan hämta de regioner som är tillgängliga för din prenumeration med [`az account list-locations`](/cli/azure/account#az-account-list-locations) kommandot. För produktion av appar sätter du din databas och din app på samma plats.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="deploy-the-code-to-azure-app-service"></a>Distribuera koden till Azure App Service

I det här avsnittet skapar du app Host i App Service app, ansluter den här appen till postgres-databasen och distribuerar sedan koden till den värden.

### <a name="create-the-app-service-app"></a>Skapa appen App Service

I terminalen kontrollerar du att du befinner dig i lagrings platsen ( `djangoapp` ) som innehåller appens kod.

Skapa en App Service app (värd processen) med [`az webapp up`](/cli/azure/webapp#az-webapp-up) kommandot:

```azurecli
az webapp up --resource-group DjangoPostgres-tutorial-rg --location westus2 --plan DjangoPostgres-tutorial-plan --sku B1 --name <app-name>
```
<!-- without --sku creates PremiumV2 plan -->

- För `--location` argumentet använder du samma plats som du gjorde för databasen i föregående avsnitt.
- Ersätt *\<app-name>* med ett unikt namn i alla Azure (Server slut punkten är `https://\<app-name>.azurewebsites.net` ). Tillåtna tecken för *\<app-name>* är `A` - `Z` , `0` - `9` och `-` . Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.

Detta kommando utför följande åtgärder, vilket kan ta några minuter:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Skapa [resurs gruppen](../azure-resource-manager/management/overview.md#terminology) om den inte redan finns. (I det här kommandot använder du samma resurs grupp där du skapade databasen tidigare.)
- Skapa [App Service plan](overview-hosting-plans.md) *DjangoPostgres-självstudie – planera* i den grundläggande pris nivån (B1) om den inte finns. `--plan` och `--sku` är valfria.
- Skapa App Service-appen om den inte finns.
- Aktivera standard loggning för appen om den inte redan är aktive rad.
- Ladda upp lagrings platsen med hjälp av ZIP-distribution med build Automation aktiverat.

Vid lyckad distribution genererar kommandot JSON-utdata som i följande exempel:

![Exempel på AZS webapp-utdata](./media/tutorial-python-postgresql-app/az-webapp-up-output.png)

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

> [!TIP]
> Många Azure CLI-kommandon cachelagrar vanliga parametrar, till exempel namnet på resurs gruppen och App Service plan, i filen *. Azure/config*. Därför behöver du inte ange samma parameter med senare kommandon. Om du till exempel vill distribuera om appen när du har gjort ändringar kan du bara köra `az webapp up` igen utan parametrar. Kommandon som kommer från CLI-tillägg, till exempel, `az postgres up` används inte för närvarande, vilket är anledningen till att du måste ange resurs gruppen och platsen här med `az webapp up` .

> [!NOTE]
> Om du försöker besöka appens URL-adress påträffas felet "DisallowedHost at/". Det här felet beror på att du ännu inte har konfigurerat appen för att använda de produktions inställningar som beskrivs ovan, vilket du gör i följande avsnitt.

### <a name="configure-environment-variables-to-connect-the-database"></a>Konfigurera miljövariabler för att ansluta databasen

Med koden nu distribuerad till App Service är nästa steg att ansluta appen till postgres-databasen i Azure.

App-koden förväntar sig att hitta databas information i ett antal miljövariabler. Om du vill ange miljövariabler i App Service skapar du "appinställningar" med kommandot [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) .

```azurecli
az webapp config appsettings set --settings DJANGO_ENV="production" DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="pollsdb" DBUSER="<username>" DBPASS="<password>"
```

- Ersätt *\<postgres-server-name>* med det namn som du använde tidigare med `az postgres up` kommandot.
- Ersätt *\<username>* och *\<password>* med de autentiseringsuppgifter som kommandot också genererat för dig.
- Resurs gruppen och App-namnet hämtas från de cachelagrade värdena i *Azure/config-* filen.
- Kommandot skapar inställningar med namnet `DJANGO_ENV` ,,, `DBHOST` `DBNAME` `DBUSER` och `DBPASS` som förväntat av appens kod.
- I python-koden får du åtkomst till de här inställningarna som miljövariabler med uttryck som `os.environ.get('DJANGO_ENV')` . Mer information finns i [Access Environment-variabler](configure-language-python.md#access-environment-variables).

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="run-django-database-migrations"></a>Köra django Database-migreringar

Django Database-migreringar ser till att schemat i PostgreSQL i Azure Database matchar de som beskrivs i din kod.

1. Öppna en SSH-session i webbläsaren genom att gå till *https:// \<app-name> . scm.azurewebsites.net/webssh/Host* och logga in med dina autentiseringsuppgifter för Azure-kontot (inte databas serverns autentiseringsuppgifter).

1. Kör följande kommandon i SSH-sessionen (du kan klistra in kommandon med **CTRL** + **Shift** + **V**):

    ```bash
    cd site/wwwroot
    
    # Activate default virtual environment in App Service container
    source /antenv/bin/activate
    # Install packages
    pip install -r requirements.txt
    # Run database migrations
    python manage.py migrate
    # Create the super user (follow prompts)
    python manage.py createsuperuser
    ```
    
1. `createsuperuser`Kommandot efterfrågar behörigheter för superanvändare. I den här självstudien använder du standard användar namnet `root` , trycker på **RETUR** för e-postadressen för att lämna den tom och anger `Pollsdb1` lösen ordet.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)
    
### <a name="create-a-poll-question-in-the-app"></a>Skapa en avsöknings fråga i appen

1. Öppna webb adressen *http: \/ / \<app-name> . azurewebsites.net*i en webbläsare. Appen ska visa meddelandet "inga avsökningar är tillgängliga" eftersom det inte finns några speciella avsökningar än i databasen.

1. Bläddra till *http: \/ / \<app-name> . azurewebsites.net/admin*. Logga in med behörigheter för superanvändare från föregående avsnitt ( `root` och `Pollsdb1` ). Under **avsökningar**väljer du **Lägg till** bredvid **frågor** och skapar en avsöknings fråga med några val.

1. Bläddra igen till *http: \/ / \<app-name> . azurewebsites.net/* för att bekräfta att frågorna nu visas för användaren. Besvara frågor som du vill skapa vissa data i databasen.

**Grattis!** Du kör en python django-webbapp i Azure App Service för Linux med en aktiv postgres-databas.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> App Service identifierar ett django-projekt genom att söka efter en *wsgi.py* -fil i varje undermapp som `manage.py startproject` skapas som standard. När App Service hittar filen laddas django-webbappen. Mer information finns i [Konfigurera inbyggd python-avbildning](configure-language-python.md).

## <a name="make-code-changes-and-redeploy"></a>Gör kod ändringar och distribuera om

I det här avsnittet ska du göra lokala ändringar i appen och distribuera om koden till App Service. I processen ställer du in en virtuell python-miljö som har stöd för pågående arbete.

### <a name="run-the-app-locally"></a>Köra appen lokalt

Kör följande kommandon i ett terminalfönster. Se till att följa anvisningarna när du skapar superanvändaren:

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

```cmd
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

När webbappen har lästs in är django-utvecklings servern den lokala appens URL i meddelandet "startar utvecklings servern på http://127.0.0.1:8000/ . Avsluta servern med CTRL-BREAK ".

![Exempel på utdata för django Development Server](./media/tutorial-python-postgresql-app/django-dev-server-output.png)

Testa appen lokalt med följande steg:

1. Gå till *http: \/ /localhost: 8000* i en webbläsare, som ska visa meddelandet "det finns inga avsökningar". 

1. Gå till *http: \/ /localhost: 8000/admin* och logga in med administratörs användaren som du skapade tidigare. Under **avsökningar**väljer du **Lägg till** bredvid **frågor** och skapar en avsöknings fråga med några val. 

1. Gå till *http: \/ /localhost: 8000* igen och besvara frågan för att testa appen. 

1. Stoppa django-servern genom att trycka på **CTRL** + **C**.

När du kör lokalt använder appen en lokal sqlite3-databas och stör inte din produktions databas. Du kan också använda en lokal PostgreSQL-databas, om du vill, för att bättre simulera produktions miljön.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="update-the-app"></a>Uppdatera appen

I `polls/models.py` , letar du upp raden som börjar med `choice_text` och ändrar `max_length` parametern till 100:

```python
# Find this lie of code and set max_length to 100 instead of 200
choice_text = models.CharField(max_length=100)
```

Eftersom du har ändrat data modellen, skapar du en ny django-migrering och migrerar databasen:

```
python manage.py makemigrations
python manage.py migrate
```

Kör utvecklings servern igen med `python manage.py runserver` och testa appen på *http: \/ /localhost: 8000/admin*:

Stoppa django-webbservern igen med **CTRL** + **+ C**.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="redeploy-the-code-to-azure"></a>Distribuera om koden till Azure

Kör följande kommando i databas roten:

```azurecli
az webapp up
```

Det här kommandot använder parametrarna som är cachelagrade i *. Azure/config-* filen. Eftersom App Service upptäcker att appen redan finns, distribuerar den bara om koden.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="rerun-migrations-in-azure"></a>Kör om migreringar i Azure

Eftersom du har gjort ändringar i data modellen måste du köra om migreringen av databasen i App Service.

Öppna en SSH-session igen i webbläsaren genom att gå till *https:// \<app-name> . scm.azurewebsites.net/webssh/Host*. Kör sedan följande kommandon:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

### <a name="review-app-in-production"></a>Granska appen i produktion

Bläddra till *http: \/ / \<app-name> . azurewebsites.net* och testa appen igen i produktion. (Eftersom du bara har ändrat längden på ett databas fält är ändringen bara märkbart om du försöker ange ett längre svar när du skapar en fråga.)

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

Du kan komma åt konsol loggar som genereras inifrån den behållare som är värd för appen på Azure.

Kör följande Azure CLI-kommando för att se logg strömmen. Det här kommandot använder parametrarna som är cachelagrade i *. Azure/config-* filen.

```azurecli
az webapp log tail
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

Om du vill stoppa logg strömningen när som helst, skriver du **CTRL** + **C**.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

> [!NOTE]
> Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .
>
> `az webapp up` aktiverar standard loggning för dig. Av prestanda skäl stänger den här loggningen av sig själv efter en stund, men inaktive ras varje gång du kör `az webapp up` igen. Kör följande kommando för att aktivera det manuellt:
>
> ```azurecli
> az webapp log config --docker-container-logging filesystem
> ```

## <a name="manage-your-app-in-the-azure-portal"></a>Hantera din app i Azure Portal

I [Azure Portal](https://portal.azure.com)söker du efter appens namn och väljer appen i resultaten.

![Navigera till din python django-app i Azure Portal](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

Som standard visar portalen appens **översikts** sida, som ger en allmän prestanda vy. Här kan du också utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta om och ta bort. På flikarna till vänster på sidan kan du se olika konfigurationssidor som du kan öppna.

![Hantera din python django-app på sidan Översikt i Azure Portal](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill behålla appen eller fortsätta till nästa självstudie kan du gå vidare till [Nästa steg](#next-steps). Annars kan du ta bort resurs gruppen som du skapar för den här självstudien för att undvika löpande kostnader:

```azurecli
az group delete
```

Kommandot använder resurs grupps namnet cachelagrat i *. Azure/config-* filen. Genom att ta bort resurs gruppen frigör och tar du även bort alla resurser som finns i den.

[Har du problem? Berätta för oss.](https://aka.ms/DjangoCLITutorialHelp)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du mappar ett anpassat DNS-namn till din app:

> [!div class="nextstepaction"]
> [Självstudie: mappa ett anpassat DNS-namn till din app](app-service-web-tutorial-custom-domain.md)

Lär dig hur App Service kör en python-app:

> [!div class="nextstepaction"]
> [Konfigurera python-app](configure-language-python.md)