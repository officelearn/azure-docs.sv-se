---
title: Azure Ytterdörr - backend hälsoövervakning | Microsoft-dokument
description: Den här artikeln hjälper dig att förstå hur Azure Front Door övervakar hälsan hos dina backends
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
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471582"
---
# <a name="health-probes"></a>Hälsotillståndsavsökningar

För att fastställa hälsa och närhet för varje serverdel från en viss ytterdörr miljö, skickar varje ytterdörr miljö regelbundet en syntetisk HTTP / HTTPS-begäran till var och en av dina konfigurerade serverdelar. Front Door använder sedan svar från dessa avsökningar för att fastställa de ”bästa” serverdelar som verkliga klientbegäranden bör vidarebefordras till. 

> [!WARNING]
> Eftersom Ytterdörren har många kantmiljöer globalt, hälsa sond förfrågningar volym till dina serverdelser kan vara ganska hög - allt från 25 förfrågningar varje minut till så högt som 1200 förfrågningar per minut, beroende på hälso-sond frekvens konfigureras. Med standardavsökningsfrekvensen på 30 sekunder bör avsökningsvolymen på serveringen vara cirka 200 begäranden per minut.

## <a name="supported-protocols"></a>Protokoll som stöds

Ytterdörren stöder att skicka avsökningar via http- eller HTTPS-protokoll. Dessa avsökningar skickas över samma TCP-portar som konfigurerats för att dirigera klientbegäranden och kan inte åsidosättas.

## <a name="supported-http-methods-for-health-probes"></a>HTTP-metoder som stöds för hälsoavsökningar

Ytterdörren stöder följande HTTP-metoder för att skicka hälsoavsökningar:

1. **FÅ:** GET-metoden innebär att hämta all information (i form av en entitet) identifieras av Request-URI.
2. **HUVUD:** HEAD-metoden är identisk med GET förutom att servern INTE får returnera en meddelandetext i svaret. För nya frontdörrprofiler ställs som standard sondmetoden in som HEAD.

> [!NOTE]
> För lägre belastning och kostnad på dina serverdelser rekommenderar Ytterdörren att du använder HEAD-begäranden om hälsosonder.

## <a name="health-probe-responses"></a>Svar på hälsoavsökning

| Svar  | Beskrivning | 
| ------------- | ------------- |
| Bestämma hälsa  |  En 200 OK-statuskod anger att backend är felfri. Allt annat anses vara ett misslyckande. Om ett giltigt HTTP-svar av någon anledning (inklusive nätverksfel) inte tas emot för en avsökning räknas avsökningen som ett fel.|
| Mäta latens  | Latens är väggklockan tid mätt från det ögonblick omedelbart innan vi skickar sonden begäran till det ögonblick då vi får den sista byte av svaret. Vi använder en ny TCP-anslutning för varje begäran, så denna mätning är inte partisk mot backends med befintliga varma anslutningar.  |

## <a name="how-front-door-determines-backend-health"></a>Hur ytterdörren avgör backend hälsa

Azure Front Door använder samma trestegsprocess nedan för alla algoritmer för att fastställa hälsa.

1. Uteslut inaktivera inaktiverade bakåtsträvningar.

2. Exkludera serverd som har hälsoavsökningar fel:
    * Det här valet görs genom att titta på de senaste _n_ hälsoavsökningssvaren. Om minst _x_ är friska, är backend anses friska.

    * _n_ konfigureras genom att ändra egenskapen SampleSize i belastningsutjämningsinställningar.

    * _x_ konfigureras genom att ändra egenskapen SuccessfulSamplesRequired i belastningsutjämningsinställningar.

3. Ur uppsättningen av friska backends i backend poolen, frontdörren mäter dessutom och upprätthåller latens (rundresa tid) för varje backend.


## <a name="complete-health-probe-failure"></a>Fullständigt fel på hälsoavsökningen

Om hälso-sonder misslyckas för varje serverdel i en serverdel pool, då Ytterdörren anser alla serverdels friska och rutter trafik i en round robin distribution över dem alla.

När en backend återgår till ett felfritt tillstånd, kommer Front Door att återuppta den normala belastningsutjämningsalgoritmen.

## <a name="disabling-health-probes"></a>Inaktivera hälsoavsökningar

Om du har en enda serverningsservering i serverdapoolen kan du välja att inaktivera hälsoavsökningarna som minskar belastningen på programmets serverd. Även om du har flera serverd i serverda poolen men bara en av dem är i aktiverat tillstånd, kan du inaktivera hälsoavsökningar.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
