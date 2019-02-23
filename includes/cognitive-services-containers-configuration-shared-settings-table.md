---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: ed512c0f56d8da5cb8e47b20f2495054fdedf020
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741205"
---
Den här behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Används för att spåra faktureringsinformation.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Du kan lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till behållaren.|
|Ja|[Billing](#billing-configuration-setting)|Anger URI för tjänstresursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har godkänt licensen för behållaren.|
|Nej|[Fluentd](#fluentd-settings)|Skriva log och eventuellt måttdata till en Fluentd-server.|
|Nej|[HTTP-Proxy](#http-proxy-credentials-settings)|Konfigurera en HTTP-proxy för utgående förfrågningar.|
|Nej|[Loggning](#logging-settings)|Ger ASP.NET Core loggning stöd för din behållare. |
|Nej|[Mounts](#mount-settings)|Läsa och skriva data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|
