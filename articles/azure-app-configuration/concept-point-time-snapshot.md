---
title: Tidpunktsbaserad ögonblicksbild för Azure App Configuration | Microsoft Docs
description: En översikt över hur tidpunktsbaserade ögonblicksbilder fungerar i Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885143"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Azure App Configuration lagrar poster över de exakta tider då ett nytt nyckel/värde-par skapas och senare ändras. Dessa poster utgör en fullständig tidslinje för ändringar av nyckelvärden. Ett appkonfigurationsarkiv kan rekonstruera historiken för alla nyckelvärden och återuppspela deras tidigare värden vid valfri tidpunkt fram till aktuell tid. Med den här funktionen kan du ”resa bakåt i tiden” och hämta ett gammalt nyckelvärde. Du kan till exempel hämta gårdagens konfigurationsinställningar precis före den senaste distributionen så att du kan återställa en tidigare konfiguration ifall du behöver återställa programmet till ett tidigare tillstånd.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

För att hämta tidigare nyckelvärden behöver du ange en tid då nyckelvärdena sparades som ögonblicksbilder i HTTP-rubriken för ett REST API-anrop. Exempel:

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

För närvarande behåller App Configuration 7 dagars ändringshistorik.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa en ASP.NET-webbapp](quickstart-aspnet-core-app.md)  
