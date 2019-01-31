---
title: Välkommen till Wingtips app – Azure SQL Database | Microsoft Docs
description: Lär dig mer om databasmodeller för innehavare, och det Wingtips SaaS-exempelprogrammet, för Azure SQL Database i molnmiljön.
keywords: sql database tutorial
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b27877e25dd3bdd4711d1c036e2f203e1b8c0e7b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462151"
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip biljetter SaaS-program

Samma *Wingtip biljetter* SaaS-program är implementerad i var och en av tre beräkningarna. Appen är en enkel lista och biljetter SaaS-app som riktar in sig på små platser - biografer, föreningar osv. Varje plats en Innehavaradministratör för appen och har sina egna data: jurisdiktionsort information, till en lista över händelser, kunder, biljett order osv.  Appen, tillsammans med-hanteringsskript och självstudier, innehåller ett SaaS-scenario för slutpunkt till slutpunkt. Detta innefattar etablering innehavare, övervaka och hantera prestanda, schemahantering, och flera klienter rapportering och analys.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tre SaaS-program och innehavare mönster

Tre versioner av appen är tillgängliga. var och en utforskar ett innehavare från en annan databas på Azure SQL Database.  Först använder ett fristående program per klient med en egen databas. Andra använder en app för flera klienter med en databas per klient. Tredje exemplet använder en app för flera klienter med shardade databaser för flera innehavare.

![Tre innehavare mönster][image-three-tenancy-patterns]

 Varje exempel innehåller programkod, plus -hanteringsskript och självstudier som utforskar olika design- och hanteringsmönster.  Varje exempel distribueras inom mindre de fem minuter.  Alla tre kan vara distribuerade sida-vid-sida så att du kan jämföra skillnader i utformningen och hantering.

## <a name="standalone-application-per-tenant-pattern"></a>Fristående program per klient mönster

Fristående app per klient mönstret använder en enda klient-program med en-databas för varje klient. Varje klient appen, inklusive databasen, distribueras till en separat Azure-resursgrupp. Resursgruppen kan distribueras i tjänsteleverantörens prenumeration eller klientens prenumeration och hanteras av providern för klientens räkning. Fristående app per klient mönstret ger bästa klientisolering, men är vanligtvis de dyra eftersom det inte finns någon möjlighet att dela resurser mellan flera klienter.  Det här mönstret är utmärkt för program som kan vara mer komplexa och som har distribuerats till ett mindre antal klienter.  Med fristående distributioner, appen kan anpassas för varje klient enklare än i andra mönster.  

Kolla in den [självstudier] [ docs-tutorials-for-wingtip-sa] och kod på GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databas per klient mönster

Databas per klient mönstret är avsett för leverantörer av tjänster som är bekymrad över klientisolering och vill köra en centraliserad tjänst som gör kostnadseffektiva användningen av delade resurser. En databas har skapats för varje plats eller -klient och alla databaserna som hanteras centralt. Databaser kan finnas i elastiska pooler att tillhandahålla kostnadseffektiv och enkel prestandahantering som utnyttjar oförutsägbar arbetsbelastningmönster för klienterna. En katalogdatabas innehåller mappningen mellan klienter och deras databaser. Den här mappningen hanteras med hjälp av hanteringsfunktionerna för fragment i den [klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md), som tillhandahåller effektiv anslutningshanteringen till programmet.

Kolla in den [självstudier] [ docs-tutorials-for-wingtip-dpt] och kod på GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Mönster för fragmenterade (sharded) databas för flera innehavare

Databaser för flera innehavare är effektiva för tjänsteleverantörer söker lägre kostnad per klient och OK med lägre klientisolering. Det här mönstret kan paketera stort antal klienter till en individuell databas, att kostnaden per klient minska. Nästan obegränsad skalning är möjlig horisontell partitionering klienter över flera databaser. En katalogdatabas mappar klienter till databaser.  

Det här mönstret kan också en *hybrid* modell där du kan optimera för kostnaden med flera klienter i en databas eller optimera för isolering med en enda klient i sin egen databas. Valet kan göras på basis av klient-av-klient, antingen när klienten är etablerade eller senare, utan någon inverkan på programmet.  Den här modellen kan användas effektivt när grupper av klienter behöver behandlas annorlunda. Låg kostnad klienter kan till exempel tilldelas till delade databaser, medan premium-klienter kan tilldelas till sina egna databaser. 

Kolla in den [självstudier] [ docs-tutorials-for-wingtip-mt] och kod på GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Nästa steg

#### <a name="conceptual-descriptions"></a>Konceptuell beskrivningar

- En mer detaljerad förklaring av innehavare programmönster är tillgänglig på [SaaS med flera klientorganisationer databasen innehavare mönster][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Självstudier och kod

- Fristående app per klient:
    - [Självstudier för fristående app ] [ docs-tutorials-for-wingtip-sa].
    - [Koden för fristående app på GitHub][github-code-for-wingtip-sa].

- Databas per klient:
    - [Självstudier för databas per klient][docs-tutorials-for-wingtip-dpt].
    - [Koden för databas per klient på GitHub][github-code-for-wingtip-dpt].

- Shardade flera innehavare:
    - [Självstudier för shardade flera innehavare][docs-tutorials-for-wingtip-mt].
    - [Koden för shardade flera innehavare, på GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tre innehavare mönster."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

