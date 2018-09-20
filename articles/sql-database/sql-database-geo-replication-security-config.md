---
title: Konfigurera Azure SQL Database-säkerhet för katastrofåterställning | Microsoft Docs
description: Lär dig säkerhetsåtgärder för att konfigurera och hantera säkerhet efter en återställning av databasen eller redundans till en sekundär server.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: 03bb4e2e8d202e13edf41c925b9341436515d8a8
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465630"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurera och hantera Azure SQL Database-säkerhet för geo-återställning eller redundans 

Det här avsnittet beskriver autentiseringskraven för att konfigurera och styra [aktiv geo-replikering](sql-database-geo-replication-overview.md) och de steg som krävs för att konfigurera åtkomst till den sekundära databasen. Det beskriver också hur du aktiverar åtkomst till den återställda databasen när du har använt [geo-återställning](sql-database-recovery-using-backups.md#geo-restore). Mer information om alternativ för Systemåterställning finns [översikt över affärskontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> [Aktiv geo-replikering](sql-database-geo-replication-overview.md) är nu tillgänglig för alla databaser på alla tjänstnivåer.
>  

## <a name="disaster-recovery-with-contained-users"></a>Haveriberedskap med inneslutna användare
Till skillnad från traditionella användare, vilket måste mappas till inloggningar i huvuddatabasen, är en innesluten användare hanteras helt av själva databasen. Detta har två fördelar. Användarna kan fortsätta att ansluta till den nya primära databasen i katastrofåterställning, eller databasen återställas med hjälp av geo-återställning utan någon ytterligare konfiguration eftersom databasen hanterar användare. Det finns även potentiella skalbarhets- och prestandafördelarna från den här konfigurationen från en inloggning perspektiv. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx). 

Den huvudsakliga kompromiss är att hantera återställningsprocessen i stor skala är mer utmanande. När du har flera databaser som använder samma inloggning, kan underhålla autentiseringsuppgifterna med inneslutna användare i flera databaser negera fördelarna med inneslutna användare. Till exempel rotation lösenordsprincip kräver att ändringarna görs konsekvent i flera databaser i stället för att ändra lösenordet för inloggningen en gång i master-databasen. Därför, om du har flera databaser som använder samma användarnamn och lösenord, rekommenderas med hjälp av inneslutna användare inte. 

## <a name="how-to-configure-logins-and-users"></a>Så här konfigurerar du inloggningar och användare
Om du använder inloggningar och användare (i stället för inneslutna användare), måste du vidta ytterligare åtgärder för att se till att samma inloggningar finns i master-databasen. I följande avsnitt beskrivs de steg som ingår och ytterligare övervägandena.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurera användaråtkomst till en sekundär eller återställda databas
För den sekundära databasen ska kunna användas som en skrivskyddad sekundär databas och säkerställa åtkomst till den nya primära databasen eller databasen återställs med geo-återställning måste, master-databasen på målservern ha lämplig säkerhet konfiguration på plats innan återställningen.

De specifika behörigheterna för varje steg beskrivs senare i det här avsnittet.

Förbereda användaråtkomst till en sekundär geo-replikering ska utföras som en del konfigurera geo-replikering. Förbereda användaråtkomst till geo-återställa databaser bör utföras när som helst när den ursprungliga servern är online (t.ex. som en del av DR-test).

> [!NOTE]
> Om du växlar över eller geo-återställning till en server som inte har korrekt konfigurerade inloggningar, åtkomst till den begränsas till serveradministratörskonto.
> 
> 

Konfigurera inloggningar på målservern omfattar tre steg som beskrivs nedan:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Fastställa inloggningar med åtkomst till den primära databasen:
Det första steget i processen är att avgöra vilka inloggningar måste kopieras på målservern. Detta åstadkoms med ett par med SELECT-uttryck, i den logiska huvuddatabasen på källservern och en på den primära databasen själva.

Endast serveradministratör eller en medlem av den **LoginManager** serverrollen kan fastställa inloggningar på källservern med följande SELECT-sats. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Endast en medlem i rollen db_owner, dbo-användaren eller -serveradministratören kan fastställa alla huvudkonton för användare i den primära databasen.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Hitta SID för inloggningar som identifierades i steg 1:
Genom att jämföra resultatet av frågorna i föregående avsnitt och matchar SID: er, kan du mappa den server-inloggningen till databasanvändare. Inloggningar som har en databasanvändare med ett matchande SID har åtkomst till databasen som användaren databasen huvudnamn. 

Följande fråga kan användas för att se alla användares huvudnamn och deras SID i en databas. Endast en medlem i db_owner databasen rollen eller administratören kan köra den här frågan.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Den **INFORMATION_SCHEMA** och **sys** användare har *NULL* SID, och **gäst** SID är **0x00**. Den **dbo** SID kan börja med *0x01060000000001648000000000048454*, om skapat databasen var serveradministratör i stället för en medlem i **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Skapa inloggningarna på målservern:
Det sista steget är att gå till målservern eller servrar och generera inloggningar med lämpliga SID. Grundläggande syntax är som följer.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Om du vill bevilja användaråtkomst till sekundärt, men inte till primärt kan du göra det genom att ändra användarinloggning på den primära servern med hjälp av följande syntax.
> 
> ALTER LOGIN <login name> INAKTIVERA
> 
> Inaktivera Ändra inte lösenordet, så att du kan aktivera den alltid om det behövs.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du hanterar åtkomst till databasen och inloggningar finns i [SQL Database-säkerhet: hantera åtkomst och logga in databassäkerhet](sql-database-manage-logins.md).
* Läs mer på oberoende databasanvändare, [oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).
* Läs om hur du använder och konfigurerar aktiv geo-replikering [aktiv geo-replikering](sql-database-geo-replication-overview.md)
* Information om hur du använder geo-återställning finns i [geo-återställning](sql-database-recovery-using-backups.md#geo-restore)

