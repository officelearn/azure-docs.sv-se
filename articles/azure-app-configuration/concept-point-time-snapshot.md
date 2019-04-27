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
ms.openlocfilehash: 83770e8c5f415670855b5cf2502d02c4d6919440
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741146"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Konfiguration av Azure syns de exakta tidpunkter när ett nytt nyckel / värde-par skapas och sedan ändras. Dessa poster utgör en fullständig tidslinje för ändringar av nyckelvärden. En appbutik konfiguration kan rekonstruera historiken för alla nyckelvärdet och spela upp det senaste värdet vid ett givet tillfälle, upp till aktuella. Med den här funktionen kan kan du ”tid resa” bakåt och hämta ett gamla nyckelvärde. Du kan till exempel få gårdagens konfigurationsinställningar, precis före den senaste distributionen för att återställa en tidigare konfigurationen och återställa programmet.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Ange en tid då nyckelvärden ögonblicksbild i HTTP-huvud för ett REST API-anrop för att hämta de senaste nyckelvärden. Exempel:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Konfiguration av håller för närvarande sju dagarna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)  
