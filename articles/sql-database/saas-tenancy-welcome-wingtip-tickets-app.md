---
title: "Välkommen till Wingtips app – Azure SQL Database | Microsoft Docs"
description: "Lär dig mer om databasen innehavare modeller och Wingtips SaaS exempelprogrammet för Azure SQL Database i molnmiljön."
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 094189e08002ce8d4a2f4f92a8c112eaf18ebe13
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip biljetter SaaS-program

Samma *Wingtip biljetter* program är implementerad i var och en av tre prover. Appen är en enkel händelse lista och biljetter SaaS-app riktad små handelsplatser - teatrar, föreningar osv. Varje plats en klient i appen och har sina egna data: platsen information, till en lista över händelser, kunder, biljett order, osv.  Appen, tillsammans med-hanteringsskript och självstudier innehåller ett SaaS-scenario för slutpunkt till slutpunkt. Detta innefattar etablering innehavare, övervaka och hantera prestanda, schemahantering av och mellan klient rapportering och analytics.

## <a name="three-saas-application-patterns"></a>Tre mönster för SaaS-program

Tre vesions av appen är tillgängliga. varje utforskar ett mönster för innehavare av annan databas på Azure SQL Database.  Först använder en enskild klient-program med en isolerad enskild klient-databas. Andra använder en app för flera innehavare, med en databas per klient. Tredje används en app för flera innehavare med delat databaser för flera innehavare.

![Tre innehavare mönster][image-three-tenancy-patterns]

 Varje prov innehåller-hanteringsskript och självstudiekurser som går igenom ett intervall av design och hantering av mönster du kan använda i ditt eget program.  Varje prov distribuerar i mindre att fem minuter.  Alla tre kan vara distribuerade sida-vid-sida, så kan du jämföra skillnaderna i hantering och design.

## <a name="standalone-application-pattern"></a>Fristående programmönster

Fristående app mönstret använder ett enda klient-program med en enda klient-databas för varje klient. Varje klient appen har distribuerats till en separat Azure-resursgrupp. Detta kan vara i tjänstproviderns prenumeration eller klientens prenumeration och hanteras av providern å klientens vägnar. Det här mönstret ger största klientisolering, men det är vanligtvis den vanligaste dyra eftersom det inte finns någon möjlighet att dela resurser över flera innehavare.

Kolla in den [självstudier] [ docs-tutorials-for-wingtip-sa] och kod på GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databas per klient mönster

Databasen per klient mönstret är avsett för leverantörer som har att göra med klientisolering och vill köra en centraliserad tjänst som gör att kostnadseffektiv användningen av delade resurser. En databas skapas för varje plats, eller klient och alla databaser hanteras centralt. Databaser kan finnas i elastiska pooler att tillhandahålla kostnadseffektiv och enkelt prestandahantering som utnyttjar oförutsägbart arbetsbelastning mönster av klienterna. En katalog databas innehåller mappningen mellan klienter och sina databaser. Den här mappningen hanteras med hjälp av hanteringsfunktionerna för Fragmentera av den [klientbibliotek för elastisk databas](sql-database-elastic-database-client-library.md), vilket möjliggör effektiv hantering till programmet.

Kolla in den [självstudier] [ docs-tutorials-for-wingtip-dpt] och kod på GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Delat flera innehavare databasen mönster

Flera innehavare databaser är effektiv för tjänstleverantörer som söker efter lägre kostnad per klient och okej med nedsatt klientisolering. Det här mönstret kan packa stort antal klienter i en enskild databas, minska kostnaden per klient. Nära oändlig skala är möjlig horisontell partitionering klienter över flera databasen.  En katalog databas mappar igen klienter till databaser.  

Det här mönstret kan även en hybrid-modell där du kan optimera för kostnad med flera klienter i en databas eller optimera för isolering med en enskild klient i sin egen databas. Valet kan göras på en klient genom att klient-basis antingen när klienten är etablerad eller senare, utan inverkan på programmet.

Kolla in den [självstudier] [ docs-tutorials-for-wingtip-mt] och kod på GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Nästa steg

#### <a name="conceptual-descriptions"></a>Konceptuell beskrivningar

- En mer detaljerad förklaring av programmet innehavare mönster är tillgänglig på [innehavare mönster för flera innehavare SaaS-databas][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Självstudiekurser och kod

- Fristående app:
    - [Självstudier för fristående app][docs-tutorials-for-wingtip-sa].
    - [Koden för fristående på Github][github-code-for-wingtip-sa].

- Databas per klient:
    - [Självstudier för databasen per klient][docs-tutorials-for-wingtip-dpt].
    - [Koden för databasen per klient på Github][github-code-for-wingtip-dpt].

- Delat flera innehavare:
    - [Självstudier för delat flera innehavare][docs-tutorials-for-wingtip-mt].
    - [Koden för delat flera innehavare på Github][github-code-for-wingtip-mt].



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

