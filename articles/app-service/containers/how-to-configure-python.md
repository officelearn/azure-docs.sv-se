---
title: Konfigurera Python-appar i Linux – Azure App Service
description: Den här självstudien beskriver alternativ för att redigera och konfigurera Python-appar för Azure App Service i Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/09/2018
ms.author: astay;cephalin;kraigb
ms.custom: seodec18
ms.openlocfilehash: 1d9b0e356f0f65be44a533fe098282084b900d89
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53249642"
---
# <a name="configure-your-python-app-for-the-azure-app-service-on-linux"></a>Konfigurera din Python-app för Azure App Service i Linux

Den här artikeln beskriver hur [Azure App Service i Linux](app-service-linux-intro.md) kör Python-appar och hur du kan anpassa beteendet för App Service när det behövs.

## <a name="set-python-version"></a>Ange Python-version

Två basavbildningar är tillgängliga: Python 3.6 och Python 3.7. Du kan skapa en app med önskad Python-basavbildning. Om du till exempel vill köra en app med Python 3.7 kör du följande kommando i Cloud Shell:

```azurecli-interactive
az webapp create --resource-group <group_name> --plan <plan_name> --name <app_name> --runtime "PYTHON|3.7"
```

Om du vill ändra Python-version (basavbildning) till exempelvis Python 3.6 kör du följande kommando i Cloud Shell:

```azurecli-interactive
az webapp config set --resource-group <group_name> --name <app_name> --linux-fx-version "PYTHON|3.6"
```

Om du behöver en annan version av Python måste du skapa och distribuera en egen containeravbildning i stället. Mer information finns i [Använda en anpassad Docker-avbildning för Web App for Containers](tutorial-custom-docker-image.md).

## <a name="container-characteristics"></a>Containeregenskaper

