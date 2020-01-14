---
title: 'Snabb start: Windows-behållare (förhands granskning)'
description: Distribuera din första anpassade Windows-behållare till Azure App Service. Dra nytta av skapa behållare, anpassa Windows-behållaren på det sätt som du vill.
ms.topic: quickstart
ms.date: 08/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7901498772b8e746fb2c87a5237f06ab279e3b64
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922298"
---
# <a name="run-a-custom-windows-container-in-azure-preview"></a>Köra en anpassad Windows-container i Azure (förhandsversion)

[Azure App Service](overview.md) har fördefinierade programstackar i Windows som ASP.NET eller Node.js, som körs i IIS. Den förkonfigurerade Windows-miljön låser ned operativ systemet från administrativ åtkomst, program varu installationer, ändringar i den globala sammansättningscachen och så vidare. Mer information finns i [operativ systemets funktioner på Azure App Service](operating-system-functionality.md). Om programmet kräver mer åtkomst än den förkonfigurerade miljön tillåter kan du istället distribuera en anpassad Windows-container.

Den här snabb starten visar hur du distribuerar en ASP.NET-app i en Windows-avbildning till [Docker Hub](https://hub.docker.com/) från Visual Studio. Du kör appen i en anpassad behållare i Azure App Service.

## <a name="prerequisites"></a>Krav

För att slutföra den här kursen behöver du:

- <a href="https://hub.docker.com/" target="_blank">Registrera dig för ett Docker Hub-konto</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Installera Docker för Windows</a>.
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Växla Docker för att köra Windows-containrar</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Installera Visual Studio 2019</a> med arbets belastningarna **ASP.net och webb utveckling** och **Azure Development** . Om du redan har installerat Visual Studio 2019:

    - Installera de senaste uppdateringarna i Visual Studio genom att välja **hjälp** > **söka efter uppdateringar**.
    - Lägg till arbets belastningarna i Visual Studio genom att välja **verktyg** > **Hämta verktyg och funktioner**.

## <a name="create-an-aspnet-web-app"></a>Skapa en ASP.NET-webbapp

Skapa en ASP.NET-webbapp genom att följa dessa steg:

1. Öppna Visual Studio och välj sedan **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt**, leta upp och välj **ASP.NET-webbprogram (.NET Framework)** för C#och välj sedan **Nästa**.

1. I **Konfigurera ditt nya projekt**namnger du programmet _myFirstAzureWebApp_och väljer sedan **skapa**.

   ![Konfigurera ditt webbapp](./media/app-service-web-get-started-windows-container/configure-web-app-project-container.png)

1. Du kan distribuera alla typer av ASP.NET-webbappar till Azure. I den här snabb starten väljer du **MVC** -mallen.

1. Välj **Docker-support**och se till att autentiseringen är inställd på **Ingen autentisering**. Välj **Skapa**.

   ![Skapa ASP.NET-webbprogram](./media/app-service-web-get-started-windows-container/select-mvc-template-for-container.png)

1. Om filen _Dockerfile_ inte öppnas automatiskt öppnar du den från **Solution Explorer**.

1. Du behöver en [överordnad avbildning som stöds](#use-a-different-parent-image). Ändra den överordnade avbildningen genom att ersätta raden `FROM` med följande kod och spara filen:

   ```Dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. Från Visual Studio-menyn väljer du **felsök** > **starta utan fel sökning** för att köra webbappen lokalt.

   ![Kör appen lokalt](./media/app-service-web-get-started-windows-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicera till Docker Hub

1. I **Solution Explorer**högerklickar du på projektet **MyFirstAzureWebApp** och väljer **publicera**.

1. Välj **App Service** och välj sedan **publicera**.

1. I Välj ett **publicerings mål**väljer du **container Registry** och **Docker Hub**och klickar sedan på **publicera**.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-windows-container/publish-to-docker-vs2019.png)

1. Ange dina autentiseringsuppgifter för Docker Hub-kontot och välj **Spara**.

   Vänta tills distributionen har slutförts. Sidan **publicera** visar nu det databas namn som ska användas senare.

   ![Publicera från projektöversiktssidan](./media/app-service-web-get-started-windows-container/published-docker-repository-vs2019.png)

1. Kopiera namnet på lagringsplatsen för senare bruk.

## <a name="create-a-windows-container-app"></a>Skapa en Windows-containerapp

1. Logga in på [Azure-portalen]( https://portal.azure.com).

1. Välj **Skapa en resurs** längst upp till vänster i Azure-portalen.

1. Sök efter **Web App for containers**i sökrutan ovanför Azure Marketplace-resurser och välj **skapa**.

1. I **webbapp skapar**väljer du din prenumeration och en **resurs grupp**. Du kan skapa en ny resurs grupp om det behövs.

1. Ange ett namn på appen, till exempel *Win-container-demo* och välj **Windows** för **operativ system**. Välj **Nästa: Docker** för att fortsätta.

   ![Skapa en Web App for Containers](media/app-service-web-get-started-windows-container/create-web-app-continer.png)

1. För **avbildnings källa**väljer du **Docker Hub** och för **bild och tagg**anger du det databas namn som du kopierade i [Publicera till Docker Hub](#publish-to-docker-hub).

   ![Konfigurera du är Web App for Containers](media/app-service-web-get-started-windows-container/configure-web-app-continer.png)

    Om du har en anpassad avbildning någon annanstans för ditt webbprogram, till exempel i [Azure Container Registry](/azure/container-registry/) eller på en annan privat lagringsplats, kan du konfigurera den här.

1. Välj **Granska och skapa** och **skapa** och vänta sedan på att Azure ska skapa de nödvändiga resurserna.

## <a name="browse-to-the-container-app"></a>Bläddra till containerappen

När Azure-åtgärden är klar visas ett meddelande.

![Distributionen är klar](media/app-service-web-get-started-windows-container/portal-create-finished.png)

1. Klicka på **Gå till resurs**.

1. I översikten över den här resursen följer du länken bredvid **URL**.

En ny webb sida öppnas på följande sida:

![Windows container app startar](media/app-service-web-get-started-windows-container/app-starting.png)

Vänta några minuter och försök igen, tills du kommer till standardvälkomstsidan för ASP.NET:

![Windows container App körs](media/app-service-web-get-started-windows-container/app-running-vs.png)

**Grattis!** Du kör din första anpassade Windows-container i Azure App Service.

## <a name="see-container-start-up-logs"></a>Se containerns startloggar

Det kan ta lite tid för Windows-containern att läsas in. Om du vill se förloppet går du till följande URL genom att ersätta *\<app_name>* med namnet på din app.
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

1. Öppna **vyer** > **Start** > **index. cshtml**i **Solution Explorer**i Visual Studio.

1. Leta reda på HTML-taggen `<div class="jumbotron">` längst upp på sidan och ersätt hela elementet med följande kod:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we’ve built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Om du vill distribuera om till Azure högerklickar du på projektet **myFirstAzureWebApp** i **Solution Explorer** och väljer **publicera**.

1. På publiceringssidan väljer du **Publicera** och väntar tills publiceringen ska slutföras.

1. Om du vill uppmana App Service att hämta den nya avbildningen från Docker Hub startar du om appen. På appsidan i portalen klickar du på **Starta om** > **Ja**.

   ![Starta om webbapp i Azure](./media/app-service-web-get-started-windows-container/portal-restart-app.png)

[Bläddra till container](#browse-to-the-container-app) igen. När du uppdaterar webbsidan ska appen återgå till sidan ”Startar” först och sedan visa den uppdaterade igen efter några minuter.

![Uppdaterad webbapp i Azure](./media/app-service-web-get-started-windows-container/azure-web-app-updated.png)

## <a name="use-a-different-parent-image"></a>Använd en annan överordnad avbildning

Du kan använda en annan anpassad Docker-avbildning för att köra din app. Du måste dock välja den högra [överordnade avbildningen (bas avbildningen)](https://docs.docker.com/develop/develop-images/baseimages/) för det ramverk som du vill använda:

- Om du vill distribuera .NET Framework appar använder du en överordnad avbildning som baseras på Windows Server Core 2019 [långsiktig LTSC-version (långsiktig service Channel)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) . 
- Om du vill distribuera .NET Core-appar använder du en överordnad avbildning som baseras på Windows Server nano 1809-versionen från [halvårs kanal (SAC)](https://docs.microsoft.com/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) . 

Det tar lite tid att ladda ned en överordnad avbildning när appen startas. Men du kan minska starttiden genom att använda någon av följande överordnade avbildningar som redan har cachelagrats i Azure App Service:

- [mcr.microsoft.com/dotnet/framework/aspnet](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/):4.7.2-windowsservercore-ltsc2019
- [MCR.Microsoft.com/Windows/nanoserver](https://hub.docker.com/_/microsoft-windows-nanoserver/): 1809 – den här avbildningen är bas behållaren som används i Microsoft [ASP.net Core](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/) Nano Server-avbildningar från Microsoft Windows.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Windows-container i Azure](app-service-web-tutorial-windows-containers-custom-fonts.md)
