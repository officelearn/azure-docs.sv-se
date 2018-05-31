---
title: Vad är Azure SignalR? | Microsoft Docs
description: Översikt över Azure SignalR Service.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868117"
---
# <a name="what-is-azure-signalr-service"></a>Vad är Azure SignalR Service?

Tjänsten Microsoft Azure SignalR är för närvarande tillgänglig som en [allmänt tillgänglig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure SignalR Service är en Azure-tjänst baserad på [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction). ASP.NET Core SignalR är ett [bibliotek med öppen källkod](https://github.com/aspnet/signalr) som förenklar arbetet med att lägga till webbfunktioner i realtid för program via HTTP. Denna realtidsfunktion medför att webbservern kan skicka innehållsuppdateringar till anslutna klienter. Därmed uppdateras klienterna utan att man behöver avsöka servern eller skicka nya HTTP-begäranden om uppdateringar.

Den här artikeln ger en översikt över Azure SignalR Service. Om du vill komma igång rekommenderar vi att du börjar med [snabbstarten för ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>Vad används SignalR Service för? 

Det finns många programtyper som kräver uppdatering av innehållet i realtid. Följande exempel på programtyper är goda kandidater för användning av Azure SignalR Service:

* Appar som kräver högfrekvent uppdatering från servern. Exempel är spel, sociala nätverk, röstning, auktioner, kartor och GPS-appar.
* Instrumentpaneler och övervakningsappar. Exempel är företagsinstrumentpaneler, omedelbara försäljningsuppdateringar och reseaviseringar.
* Samarbetsappar. Whiteboard-appar och programvaror för teammöten är exempel på samarbetsappar.
* Appar som kräver aviseringar. Sociala nätverk, e-post, chattar, spel, reseaviseringar och många andra appar använder sig av aviseringar.

Internt är SignalR en abstrakt samling tekniker som används för att skapa webbappar av realtidstyp. [WebSockets](https://wikipedia.org/wiki/WebSocket) är den optimala transportmetoden, men andra metoder som [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) och lång avsökning används när andra alternativ inte är tillgängliga. SignalR identifierar och initierar automatiskt lämplig transportmetod baserat på de funktioner som stöds på servern och klienten.

## <a name="developing-signalr-apps"></a>Utveckla SignalR-appar

Det finns för närvarande två versioner av SignalR som du kan använda för dina webbappar: SignalR för ASP.NET och ASP.NET Core SignalR, som är den senaste versionen. Azure SignalR Service är en Azure-hanterad tjänst som bygger på ASP.NET Core SignalR. 

ASP.NET Core SignalR är en omarbetning av den föregående versionen. Därför är ASP.NET Core SignalR inte bakåtkompatibel med den tidigare SignalR-versionen. Både API:er och beteenden skiljer sig åt. ASP.NET Core SignalR SDK är .NET-standard så du kan fortfarande använda den med .NET Framework. Du måste dock använda de nya API:erna i stället för de gamla. Om du använder SignalR och vill flytta till ASP.NET Core SignalR eller Azure SignalR Service måste du ändra din kod för att hantera API-skillnaderna.

Hos Azure SignalR Service finns serversidans komponent av ASP.NET Core SignalR i Azure. Men eftersom tekniken bygger på ASP.NET Core, har du möjlighet att köra din webbapp på flera plattformar (Windows, Linux och MacOS) om hanteringen sker med [Azure App Service](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) eller [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). Du kan också själv stå för anslutningen till Internet i en egen process.

Om din app ska ha stöd för de senaste funktionerna för innehållsuppdatering av webbklienter i realtid, körning på flera plattformar (Azure, Windows, Linux och MacOS) och hantering i olika miljöer kan Azure SignalR Service vara det bästa valet.


## <a name="why-not-deploy-signalr-myself"></a>Varför inte distribuera SignalR på egen hand?

Det är fortfarande möjligt att distribuera en Azure-webbapp på egen hand med stöd för ASP.NET Core SignalR som backend-komponent för din övergripande webbapp.

Ett av de viktigaste skälen till att använda Azure SignalR Service är enkelheten. Med Azure SignalR Service behöver du inte hantera problem med prestanda, skalbarhet och tillgänglighet. Sådana problem hanteras åt dig med ett servicenivåavtal på 99,9 %.

WebSockets är också normalt den teknik som föredras för stöd för innehållsuppdateringar i realtid. Belastningsutjämning av ett stort antal beständiga WebSocket-anslutningar blir dock ett komplicerat problemet att lösa vid skalning. Vanliga lösningar använder sig av DNS-belastningsutjämning, maskinvarubaserade belastningsutjämnare och programvarubaserad belastningsutjämning. Azure SignalR Service hanterar problemen åt dig.

En annan orsak kan vara att du inte har något behov av att vara värd för webbappen. Logiken för din webbappp kan använda sig av [serverlös databehandling](https://azure.microsoft.com/overview/serverless-computing/). Som exempel kanske din kod bara hanteras och körs på begäran med [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)-utlösare. Det här scenariot kan vara komplicerat eftersom koden endast körs på begäran och inte upprätthåller längre anslutningar med klienter. Azure SignalR Service kan hantera den detta eftersom tjänsten redan hanterar anslutningar för dig.

## <a name="how-does-it-scale"></a>Hur går skalningen till?

Det är vanligt att skala SignalR med SQL Server, Azure Service Bus eller Redis-Cache. Azure SignalR Service hanterar skalningsarbetet åt dig. Prestandan och kostnaden kan jämföras med dessa metoder utan komplexiteten som är förknippad med hanteringen av dessa övriga tjänster. Allt du behöver göra är att uppdatera antal enheter för din tjänst. Varje tjänsteenhet stöder upp till 1 000 klientanslutningar.

## <a name="next-steps"></a>Nästa steg
* [Snabbstart: Skapa ett chattrum med Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

