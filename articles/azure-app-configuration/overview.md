---
title: Vad är Azure App Configuration | Microsoft Docs
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
ms.openlocfilehash: 3e96a1af4746c6344f9565d0f0de8ab2b2b8d8bc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58009392"
---
# <a name="what-is-azure-app-configuration"></a>Vad är Azure App Configuration

Azure App Configuration är en tjänst för att hantera programinställningar centralt. Moderna program, särskilt de som körs i ett moln, har generellt många komponenter som är distribuerade. Om konfigurationsinställningar sprids mellan dessa komponenter kan det ge upphov till fel som är svåra att felsöka under en programdistribution. Med App Configuration kan du lagra alla inställningar för ditt program och skydda åtkomst till dem på ett och samma ställe.

App-tjänsten för konfiguration är för närvarande i **förhandsversion**. Det är kostnadsfritt att använda den här förhandsversionen. Det finns inga särskilda godkännandeprocess för åtkomst till tjänsten, [registrera](https://aka.ms/azconfig/register) med oss så att vi kan informera dig betydande ändringar i förväg.

## <a name="why-use-app-configuration"></a>Varför App Configuration är användbart

Molnbaserade program körs ofta på flera virtuella datorer eller containrar i flera regioner och använder flera externa tjänster. Det är en rejäl utmaning att skapa ett sådant distribuerat program som är stabilt och skalbart. Olika programmeringsmetoder har skapats för att hjälpa utvecklare att hantera den ökande komplexiteten med att skapa dessa program. Till exempel beskriver 12-faktorsappen många gediget testade arkitekturmönster och bästa praxis för användning med molnprogram. En viktig rekommendation från den här guiden är att avgränsa konfigurationen från koden. Det innebär att konfigurationen för ett program, till exempel inställningar, bör förvaras utanför dess körbara fil och läsas in från dess körningsmiljö eller en extern källa.

Även om alla program kan använda App Configuration är följande bra exempel på de typer av program som bör använda det:

* Mikrotjänster som baseras på AKS, Service Fabric eller andra containerbaserade appar som distribueras i ett eller flera geografiska områden.
* Serverlösa appar såsom Azure Functions eller andra aktivitetsdrivna tillståndslösa beräkningsappar.
* Pipeline för kontinuerlig distribution.

App Configuration ger följande fördelar:

* En fullständigt hanterad tjänst som kan konfigureras på några minuter.
* Flexibla nyckelrepresentationer och mappningar.
* Taggning med etiketter.
* Tidpunktsbaserad återuppspelning av inställningar.
* Jämförelse av två uppsättningar konfigurationer på egendefinierade dimensioner.
* Förbättrad säkerhet via Azure-hanterade identiteter.
* Fullständig datakryptering i vila eller under överföring.
* Intern integrering med populära ramverk.

Konfiguration av kompletterar [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) används för att lagra programhemligheter. Konfiguration av gör det enklare att implementera följande scenarier:

* Centraliserad hantering och distribution av hierarkiska konfigurationsdata för olika miljöer och områden.
* Dynamisk konfigurationsändringar utan att omdistribuera eller starta om ett program.
* Funktionen hantering.

## <a name="how-to-use-app-configuration"></a>Så använder du App Configuration

Det enklaste sättet att lägga till ett appkonfigurationsarkiv för ditt program är via ett klientbibliotek som Microsoft tillhandahåller. Beroende på programmeringsspråk och ramverk visar följande exempel de bästa metoderna som är tillgängliga för dig:

| Programmeringsspråk och ramverk | Ansluta |
|---|---|
| **.NET Core** och **ASP.NET Core** | App Configuration-konfigurationsprovider för .NET Core. |
| **.NET** och **ASP.NET** | App Configuration-konfigurationsbyggare för .NET. |
| **Java Spring** | App Configuration-konfigurationsklient för Spring Cloud. |
| Andra | App Configuration REST API. |

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa en ASP.NET-webbapp](quickstart-aspnet-core-app.md)  
