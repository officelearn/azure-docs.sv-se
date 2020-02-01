---
title: Azure App konfigurations tidpunkt för ögonblicks bilder
description: En översikt över hur tidpunktsbaserade ögonblicksbilder fungerar i Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4a352ba913b6ad4e3c8607677078e21070f294fd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899586"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Azure App-konfigurationen sparar poster av exakta tider när ett nytt nyckel/värde-par skapas och sedan ändras. Dessa poster utgör en fullständig tidslinje för ändringar av nyckelvärden. Ett konfigurations Arkiv för appar kan konstruera om historiken för ett nyckel värde och spela upp det tidigare värdet vid ett givet tillfälle, upp till det aktuella. Med den här funktionen kan du "Time-Travel" bakåt och hämta ett gammalt nyckel värde. Du kan till exempel få upp till igår: s konfigurations inställningar, precis innan den senaste distributionen, för att återställa en tidigare konfiguration och återställa programmet.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Om du vill hämta tidigare nyckel värden anger du en tidpunkt då nyckel värden är ögonblicks bilder i HTTP-huvudet för ett REST API-anrop. Ett exempel:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

För närvarande behåller app-konfigurationen sju dagars ändrings historik.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core webbapp](./quickstart-aspnet-core-app.md)  
