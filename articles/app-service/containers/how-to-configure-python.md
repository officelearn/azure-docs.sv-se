---
title: Konfigurera Linux Python-appar
description: Lär dig hur du konfigurerar en förbyggd Python-behållare för din app. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
ms.topic: quickstart
ms.date: 03/28/2019
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: 8a9276f73c1d9bdf0289f41bb59340b29f5a2575
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046031"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Konfigurera en Linux Python-app för Azure App Service

Den här artikeln beskriver hur [Azure App Service](app-service-linux-intro.md) kör Python-appar och hur du kan anpassa beteendet för App Service när det behövs. Python-appar måste distribueras [pip](https://pypi.org/project/pip/) med alla nödvändiga kärnmoduler.

Distributionsmotorn för App-tjänsten aktiverar automatiskt `pip install -r requirements.txt` en virtuell miljö och körs åt dig när du distribuerar en [Git-lagringsplats](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)eller ett [Zip-paket](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) med byggprocesser påslagna.

Den här guiden innehåller viktiga begrepp och instruktioner för Python-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service bör du följa [snabbstarten Python](quickstart-python.md) och [Python med PostgreSQL-självstudien](tutorial-python-postgresql-app.md) först.

> [!NOTE]
> Linux är för närvarande det rekommenderade alternativet för att köra Python-appar i App Service. Information om Windows-alternativet finns i [Python om Windows-smaken av App Service](https://docs.microsoft.com/visualstudio/python/managing-python-on-azure-app-service).
>

## <a name="show-python-version"></a>Visa Python-version

För att visa den aktuella versionen av Python kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

För att visa alla versioner av Python som stöds kör du följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PYTHON
```

Du kan köra en version av Python genom att skapa en egen containeravbildning i stället. Mer information finns i [Använda anpassad Docker-avbildning](tutorial-custom-docker-image.md).

## <a name="set-python-version"></a>Ange Python-version

Kör följande kommando i [Cloud Shell](https://shell.azure.com) för att ställa in Python-versionen på 3.7:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
```

## <a name="customize-build-automation"></a>Anpassa byggautomation

Om du distribuerar din app med Git- eller zip-paket med build automation aktiverat, steg apptjänstens automatisering genom följande sekvens:

1. Kör anpassat skript om `PRE_BUILD_SCRIPT_PATH`det anges av .
1. Kör `pip install -r requirements.txt`.
1. Om *manage.py* finns i roten i databasen kör *du manage.py collectstatic*. Men om `DISABLE_COLLECTSTATIC` är `true`inställt på , hoppas det här steget över.
1. Kör anpassat skript om `POST_BUILD_SCRIPT_PATH`det anges av .

`PRE_BUILD_COMMAND`och `POST_BUILD_COMMAND` `DISABLE_COLLECTSTATIC` är miljövariabler som är tomma som standard. Om du vill köra förbyggningskommandon definierar du `PRE_BUILD_COMMAND`. Om du vill köra kommandon `POST_BUILD_COMMAND`efter build definierar du . Om du vill inaktivera att köra collectstatic när du skapar Django-appar anger du `DISABLE_COLLECTSTATIC=true`.

I följande exempel anges de två variablerna till en serie kommandon, avgränsade med kommatecken.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Ytterligare miljövariabler för att anpassa byggautomatisering finns i [Oryx-konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Mer information om hur App Service körs och bygger Python-appar i Linux finns i [Oryx-dokumentation: Så identifieras och skapas Python-appar](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

## <a name="container-characteristics"></a>Containeregenskaper

Python-appar som distribueras till App Service på Linux körs i en Docker-behållare som har definierats i [App Service Python GitHub-databasen](https://github.com/Azure-App-Service/python). Du hittar avbildningskonfigurationerna i de versionsspecifika katalogerna.

Den här containern har följande egenskaper:

- Appar körs med hjälp av [Gunicorn WSGI HTTP Server](https://gunicorn.org/), med de ytterligare argumenten `--bind=0.0.0.0 --timeout 600`.

- Som standard innehåller basavbildningen Flask-webbramverket, men containern har stöd för andra ramverk som är WSGI-kompatibla och kompatibla med Python 3.7, till exempel Django.

- Om du vill installera ytterligare paket, till exempel Django, skapar du en [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files)-fil i roten av projektet med hjälp av `pip freeze > requirements.txt`. Sedan publicerar du ditt projekt till App Service med hjälp av Git-distribution, som automatiskt kör `pip install -r requirements.txt` i containern för att installera din apps beroenden.

## <a name="container-startup-process"></a>Startprocessen för container

Under starten kör App Service i Linux-containern följande steg:

1. Använd ett [anpassat startkommando](#customize-startup-command) om sådant tillhandahålls.
2. Kontrollera om finns en [Django-app](#django-app), och starta Gunicorn för den om det finns.
3. Kontrollera om finns en [Flask-app](#flask-app), och starta Gunicorn för den om det finns.
4. Om ingen annan app hittas startar du en standardapp som är inbyggd i containern.

Följande avsnitt innehåller ytterligare information om varje alternativ.

### <a name="django-app"></a>Django-app

För Django-appar letar App Service efter en fil med namnet `wsgi.py` i din appkod och kör sedan Gunicorn med hjälp av följande kommando:

```bash
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Om du vill ha mer specifik kontroll över startkommandot använder du ett [anpassat startkommando](#customize-startup-command) och ersätter `<module>` med namnet på modulen som innehåller *wsgi.py*.

### <a name="flask-app"></a>Flask-app

För Flask letar App Service efter en fil med namnet *application.py* eller *app.py* och startar Gunicorn enligt följande:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app
# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Om huvudappmodulen finns i en annan fil använder du ett annat namn för appobjektet, eller om du vill ge ytterligare argument till Gunicorn, använder du ett [anpassat startkommando](#customize-startup-command).

### <a name="default-behavior"></a>Standardbeteende

Om App Service inte hittar något anpassat kommando, någon Django-app eller någon Flask-app kör den en skrivskyddad standardapp som finns i mappen _opt/defaultsite_. Standardappen visas på följande sätt:

![Standardmässig App Service på Linux-webbplats](media/how-to-configure-python/default-python-app.png)

## <a name="customize-startup-command"></a>Anpassa startkommando

Du kan styra containerns startbeteende genom att ange ett anpassat Gunicorn-startkommando. Så här gör du följande kommando i [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

Om du till exempel har en Flask-app vars huvudmodul är *hello.py* och `myapp`flask-appobjektet i filen heter, är * \<anpassade>* följande:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Om din huvudmodul är i en undermapp, till exempel `website`, anger du den mappen med argumentet `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Du kan också lägga till ytterligare argument för Gunicorn i `--workers=4` * \<>* med anpassade kommandon, till exempel . Mer information finns i [Köra Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Om du vill använda en icke-Gunicorn-server, * \<* till exempel [aiohttp,](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html)kan du ersätta>med anpassade kommandon med något liknande:

```bash
python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
```

> [!Note]
> App Service ignorerar eventuella fel som inträffar vid bearbetning av en fil med anpassat kommando och fortsätter sedan med startprocessen genom att söka efter Django- och Flask-appar. Om du inte ser det beteende du förväntar dig kontrollerar du att din startfil distribueras till App Service och att den inte innehåller några fel.

## <a name="access-environment-variables"></a>Få åtkomst till miljövariabler

I App Service kan du [ställa in appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) utanför appkoden. Du kan använda dem med hjälp av standardmönstret [os.environ](https://docs.python.org/3/library/os.html#os.environ). Om du till exempel vill få åtkomst till en appinställning med namnet `WEBSITE_SITE_NAME` använder du följande kod:

```python
os.environ['WEBSITE_SITE_NAME']
```

## <a name="detect-https-session"></a>Identifiera HTTPS-sessionen

I App Service sker [SSL-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) på lastbalanserare för nätverk, så alla HTTPS-begäranden når din app som okrypterade HTTP-begäranden. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Med populära ramverk får du åtkomst till `X-Forwarded-*` information i standardappens mönster. I [CodeIgniter](https://codeigniter.com/) kontrollerar [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) värdet för `X_FORWARDED_PROTO` som standard.

## <a name="access-diagnostic-logs"></a>Få åtkomst till diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Öppna SSH-session i webbläsaren

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Felsökning

- **Du ser standardappen när du har distribuerat din egen appkod.** Standardappen visas eftersom du antingen inte har distribuerat din kod till App Service eller för att App Service inte kunde hitta din appkod och körde standardappen i stället.
- Starta om App Service, vänta 15–20 sekunder och kontrollera appen igen.
- Se till att du använder App Service för Linux i stället för en Windows-baserad instans. Från Azure-CLI kör du kommandot `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`. Byt ut `<resource_group_name>` och `<app_service_name>` därefter. Du bör se `app,linux` som utdata; annars återskapar du App Service och väljer Linux.
- Använd SSH- eller Kudu-konsolen för att ansluta direkt till App Service och kontrollera att dina filer finns under *site/wwwroot*. Om filerna inte finns granskar du din distributionsprocess och distribuerar appen på nytt.
- Om filerna finns kunde App Service inte identifiera din specifika startfil. Kontrollera att appen är strukturerad som App Service förväntar sig för [Django](#django-app) eller [Flask](#flask-app)eller använd ett [anpassat startkommando](#customize-startup-command).
- **Du ser meddelandet ”Service Unavailable” (Tjänsten är ej tillgänglig) i webbläsaren.** Webbläsaren nådde tidsgränsen i väntan på svar från App Service, vilket indikerar att App Service startade Gunicorn-servern, men de argument som specificerar appkoden är felaktiga.
- Uppdatera webbläsaren, särskilt om du använder de lägsta prisnivåerna i din App Service-plan. Till exempel kan appen ta längre tid att starta om du använder de kostnadsfria nivåerna och blir tillgänglig när du uppdaterar webbläsaren.
- Kontrollera att appen är strukturerad som App Service förväntar sig för [Django](#django-app) eller [Flask](#flask-app)eller använd ett [anpassat startkommando](#customize-startup-command).
- [Öppna loggströmmen](#access-diagnostic-logs).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudiekurs: Python-app med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Självstudiekurs: Distribuera från privat behållardatabas](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar om App Service Linux](app-service-linux-faq.md)
