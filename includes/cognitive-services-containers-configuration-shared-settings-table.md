---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484096"
---
Behållaren har följande konfigurations inställningar:

|Krävs|Inställningen|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Spårar fakturerings information.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Aktiverar tillägg av stöd för [Azure Application insikter](https://docs.microsoft.com/azure/application-insights) -telemetri till din behållare.|
|Ja|[Fakturering](#billing-configuration-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Nej|[Fluent](#fluentd-settings)|Skriver logg och, om du vill, Metric-data till en Fluent-Server.|
|Nej|HTTP-proxy|Konfigurerar en HTTP-proxy för att göra utgående begär Anden.|
|Nej|[Loggning](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|Nej|[Monterar](#mount-settings)|Läser och skriver data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|
