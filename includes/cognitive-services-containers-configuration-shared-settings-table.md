---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484096"
---
Behållaren har följande konfigurations inställningar:

|Krävs|Inställning|Syfte|
|--|--|--|
|Ja|[ApiKey](#apikey-configuration-setting)|Spårar fakturerings information.|
|Nej|[ApplicationInsights](#applicationinsights-setting)|Aktiverar tillägg av stöd för [Azure Application insikter](https://docs.microsoft.com/azure/application-insights) -telemetri till din behållare.|
|Ja|[Billing](#billing-configuration-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Ja|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|Nej|[Fluent](#fluentd-settings)|Skriver logg och, om du vill, Metric-data till en Fluent-Server.|
|Nej|HTTP-proxy|Konfigurerar en HTTP-proxy för att göra utgående begär Anden.|
|Nej|[Logging](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|Nej|[Monterar](#mount-settings)|Läser och skriver data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|
