---
title: Azure-infrastruktursäkerhet | Microsoft Docs
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903864"
---
# <a name="azure-infrastructure-security"></a>Säkerhet för Azure-infrastrukturen
Microsoft Azure körs på datacenter som hanteras och drivs av Microsoft. Dessa geografiskt utspridda datacenter som är kompatibel med branschstandard, t.ex ISO/IEC 27001: 2013 och SP NIST 800-53, gällande säkerhet och tillförlitlighet. Datacenter hanteras, övervakas och administreras av Microsofts driftpersonal. Driftpersonalen har års erfarenhet av att leverera världens största onlinetjänster med 24 x 7 affärskontinuitet.

Den här artikelserien innehåller information om vad Microsoft gör för att skydda Azure-infrastrukturen. Artiklar-adress:

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

## <a name="shared-responsibility-model"></a>Delat ansvar modell
Det är viktigt att förstå divisionen av ansvar mellan dig och Microsoft. Lokalt, du äger i hela stacken men när du migrerar till molnet några ansvarsområden Överför till Microsoft. Följande ansvar matrisen visar områden i stacken i en programvara som en tjänst (SaaS), plattform som en tjänst (PaaS) och infrastruktur som en tjänst (IaaS) distribution som du är ansvarig för och Microsoft är ansvarig för.

![Delat ansvar][1]

Ansvarsområden som behålls alltid av dig, oavsett vilken typ av distribution, är:

- Data
- Slutpunkter
- Konto
- Åtkomsthantering

Var noga med att du förstår divisionen av ansvar mellan dig och Microsoft i en SaaS, PaaS och IaaS-distribution. Se [delat ansvar för molnbaserad databehandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) för mer information.

## <a name="next-steps"></a>Nästa steg
Mer information om vad Microsoft gör för att skydda Azure-infrastrukturen finns:

- [Azure anläggningar, plats och fysisk säkerhet](azure-physical-security.md)
- [Tillgänglighet för Azure-infrastrukturen](azure-infrastructure-availability.md)
- [Azure information systemkomponenter och gränser](azure-infrastructure-components.md)
- [Arkitektur för Azure-nätverk](azure-infrastructure-network.md)
- [Azure-produktionsnätverket](azure-production-network.md)
- [Microsoft Azure SQL Database-säkerhetsfunktioner](azure-infrastructure-sql.md)
- [Azure produktionsåtgärder och hantering](azure-infrastructure-operations.md)
- [Övervakning av Azure-infrastrukturen](azure-infrastructure-monitoring.md)
- [Azure-infrastrukturen integritet](azure-infrastructure-integrity.md)
- [Skydd av kunddata i Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
