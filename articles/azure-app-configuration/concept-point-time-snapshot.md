---
title: Azure App konfigurations tidpunkt för ögonblicks bilder
description: En översikt över hur tidpunktsbaserade ögonblicksbilder fungerar i Azure App Configuration
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4db52ce1897aa5a2b809cb7044b9764baffd0767
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495254"
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
