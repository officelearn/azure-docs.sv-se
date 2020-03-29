---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73484096"
---
Behållaren har följande konfigurationsinställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey (ApiKey)](#apikey-configuration-setting)|Spårar faktureringsinformation.|
|Inga|[ApplicationInsights](#applicationinsights-setting)|Gör det möjligt att lägga till [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetri stöd till din behållare.|
|Ja|[Fakturering](#billing-configuration-setting)|Anger slutpunkts-URI för tjänstresursen på Azure.|
|Ja|[Eula](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Inga|[Flytande](#fluentd-settings)|Skriver logg- och eventuellt måttdata till en Fluentd-server.|
|Inga|HTTP-proxy|Konfigurerar en HTTP-proxy för att göra utgående begäranden.|
|Inga|[Loggning](#logging-settings)|Innehåller ASP.NET Core-loggningsstöd för din behållare. |
|Inga|[Fästen](#mount-settings)|Läser och skriver data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|
