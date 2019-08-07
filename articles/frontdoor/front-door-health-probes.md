---
title: Azure frontend-tjänst – Server dels hälso övervakning | Microsoft Docs
description: Den här artikeln hjälper dig att förstå hur Azure-frontend-tjänsten övervakar hälso tillståndet för dina arbets ändar
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
ms.openlocfilehash: 289b05a2c50a2b4af50eb2114515a49bb653cf1a
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742393"
---
# <a name="health-probes"></a>Hälsoavsökningar

För att fastställa hälso tillståndet för varje server del skickar varje front dörr-miljö regelbundet en syntetisk HTTP/HTTPS-begäran till var och en av dina konfigurerade Server delar. Front Door använder sedan svar från dessa avsökningar för att fastställa de ”bästa” serverdelar som verkliga klientbegäranden bör vidarebefordras till. Observera att eftersom front dörren har många gräns miljöer globalt kan hälso avsökningen begära volym till dina Server delar så hög som mer än en begäran per sekund beror på hälso avsöknings frekvensen som har kon figurer ATS. 



## <a name="supported-protocols"></a>Protokoll som stöds

Front dörr stöder sändning av avsökningar via antingen HTTP-eller HTTPS-protokoll. Dessa avsökningar skickas över samma TCP-portar som konfigurerats för att dirigera klientbegäranden och kan inte åsidosättas.

## <a name="health-probe-responses"></a>Svar på hälso avsökning

| Responses  | Beskrivning | 
| ------------- | ------------- |
| Fastställa hälsa  |  En status på 200 OK anger att Server delen är felfri. Allt annat anses vara ett haveri. Om ett giltigt HTTP-svar inte tas emot för en avsökning räknas inte avsökningen som ett fel (inklusive nätverks fel).|
| Mäta svars tid  | Svars tiden är den tid i väggen som mäts från tiden omedelbart innan vi skickar en avsöknings förfrågan till den tidpunkt då vi får de sista byten av svaret. Vi använder en ny TCP-anslutning för varje begäran, så den här mätningen prioriteras inte mot Server delar med befintliga varma anslutningar.  |

## <a name="how-front-door-determines-backend-health"></a>Hur front dörren avgör Server dels hälsa

Azure-tjänsten för front dörr använder samma tre stegs process nedan för alla algoritmer för att fastställa hälso tillståndet.

1. Uteslut inaktiverade Server delar.

2. Uteslut Server delar med hälso avsöknings fel:
    * Valet görs genom att titta på de senaste _n_ hälso avsöknings svaren. Om minst _x_ är felfritt betraktas Server delen som felfri.

    * _n_ konfigureras genom att ändra egenskapen SampleSize i inställningarna för belastnings utjämning.

    * _x_ konfigureras genom att ändra egenskapen SuccessfulSamplesRequired i inställningar för belastnings utjämning.

3. Från och med en uppsättning felfria Server delar i backend-poolen, åtgärdar även front dörren och bibehåller svars tiden (fördröjnings tid) för varje server del.


## <a name="complete-health-probe-failure"></a>Slutför hälso avsöknings fel

Om hälso avsökningar inte kan utföras för varje server del i en backend-pool, anser front-dörren att alla backar är felfria och dirigerar trafik i en Round Robin-distribution över alla.

När en server del återgår till ett felfritt tillstånd återupptar front dörren den normala belastnings Utjämnings algoritmen.

## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs [hur Front Door fungerar](front-door-routing-architecture.md).
