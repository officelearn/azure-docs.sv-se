---
title: Konfigurera Azure SQL Database-säkerhet för katastrofåterställning | Microsoft Docs
description: Lär dig säkerhetsaspekter för att konfigurera och hantera säkerhet efter en återställning av databasen eller en växling till en sekundär server.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sashan
ms.openlocfilehash: 0796e5900bc67d93e51a0ce377ef5d1144346e2c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646872"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurera och hantera Azure SQL Database-säkerhet för geo-återställning eller växling vid fel 

Det här avsnittet beskrivs autentiseringskraven för att konfigurera och styra [aktiv geo-replikering](sql-database-geo-replication-overview.md) och de steg som krävs för att ställa in användaråtkomst till den sekundära databasen. Beskriver också hur du aktiverar åtkomst till den återställda databasen när du använder [geo-återställning](sql-database-recovery-using-backups.md#geo-restore). Mer information om alternativ för återställning finns [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).

> [!NOTE]
> [Aktiv geo-replikering](sql-database-geo-replication-overview.md) är nu tillgänglig för alla databaser i alla servicenivåer.
>  

## <a name="disaster-recovery-with-contained-users"></a>Katastrofåterställning med inneslutna användare
Till skillnad från vanliga användare, vilket måste mappas till inloggningar i master-databasen är en innesluten användare hanteras helt av själva databasen. Detta har två fördelar. Användarna kan fortsätta att ansluta till den nya primära databasen i katastrofåterställning, eller databasen återställas med hjälp av geo-återställning utan någon ytterligare konfiguration eftersom databasen hanterar användarna. Det finns också potentiella skalbarhet och prestandafördelar från den här konfigurationen ur inloggningen. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx). 

Den huvudsakliga kompromissen är att hantera återställningsprocessen i skala mer utmanande. När du har flera databaser som använder samma inloggningen kan underhålla autentiseringsuppgifterna med inneslutna användare i flera databaser negera fördelarna med inneslutna användare. Till exempel rotation lösenordsprincipen kräver att ändringar görs konsekvent i flera databaser i stället för att ändra lösenordet för inloggningen en gång i master-databasen. Därför, om du har flera databaser som använder samma användarnamn och lösenord rekommenderas med hjälp av inneslutna användare inte. 

## <a name="how-to-configure-logins-and-users"></a>Så här konfigurerar du inloggningar och användare
Om du använder inloggningar och användare (i stället för inneslutna användare), måste du vidta ytterligare åtgärder för att säkerställa att samma inloggningar finns i master-databasen. I följande avsnitt beskrivs de steg ingår och ytterligare övervägandena.

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurera användaråtkomst till en sekundär eller återställda databas
För den sekundära databasen att användas som en skrivskyddad sekundär databas och lämplig åtkomst till den nya primära databasen eller databasen återställs med geo-återställning måste, master-databasen på målservern ha lämplig säkerhet konfiguration på plats innan återställningen.

De specifika behörigheterna för varje steg beskrivs senare i det här avsnittet.

Förbereda användaråtkomst till en sekundär geo-replikering ska utföras som en del konfigurera geo-replikering. Förbereda användaråtkomst till geo återställt databaserna bör utföras när som helst när den ursprungliga servern är online (t.ex. som en del av DR-test).

> [!NOTE]
> Om du växlar över eller geo-återställning till en server som inte har korrekt konfigurerad inloggningar, åtkomst till den kommer att begränsas till server-administratörskonto.
> 
> 

Konfigurera inloggningar på målservern omfattar tre steg som beskrivs nedan:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Avgör inloggningar med åtkomst till den primära databasen:
Det första steget i processen är att avgöra vilka inloggningar måste kopieras på målservern. Detta åstadkoms med en SELECT-uttryck, i den logiska huvuddatabasen på källservern och i den primära databasen sig själv.

-Administratören eller en medlem av den **LoginManager** -serverrollen kan fastställa inloggningar på källservern med följande SELECT-sats. 

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Endast en medlem av databasrollen db_owner, dbo-användaren eller -administratören kan bestämma databasen användaren säkerhetsobjekt i den primära databasen.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Hitta SID för inloggningar som identifierades i steg 1:
Genom att jämföra resultatet av frågorna i det föregående avsnittet och matchar SID: er, kan du mappa server-inloggning till databasanvändare. Inloggningar som har en databasanvändare med ett matchande SID har användaråtkomst till databasen som den primära databas användaren. 

Följande fråga kan användas för att visa alla användare säkerhetsobjekt och deras SID i en databas. Bara medlemmar i db_owner databasen roll- eller administratören kan köra den här frågan.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Den **INFORMATION_SCHEMA** och **sys** användare har *NULL* SID, och **gäst** SID är **0x00**. Den **dbo** SID kan börja med *0x01060000000001648000000000048454*om skapat databasen var serveradministratören i stället för en medlem av **DbManager**.
> 
> 

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Skapa inloggningar på målservern:
Det sista steget är att gå till målservern eller servrar och generera inloggningar med lämpliga SID. Den grundläggande syntaxen är som följer.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Om du vill bevilja användaråtkomst till sekundärt, men inte på den primära servern kan du göra det genom att ändra användarinloggning på den primära servern med hjälp av följande syntax.
> 
> ALTER INLOGGNINGEN <login name> INAKTIVERA
> 
> Inaktivera Ändra inte lösenordet, så du kan aktivera den alltid om det behövs.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du hanterar åtkomst till databasen och inloggningar finns [SQL Database-säkerhet: hantera åtkomst och logga in databassäkerhet](sql-database-manage-logins.md).
* Mer information om oberoende databasanvändare finns [innehöll databasanvändare - göra din databas bärbara](https://msdn.microsoft.com/library/ff929188.aspx).
* Information om hur du använder och konfigurerar aktiv geo-replikering finns [aktiv geo-replikering](sql-database-geo-replication-overview.md)
* Information om hur du använder geo-återställning finns [geo-återställning](sql-database-recovery-using-backups.md#geo-restore)

