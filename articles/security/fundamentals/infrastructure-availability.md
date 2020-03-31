---
title: Tillgänglighet för Azure-infrastruktur – Azure-säkerhet
description: Den här artikeln innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen och ge maximal tillgänglighet för kundernas data.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727219"
---
# <a name="azure-infrastructure-availability"></a>Tillgänglighet för Azure-infrastruktur
Den här artikeln innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen och ge maximal tillgänglighet för kundernas data. Azure ger robust tillgänglighet, baserat på omfattande redundans som uppnåtts med virtualiseringsteknik.

## <a name="temporary-outages-and-natural-disaster"></a>Tillfälliga avbrott och naturkatastrofer
Microsofts cloud infrastructure and operations-team designar, bygger, driver och förbättrar säkerheten för molninfrastrukturen. Det här teamet ser till att Azure-infrastrukturen ger hög tillgänglighet och tillförlitlighet, hög effektivitet och smart skalbarhet. Teamet tillhandahåller ett säkrare, privat och pålitligare moln.

Avbrottsfri strömförsörjning och stora banker av batterier se till att el förblir kontinuerlig om en kortsiktig strömavbrott inträffar. Nödgeneratorer ger reservkraft för utökade avbrott och planerat underhåll. Om en naturkatastrof inträffar kan datacentret använda bränslereserver på plats.

Höghastighets- och robusta fiberoptiska nätverk ansluter datacenter med andra större nav och internetanvändare. Beräkningsnoder är värd för arbetsbelastningar som är närmare användare för att minska svarstiden, tillhandahålla georedundans och öka den totala tjänstens återhämtning. Ett team av ingenjörer arbetar dygnet runt för att säkerställa att tjänsterna är ständigt tillgängliga.

Microsoft säkerställer hög tillgänglighet genom avancerad övervakning och incidenthantering, servicesupport och säkerhetskopieringsväxlingskapacitet. Geografiskt distribuerade Microsoft-driftcenter fungerar 24/7/365. Azure-nätverket är ett av de största i världen. Det fiberoptiska och innehållsdistributionsnätverket ansluter datacenter och kantnoder för att säkerställa hög prestanda och tillförlitlighet.

## <a name="disaster-recovery"></a>Haveriberedskap
Azure håller dina data hållbara på två platser. Du kan välja plats för säkerhetskopieringsplatsen. På båda platserna underhåller Azure ständigt tre felfria repliker av dina data.

## <a name="database-availability"></a>Databastillgänglighet
Azure säkerställer att en databas är internet tillgänglig via en internetgateway med ihållande databastillgänglighet. Övervakningen bedömer hälsotillståndet och tillståndet för de aktiva databaserna med fem minuters tidsintervall.

## <a name="storage-availability"></a>Lagringstillgänglighet
Azure levererar lagring via en mycket skalbar och hållbar lagringstjänst, som tillhandahåller anslutningsslutpunkter. Det innebär att ett program kan komma åt lagringstjänsten direkt. Lagringstjänsten bearbetar inkommande lagringsbegäranden effektivt, med transaktionsintegritet.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns i:

- [Azure-anläggningar, lokaler och fysisk säkerhet](physical-security.md)
- [Komponenter och gränser för Azure-informationssystem](infrastructure-components.md)
- [Azure-nätverksarkitektur](infrastructure-network.md)
- [Azure-produktionsnätverk](production-network.md)
- [Säkerhetsfunktioner i Azure SQL Database](infrastructure-sql.md)
- [Azure-produktionsåtgärder och -hantering](infrastructure-operations.md)
- [Övervakning av Azure-infrastruktur](infrastructure-monitoring.md)
- [Azure-infrastrukturintegritet](infrastructure-integrity.md)
- [Azure-kunddataskydd](protection-customer-data.md)
