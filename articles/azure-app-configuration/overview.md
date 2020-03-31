---
title: Vad är Azure App Configuration?
description: En översikt över tjänsten Azure App Configuration.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: overview
ms.date: 02/19/2020
ms.openlocfilehash: 1f1cec68813d33e7fa19a414a30adfc9a41df91f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77523483"
---
# <a name="what-is-azure-app-configuration"></a>Vad är Azure App Configuration?

Azure App Configuration tillhandahåller en tjänst för att centralt hantera programinställningar och funktionsflaggor. Moderna program, särskilt program som körs i ett moln, har i allmänhet många komponenter som distribueras i naturen. Om konfigurationsinställningar sprids mellan dessa komponenter kan det ge upphov till fel som är svåra att felsöka under en programdistribution. Använd Appkonfiguration för att lagra alla inställningar för ditt program och skydda deras åtkomster på ett ställe.

## <a name="why-use-app-configuration"></a>Varför använda Appkonfiguration?

Molnbaserade program körs ofta på flera virtuella datorer eller containrar i flera regioner och använder flera externa tjänster. Att skapa ett robust och skalbart program i en distribuerad miljö innebär en stor utmaning.

Olika programmeringsmetoder hjälper utvecklare att hantera den ökande komplexiteten i att bygga applikationer. [Appen Twelve-Factor](https://12factor.net/) beskriver till exempel många väl testade arkitektoniska mönster och metodtips för användning med molnprogram. En viktig rekommendation från den här guiden är att avgränsa konfigurationen från koden. Ett programs konfigurationsinställningar bör hållas externa till dess körbara och läsas in från dess körningsmiljö eller en extern källa.

Även om alla program kan använda appkonfiguration, är följande exempel de typer av program som drar nytta av användningen av den:

* Mikrotjänster baserade på Azure Kubernetes Service, Azure Service Fabric eller andra behållarappar som distribueras i en eller flera geografiska områden
* Serverlösa appar, som inkluderar Azure-funktioner eller andra händelsedrivna tillståndslösa beräkningsappar
* Pipeline för kontinuerlig distribution

App Configuration ger följande fördelar:

* En fullständigt hanterad tjänst som kan ställas in på några minuter
* Flexibla nyckelrepresentationer och kartläggningar
* Tagga med etiketter
* Point-in-time-repris av inställningar
* Dedikerat användargränssnitt för funktionsflagganhantering
* Jämförelse av två uppsättningar konfigurationer på anpassade definierade dimensioner
* Förbättrad säkerhet via Azure-hanterade identiteter
* Kryptering av känslig information i vila och under transport
* Inbyggd integration med populära ramverk

Appkonfiguration kompletterar [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), som används för att lagra programhemligheter. Appkonfiguration gör det enklare att implementera följande scenarier:

* Centralisera hantering och distribution av hierarkiska konfigurationsdata för olika miljöer och geografiska områden
* Ändra programinställningar dynamiskt utan att behöva distribuera om eller starta om ett program
* Kontrollera funktionens tillgänglighet i realtid

## <a name="use-app-configuration"></a>Använda appkonfiguration

Det enklaste sättet att lägga till en App Configuration Store i ditt program är via ett klientbibliotek som tillhandahålls av Microsoft. Följande metoder finns tillgängliga för att ansluta till ditt program, beroende på ditt valda språk och ramverk

| Programmeringsspråk och ramverk | Ansluta |
|---|---|
| .NET Core och ASP.NET Core | Leverantör av appkonfiguration för .NET Core |
| .NET Framework och ASP.NET | Appkonfigurationsverktyget för .NET |
| Java Spring | Appkonfigurationsklient för Spring Cloud |
| Andra | REST-API för appkonfiguration |

## <a name="next-steps"></a>Nästa steg

* [ASP.NET Core snabbstart](./quickstart-aspnet-core-app.md)
* [.NET Core snabbstart](./quickstart-dotnet-core-app.md)
* [.NET Framework snabbstart](./quickstart-dotnet-app.md)
* [Snabbstart för Azure-funktioner](./quickstart-azure-functions-csharp.md)
* [Snabbstart för Java-våren](./quickstart-java-spring-app.md)
* [ASP.NET Core-funktionen flaggar snabbstart](./quickstart-feature-flag-aspnet-core.md)
* [Fartstart för fjäderstart-funktionen](./quickstart-feature-flag-spring-boot.md)
