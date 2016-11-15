---
title: "Django och MySQL på Azure med Python Tools 2.2 för Visual Studio"
description: "Läs om hur du använder Python Tools för Visual Studio till att skapa en Django-webbapp som lagrar data i en MySQL-databasinstans och distribuera den till webbappar i Azure App Service Web Apps."
services: app-service\web
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
ms.assetid: c60a50b5-8b5e-4818-a442-16362273dabb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: get-started-article
ms.date: 07/07/2016
ms.author: huvalo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 759441c5d64ee59f13d50eb415fbaa884dd4821a


---
# <a name="django-and-mysql-on-azure-with-python-tools-22-for-visual-studio"></a>Django och MySQL på Azure med Python Tools 2.2 för Visual Studio
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

I den här kursen ska du använda [Python Tools för Visual Studio](https://www.visualstudio.com/vs/python) för att skapa en enkel webbapp för omröstning med någon av exempelmallarna i PTVS. Du får lära dig hur du använder en MySQL-tjänst som finns i Azure, hur du konfigurerar webbappen att använda MySQL och hur du publicerar webbappen till [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

> [!NOTE]
> Informationen i den här kursen finns också i följande video:
> 
> [PTVS 2.1: Django-app med MySQL][video]
> 
> 

I [Python Developer Center] finns flera artiklar om att utveckla appar i Azure App Service Web Apps med PTVS med hjälp av webbramverken Bottle, Flask och Django med tjänsterna Azure Table Storage, MySQL och SQL Database. Den här artikeln fokuserar på App Service, men stegen är ungefär desamma för att utveckla [Azure Cloud Services].

## <a name="prerequisites"></a>Krav
* Visual Studio 2015
* [Python 2.7 32-bitars] eller [Python 3.4 32-bitars]
* [Python Tools 2.2 för Visual Studio]
* [Python Tools 2.2 för Visual Studio Samples VSIX]
* [Azure SDK Tools för VS 2015]
* Django 1.9 eller senare

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<!-- This note should not render as part of the the previous include. -->

> [!NOTE]
> Om du vill komma igång med Azure App Service innan du registrerar dig för ett Azure-konto kan du gå till [Prova App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Där kan du direkt skapa en tillfällig startwebbapp i App Service. Inget kreditkort krävs och du behöver inte göra några åtaganden.
> 
> 

## <a name="create-the-project"></a>Skapa projektet
I det här avsnittet får du skapa ett Visual Studio-projekt utifrån en exempelmall. Du får skapa en virtuell miljö och installera nödvändiga paket. Du får skapa en lokal databas med hjälp av sqlite. Sedan får du köra programmet lokalt.

1. I Visual Studio väljer du **Arkiv**, **Nytt projekt**.
2. Du hittar projektmallarna från [Python Tools 2.2 för Visual Studio Samples VSIX] under **Python**, **Exempel**. Välj **Polls Django Web Project** (Omröstningar, Django-webbprojekt) och skapa projektet genom att klicka på OK.
   
    ![Dialogrutan Nytt projekt](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)
3. Du uppmanas att installera externa paket. Välj **Install into a virtual environment** (Installera i en virtuell miljö).
   
    ![Dialogrutan Externa paket](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)
4. Välj **Python 2.7** eller **Python 3.4** som bastolk.
   
    ![Dialogrutan Add Virtual Environment (Lägg till virtuell miljö)](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)
5. Högerklickar på projektnoden i **Solution Explorer** och välj först **Python** och sedan **Django Migrate**.  Välj därefter **Django – skapa superanvändare**.
6. Öppnar Django Management Console och en sqlite-databas skapas i projektmappen. Skapa en användare genom att följa anvisningarna.
7. Bekräfta att programmet fungerar genom att trycka på `F5`.
8. Klicka på **Log in** (Logga in) i navigeringsfältet längst upp.
   
    ![Navigeringsfältet i Django](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)
9. Ange autentiseringsuppgifterna för användaren du skapade när du synkroniserade databasen.
   
    ![Inloggningsformulär](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)
10. Klicka på **Create Sample Polls** (Skapa exempelomröstningar).
    
     ![Create Sample Polls (Skapa exempelomröstningar)](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)
11. Klicka på en omröstning och rösta.
    
     ![Rösta i exempelomröstningar](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## <a name="create-a-mysql-database"></a>Skapa en MySQL-databas
Som databas skapar du en ClearDB MySQL-baserad databas i Azure.

Du kan också välja att skapa din egen virtuella dator som körs i Azure och sedan installera och administrera MySQL själv.

Du kan skapa en databas med en kostnadsfri plan genom att följa dessa steg.

1. Logga in på [Azure Portal].
2. Överst i navigeringsfönstret klickar du på **Nytt**, **Data + Storage** och sedan på **MySQL-databas**.
3. Konfigurera den nya MySQL-databasen genom att skapa en ny resursgrupp och välja en lämplig plats för den.
4. När MySQL-databasen har skapats klickar du på **Egenskaper** i databasbladet.
5. Använd kopieringsknappen till att ange värdet för **CONNECTION STRING** (anslutningssträng) i Urklipp.

## <a name="configure-the-project"></a>Konfigurera projektet
I det här avsnittet får du konfigurera webbappen till att använda MySQL-databasen du nyss skapade. Du måste också installera ytterligare Python-paket som krävs för att använda MySQL-databaser med Django. Sedan kör du webbappen lokalt.

1. I Visual Studio öppnar du **settings.py** i mappen *Projektnamn*. Klistra in anslutningssträngen i redigeringsprogrammet. Anslutningssträngen har det här formatet:
   
        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>
   
    Ändra standarddatabasens **ENGINE** (motor) så att MySQL används och ange värden för **NAME**, **USER**, **PASSWORD** och **HOST** (Namn, användare, lösenord och värd) från **CONNECTIONSTRING** (Anslutningssträngen).
   
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }
2. I Solution Explorer under **Python Environments** (Python-miljöer), högerklickar du på den virtuella miljön och väljer **Install Python Package** (Installera Python-paket).
3. Installera `mysqlclient`-paketet med **pip**.
   
    ![Dialogrutan Installera paket](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)
4. Högerklickar på projektnoden i **Solution Explorer** och välj först **Python** och sedan **Django Migrate**.  Välj därefter **Django – skapa superanvändare**.
   
    När du gör det skapas tabellerna för MySQL-databasen du skapade i det föregående steget. Följ anvisningarna för att skapa en användare som inte behöver matcha användaren i sqlite-databasen som skapades i den första delen av den här artikeln.
5. Kör programmet med `F5`. Omröstningar som skapas med **Create Sample Polls** (Skapa exempelomröstningar) och de data som skickats vid röstning serialiseras i MySQL-databasen.

## <a name="publish-the-web-app-to-azure-app-service"></a>Publicera webbappen i Azure App Service
Med Azure .NET SDK kan du enkelt distribuera webbappen till Azure App Service.

1. I **Solution Explorer** högerklickar du på projektnoden och väljer **Publicera**.
   
    ![Dialogrutan Publicera webbplats](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)
2. Klicka på **Microsoft Azure App Service**.
3. Skapa en ny webbapp genom att klicka på **Ny**.
4. Fyll i följande fält och klicka på **Skapa**:
   
   * **Webbappens namn**
   * **App Service-plan**
   * **Resursgrupp**
   * **Region**
   * Lämna **Databasserver** inställd på **Ingen databas**
5. Godkänn alla standardvärden och klicka på **Publicera**.
6. Den publicerade webbappen öppnas automatiskt i webbläsaren. Du bör kunna se att webbappen fungerar som förväntat med hjälp av **MySQL**-databasen på Azure.
   
    ![Webbläsare](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)
   
    Grattis! Du har publicerat din MySQL-baserade webbapp i Azure.

## <a name="next-steps"></a>Nästa steg
Via de här länkarna hittar du mer information om Python Tools för Visual Studio, Django och MySQL:

* [Dokumentation för Python Tools för Visual Studio]
  * [Webbprojekt]
  * [Molntjänstprojekt]
  * [Fjärrfelsökning i Microsoft Azure]
* [Django-dokumentation]
* [MySQL]

Mer information finns i [Python Developer Center](/develop/python/).

<!--Link references-->

[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services/cloud-services-python-ptvs.md

<!--External Link references-->

[Azure-portalen]: https://portal.azure.com
[Python Tools för Visual Studio]: https://www.visualstudio.com/vs/python/
[Python Tools 2.2 för Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 för Visual Studio Samples VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK Tools för VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bitars]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bitars]: http://go.microsoft.com/fwlink/?LinkId=517191
[Dokumentation för Python Tools för Visual Studio]: http://aka.ms/ptvsdocs
[Fjärrfelsökning i Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Webbprojekt]: http://go.microsoft.com/fwlink/?LinkId=624027
[Molntjänstprojekt]: http://go.microsoft.com/fwlink/?LinkId=624028
[Django-dokumentation]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
[video]: http://youtu.be/oKCApIrS0Lo



<!--HONumber=Nov16_HO2-->


