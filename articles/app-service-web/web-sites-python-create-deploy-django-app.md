---
title: Skapa webbappar med Django i Azure
description: "En introduktionskurs till att köra en Python-webbapp i Azure App Service Web Apps."
services: app-service\web
documentationcenter: python
tags: python
author: huguesv
manager: erikre
editor: 
ms.assetid: 9be1a05a-9460-49ae-94fb-9798f82c11cf
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 02/19/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c6679cf7a6b059ee7c28e1754c54afa6414d633a
ms.lasthandoff: 01/20/2017


---
# <a name="creating-web-apps-with-django-in-azure"></a>Skapa webbappar med Django i Azure
Under den här kursen får du veta hur du kommer igång med Python i [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). Med Web Apps får du begränsat ledigt värdutrymme och snabb distribution och kan använda Python. I takt med att appen växer kan du gå över till betald hantering och även integrera med alla övriga Azure-tjänster.

Du får skapa ett program med hjälp av Djangos webbramverk (se motsvarande versioner av kursen för [Flask](web-sites-python-create-deploy-flask-app.md) och [Bottle](web-sites-python-create-deploy-bottle-app.md)). Du får skapa webbappen från Azure Marketplace, konfigurera Git-distribution och klona lagringsplatsen lokalt. Sedan kör du programmet lokalt, gör ändringar, checkar in dem och push-överför dem till Azure. I den här kursen får du veta hur du gör det från Windows eller Mac/Linux.

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](https://azure.microsoft.com/try/app-service/). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inga kreditkort krävs. Inga åtaganden.
> 
> 

## <a name="prerequisites"></a>Krav
* Windows, Mac eller Linux
* Python 2.7 eller 3.4
* installationsverktyg, pip, virtuell miljö (endast Python 2.7)
* Git
* [Python Tools för Visual Studio][Python Tools för Visual Studio] (PTVS) - Obs! det här är valfritt

**Obs**! TFS-publicering stöds för närvarande inte för Python-projekt.

### <a name="windows"></a>Windows
Om du inte redan har Python 2.7 eller 3.4 installerat (32-bitars) rekommenderar vi att du installerar [Azure SDK för Python 2.7] eller [Azure SDK för Python 3.4] med hjälp av installationsprogrammet för webbplattform. När du gör det installeras 32-bitarsversionen av Python, installationsverktyg, pip, virtuell miljö med mera. (Det är 32-bitarsversionen av Python som finns installerad på Azure-värddatorerna.) Du kan också hämta Python på [python.org].

För Git rekommenderar vi [Git för Windows] eller [GitHub för Windows]. Om du använder Visual Studio kan du använda det integrerade Git-stödet.

Vi rekommenderar också att du installerar [Python Tools 2.2 för Visual Studio]. Det här är valfritt, men om du har [Visual Studio], inklusive det kostnadsfria Visual Studio Community 2013 eller Visual Studio Express 2013 för webben, har du en utmärkt Python IDE.

### <a name="maclinux"></a>Mac/Linux
Du bör redan ha Python och Git installerade, men kontrollera att du har Python 2.7 eller 3.4.

## <a name="web-app-creation-on-portal"></a>Skapa en webbapp i Azure Portal
Det första steget i att skapa din app är att skapa webbappen via [Azure Portal](https://portal.azure.com).

1. Logga in på Azure Portal och klicka på knappen **NY** i det nedre vänstra hörnet.
2. Skriv "python" i sökrutan.
3. I sökresultaten väljer Välj **Django** (publicerat av PTVS) i sökresultatet och klicka sedan på **Skapa**.
4. Konfigurera den nya Django-appen och skapa till exempel en ny App Service-plan och en ny resursgrupp för den. Klicka på **Skapa**.
5. Konfigurera Git-publicering för den nya webbappen genom att följa anvisningarna i [Lokal Git-distribution till Azure App Service](app-service-deploy-local-git.md).

## <a name="application-overview"></a>Programöversikt
### <a name="git-repository-contents"></a>Innehåll på Git-lagringsplatsen
Här är en översikt över filerna på den första Git-lagringsplatsen, som vi ska klona i nästa avsnitt.

    \app\__init__.py
    \app\forms.py
    \app\models.py
    \app\tests.py
    \app\views.py
    \app\static\content\
    \app\static\fonts\
    \app\static\scripts\
    \app\templates\about.html
    \app\templates\contact.html
    \app\templates\index.html
    \app\templates\layout.html
    \app\templates\login.html
    \app\templates\loginpartial.html
    \DjangoWebProject\__init__.py
    \DjangoWebProject\settings.py
    \DjangoWebProject\urls.py
    \DjangoWebProject\wsgi.py

Programmets huvudsakliga källor. Består av tre sidor (index, om och kontakt) med bakgrundslayout. Statiskt innehåll och skript inkluderar bootstrap, jquery, modernizr och respond.

    \manage.py

Stöd för lokal hanterings- och utvecklingsserver. Används till att köra programmet lokalt, synkronisera databasen med mera.

    \db.sqlite3

Standarddatabas. Innehåller de tabeller som krävs för att programmet ska köras, men inga användare. (Du kan skapa användare genom att synkronisera databasen.)

    \DjangoWebProject.pyproj
    \DjangoWebProject.sln

Projektfiler för användning med [Python Tools för Visual Studio].

    \ptvs_virtualenv_proxy.py

IIS-proxy för virtuella miljöer och PTVS-stöd för fjärrfelsökning.

    \requirements.txt

Externa paket som krävs för programmet. Distributionsskriptet pip-installerar paketen som listas i den här filen.

    \web.2.7.config
    \web.3.4.config

IIS-konfigurationsfiler. Distributionsskriptet använder lämplig web.x.y.config och skapar kopian web.config.

### <a name="optional-files---customizing-deployment"></a>Valfria filer – anpassa distributionen
[!INCLUDE [web-sites-python-django-customizing-deployment](../../includes/web-sites-python-django-customizing-deployment.md)]

### <a name="optional-files---python-runtime"></a>Valfria filer – Python-körning
[!INCLUDE [web-sites-python-customizing-runtime](../../includes/web-sites-python-customizing-runtime.md)]

### <a name="additional-files-on-server"></a>Ytterligare filer på servern
Det finns filer på servern som inte har lagts till på Git-lagringsplatsen. Dessa skapas med skriptet för distribution.

    \web.config

IIS-konfigurationsfilen. Skapas utifrån web.x.y.config vid varje distribution.

    \env\

Python, virtuell miljö Skapas under distributionen om det inte redan finns en kompatibel virtuell miljö i webbappen. De paket som anges i requirements.txt pip-installeras, men pip hoppar över installationen om paketen redan är installerade.

I följande tre avsnitt beskrivs hur du fortsätter med webbappsutvecklingen i tre olika miljöer:

* Windows med Python Tools för Visual Studio
* Windows, med kommandorad
* Mac/Linux, med kommandorad

## <a name="web-app-development---windows---python-tools-for-visual-studio"></a>Webbappsutveckling – Windows – Python Tools för Visual Studio
### <a name="clone-the-repository"></a>Klona lagringsplatsen
Först klonar du lagringsplatsen med hjälp av webbadressen som tillhandahålls på Azure Portal. Mer information finns i [Lokal Git-distribution till Azure App Service](app-service-deploy-local-git.md).

Öppna lösningsfilen (.sln) som finns i lagringsplatsens rot.

![](./media/web-sites-python-create-deploy-django-app/ptvs-solution-django.png)

### <a name="create-virtual-environment"></a>Skapa en virtuell miljö
Nu ska vi skapa en virtuell miljö för lokal utveckling. Högerklicka på **Python Environments** (Python-miljöer) och välj **Add Virtual Environment...** (Lägg till virtuell miljö).

* Kontrollera att namnet på miljön är `env`.
* Välj bastolk. Se till att använda samma version av Python som valts för webbappen (i runtime.txt eller i bladet **Programinställningar** för webbappen i Azure Portal).
* Kontrollera att alternativet för att ladda ned och installera paket är markerat.

![](./media/web-sites-python-create-deploy-django-app/ptvs-add-virtual-env-27.png)

Klicka på **Skapa**. När du gör det skapas den virtuella miljön och beroenden som är angivna i requirements.txt installeras.

### <a name="create-a-superuser"></a>Skapa en användare med fullständig behörighet
I databasen som ingår i programmet finns ingen definierad användare med fullständig behörighet. Du måste skapa en användare med fullständig behörighet för att kunna använda inloggningsfunktionen i programmet och administratörsgränssnittet för Django (om du väljer att aktivera det).

Kör följande från kommandoraden i projektmappen:

    env\scripts\python manage.py createsuperuser

Följ anvisningarna för att ange användarnamn, lösenord osv.

### <a name="run-using-development-server"></a>Kör med utvecklingsservern
Tryck på F5 för att starta felsökningen. Sidan som körs lokalt öppnas automatiskt i webbläsaren.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

Du kan ange brytpunkter i källorna, använda bevakningsfönstren med mera. Mer information om de olika funktionerna finns i [Dokumentationen om Python Tools för Visual Studio].

### <a name="make-changes"></a>Göra ändringar
Nu kan du experimentera med att göra ändringar i programmets källor och mallar.

När du har testat ändringarna checkar du in dem på Git-lagringsplatsen:

![](./media/web-sites-python-create-deploy-django-app/ptvs-commit-django.png)

### <a name="install-more-packages"></a>Installera fler paket
Programmet kan ha beroenden utöver Python och Django.

Du kan installera ytterligare paket med hjälp av pip. Om du vill installera ett paket högerklickar du på den virtuella miljön och väljer **Install Python Package** (Installera Python-paket).

Om du till exempel vill installera Azure SDK för Python, som ger dig tillgång till Azure Storage, Service Bus och andra Azure-tjänster, anger du `azure`:

![](./media/web-sites-python-create-deploy-django-app/ptvs-install-package-dialog.png)

Uppdatera requirements.txt genom att högerklicka på den virtuella miljön och välja **Generate requirements.txt** (Skapa requirements.txt).

Checka sedan in ändringarna i requirements.txt på Git-lagringsplatsen.

### <a name="deploy-to-azure"></a>Distribuera till Azure
Utlös en distribution genom att klicka på **Sync** (Synkronisera) eller **Push** (Pusha). Med synkronisering görs både en överföring och en hämtning.

![](./media/web-sites-python-create-deploy-django-app/ptvs-git-push.png)

Den första distributionen tar en stund i och med att en virtuell miljö skapas, paket installeras osv.

I Visual Studio visas inte förloppet för distributionen. Information om hur du granskar utdata finns i avsnittet [Felsökning – distribution](#troubleshooting-deployment).

Visa ändringarna genom att gå till Azure-webbadressen.

## <a name="web-app-development---windows---command-line"></a>Webbappsutveckling – Windows – kommandorad
### <a name="clone-the-repository"></a>Klona lagringsplatsen
Först klonar du lagringsplatsen med hjälp av webbadressen som tillhandahålls på Azure Portal. Lägg till Azure-lagringsplatsen som fjärransluten. Mer information finns i [Lokal Git-distribution till Azure App Service](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### <a name="create-virtual-environment"></a>Skapa en virtuell miljö
Vi ska skapa en ny virtuell miljö för utveckling (lägg inte till den på lagringsplatsen). Virtuella miljöer i Python är inte relokerbara. Därför måste alla utvecklare som arbetar med programmet skapa sina egna lokalt.

Se till att använda samma version av Python som valts för webbappen (i runtime.txt eller i bladet Programinställningar för webbappen i Azure Portal).

För Python 2.7:

    c:\python27\python.exe -m virtualenv env

För Python 3.4:

    c:\python34\python.exe -m venv env

Installera eventuella externa paket som krävs för programmet. Du kan använda requirements.txt filen i lagringsplatsens rot till att installera paket i den virtuella miljön:

    env\scripts\pip install -r requirements.txt

### <a name="create-a-superuser"></a>Skapa en användare med fullständig behörighet
I databasen som ingår i programmet finns ingen definierad användare med fullständig behörighet. Du måste skapa en användare med fullständig behörighet för att kunna använda inloggningsfunktionen i programmet och administratörsgränssnittet för Django (om du väljer att aktivera det).

Kör följande från kommandoraden i projektmappen:

    env\scripts\python manage.py createsuperuser

Följ anvisningarna för att ange användarnamn, lösenord osv.

### <a name="run-using-development-server"></a>Kör med utvecklingsservern
Du kan starta programmet under en utvecklingsserver med följande kommando:

    env\scripts\python manage.py runserver

Konsolen visar webbadressen och porten servern tar emot kommunikation från:

![](./media/web-sites-python-create-deploy-django-app/windows-run-local-django.png)

Sedan öppnar du webbadressen i webbläsaren.

![](./media/web-sites-python-create-deploy-django-app/windows-browser-django.png)

### <a name="make-changes"></a>Göra ändringar
Nu kan du experimentera med att göra ändringar i programmets källor och mallar.

När du har testat ändringarna checkar du in dem på Git-lagringsplatsen:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installera fler paket
Programmet kan ha beroenden utöver Python och Django.

Du kan installera ytterligare paket med hjälp av pip. Om du till exempel vill installera Azure SDK för Python, som ger dig tillgång till Azure Storage, Service Bus och andra Azure-tjänster, anger du:

    env\scripts\pip install azure

Se till att uppdatera requirements.txt:

    env\scripts\pip freeze > requirements.txt

Checka in ändringarna:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Distribuera till Azure
Utlös en distribution genom att push-överföra ändringarna till Azure:

    git push azure master

Nu visas utdata från skriptet för distribution, inklusive skapandet av en virtuell miljö, installation av paket och skapande av web.config.

Visa ändringarna genom att gå till Azure-webbadressen.

## <a name="web-app-development---maclinux---command-line"></a>Webbappsutveckling – Mac/Linux – kommandorad
### <a name="clone-the-repository"></a>Klona lagringsplatsen
Först klonar du lagringsplatsen med hjälp av webbadressen som tillhandahålls på Azure Portal. Lägg till Azure-lagringsplatsen som fjärransluten. Mer information finns i [Lokal Git-distribution till Azure App Service](app-service-deploy-local-git.md).

    git clone <repo-url>
    cd <repo-folder>
    git remote add azure <repo-url>

### <a name="create-virtual-environment"></a>Skapa en virtuell miljö
Vi ska skapa en ny virtuell miljö för utveckling (lägg inte till den på lagringsplatsen). Virtuella miljöer i Python är inte relokerbara. Därför måste alla utvecklare som arbetar med programmet skapa sina egna lokalt.

Se till att använda samma version av Python som valts för webbappen (i runtime.txt eller i bladet Programinställningar för webbappen i Azure Portal).

För Python 2.7:

    python -m virtualenv env

För Python 3.4:

    python -m venv env

eller

    pyvenv env

Installera eventuella externa paket som krävs för programmet. Du kan använda requirements.txt filen i lagringsplatsens rot till att installera paket i den virtuella miljön:

    env/bin/pip install -r requirements.txt

### <a name="create-a-superuser"></a>Skapa en användare med fullständig behörighet
I databasen som ingår i programmet finns ingen definierad användare med fullständig behörighet. Du måste skapa en användare med fullständig behörighet för att kunna använda inloggningsfunktionen i programmet och administratörsgränssnittet för Django (om du väljer att aktivera det).

Kör följande från kommandoraden i projektmappen:

    env/bin/python manage.py createsuperuser

Följ anvisningarna för att ange användarnamn, lösenord osv.

### <a name="run-using-development-server"></a>Kör med utvecklingsservern
Du kan starta programmet under en utvecklingsserver med följande kommando:

    env/bin/python manage.py runserver

Konsolen visar webbadressen och porten servern tar emot kommunikation från:

![](./media/web-sites-python-create-deploy-django-app/mac-run-local-django.png)

Sedan öppnar du webbadressen i webbläsaren.

![](./media/web-sites-python-create-deploy-django-app/mac-browser-django.png)

### <a name="make-changes"></a>Göra ändringar
Nu kan du experimentera med att göra ändringar i programmets källor och mallar.

När du har testat ändringarna checkar du in dem på Git-lagringsplatsen:

    git add <modified-file>
    git commit -m "<commit-comment>"

### <a name="install-more-packages"></a>Installera fler paket
Programmet kan ha beroenden utöver Python och Django.

Du kan installera ytterligare paket med hjälp av pip. Om du till exempel vill installera Azure SDK för Python, som ger dig tillgång till Azure Storage, Service Bus och andra Azure-tjänster, anger du:

    env/bin/pip install azure

Se till att uppdatera requirements.txt:

    env/bin/pip freeze > requirements.txt

Checka in ändringarna:

    git add requirements.txt
    git commit -m "Added azure package"

### <a name="deploy-to-azure"></a>Distribuera till Azure
Utlös en distribution genom att push-överföra ändringarna till Azure:

    git push azure master

Nu visas utdata från skriptet för distribution, inklusive skapandet av en virtuell miljö, installation av paket och skapande av web.config.

Visa ändringarna genom att gå till Azure-webbadressen.

## <a name="troubleshooting---package-installation"></a>Felsökning – installation av paket
[!INCLUDE [web-sites-python-troubleshooting-package-installation](../../includes/web-sites-python-troubleshooting-package-installation.md)]

## <a name="troubleshooting---virtual-environment"></a>Felsökning – virtuell miljö
[!INCLUDE [web-sites-python-troubleshooting-virtual-environment](../../includes/web-sites-python-troubleshooting-virtual-environment.md)]

## <a name="troubleshooting---static-files"></a>Felsökning – statiska filer
Med Django samlas statiska filer. Det gör att alla statiska filer kopieras från sina ursprungliga platser till en enda mapp. För det här programmet kopieras de till `/static`.

Det görs eftersom statiska filer kan komma från olika "Django-appar". De statiska filerna från administratörsgränssnittet för Django finns till exempel i en undermapp för Django-biblioteket i den virtuella miljön. Statiska filer som definieras av programmet finns i `/app/static`. När du använder flera "Django-appar" sparas statiska filer på flera platser.

När du kör programmet i felsökningsläge hanterar programmet de statiska filerna från deras ursprungliga plats.

När du kör programmet i versionsläge hanterar programmet **inte** de statiska filerna. Det är webbserverns uppgift att hantera filerna. För det här programmet hanterar IIS statiska filer från `/static`.

Insamling av statiska filer görs automatiskt som en del av distributionsskriptet och tidigare insamlade filer rensas. Det innebär att samlingen sker för varje distribution. Det gör distribution något långsammare, men ser till att filer som inte längre behövs inte är tillgängliga, vilket undanröjer potentiella säkerhetsproblem.

Om du vill hoppa över insamlingen av statiska filer för Django-programmet:

    \.skipDjango

måste du göra insamlingen manuellt på den lokala datorn:

    env\scripts\python manage.py collectstatic

Ta sedan bort `\static`-mappen från `.gitignore` och lägg till den på Git-lagringsplatsen.

## <a name="troubleshooting---settings"></a>Felsökning – inställningar
Ett flertal inställningar för programmet kan ändras i `DjangoWebProject/settings.py`.

För enklare utveckling är felsökningsläget aktiverat. En bra bieffekt är att du kan se bilder och annat statiskt innehåll vid lokal körning utan att du behöver samla in statiska filer.

Så här inaktiverar du felsökningsläget:

    DEBUG = False

När felsökningen är inaktiverad måste värdet för `ALLOWED_HOSTS` uppdateras så att det inkluderar Azure-värdnamnet. Exempel:

    ALLOWED_HOSTS = (
        'pythonapp.azurewebsites.net',
    )

så här aktiverar du valfri:

    ALLOWED_HOSTS = (
        '*',
    )

I praktiken kan du vilja göra något lite mer komplext för att hantera växlingen mellan felsöknings- och versionsläge och hämta värdnamnet.

Du kan ange miljövariabler i Azure Portal på sidan **Konfigurera** i avsnittet **Programinställningar**.  Det kan vara lämpligt när du vill ange värden som du inte vill ska visas i källorna (anslutningssträngar, lösenord osv) eller som du vill ställa in på olika sätt för Azure och den lokala datorn. I `settings.py` kan du söka i miljövariablerna med hjälp av `os.getenv`.

## <a name="using-a-database"></a>Använda en databas
Den databas som ingår i programmet är en sqlite-databas. Det är en praktisk standarddatabas för utveckling, eftersom den knappt behöver konfigureras. Databasen lagras i filen db.sqlite3 i projektmappen.

Azure tillhandahåller databastjänster som är lätta att använda i ett Django-program. I kurserna om att använda [SQL Database] och [MySQL] i ett Django-program får du anvisningar för de steg som krävs för att skapa databastjänsten, ändra databasinställningarna i `DjangoWebProject/settings.py` och de bibliotek som måste installeras.

Om du föredrar att hantera egna databasservrar kan du göra det med hjälp av virtuella Windows- och Linux-datorer som körs på Azure.

## <a name="django-admin-interface"></a>Administratörsgränssnittet för Django
När du börjar bygga modeller vill du införa data i databasen. Ett enkelt sätt att lägga till och redigera innehåll interaktivt är att använda administratörsgränssnittet för Django.

Koden för administratörsgränssnittet har kommenterats ut i programkällorna, men den är tydligt markerad så att du enkelt kan aktivera den (Sök efter "admin").

När den är aktiverad, synkroniserar du databasen, kör programmet och går till `/admin`.

## <a name="next-steps"></a>Nästa steg
Via de här länkarna hittar du mer information om Django och Python Tools för Visual Studio:

* [Django-dokumentation]
* [Dokumentationen om Python Tools för Visual Studio]

Information om hur du använder SQL Database och MySQL finns i:

* [Django och MySQL på Azure med Python Tools för Visual Studio]
* [Django och SQL Database på Azure med Python Tools för Visual Studio]

Mer information finns i [Python Developer Center](/develop/python/).

## <a name="whats-changed"></a>Nyheter
* En guide till övergången från Webbplatser till App Service finns i: [Azure App Service och dess påverkan på befintliga Azure-tjänster](http://go.microsoft.com/fwlink/?LinkId=529714)

<!--Link references-->
[Django och MySQL på Azure med Python Tools för Visual Studio]: web-sites-python-ptvs-django-mysql.md
[Django och SQL Database på Azure med Python Tools för Visual Studio]: web-sites-python-ptvs-django-sql.md
[SQL Database]: web-sites-python-ptvs-django-sql.md
[MySQL]: web-sites-python-ptvs-django-mysql.md

<!--External Link references-->
[Azure SDK för Python 2.7]: http://go.microsoft.com/fwlink/?linkid=254281
[Azure SDK för Python 3.4]: http://go.microsoft.com/fwlink/?linkid=516990
[python.org]: http://www.python.org/
[Git för Windows]: http://msysgit.github.io/
[GitHub för Windows]: https://windows.github.com/
[Python Tools för Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 för Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Visual Studio]: http://www.visualstudio.com/
[Dokumentationen om Python Tools för Visual Studio]: http://aka.ms/ptvsdocs
[Django-dokumentation]: https://www.djangoproject.com/

