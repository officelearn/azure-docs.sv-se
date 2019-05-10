---
title: Vad är Azure konfiguration? | Microsoft Docs
description: En översikt över tjänsten Azure App Configuration.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 985845197f8a1ece76fe0a620f05194109f51bd6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408664"
---
# <a name="what-is-azure-app-configuration"></a>Vad är Azure konfiguration?

Konfiguration av Azure är en tjänst för att centralt hantera programinställningar och funktionen flaggor. Moderna program, särskilt de program som körs i ett moln generellt sett ha många komponenter som distribueras till sin natur. Om konfigurationsinställningar sprids mellan dessa komponenter kan det ge upphov till fel som är svåra att felsöka under en programdistribution. Använd konfiguration av lagra alla inställningar för ditt program och skydda sina åtkomst på samma ställe.

Konfiguration av är för närvarande i offentlig förhandsversion. Det är kostnadsfritt under förhandsperioden. Du kan registrera dig för den i den [Azure-portalen](https://portal.azure.com).

## <a name="why-use-app-configuration"></a>Varför använda konfiguration?

Molnbaserade program körs ofta på flera virtuella datorer eller containrar i flera regioner och använder flera externa tjänster. Skapa ett distribuerat program som är stabila och skalbara är en utmaning.

Olika programmeringsspråk metodik hjälpa utvecklare att hantera ökar komplexiteten i att skapa program. Exempelvis beskriver appen 12-faktortjänster många väl testade arkitektoniska mönstren och bästa praxis för användning med molnprogram. En viktig rekommendation från den här guiden är att avgränsa konfigurationen från koden. I det här fallet bör konfigurationsinställningar för ett program förvaras utanför den körbara filen och har lästs in från dess körningsmiljö eller en extern källa.

Alla program kan utnyttja Appkonfiguration, är i följande exempel de typer av program som har nytta av användningen av det:

* Mikrotjänster baserat på Azure Kubernetes Service, Azure Service Fabric eller andra behållarbaserade appar som distribueras i en eller flera geografiska områden
* Serverlösa appar, bland annat Azure Functions eller andra aktivitetsdriven tillståndslösa databearbetning-appar
* Pipeline för kontinuerlig distribution

App Configuration ger följande fördelar:

* En helt hanterad tjänst som kan ställas in på några minuter
* Flexibla viktiga uppgifter och mappningar
* Tagga med etiketter
* Point-in-time-återuppspelning av inställningar
* Speciellt användargränssnitt för hantering av funktionen flagga
* Jämförelse av två uppsättningar konfigurationer på egendefinierat dimensioner
* Förbättrad säkerhet via Azure-hanterade identiteter
* Kompletta data krypteringar vilande eller överförs
* Intern integrering med populära ramverk

Konfiguration av kompletterar [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), som används för att lagra programhemligheter. Konfiguration av gör det enklare att implementera följande scenarier:

* Centralisera hantering och distribution av hierarkiska konfigurationsdata för olika miljöer och områden
* Dynamiskt ändra programinställningarna utan att behöva distribuera om eller starta om ett program
* Kontrollen funktionstillgänglighet i realtid

## <a name="use-app-configuration"></a>Använd App-konfiguration

Det enklaste sättet att lägga till ett appkonfigurationsarkiv för ditt program är via ett klientbibliotek som Microsoft tillhandahåller. Utifrån programmeringsspråk och ramverk, är följande bästa metoder tillgängliga för dig.

| Programmeringsspråk och ramverk | Ansluta |
|---|---|
| .NET core och ASP.NET Core | App-konfigurationsprovider för .NET Core |
| .NET och ASP.NET | Konfiguration av apputvecklare för .NET |
| Java Spring | Klienten för Spring Cloud-konfiguration |
| Andra | Konfiguration av REST-API |

## <a name="next-steps"></a>Nästa steg

* [Snabbstart för ASP.NET Core](./quickstart-aspnet-core-app.md)
* [Snabbstart för .NET core](./quickstart-dotnet-core-app.md)
* [Snabbstart för .NET framework](./quickstart-dotnet-app.md)
* [Azure Function quickstart](./quickstart-azure-function-csharp.md)
* [Java Spring-Snabbstart](./quickstart-java-spring-app.md)
* [Snabbstart för ASP.NET Core funktionen flagga](./quickstart-feature-flag-aspnet-core.md)
