---
title: Skapa appar i realtid med Azure Functions och Azure SignalR Service
description: En översikt över med Azure SignalR Service i program utan server.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: c06203cfd7537dd4c9bc35c75a3f21d12bf64d26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401889"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Skapa appar i realtid med Azure Functions och Azure SignalR Service

Eftersom Azure SignalR Service och Azure Functions båda är fullständigt hanterade, mycket skalbara tjänster som gör att du kan fokusera på att bygga program i stället för att hantera infrastrukturen är det vanligt att använda de två tjänsterna tillsammans för att tillhandahålla realtidskommunikation i en [serverlös](https://azure.microsoft.com/solutions/serverless/) miljö.

## <a name="integrate-real-time-communications-with-azure-services"></a>Integrera realtidskommunikation med Azure-tjänster

Med Azure Functions kan du skriva kod i [flera språk](../azure-functions/supported-languages.md), inklusive JavaScript, C# och Java, som utlöses när händelser inträffar i molnet. Exempel på dessa händelser:

* HTTP- och webhook-begäranden
* Periodiska timers
* Händelser från Azure-tjänster såsom:
    - Event Grid
    - Event Hubs
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

Fullständig information om hur du använder Azure Functions och SignalR Service tillsammans finns i följande guide.

* [Med SignalR Service och Azure Functions-utvecklingen](signalr-concept-serverless-development-config.md)

Följ någon av de här snabbstarterna om du vill veta mer.

* [Snabbsnart om serverlöst med Azure SignalR Service – C#](signalr-quickstart-azure-functions-csharp.md)
* [Snabbsnart om serverlöst med Azure SignalR Service – JavaScript](signalr-quickstart-azure-functions-javascript.md)