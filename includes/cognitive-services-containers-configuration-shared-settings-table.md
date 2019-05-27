---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2019
ms.openlocfilehash: c4abc5fa89b48b6fc55637e9ff3b259387d0d410
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66116679"
---
Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-configuration-setting)|Anger URI för tjänstresursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|HTTP-Proxy|Konfigurera en HTTP-proxy för utgående förfrågningar.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Nej|[Mounts](#mount-settings)|Läsa och skriva data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|
