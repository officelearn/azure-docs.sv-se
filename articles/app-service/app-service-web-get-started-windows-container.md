---
title: 'Snabbstart: Windows-behållare (förhandsversion)'
description: Distribuera din första anpassade Windows-behållare till Azure App Service. Dra nytta av containerisering, anpassa Windows-behållaren som du vill ha den.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: mvc, seodec18
ms.openlocfilehash: cd6b78e5fd824cc013cc946d23677237923f485e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047105"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Köra en anpassad Windows-container i Azure (förhandsversion)

[Azure App Service](overview.md) har fördefinierade programstackar i Windows som ASP.NET eller Node.js, som körs i IIS. Den förkonfigurerade Windows-miljön låser operativsystemet från administrativ åtkomst, programvaruinstallationer, ändringar i den globala sammansättningscachen och så vidare. Mer information finns i [Operativsystemsfunktioner på Azure App Service](operating-system-functionality.md). Om programmet kräver mer åtkomst än den förkonfigurerade miljön tillåter kan du istället distribuera en anpassad Windows-container.

Den här snabbstarten visar hur du distribuerar en ASP.NET app, i en Windows-avbildning, till [Docker Hub](https://hub.docker.com/) från Visual Studio. Du kör appen i en anpassad behållare i Azure App Service.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- <a href="https://hub.docker.com/" target="_blank">Registrera dig för ett Docker Hub-konto</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installera Docker för Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Växla Docker för att köra Windows-containrar</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installera Visual Studio 2019</a> med **ASP.NET- och webbutvecklings-** och **Azure-utvecklingsarbetsbelastningar.** Om du redan har installerat Visual Studio 2019:

    - Installera de senaste uppdateringarna i Visual Studio genom att välja **Hjälp** > **sök efter uppdateringar**.
    - Lägg till arbetsbelastningarna i Visual Studio genom att välja **Verktyg** > **hämta verktyg och funktioner**.

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa en ASP.NET webbapp genom att följa dessa steg:

1. Öppna Visual Studio och välj sedan **Skapa ett nytt projekt**.

1. Leta reda på och välja **ASP.NET webbprogram (.NET Framework)** för C#i **Skapa ett nytt projekt**och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du programmet _myFirstAzureWebApp_och väljer sedan **Skapa**.

   ![Konfigurera webbappprojektet](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Du kan distribuera alla typer av ASP.NET-webbappar till Azure. För den här snabbstarten väljer du **MVC-mallen.**

1. Välj **Docker-stöd**och kontrollera att autentiseringen är inställd på **Ingen autentisering**. Välj **Skapa**.

   ![Skapa ASP.NET webbprogram](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Om filen _Dockerfile_ inte öppnas automatiskt öppnar du den från **Solution Explorer**.

1. Du behöver en [överordnad bild som stöds](#use-a-different-parent-image). Ändra den överordnade avbildningen genom att ersätta raden `FROM` med följande kod och spara filen:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. På Visual Studio-menyn väljer du **Felsökning** > **start utan felsökning** för att köra webbappen lokalt.

   ![Kör appen lokalt](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicera till Docker Hub

1. Högerklicka på **projektet myFirstAzureWebApp** i **Solution Explorer**och välj **Publicera**.

1. Välj **Apptjänst** och välj sedan **Publicera**.

1. Välj **Behållarregister** och **Docker Hub**i Välj ett **publiceringsmål**och klicka sedan på **Publicera**.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Ange dina Docker Hub-kontouppgifter och välj **Spara**.

   Vänta tills distributionen har slutförts. På sidan **Publicera** visas nu det databasnamn som ska användas senare.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Kopiera namnet på lagringsplatsen för senare bruk.

## <a name="create-a-windows-container-app"></a>Skapa en Windows-containerapp

1. Logga in på [Azure-portalen]( https://portal.azure.com).

1. Välj **Skapa en resurs** längst upp till vänster i Azure Portal.

1. Sök efter **behållare**i sökrutan ovanför listan över Azure Marketplace-resurser och välj **Skapa**.

1. Välj din prenumeration och en **resursgrupp**i **Web App Create**. Du kan skapa en ny resursgrupp om det behövs.

1. Ange ett appnamn, till exempel *win-container-demo* och välj **Windows** för **operativsystem**. Välj **Nästa: Docker** för att fortsätta.

   ![Skapa en webbapp för behållare](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. För **Bildkälla**väljer du **Docker Hub** och för **Bild och tagg**anger du databasnamnet som du kopierade i Publicera [till Docker Hub](#publish-to-docker-hub).

   ![Konfigurera dig som en webbapp för behållare](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Om du har en anpassad avbildning någon annanstans för ditt webbprogram, till exempel i [Azure Container Registry](/azure/container-registry/) eller på en annan privat lagringsplats, kan du konfigurera den här.

1. Välj **Granska och skapa** och sedan **skapa** och vänta på att Azure ska skapa de resurser som krävs.

## <a name="browse-to-the-container-app"></a>Bläddra till containerappen

När Azure-åtgärden är klar visas ett meddelande.

![Distributionen lyckades](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klicka på **Gå till resurs**.

1. I översikten över den här resursen följer du länken **bredvid URL.**

En ny webbläsarsida öppnas på följande sida:

![Windows Container App Startar](media/app-service-web-get-started-windows-container/app-starting.png)

Vänta några minuter och försök igen, tills du kommer till standardvälkomstsidan för ASP.NET:

![Windows Container App körs](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Grattis!** Du kör din första anpassade Windows-container i Azure App Service.

## <a name="see-container-start-up-logs"></a>Se containerns startloggar

Det kan ta lite tid för Windows-containern att läsas in. Om du vill se förloppet navigerar du till följande WEBBADRESS genom att ersätta * \<app_name>* med namnet på appen.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

De strömmade loggarna ser ut så här:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Uppdatera lokalt och omdistribuera

1. Öppna **Vyer** > **Home** > **Index.cshtml**i Solution Studio i Visual **Studio**.

1. Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela elementet med följande kod:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Om du vill distribuera om till Azure högerklickar du på **projektet myFirstAzureWebApp** i **Solution Explorer** och väljer **Publicera**.

1. På publiceringssidan väljer du **Publicera** och väntar tills publiceringen ska slutföras.

1. Om du vill uppmana App Service att hämta den nya avbildningen från Docker Hub startar du om appen. Klicka på **Starta om** > **ja**på appsidan i portalen .

   ![Starta om webbapp i Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Bläddra till container](#browse-to-the-container-app) igen. När du uppdaterar webbsidan ska appen återgå till sidan ”Startar” först och sedan visa den uppdaterade igen efter några minuter.

![Uppdaterad webbapp i Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Använd en annan överordnad avbildning

Du kan använda en annan anpassad Docker-avbildning för att köra appen. Du måste dock välja rätt [överordnad bild (basavbildning)](https://docs.docker.com/develop/develop-images/baseimages/) för det ramverk du vill ha:

- Om du vill distribuera .NET Framework-appar använder du en överordnad avbildning baserad på [LTSC-versionen (Long-Term Service Channel) för](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) Windows Server Core 2019. 
- Om du vill distribuera .NET Core-appar använder du en överordnad avbildning baserad på [SAC-versionen (Semi-Annual Servicing Channel).](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 

Det tar lite tid att ladda ned en överordnad avbildning när appen startas. Men du kan minska starttiden genom att använda någon av följande överordnade avbildningar som redan har cachelagrats i Azure App Service:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [mcr.microsoft.com/windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/)mcr.microsoft.com/windows/nanoserver:1809 - den här avbildningen är basbehållaren som används i Microsoft [ASP.NET Microsoft](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Windows Nano Server-avbildningar.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Windows-container i Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
