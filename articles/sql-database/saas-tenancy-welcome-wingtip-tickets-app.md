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
ms.date: 11/12/2017
ms.author: billgib;genemi
ms.openlocfilehash: e10a954ba57782f4f79131ab583b5a73edf4ba02
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Välkommen till Wingtip biljetter SaaS Azure SQL Database innehavare exempelappen

Välkommen till Wingtip biljetter SaaS Azure SQL Database innehavare exempelprogrammet och dess självstudier. Databasen innehavare refererar till isoleringsläge för data som din app tillhandahåller till dina klienter som ska finnas i ditt program. För att förenkla för tillfället över, antingen varje klient har hela databasen till sig själv eller den delar en databas med andra klienter.

## <a name="wingtip-tickets-app"></a>Wingtip biljetter app

Exempelprogrammet Wingtip biljetter illustrerar effekterna av en annan databas innehavare modeller på designen och hantering av flera innehavare SaaS-program. Medföljande självstudier beskriver direkt dessa samma effekter. Wingtip biljetter bygger på Azure SQL Database.

Wingtip biljetter är utformat för att hantera olika design- och scenarier som används av den faktiska SaaS klienter. Mönster för användning som kläckas redovisas i Wingtip biljetter.

Du kan installera appen Wingtip biljetter i din egen Azure-prenumeration inom fem minuter. Installationen innehåller infogning av exempeldata för flera innehavare. Du kan på ett säkert sätt installera program och av hanteringsskript för alla modeller, eftersom de inte samverka eller störa varandra.

#### <a name="code-in-github"></a>Koden i Github

Programkod och av hanteringsskript, är alla tillgängliga på GitHub:

- **Fristående app** modell: *(kommer i dagar).*
- **Databasen per klient** modell: [WingtipSaaS databasen](https://github.com/Microsoft/WingtipSaaS/).
- **Delat flera innehavare** modellen i *hybrid*: *(kommer i dagar).*

Samma en kodbas för appen Wingtip biljetter återanvänds för alla föregående modellerna visas. Du kan använda koden från Github för att starta SaaS projekt.



## <a name="major-database-tenancy-models"></a>Större databas innehavare modeller

Wingtip biljetter är en händelse lista och biljetter SaaS-program. Wingtip tillhandahåller tjänster som krävs av handelsplatser. Följande objekt gäller för varje plats:

- Betalar du ska finnas i ditt program.
- Är en *klient* i företaget.
- Värdar händelser. Följande händelser ingår:
    - Biljettpriser.
    - Biljettförsäljning.
    - Kunder som köper biljetter.

Appen, tillsammans med-hanteringsskript och självstudier innehåller en fullständig SaaS-scenariot. Scenariot omfattar följande aktiviteter:

- Etablering av klienter.
- Övervaka och hantera prestanda.
- Schemahantering av.
- Mellan klient, rapportering och analys.

Alla dessa aktiviteter finns i oavsett skala krävs.



## <a name="code-samples-for-each-tenancy-model"></a>Kodexempel för varje innehavare modell

En uppsättning programmodeller framhävs. Andra implementeringar kan dock alla elementen av två eller flera modeller.

#### <a name="standalone-app-model"></a>Fristående app-modellen

![Fristående app-modellen][standalone-app-model-62s]

Den här modellen används ett enda klient-program. Därför kan den här modellen måste bara en databas och lagras data för endast en klient. Klienten har slutförts isolerade från andra klienter i databasen.

Du kan använda den här modellen när du säljer instanser av din app på många olika klienter, för varje klient ska köras på egen hand. Klienten är den enda innehavaren. När databasen lagrar data för endast en klient, lagrar data i databasen för många kunder av klienten.

- *(Självstudier för den här modellen kommer att publiceras här inom några dagar. En länk ska vara här.)*

#### <a name="database-per-tenant"></a>Databasen per klient

![Databas per klient modellen][database-per-tenant-model-35d]

Den här modellen har flera klienter i instans av programmet. Ännu för varje ny klient har en annan databas allokerats för användning bara av den nya innehavaren.

Den här modellen ger fullständig isolering för varje klient. Azure SQL Database-tjänsten har nivå så att den här modellen rimligt.

- [Introduktion till en SQL-databas med flera innehavare SaaS app exempel] [ saas-dbpertenant-wingtip-app-overview-15d] -har mer information om den här modellen.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>Delat databaser för flera innehavare, hybrid

![Delat flera innehavare databasmodell, hybrid][sharded-multitenantdb-model-hybrid-79m]

Den här modellen har flera klienter i instans av programmet. Den här modellen har också flera innehavare i ett eller flera av dess databaser. Den här modellen är bra för att erbjuda olika tjänstnivåer så att klienter kan betala mer om värdet fullständig isolering av databasen.

Schemat för varje databas innehåller ett klient-ID. Klient-ID måste även i de databaser som lagrar bara en klient.

- *(Självstudier för den här modellen kommer att publiceras här inom några dagar. En länk ska vara här.)*




## <a name="tutorials-for-each-tenancy-model"></a>Självstudier för varje innehavare modell

Varje innehavare modell dokumenteras med följande:

- En uppsättning självstudiekursen artiklar.
- Källkoden som lagras i en Github-repo som är dedikerad till modellen:
    - Koden för programmet Wingtip biljetter.
    - Skriptkod scenarier.

#### <a name="tutorials-for-management-scenarios"></a>Självstudier för scenarier för hantering

I självstudiekursen artiklar för varje modell omfatta i följande scenarier:

- Innehavaren etablering.
- Övervaka prestanda och hantering.
- Schemahantering av.
- Mellan klient, rapportering och analys.
- Återställning av en klient till en tidigare tidpunkt.
- Haveriberedskap.



## <a name="next-steps"></a>Nästa steg

- [Introduktion till en SQL-databas med flera innehavare SaaS app exempel] [ saas-dbpertenant-wingtip-app-overview-15d] -har mer information om den här modellen.

- [Flera innehavare SaaS databasen innehavare mönster][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Fristående app-modellen"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Databas per klient modellen"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Delat flera innehavare databasmodell, hybrid"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

