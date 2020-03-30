---
title: Övervakning av Azure-infrastruktur
description: I den här artikeln beskrivs övervakning av Azure-produktionsnätverket.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: af9c157b4644156edc6dcdb1b53c141263576500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727153"
---
# <a name="azure-infrastructure-monitoring"></a>Övervakning av Azure-infrastruktur   

## <a name="configuration-and-change-management"></a>Konfigurations- och ändringshantering
Azure granskar och uppdaterar konfigurationsinställningar och baslinjekonfigurationer för maskinvara, programvara och nätverksenheter årligen. Förändringar utvecklas, testas och godkänns innan de kommer in i produktionsmiljön från en utvecklings- och/eller testmiljö.

De baslinjekonfigurationer som krävs för Azure-baserade tjänster granskas av Azure-säkerhets- och efterlevnadsteamet och av serviceteam. En granskning av serviceteam är en del av testningen som sker före distributionen av deras produktionstjänst.

## <a name="vulnerability-management"></a>Hantering av sårbarhet
Hantering av säkerhetsuppdatering hjälper till att skydda system från kända sårbarheter. Azure använder integrerade distributionssystem för att hantera distribution och installation av säkerhetsuppdateringar för Microsoft-programvara. Azure kan också utnyttja resurserna i Microsoft Security Response Center (MSRC). MSRC identifierar, övervakar, svarar på och löser säkerhetsincidenter och molnsårbarheter dygnet runt, alla dagar på året.

## <a name="vulnerability-scanning"></a>Sårbarhetsskanning
Sårbarhetsskanning utförs på serveroperativsystem, databaser och nätverksenheter. Sårbarhetsskanningar utförs på kvartalsbasis som minimum. Azure-kontrakt med oberoende bedömare för att utföra penetrationstester av Azure-gränsen. Red-team övningar utförs också rutinmässigt och resultaten används för att göra säkerhetsförbättringar.

## <a name="protective-monitoring"></a>Skyddsövervakning
Azure-säkerhet har definierat krav för aktiv övervakning. Serviceteam konfigurerar aktiva övervakningsverktyg i enlighet med dessa krav. Aktiva övervakningsverktyg inkluderar MICROSOFT Monitoring Agent (MMA) och System Center Operations Manager. Dessa verktyg är konfigurerade för att ge tidsaviseringar till Azure-säkerhetspersonal i situationer som kräver omedelbar åtgärd.

## <a name="incident-management"></a>Incidenthantering
Microsoft implementerar en process för hantering av säkerhetsincidenter för att underlätta ett samordnat svar på incidenter, om en sådan skulle inträffa.

Om Microsoft får kännedom om obehörig åtkomst till kunddata som lagras på dess utrustning eller i dess anläggningar, eller om det blir medvetet om obehörig åtkomst till sådan utrustning eller sådana anläggningar som leder till förlust, avslöjande eller ändring av kunddata, Microsoft vidtar följande åtgärder:

- Meddelar omedelbart kunden om säkerhetsincidenten.
- Undersöker omedelbart säkerhetsincidenten och ger kunderna detaljerad information om säkerhetsincidenten.
- Vidtar rimliga och snabba åtgärder för att mildra effekterna och minimera eventuella skador till följd av säkerhetsincidenten.

Ett ramverk för incidenthantering har upprättats som definierar roller och fördelar ansvarsområden. Azure security incident management team ansvarar för att hantera säkerhetsincidenter, inklusive eskalering, och säkerställa deltagande av specialiserade team vid behov. Azure-verksamhetshanterare ansvarar för att övervaka utredning och lösning av säkerhets- och sekretessincidenter.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Tillgänglighet för Azure-infrastruktur](infrastructure-availability.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)
