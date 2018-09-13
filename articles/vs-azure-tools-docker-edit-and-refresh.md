---
title: Felsök appar i en lokal Docker-behållare | Microsoft Docs
description: Lär dig hur du ändrar en app som körs i en lokal dockerbehållare, uppdatera behållare via Redigera och uppdatera och ange brytpunkter-felsökning
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
ms.date: 09/11/2018
ms.author: ghogen
ms.openlocfilehash: 0f3f323cb4486c06f6f18de4c695efaf8dce4d99
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715728"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Felsök appar i en lokal Docker-container
## <a name="overview"></a>Översikt
Visual Studio 2017 ger ett konsekvent sätt att utveckla i en Docker-behållare och validera ditt program lokalt.
Du behöver starta om behållaren varje gång du gör en kod ändra.
Den här artikeln visar hur du använder funktionen ”Redigera och uppdatera” för att starta en ASP.NET Core-webbapp i en lokal dockerbehållare, gör nödvändiga ändringar och uppdatera sedan webbläsaren så visas dessa ändringar.
Den här artikeln visar också hur du kan ange brytpunkter för felsökning.

## <a name="prerequisites"></a>Förutsättningar
Följande verktyg installeras.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) med arbetsbelastningen webbutveckling installerad.

Om du vill köra Docker-behållare lokalt, måste en lokal docker-klienten.
Du kan använda den [Docker verktygslådan](https://www.docker.com/products/docker-toolbox), vilket kräver att Hyper-V kan inaktiveras eller du kan använda [Docker för Windows](https://www.docker.com/get-docker), som använder Hyper-V och kräver Windows 10.

Om du använder Docker verktygslådan, du behöver [konfigurera Docker-klienten](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Skapa en webbapp
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2. Redigera din kod och uppdatering
För att snabbt iterera ändringar, kan du starta appen i en behållare och fortsätta att göra ändringar, visar dem precis som med IIS Express.

1. Konfigurera lösningen `Debug` och tryck på  **&lt;CTRL + F5 >** och skapa en docker-avbildning som du kan köra det lokalt.

    När behållaravbildningen har skapats och körs i en Docker-behållare, startar Visual Studio Web-app i din standardwebbläsare.
    Om du använder Microsoft Edge-webbläsaren eller på annat sätt har fel finns i [felsökning](vs-azure-tools-docker-troubleshooting-docker-errors.md) avsnittet.
2. Gå till sidan om, vilket är där vi ska göra våra ändringar.
3. Gå tillbaka till Visual Studio och öppna `Views\Home\About.cshtml`.
4. Lägg till följande HTML-innehåll i slutet av filen och spara ändringarna.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Visa utdatafönstret när .NET-version är klar och du ser dessa rader kan gå tillbaka till webbläsaren och uppdatera sidan om.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```

6. Ändringarna har tillämpats!

## <a name="3-debug-with-breakpoints"></a>3. Felsöka med brytpunkter
Ofta behöver ändringar ytterligare kontroll, att utnyttja felsökningsfunktioner i Visual Studio.

1. Gå tillbaka till Visual Studio och öppna `About.cshtml.cs`
2. Ersätt innehållet i metoden OnGet() med följande:

   ```cs
       Message = "Your application description page from within a Container";
   ```

3. Konfigurera en brytpunkt till vänster om raden kod.
4. Tryck på  **&lt;F5 >** att starta felsökningen.
5. Gå till sidan om att kommer din brytpunkten.
6. Växla till Visual Studio för att visa brytpunkten och kontrollera värdet för meddelande.

   ![][2]

## <a name="summary"></a>Sammanfattning
Du kan hämta produktivitet arbeta lokalt, med produktion mer i att utveckla i en dockerbehållare med Docker-stöd i Visual Studio 2017.

## <a name="troubleshooting"></a>Felsökning
[Felsökning av Visual Studio Docker-utveckling](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Mer om Docker med Visual Studio, Windows och Azure
* [Behållare-utveckling med Visual Studio](/visualstudio/containers) – en behållare utveckling-hubbsida
* [Docker-integrering för Azure Pipelines](http://aka.ms/dockertoolsforvsts) – skapa och distribuera docker-behållare
* [Docker-verktyg för Visual Studio Code](http://aka.ms/dockertoolsforvscode) -språktjänster för redigering av dockerfiler med flera e2e scenarier kommer
* [Windows-behållare Information](http://aka.ms/containers)-information om Windows Server och Nano Server
* [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) - [dokumentation om Azure Kubernetes Service](/azure/aks)

## <a name="various-docker-tools"></a>Olika Docker-verktyg
[Några bra docker-verktyg (Steve Lasker blogg)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Bra artiklar
[Introduktion till Mikrotjänster från NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Presentationer
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduktion till ASP.NET Core @ build 2016 - där du på Demo](https://channel9.msdn.com/Events/Build/2016/B810)
* [Utveckla .NET-appar i behållare, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
