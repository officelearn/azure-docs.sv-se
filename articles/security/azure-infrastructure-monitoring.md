---
title: Övervakning av Azure-infrastrukturen
description: Den här artikeln handlar om övervakning av Azure-produktionsnätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102712"
---
# <a name="monitoring-of-azure-infrastructure"></a>Övervakning av Azure-infrastrukturen   

## <a name="configuration-and-change-management"></a>Konfiguration och ändringshantering
Microsoft Azure granskar och uppdaterar konfigurationsinställningar och grundläggande konfigurationer av maskinvara, programvara och nätverksenheter årligen. Ändringar utvecklas, testas och godkänts före anger produktionsmiljön från en miljö för utveckling och test.

Baslinje-konfigurationer som krävs för Azure-baserade tjänster granskas genom Azure-säkerhet och efterlevnad teamet och serviceteam. Service-team granskning är en del av testning innan distributionen av produktionstjänsten.

## <a name="vulnerability-management"></a>Hantering av säkerhetsrisk
Hantering av säkerhetsuppdateringar skyddar system från kända säkerhetsrisker. Azure använder integrerad distribution system för att hantera distribution och installation av uppdateringar för programvara från Microsoft. Azure är också kunna rita på resurser av Microsoft Security Response Center (MSRC). MSRC identifierar, övervakar, svarar och löser säkerhetsincidenter och molnet säkerhetsriskerna dygnet runt, varje dag på året.

## <a name="vulnerability-scanning"></a>Säkerhetsproblem scanning
Genomsökning av säkerhetsproblem utförs på server-operativsystem, databaser och nätverksenheter. Sårbarhetsgenomsökningar utförs på kvartalsbasis som minimum. Microsoft Azure-kontrakt med oberoende experter att utföra intrång tester för Microsoft Azure-gräns. Röd Team övningarna utförs även regelbundet och resultat som används för att göra förbättringar av säkerhet.

## <a name="protective-monitoring"></a>Skyddande övervakning
Microsoft Azure-säkerhet har definierats krav för aktiv övervakning. Tjänsten team konfigurera active övervakningsverktyg i enlighet med dessa krav. Aktiva övervakningsverktyg inkluderar Monitoring Agent (MA) och System Center Operations Manager. Dessa verktyg är konfigurerade för att ge tid aviseringar till Microsoft Azure-säkerhet personal i situationer som kräver omedelbar åtgärd.

## <a name="incident-management"></a>Hantering av incidenter
Microsoft implementerar en säkerhetsprocess incidenthantering för att underlätta ett samordnat svar vid incidenter, ska en ske.

Om Microsoft blir medveten om otillåten åtkomst till kundinformation som lagras på utrustningen eller i dess verksamhet eller obehörig åtkomst till sådana utrustning eller verksamhet, vilket resulterar i förlust, avslöjande eller ändring av kundinformation, tar Microsoft följande åtgärder:

- Snabbt meddelar kunden om incidenten säkerhet
- Snabbt undersöker säkerhetsincident och tillhandahåller kunden med detaljerad information om säkerhetsincident
- Tar rimligt och fråga steg för att minimera effekterna och minimera skador som härrör från säkerhetsincident.

Ett ramverk för incidenthantering har upprättats med definierade-roller och ansvarsområden som allokerats. Windows Azure Security Incident Management (WASIM)-teamet ansvarar för att hantera säkerhetsincidenter, inklusive eskalering och garantera specialist team vid behov. Azure-verksamhetschefer som ansvarar för att granska undersökningar och lösning av incidenter för säkerhet och sekretess.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)
