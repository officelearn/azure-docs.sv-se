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
ms.openlocfilehash: 256d530590fadc9e2aeb1ea1efb7a52608014978
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988571"
---
# <a name="health-probes"></a>Hälsotillståndsavsökningar

För att fastställa hälsotillståndet för varje serverdel skickar varje ytterdörren miljö regelbundet en syntetisk HTTP/HTTPS-begäran till var och en av dina konfigurerade serverdelar. Ytterdörren använder sedan svar från dessa avsökningar för att fastställa ”bästa” serverdelar som den ska vidarebefordra verkliga klientbegäranden.


## <a name="supported-protocols"></a>Protokoll som stöds

Dörren har stöd för skicka avsökningar via HTTP eller HTTPS-protokoll. Dessa avsökningar som skickas över samma TCP-portar som konfigurerats för routning klientbegäranden och kan inte åsidosättas.

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

- Lär dig hur du [skapa en ytterdörren](quickstart-create-front-door.md).
- Lär dig [hur ytterdörren fungerar](front-door-routing-architecture.md).