Python-appar som distribueras till App Service i Linux körs i en Docker-container som har definierats på GitHub-lagringsplatsen, [Python 3.6](https://github.com/Azure-App-Service/python/tree/master/3.6.6) eller [Python 3.7](https://github.com/Azure-App-Service/python/tree/master/3.7.0).

Den här containern har följande egenskaper:

- Appar körs med hjälp av [Gunicorn WSGI HTTP Server](https://gunicorn.org/), med de ytterligare argumenten `--bind=0.0.0.0 --timeout 600`.

- Som standard innehåller basavbildningen Flask-webbramverket, men containern har stöd för andra ramverk som är WSGI-kompatibla och kompatibla med Python 3.7, till exempel Django.

- Om du vill installera ytterligare paket, till exempel Django, skapar du en [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files)-fil i roten av projektet med hjälp av `pip freeze > requirements.txt`. Sedan publicerar du ditt projekt till App Service med hjälp av Git-distribution, som automatiskt kör `pip install -r requirements.txt` i containern för att installera din apps beroenden.

## <a name="container-startup-process-and-customizations"></a>Containerns startprocess och anpassningar

Under starten kör App Service i Linux-containern följande steg:

1. Sök efter och tillämpa ett anpassat startkommandot om sådant tillhandahålls.
2. Kontrollera om finns en Django-apps *wsgi.py*-fil, och starta i så fall Gunicorn med hjälp av den filen.
3. Sök efter en fil med namnet *application.py*, och om den hittas startar du Gunicorn med hjälp av `application:app` förutsatt att en Flask-app används.
4. Om ingen annan app hittas startar du en standardapp som är inbyggd i containern.

Följande avsnitt innehåller ytterligare information om varje alternativ.

### <a name="django-app"></a>Django-app

För Django-appar letar App Service efter en fil med namnet `wsgi.py` i din appkod och kör sedan Gunicorn med hjälp av följande kommando:

```bash
# <module> is the path to the folder containing wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Om du vill ha mer specifik kontroll över startkommandot använder du ett [anpassat startkommando](#custom-startup-command) och ersätter `<module>` med namnet på den modul som innehåller *wsgi.py*.

### <a name="flask-app"></a>Flask-app

For Flask letar App Service efter en fil med namnet *application.py* och startar Gunicorn enligt följande:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 application:app
```

Om din huvudappmodul finns i en annan fil använder du ett annat namn för appobjektet, eller om du vill ange ytterligare argument till Gunicorn så använder du ett [anpassat startkommando](#custom-startup-command). Det avsnittet har ett exempel för Flask med hjälp av en inmatningskod i *hello.py* och ett Flask-appobjekt med namnet `myapp`.

### <a name="custom-startup-command"></a>Anpassat startkommando

Du kan styra containerns startbeteende genom att ange ett anpassat Gunicorn-startkommando. Om du till exempel har en Flask-app vars huvudmodul är *hello.py* och Flask-appobjektet i den filen heter `myapp` blir kommandot följande:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
```

Om din huvudmodul är i en undermapp, till exempel `website`, anger du den mappen med argumentet `--chdir`:

```bash
gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
```

Du kan även lägga till ytterligare argument för Gunicorn till kommandot, till exempel `--workers=4`. Mer information finns i [Köra Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (docs.gunicorn.org).

Utför följande steg för att tillhandahålla ett anpassat kommando:

1. Navigera till sidan [Programinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) på Azure-portalen.

1. I inställningarna för **Runtime** anger du **Stack**-alternativet till **Python 3.7** och anger kommandot direkt i fältet **Startfil**.

    Alternativt kan du spara kommandot i en textfil i roten av projektet med ett namn som *startup.txt* (eller valfritt namn). Distribuera sedan filen till App Service och ange filnamnet i fältet **Startfil** i stället. Det här alternativet gör att du kan hantera kommandot på lagringsplatsen för din källkod i stället för via Azure-portalen.

1. Välj **Spara**. App Service startas om automatiskt och efter några sekunder bör du se att det anpassade startkommandot tillämpas.

> [!Note]
> App Service ignorerar eventuella fel som inträffar vid bearbetning av en fil med anpassat kommando och fortsätter sedan med startprocessen genom att söka efter Django- och Flask-appar. Om du inte ser det beteende du förväntar dig kontrollerar du att din startfil distribueras till App Service och att den inte innehåller några fel.

### <a name="default-behavior"></a>Standardbeteende

Om App Service inte hittar något anpassat kommando, någon Django-app eller någon Flask-app kör den en skrivskyddad standardapp som finns i mappen _opt/defaultsite_. Standardappen visas på följande sätt:

![Standardmässig App Service på Linux-webbplats](media/how-to-configure-python/default-python-app.png)

## <a name="troubleshooting"></a>Felsökning

- **Du ser standardappen när du har distribuerat din egen appkod.**  Standardappen visas eftersom du antingen inte faktiskt har distribuerat din kod till App Service eller för att App Service inte kunde hitta din appkod och körde standardappen i stället.
  - Starta om App Service, vänta 15–20 sekunder och kontrollera appen igen.
  - Se till att du använder App Service för Linux i stället för en Windows-baserad instans. Från Azure-CLI kör du kommandot `az webapp show --resource-group <resource_group_name> --name <app_service_name> --query kind`. Byt ut `<resource_group_name>` och `<app_service_name>` därefter. Du bör se `app,linux` som utdata; annars återskapar du App Service och väljer Linux.
    - Använd SSH- eller Kudu-konsolen för att ansluta direkt till App Service och kontrollera att dina filer finns under *site/wwwroot*. Om filerna inte finns granskar du din distributionsprocess och distribuerar appen på nytt.
  - Om filerna finns kunde App Service inte identifiera din specifika startfil. Kontrollera att din app är strukturerad på det sätt som App Service förväntar sig för [Django](#django-app) eller [Flask](#flask-app), eller använd ett [anpassat startkommando](#custom-startup-command).
  
- **Du ser meddelandet ”Service Unavailable” (Tjänsten är ej tillgänglig) i webbläsaren.** Webbläsaren nådde tidsgränsen i väntan på svar från App Service, vilket indikerar att App Service startade Gunicorn-servern, men de argument som specificerar appkoden är felaktiga.
  - Uppdatera webbläsaren, särskilt om du använder de lägsta prisnivåerna i din App Service-plan. Till exempel kan appen ta längre tid att starta om du använder de kostnadsfria nivåerna och blir tillgänglig när du uppdaterar webbläsaren.
  - Kontrollera att din app är strukturerad på det sätt som App Service förväntar sig för [Django](#django-app) eller [Flask](#flask-app), eller använd ett [anpassat startkommando](#custom-startup-command).
  - Använd SSH- eller Kudu-konsolen för att ansluta till App Service och undersök sedan de diagnostikloggar som lagras i mappen *LogFiles*. Mer information om loggning finns i [Aktivera diagnostikloggning för webbappar i Azure App Service](../web-sites-enable-diagnostic-log.md).
