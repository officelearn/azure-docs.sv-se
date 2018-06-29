---
title: Säkerheten för Azure-infrastrukturen | Microsoft Docs
description: Artikeln beskriver hur Microsoft garanterar säkerheten för våra Azure-datacenter.
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
ms.openlocfilehash: 313fbc0fea317e8888bf64e7f7817ab0e5c9f049
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102699"
---
# <a name="security-of-azure-infrastructure"></a>Säkerheten för Azure-infrastrukturen
Microsoft Azure körs i datacenter hanteras och drivas av Microsoft. Dessa geografiskt spridda Datacenter uppfylla viktiga branschstandarder, till exempel ISO/IEC 27001: 2013 och NIST SP 800-53 för säkerhet och tillförlitlighet. Datacenter hanteras, övervakas och hanteras av Microsoft-driftspersonal. Driftspersonal har års erfarenhet av att leverera världens största onlinetjänster med 24 x 7 verksamhetskontinuitet.

Dessa artiklar innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen. Artiklar-adress:

- [Fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet](azure-infrastructure-availability.md)
- [Komponenter och gränser](azure-infrastructure-components.md)
- [Nätverksarkitektur](azure-infrastructure-network.md)
- [Produktionsnätverk](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Åtgärder](azure-infrastructure-operations.md)
- [Övervakning](azure-infrastructure-monitoring.md)
- [Integritet](azure-infrastructure-integrity.md)
- [Dataskydd](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Delat ansvar modellen
Det är viktigt att förstå divisionen av ansvar mellan dig och Microsoft. Lokal du äger hela stacken men när du flyttar till molnet några ansvarsområden Överför till Microsoft. Följande ansvar matrisen visar områden för stacken i en programvara som en tjänst (SaaS), plattform som en tjänst (PaaS) och en infrastruktur som en tjänst (IaaS) distribution som du är ansvarig för och Microsoft ansvarar för.

![Delat ansvar][1]

Ansvarsområden som behålls alltid av du, oavsett vilken typ av distribution är:

- Data
- Slutpunkter
- Konto
- Åtkomsthantering

Var noga med att du förstår divisionen av ansvar mellan dig och Microsoft i en SaaS-PaaS och IaaS-distribution. Se [delade ansvarsområden för Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure verksamhet, lokal och fysisk säkerhet](azure-physical-security.md)
- [Tillgängligheten för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktion och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Integriteten hos Azure-infrastrukturen](azure-infrastructure-integrity.md)
- [Skydd av kundinformation i Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
