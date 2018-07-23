---
title: Tillgänglighet för Azure-infrastrukturen
description: Artikeln beskriver nivåer av redundans för att ge högsta tillgänglighet av kunders data.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 3679361522105d1f3c55eae6b1f33c1c6baa1d55
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171501"
---
# <a name="azure-infrastructure-availability"></a>Tillgänglighet för Azure-infrastrukturen
Azure tillhandahåller robust tillgängliga, baserat på omfattande redundans uppnås med virtualiseringsteknik. Azure tillhandahåller flera nivåer av redundans för att ge högsta tillgänglighet av kunders data.

## <a name="temporary-outages-and-natural-disaster"></a>Tillfälliga avbrott och naturkatastrofer
Microsoft-Molninfrastruktur and Operations-teamet designar, bygger, fungerar och förbättrar säkerheten för molninfrastrukturen. Det här laget säkerställer att Azure-infrastrukturen levererar hög tillgänglighet och tillförlitlighet, hög effektivitet och smart skalbarhet. Teamet ger ett säkrare, privata och betrodda moln.

UPS-enheter och stora banker av batterier kan du säkerställa att elektricitet förblir kontinuerlig om kortsiktig power avbrott inträffar. Vid akutfall generatorer ger kraft som säkerhetskopiering för långvariga avbrott och planerat underhåll. Om en naturkatastrof inträffar, använda datacenter på plats bränsle reserver.

Snabba och stabila fiber fiberoptiska nätverk ansluta datacenter med andra viktiga NAV och internet-användare. Compute-noder närmare värd för arbetsbelastningar för användare att minska svarstiderna, tillhandahålla geo-redundans och öka tjänstens övergripande återhämtning. Ett team med tekniker fungerar dygnet runt så att tjänster är ett beständigt sätt tillgängliga.

Microsoft garanterar hög tillgänglighet via avancerad övervakning och incidenter, stöd för tjänst och säkerhetskopiering redundans. Geografiskt distribuerade Microsoft operations Datacenter fungerar 24/7/365. Azure-nätverket är en av de största i världen. Fiber fiberoptiska och content distribution network ansluter datacenter och edge-noder för att säkerställa hög prestanda och tillförlitlighet.

## <a name="disaster-recovery"></a>Haveriberedskap
Azure håller dina data varaktiga på två platser. Du kan välja platsen för säkerhetskopieringsplatsen. På båda platserna underhåller Azure ständigt tre felfri repliker av dina data.

## <a name="database-availability"></a>Databastillgänglighet
Azure ser till att en databas är internet är tillgängliga via en internet-gateway med varaktigt databastillgänglighet. Utvärderar övervakning av hälsotillstånd och status för aktiva databaser med fem minuters tidsintervall.

## <a name="storage-availability"></a>Lagringstillgänglighet
Azure ger lagring via en mycket skalbara och hållbara storage-tjänst som tillhandahåller anslutningar slutpunkter. Det innebär att ett program kan komma åt lagringstjänsten direkt. Storage-tjänsten bearbetar inkommande lagringsbegäranden effektivt, med transaktionsintegritet.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Dataskydd för Azure-kund](azure-protection-of-customer-data.md)
