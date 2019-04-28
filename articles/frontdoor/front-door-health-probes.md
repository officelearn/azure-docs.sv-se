---
title: Azure ytterdörren Service - serverdelen hälsoövervakning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure ytterdörren tjänsten övervakar hälsotillståndet för serverdelen
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 59a3bac39437b91eeee3b005bd23476a34a308b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736589"
---
# <a name="health-probes"></a>Hälsotillståndsavsökningar

För att fastställa hälsotillståndet för varje serverdel skickar varje ytterdörren miljö regelbundet en syntetisk HTTP/HTTPS-begäran till var och en av dina konfigurerade serverdelar. Front Door använder sedan svar från dessa avsökningar för att fastställa de ”bästa” serverdelar som verkliga klientbegäranden bör vidarebefordras till.


## <a name="supported-protocols"></a>Protokoll som stöds

Dörren har stöd för skicka avsökningar via HTTP eller HTTPS-protokoll. Dessa avsökningar skickas över samma TCP-portar som konfigurerats för att dirigera klientbegäranden och kan inte åsidosättas.

## <a name="health-probe-responses"></a>Avsökningen hälsodeklarationssvar

| Svar  | Beskrivning | 
| ------------- | ------------- |
| Fastställa hälsotillstånd  |  Ett 200 OK statuskod visar serverdelen är felfri. Allt annat anses vara fel. Om en giltig HTTP-svar inte tas emot för en avsökning av någon anledning (inklusive nätverksfel), räknas avsökningen som misslyckat.|
| Mäta svarstiden  | Svarstiden är wall-clock-tiden mäts från det ögonblick då omedelbart innan vi skickar avsökningen begäran till den tidpunkt när vi får de sista byten av svaret. Vi använder en ny TCP-anslutning för varje begäran så att denna mätning inte prioriterar mot serverdelar med befintliga varma anslutningar.  |

## <a name="how-front-door-determines-backend-health"></a>Hur ytterdörren fastställer hälsotillstånd för serverdel

Azure ytterdörren-tjänsten använder samma process i tre steg nedan över alla algoritmer för att kontrollera statusen.

1. Undanta inaktiverad serverdelar.

2. Exkludera servrar som har problem med hälsotillståndet avsökningar:
    * Det här alternativet gör du genom att titta på senaste _n_ hälsotillstånd avsökningssvar. Om minst _x_ är felfri och serverdelen betraktas som felfri.

    * _n_ konfigureras genom att ändra egenskapen SampleSize i inställningar för belastningsutjämning.

    * _x_ konfigureras genom att ändra egenskapen SuccessfulSamplesRequired i inställningar för belastningsutjämning.

3. Utanför uppsättningen med felfri serverdelar i serverdelspoolen ytterdörren dessutom mäter och underhåller fördröjning (fram och åter time) för varje serverdel.


## <a name="complete-health-probe-failure"></a>Fullständig uteblivna för avsökning

Om det misslyckas hälsoavsökningar för varje serverdel i en serverdelspool kan ytterdörren betraktar alla serverdelar felfritt och dirigerar trafik i en resursallokeringsdistribution mellan dem.

När alla serverdelar återgår till felfritt tillstånd, sedan återupptas ytterdörren normal belastningsutjämningsalgoritm.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
