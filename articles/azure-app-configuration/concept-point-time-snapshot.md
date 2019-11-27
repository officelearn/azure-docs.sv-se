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
ms.openlocfilehash: fdbe4846700c690261dbc734063f4420478666a8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185206"
---
# <a name="point-in-time-snapshot"></a>Ögonblicksbild av lagring vid olika tidpunkter

Azure App-konfigurationen sparar poster av exakta tider när ett nytt nyckel/värde-par skapas och sedan ändras. Dessa poster utgör en fullständig tidslinje för ändringar av nyckelvärden. Ett konfigurations Arkiv för appar kan konstruera om historiken för ett nyckel värde och spela upp det tidigare värdet vid ett givet tillfälle, upp till det aktuella. Med den här funktionen kan du "Time-Travel" bakåt och hämta ett gammalt nyckel värde. Du kan till exempel få upp till igår: s konfigurations inställningar, precis innan den senaste distributionen, för att återställa en tidigare konfiguration och återställa programmet.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Om du vill hämta tidigare nyckel värden anger du en tidpunkt då nyckel värden är ögonblicks bilder i HTTP-huvudet för ett REST API-anrop. Till exempel:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

För närvarande behåller app-konfigurationen sju dagars ändrings historik.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core webbapp](./quickstart-aspnet-core-app.md)  
