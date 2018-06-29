---
title: Tillgängligheten för Azure-infrastrukturen
description: Den här artikeln innehåller nivåer av redundans ger högsta tillgänglighet för kunders data.
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
ms.openlocfilehash: db13e4835e483b4738074a71861737c4851d8dbc
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102679"
---
# <a name="availability-of-azure-infrastructure"></a>Tillgängligheten för Azure-infrastrukturen
Azure ger robust tillgänglighet baserat på omfattande redundans uppnås med virtualiseringsteknik. Azure tillhandahåller flera nivåer av redundans ger högsta tillgänglighet för kunders data.

## <a name="temporary-outages-and-natural-disaster"></a>Tillfälliga avbrott och naturkatastrof
Microsoft-Molninfrastruktur och åtgärder teamet designerna, bygger, fungerar och skyddar molninfrastrukturen. Det här teamet garanterar att leverera Azure-infrastrukturen med hög tillgänglighet och tillförlitlighet, hög effektivitet, smart skalbarhet och ett privat, säker & betrodda moln.

Se till att el förblir kontinuerlig om ett kortsiktigt power avbrott inträffar avbrottsfri strömförsörjning och stora banker med batterierna.

Nödgeneratorer ger säkerhetskopiering kraft för långvariga avbrott och planerat underhåll. Datacenter drivs med på plats bränsle reserver om en naturkatastrof inträffar.

Hög hastighet och robusta fiber fiberoptiska nätverk ansluta datacenter med andra viktiga NAV- och Internet-användare. Compute-noder närmare arbetsbelastning på värden för slutanvändarna att minska svarstiden ger geo-redundans och öka övergripande tjänsten återhämtning. En grupp av tekniker fungerar dygnet runt så tjänster är beständigt tillgängliga för kunder.

Microsoft garanterar hög tillgänglighet via avancerad övervakning och incidenter, stöd för och säkerhetskopiera redundanskapacitet. Geografiskt distribuerade Microsoft operations centers fungerar 24/7/365. Azure-nätverk är en av de största i världen. Nätverkets fiber fiberoptiska och innehåll ansluter datacenter och kant-noder för att säkerställa hög prestanda och tillförlitlighet.

## <a name="disaster-recovery"></a>Haveriberedskap
Azure behåller kundinformation varaktiga på två platser med kunden har möjlighet att ange platsen för platsserver för säkerhetskopiering. I båda platserna underhåller Azure ständigt flera (3) felfri repliker av kundens data.

## <a name="database-availability"></a>Tillgänglighet till databas
Azure garanterar att en databas är tillgängligt via Internet-gateway med varaktigt databasens tillgänglighet Internet. Övervaka utvärderar hälsa och status för aktiva databaser med 5 minuters intervall.

## <a name="storage-availability"></a>Lagringstillgänglighet
Azure ger lagring via en skalbar och varaktig storage-tjänst som tillhandahåller slutpunkter för anslutningen så att den kan nås direkt av en användningsprogram. Via lagringstjänsten bearbetas inkommande lagringsbegäranden om effektivt med transaktionella integritet.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)
