---
title: "Felsökning av appar i en lokal dockerbehållare | Microsoft Docs"
description: "Lär dig hur du ändrar en app som körs i en lokal dockerbehållare, uppdatera behållaren via Redigera och uppdatera och ange brytpunkter-felsökning"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: fcd58736d8915a61683a416fb9bf3892ba7b7bd8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Felsök appar i en lokal Docker-behållare
## <a name="overview"></a>Översikt
Visual Studio Tools för Docker ger ett konsekvent sätt att utveckla i och validera ditt program lokalt i en Linux-dockerbehållare.
Du behöver inte starta om behållaren varje gång du gör en kod ändra.
Den här artikeln visar hur funktionen ”Redigera och uppdatera” för att starta en ASP.NET Core webbprogram i en lokal dockerbehållare, gör nödvändiga ändringar och sedan uppdatera webbläsaren om du vill se ändringarna.
Den här artikeln beskriver också hur du ställer in brytpunkter för felsökning.

> [!NOTE]
> Stöd för Windows-behållare kommer i en framtida version
>
>

## <a name="prerequisites"></a>Krav
Följande verktyg måste vara installerad.

* [Senaste version av Visual Studio](https://www.visualstudio.com/downloads/)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Om du vill köra Docker behållare lokalt, behöver du en lokal docker-klient.
Du kan använda den [Docker verktygslådan](https://www.docker.com/products/docker-toolbox), vilket kräver Hyper-V kan inaktiveras eller du kan använda [Docker för Windows](https://www.docker.com/get-docker), som använder Hyper-V och kräver att Windows 10.

Om du använder Docker verktygslådan, måste du [konfigurerar Docker-klient](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Skapa en webbapp
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Lägga till Docker-stöd
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Redigera koden och uppdatera
För att snabbt iterera ändringar, kan du starta appen i en behållare och fortsätta att göra ändringar, visa dem precis som med IIS Express.

1. Konfigurera lösningen `Debug` och tryck på  **&lt;CTRL + F5 >** att skapa en docker-avbildning och köra det lokalt.

    När avbildningen behållaren har skapats och körs i en dockerbehållare, startas Visual Studio webbappen i din standardwebbläsare.
    Om du använder Microsoft Edge-webbläsaren eller på annat sätt har fel finns [felsökning](vs-azure-tools-docker-troubleshooting-docker-errors.md) avsnitt.
2. Gå till sidan om, vilket är där vi göra våra ändringar.
3. Gå tillbaka till Visual Studio och öppna `Views\Home\About.cshtml`.
4. Lägg till följande HTML-innehåll i slutet av filen och spara ändringarna.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Visa utdatafönstret när .NET-versionen har slutförts och du ser dessa rader kan gå tillbaka till din webbläsare och uppdatera sidan om.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Ändringarna har tillämpats!

## <a name="4-debug-with-breakpoints"></a>4. Felsöka med brytpunkter
Ofta behöver ändringar ytterligare kontroll, utnyttja funktionerna för felsökning av Visual Studio.

1. Gå tillbaka till Visual Studio och öppna`Controllers\HomeController.cs`
2. Ersätt innehållet i metoden About() med följande:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Ange en brytpunkt till vänster om den `string message`... rad.
4. Träffa  **&lt;F5 >** att starta felsökningen.
5. Gå till sidan om att träffa din brytpunkt.
6. Växla till Visual Studio för att visa brytpunkten och kontrollera värdet för meddelandet.

   ![][2]

## <a name="summary"></a>Sammanfattning
Med [Visual Studio 2015 Tools för Docker](https://aka.ms/DockerToolsForVS), kan du arbeta lokalt, med produktion mer att utveckla i en dockerbehållare produktivitet.

## <a name="troubleshooting"></a>Felsökning
[Felsökning av Visual Studio Docker-utveckling](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Mer information om Docker med Visual Studio och Windows Azure
* [Docker Tools för Visual Studio](http://aka.ms/dockertoolsforvs) -utveckla .NET Core koden i en behållare
* [Docker-verktyg för Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) – skapa och distribuera docker-behållare
* [Docker-verktyg för Visual Studio Code](http://aka.ms/dockertoolsforvscode) -språk services för att redigera docker-filer, med mer e2e scenarier kommer
* [Information om Windows behållaren](http://aka.ms/containers)-information för Windows Server och Nano Server
* [Azure Container Service](https://azure.microsoft.com/services/container-service/) - [Azure Container Service-innehåll](http://aka.ms/AzureContainerService)
* Fler exempel i arbetet med Docker finns [arbeta med Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) från den [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Anslut [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Fler snabbstartsguider från HealthClinic.biz-demonstrationen finns i [Snabbstartsguider för Azure Developer Tools](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Olika Docker-verktyg
[Vissa bra docker-verktyg (Steve Lasker blogg)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Bra artiklar
[Introduktion till Mikrotjänster från NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentationer
* [Steve Lasker: VS Live nu är det jul 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduktion till ASP.NET Core @ build 2016 - där du vid Demo](https://channel9.msdn.com/Events/Build/2016/B810)
* [Utveckla .NET appar i behållare, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
