---
title: Bygg realtidsapp - Azure-funktioner & Azure SignalR-tjänst
description: Lär dig hur du utvecklar serverlösa webbprogram i realtid med Azure SignalR Service genom att följa exempel.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: cbb1fcf320a78f11045bf9627ffcc438af3e388a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157617"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Skapa appar i realtid med Azure-funktioner och Azure SignalR-tjänst

Eftersom Azure SignalR Service och Azure Functions båda är fullständigt hanterade, mycket skalbara tjänster som gör att du kan fokusera på att bygga program i stället för att hantera infrastrukturen är det vanligt att använda de två tjänsterna tillsammans för att tillhandahålla realtidskommunikation i en [serverlös](https://azure.microsoft.com/solutions/serverless/) miljö.

> [!NOTE]
> Lär dig att använda SignalR och Azure Functions tillsammans i den interaktiva självstudien [Aktivera automatiska uppdateringar i ett webbprogram med Azure Functions and SignalR Service](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr).

## <a name="integrate-real-time-communications-with-azure-services"></a>Integrera realtidskommunikation med Azure-tjänster

Med Azure Functions kan du skriva kod på [flera språk](../azure-functions/supported-languages.md), inklusive JavaScript, Python, C#och Java, som utlöser när händelser inträffar i molnet. Exempel på dessa händelser:

* HTTP- och webhook-begäranden
* Periodiska timers
* Händelser från Azure-tjänster såsom:
    - Event Grid
    - Händelsehubbar
    - Service Bus
    - Cosmos DB-ändringsflöde
    - Storage-blobbar och köer
    - Logic Apps-anslutningsappar som Salesforce och SQL Server

Genom att använda Azure Functions för att integrera dessa händelser med Azure SignalR Service har du möjligheten att meddela tusentals olika klienter när händelser inträffar.

Några vanliga scenarier för serverlösa realtidsmeddelanden som du kan implementera med Azure Functions och SignalR Service är:

* Visualisera IoT-enhetstelemetri på en realtidsinstrumentpanel eller karta
* Uppdatera data i ett program när dokument uppdateras i Cosmos DB
* Skicka aviseringar via app när nya beställningar skapas i Salesforce

## <a name="signalr-service-bindings-for-azure-functions"></a>SignalR Service-bindningar för Azure Functions

SignalR Service-bindningar för Azure Functions gör att en Azure Functions-app kan publicera meddelanden till klienter som är anslutna till SignalR Service. Klienter kan ansluta till tjänsten med hjälp av en SignalR-klient-SDK som är tillgänglig i .NET, JavaScript och Java. Fler språk kommer snart.

### <a name="an-example-scenario"></a>Ett exempelscenario

Ett exempel på hur du använder SignalR Service-bindningar är att använda Azure Functions för att integrera med Azure Cosmos DB och SignalR Service för att skicka realtidsmeddelanden när nya händelser visas i ett Cosmos-DB-ändringsflöde.

![Cosmos DB, Azure Functions, SignalR Service](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. En ändring görs i en Cosmos DB-samling
2. Ändringshändelsen sprids till Cosmos DB-ändringsflödet
3. En Azure Functions utlöses av ändringshändelsen med hjälp av Cosmos DB-utlösaren
4. SignalR Service-utdatabindningen publicerar ett meddelande till SignalR Service
5. SignalR Service publicerar meddelandet till alla anslutna klienter

### <a name="authentication-and-users"></a>Autentisering och användare

SignalR Service gör att du kan skicka broadcast-meddelanden till alla klienter eller bara till en delmängd av klienter, till exempel dem som tillhör en enskild användare. SignalR Service-bindningar för Azure Functions kan kombineras med App Service-autentisering för att autentisera användare med leverantörer, till exempel Azure Active Directory, Facebook och Twitter. Du kan sedan skicka meddelanden direkt till dessa autentiserade användare.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du en översikt över hur du använder Azure Functions med SignalR Service för att aktivera en mängd olika scenarier med serverlösa realtidsmeddelanden.

Mer information om hur du använder Azure Functions and SignalR Service finns tillsammans på följande resurser:

* [Utveckling och konfiguration av Azure Functions med SignalR-tjänsten](signalr-concept-serverless-development-config.md)
* [Aktivera automatiska uppdateringar i en webbapp med Azure Functions och SignalR Service](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

Följ någon av de här snabbstarterna om du vill veta mer.

* [Snabbstart för serverlös Azure SignalR Service – C#](signalr-quickstart-azure-functions-csharp.md)
* [Snabbstart för serverlös Azure SignalR Service – JavaScript](signalr-quickstart-azure-functions-javascript.md)
