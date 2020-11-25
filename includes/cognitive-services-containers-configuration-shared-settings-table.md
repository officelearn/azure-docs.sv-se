---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006889"
---
Behållaren har följande konfigurations inställningar:

|Obligatorisk|Inställning|Syfte|
|--|--|--|
|Yes|[ApiKey](#apikey-configuration-setting)|Spårar fakturerings information.|
|No|[ApplicationInsights](#applicationinsights-setting)|Aktiverar tillägg av stöd för [Azure Application insikter](/azure/application-insights) -telemetri till din behållare.|
|Yes|[Billing](#billing-configuration-setting)|Anger slut punkts-URI för tjänst resursen på Azure.|
|Yes|[Villkoren](#eula-setting)| Anger att du har accepterat licensen för behållaren.|
|No|[Fluent](#fluentd-settings)|Skriver logg och, om du vill, Metric-data till en Fluent-Server.|
|No|HTTP-proxy|Konfigurerar en HTTP-proxy för att göra utgående begär Anden.|
|No|[Loggning](#logging-settings)|Tillhandahåller ASP.NET Core loggnings stöd för din behållare. |
|No|[Monterar](#mount-settings)|Läser och skriver data från värddatorn till behållaren och från behållaren tillbaka till värddatorn.|