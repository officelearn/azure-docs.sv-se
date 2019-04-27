---
title: Övervakning av Azure-infrastrukturen
description: Den här artikeln beskriver övervakning av Azure-produktionsnätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 88bc76116392140c533f67402642c68d714227c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591646"
---
# <a name="azure-infrastructure-monitoring"></a>Övervakning av Azure-infrastrukturen   

## <a name="configuration-and-change-management"></a>Konfiguration och ändringshantering
Azure går igenom och uppdaterar konfigurationsinställningarna och grundläggande konfigurationer av maskinvara, programvara och nätverksenheter årligen. Ändringar har utvecklats, testats och godkänts innan produktionsmiljön från en miljö för utveckling och/eller testning.

Baslinjekonfigurationer som krävs för Azure-baserade tjänster granskas genom Azure säkerhet och efterlevnad-teamet och serviceteam. En service-teamet granskning är en del av testning innan distributionen av sina tjänster i produktion.

## <a name="vulnerability-management"></a>Hantering av sårbarhet
Hantering av säkerhetsuppdateringar hjälper dig att skydda system från kända säkerhetsproblem. Azure använder integrerade distributionssystem hanterar distribution och installation av säkerhetsuppdateringar för programvara från Microsoft. Azure är också kunna rita på resurser för Microsoft Security Response Center (MSRC). MSRC identifierar, övervakar, svarar på och löser incidenter och molnet sårbarheter dygnet runt, varje dag på året.

## <a name="vulnerability-scanning"></a>Granskar säkerhetsrisker
Säkerhetsriskssökning utförs på server-operativsystem, databaser och nätverksenheter. Sårbarhetsgenomsökningar utförs på kvartalsbasis minimikrav. Azure kontrakt med oberoende bedömare att utföra penetrationstester för Azure gräns. Red team övningarna utförs även regelbundet och resultaten används för att göra förbättringar av säkerhet.

## <a name="protective-monitoring"></a>Skyddande övervakning
Azure-säkerhet har definierat krav för övervakning av aktiva. Serviceteam konfigurera active övervakningsverktyg i enlighet med de här kraven. Verktyg för övervakning av aktiva inkluderar Microsoft Monitoring Agent (MMA) och System Center Operations Manager. Dessa verktyg är konfigurerade för att ge tid aviseringar till Azure-säkerhet personal i situationer som kräver omedelbar åtgärd.

## <a name="incident-management"></a>incidenthantering
Microsoft implementerar en säkerhetsprocess incidenthantering för att underlätta koordinerade svarsrutiner för incidenter, görs en.

Om Microsoft får kännedom om obehörig åtkomst till kunddata som lagras med dess utrustning eller i dess lokaler eller blir medveten om obehörig åtkomst till sådana utrustning eller anläggningar, vilket resulterar i förlust, avslöjande av eller ändring av kundinformation, Microsoft utför följande åtgärder:

- Snabbt meddelar kunden om säkerhetsincidenten.
- Snabbt undersöker säkerhetsincidenten och ger kunderna detaljerad information om säkerhetsincidenten.
- Tar rimligt och fråga steg för att minimera effekterna och minimerar risken för skador som härrör från säkerhetsincidenten.

Ett ramverk för incidenthantering har upprättats som definierar roller och allokerar ansvarsområden. Azure security incident management-teamet ansvarar för att hantera säkerhetsincidenter, inklusive eskalering och garantera specialist team när det behövs. Azure-verksamhetschefer som ansvarar för överse undersökning och lösning av incidenter för säkerhet och sekretess.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)
