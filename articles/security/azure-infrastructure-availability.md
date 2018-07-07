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
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902585"
---
# <a name="azure-infrastructure-availability"></a>Tillgänglighet för Azure-infrastrukturen
Azure ger stabil tillgänglighet baserat på omfattande redundans uppnås med virtualiseringsteknik. Azure tillhandahåller flera nivåer av redundans för att ge högsta tillgänglighet av kunders data.

## <a name="temporary-outages-and-natural-disaster"></a>Tillfälliga avbrott och naturkatastrofer
Microsoft-Molninfrastruktur and Operations-teamet designar, bygger, fungerar och skyddar molninfrastrukturen. Det här laget säkerställer att Azure-infrastrukturen levererar hög tillgänglighet och tillförlitlighet, hög effektivitet, smart skalbarhet och ett säkert, privat & betrodda moln.

UPS-enheter och stora banker av batterier kan du säkerställa att elektricitet förblir kontinuerlig om kortsiktig power avbrott inträffar.

Vid akutfall generatorer ger kraft som säkerhetskopiering för långvariga avbrott och planerat underhåll. Datacentret drivs med på plats bränsle förbehåller sig om en naturkatastrof inträffar.

Hög hastighet och robust fiber fiberoptiska nätverk ansluta datacenter med andra viktiga NAV och Internet-användare. Compute-noder närmare värd för arbetsbelastningar för slutanvändarna att minska svarstiderna, tillhandahålla geo-redundans och öka tjänstens övergripande återhämtning. Ett team med tekniker fungerar dygnet runt så att tjänster är ett beständigt sätt tillgängliga för kunder.

Microsoft garanterar hög tillgänglighet via avancerad övervakning och svar om incidenter, stöd för tjänst och säkerhetskopiera redundans. Geografiskt distribuerade Microsoft operations Datacenter fungerar 24/7/365. Azure-nätverket är en av de största i världen. Fiber fiberoptiska och content distribution network ansluter datacenter och Edge-noder för att säkerställa hög prestanda och tillförlitlighet.

## <a name="disaster-recovery"></a>Haveriberedskap
Azure håller kunddata varaktiga på två platser med den kund som har möjlighet att välja platsen för säkerhetskopieringsplatsen. På båda platserna underhåller Azure ständigt flera (3) felfri repliker av kunddata.

## <a name="database-availability"></a>Databastillgänglighet
Azure ser till att en databas är Internet är tillgängliga via en Internet-gateway med varaktigt databastillgänglighet. Utvärderar övervakning av hälsotillstånd och status för aktiva databaser med 5 minuters tidsintervall.

## <a name="storage-availability"></a>Lagringstillgänglighet
Azure ger lagring via en mycket skalbara och hållbara storage-tjänst som tillhandahåller anslutningar slutpunkter så att den kan nås direkt av en användningsprogram. Via tjänsten storage kommer förfrågningar till storage att behandlas effektivt med transaktionsintegritet.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Skydd av kunddata i Azure](azure-protection-of-customer-data.md)
