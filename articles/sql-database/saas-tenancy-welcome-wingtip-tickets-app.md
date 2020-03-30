---
title: Välkommen till Wingtips-appen
description: Lär dig mer om databasarreningsmodeller och om exempelprogrammet Wingtips SaaS för Azure SQL Database i molnmiljön.
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
ms.date: 01/25/2019
ms.openlocfilehash: 4e0b3afe51ac7c7a6b9213fcee79af57cbbd8197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818322"
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Biljetter SaaS ansökan

Samma *Wingtip Biljetter* SaaS ansökan genomförs i vart och ett av tre prover. Appen är en enkel händelse notering och biljettförsäljning SaaS app inriktning små arenor - teatrar, klubbar, etc. Varje plats är en hyresgäst i appen, och har sina egna uppgifter: plats detaljer, listor över händelser, kunder, biljettorder, etc.  Appen, tillsammans med hanteringsskript och självstudier, visar upp ett heltäckande SaaS-scenario. Detta inkluderar etablering av klienter, övervakning och hantering av prestanda, schemahantering och rapportering och analys mellan klienter.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tre SaaS-applikations- och hyresmönster

Tre versioner av appen är tillgängliga. varje utforskar olika databasarreningsmönster på Azure SQL Database.  Den första använder ett fristående program per klient med egen databas. Den andra använder en app med flera innehavare med en databas per klient. Det tredje exemplet använder en app med flera innehavare med fragmenterade databaser med flera innehavare.

![Tre hyresmönster][image-three-tenancy-patterns]

 Varje exempel innehåller programkoden, plus hanteringsskript och självstudier som utforskar en rad design- och hanteringsmönster.  Varje prov distribueras på mindre än fem minuter.  Alla tre kan distribueras sida vid sida så att du kan jämföra skillnaderna i design och hantering.

## <a name="standalone-application-per-tenant-pattern"></a>Fristående program per klientmönster

Den fristående appen per klientmönster använder ett enda klientprogram med en databas för varje klient. Varje klientapp, inklusive dess databas, distribueras till en separat Azure-resursgrupp. Resursgruppen kan distribueras i tjänsteleverantörens prenumeration eller klientens prenumeration och hanteras av leverantören för klientens räkning. Den fristående appen per klientmönster ger den största klientisoleringen, men är vanligtvis den dyraste eftersom det inte finns någon möjlighet att dela resurser mellan flera klienter.  Det här mönstret passar väl för program som kan vara mer komplexa och som distribueras till mindre antal klienter.  Med fristående distributioner kan appen anpassas för varje klient enklare än i andra mönster.  

Kolla in [självstudier][docs-tutorials-for-wingtip-sa] och kod på GitHub [.../Microsoft/WingtipTicketsSSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Databas per klientmönster

Databasen per klientmönster är effektiv för tjänstleverantörer som sysslar med klientisolering och vill köra en centraliserad tjänst som möjliggör kostnadseffektiv användning av delade resurser. En databas skapas för varje plats eller klient, och alla databaser hanteras centralt. Databaser kan finnas i elastiska pooler för att tillhandahålla kostnadseffektiv och enkel prestandahantering, vilket utnyttjar klienternas oförutsägbara arbetsbelastningsmönster. En katalogdatabas innehåller mappningen mellan klienter och deras databaser. Den här mappningen hanteras med hjälp av shard map management-funktionerna i [klientbiblioteket för elastisk databas](sql-database-elastic-database-client-library.md), som ger effektiv anslutningshantering till programmet.

Kolla in [självstudier][docs-tutorials-for-wingtip-dpt] och kod på GitHub [.../Microsoft/WingtipTicketsSAaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Sharded databasmönster för flera innehavare

Databaser med flera innehavare är effektiva för tjänsteleverantörer som söker lägre kostnad per klient och okej med minskad klientisolering. Det här mönstret gör det möjligt att packa ett stort antal klienter i en enskild databas, vilket gör att kostnaden per klient nere. Nästan oändlig skala är möjligt genom att sharding klienterna över flera databaser. En katalogdatabas mappar klienter till databaser.  

Det här mönstret möjliggör också en *hybridmodell* där du kan optimera för kostnad med flera klienter i en databas, eller optimera för isolering med en enda klient i sin egen databas. Valet kan göras på klient-för-klient basis, antingen när klienten är etablerad eller senare, utan påverkan på programmet.  Den här modellen kan användas effektivt när grupper av klienter måste behandlas olika. Till exempel kan billiga klienter tilldelas till delade databaser, medan premiumklienter kan tilldelas sina egna databaser. 

Kolla in [självstudier][docs-tutorials-for-wingtip-mt] och kod på GitHub [.../Microsoft/WingtipTicketsSSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Nästa steg

#### <a name="conceptual-descriptions"></a>Begreppsmässiga beskrivningar

- En mer detaljerad förklaring av programhyresmönster finns på [Multi-tenant SaaS databas hyresmönster][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Självstudier och kod

- Fristående app per klient:
    - [Självstudier för fristående app][docs-tutorials-for-wingtip-sa].
    - [Kod för fristående app, på GitHub][github-code-for-wingtip-sa].

- Databas per klient:
    - [Självstudier för databas per klient .][docs-tutorials-for-wingtip-dpt]
    - [Kod för databas per klient, på GitHub][github-code-for-wingtip-dpt].

- Fragmenterad flera innehavare:
    - [Självstudier för fragmenterade flera innehavare][docs-tutorials-for-wingtip-mt].
    - [Kod för fragmenterad flera innehavare, på GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tre hyresmönster."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

