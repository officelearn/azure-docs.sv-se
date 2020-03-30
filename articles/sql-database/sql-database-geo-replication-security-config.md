---
title: Konfigurera säkerhet för haveriberedskap
description: Lär dig om säkerhetsaspekterna för att konfigurera och hantera säkerhet efter en databasåterställning eller en redundans till en sekundär server.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 12/18/2018
ms.openlocfilehash: 9d628583168883276e67d9e2f2fcafdce292769e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73807486"
---
# <a name="configure-and-manage-azure-sql-database-security-for-geo-restore-or-failover"></a>Konfigurera och hantera Azure SQL Database-säkerhet för geo-återställning eller redundans

I den här artikeln beskrivs autentiseringskraven för att konfigurera och styra [aktiva geo-replikerings-](sql-database-active-geo-replication.md) och [automatisk redundansgrupper](sql-database-auto-failover-group.md). Det innehåller också de steg som krävs för att ställa in användaråtkomst till den sekundära databasen. Slutligen beskrivs också hur du aktiverar åtkomst till den återställda databasen efter att ha använt [geo-återställning](sql-database-recovery-using-backups.md#geo-restore). Mer information om återställningsalternativ finns i [Översikt över affärskontinuitet](sql-database-business-continuity.md).

## <a name="disaster-recovery-with-contained-users"></a>Haveriberedskap med slutna användare

Till skillnad från traditionella användare, som måste mappas till inloggningar i huvuddatabasen, hanteras en innesluten användare helt av själva databasen. Detta har två fördelar. I scenariot för haveriberedskap kan användarna fortsätta att ansluta till den nya primära databasen eller databasen som återställts med hjälp av geoåterställning utan ytterligare konfiguration, eftersom databasen hanterar användarna. Det finns också potentiella skalbarhets- och prestandafördelar med den här konfigurationen ur ett inloggningsperspektiv. Mer information finns i [Oberoende databasanvändare – göra databasen portabel](https://msdn.microsoft.com/library/ff929188.aspx).

Den viktigaste kompromissen är att hanteringen av katastrofåterställningsprocessen i stor skala är mer utmanande. När du har flera databaser som använder samma inloggning kan det hända att fördelarna med innehållna användare inte får behålla autentiseringsuppgifterna med hjälp av användare i flera databaser. Principen för lösenordsrotation kräver till exempel att ändringar görs konsekvent i flera databaser i stället för att ändra lösenordet för inloggningen en gång i huvuddatabasen. Därför rekommenderas inte att använda inneslutna användare om du har flera databaser som använder samma användarnamn och lösenord.

## <a name="how-to-configure-logins-and-users"></a>Konfigurera inloggningar och användare

Om du använder inloggningar och användare (i stället för att innehållit användare) måste du vidta extra åtgärder för att säkerställa att samma inloggningar finns i huvuddatabasen. I följande avsnitt beskrivs de berörda stegen och ytterligare överväganden.

  >[!NOTE]
  > Det är också möjligt att använda Azure Active Directory (AAD) inloggningar för att hantera dina databaser. Mer information finns i [Azure SQL-inloggningar och användare](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

### <a name="set-up-user-access-to-a-secondary-or-recovered-database"></a>Konfigurera användaråtkomst till en sekundär eller återställd databas

För att den sekundära databasen ska kunna användas som en skrivskyddad sekundär databas och för att säkerställa korrekt åtkomst till den nya primära databasen eller databasen som återställs med hjälp av geoåterställning, måste målserverns huvuddatabas ha rätt säkerhet konfigurationen på plats före återställningen.

De specifika behörigheterna för varje steg beskrivs senare i det här avsnittet.

Förbereda användaråtkomst till en sekundär georeplikering bör utföras som en del som konfigurerar geo-replikering. Förbereda användaråtkomst till de geoåterslutna databaserna bör utföras när som helst när den ursprungliga servern är online (t.ex. som en del av DR-borren).

> [!NOTE]
> Om du växlar över eller geo-återställer till en server som inte har korrekt konfigurerade inloggningar, kommer åtkomsten till den att begränsas till serveradministratörskontot.

Ställa in inloggningar på målservern innebär tre steg som beskrivs nedan:

#### <a name="1-determine-logins-with-access-to-the-primary-database"></a>1. Bestäm inloggningar med tillgång till den primära databasen

Det första steget i processen är att avgöra vilka inloggningar som måste dupliceras på målservern. Detta sker med ett par SELECT-satser, en i den logiska huvuddatabasen på källservern och en i själva den primära databasen.

Endast serveradministratören eller en medlem av **rollen LoginManager-server** kan bestämma inloggningarna på källservern med följande SELECT-sats.

    SELECT [name], [sid]
    FROM [sys].[sql_logins]
    WHERE [type_desc] = 'SQL_Login'

Endast en medlem av db_owner databasrollen, dbo-användaren eller serveradministratören, kan fastställa alla databasanvändarobjektnamn i den primära databasen.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### <a name="2-find-the-sid-for-the-logins-identified-in-step-1"></a>2. Hitta SID för inloggningar som identifierats i steg 1

Genom att jämföra utdata från frågorna från föregående avsnitt och matcha SID-kort kan du mappa serverinloggningen till databasanvändaren. Inloggningar som har en databasanvändare med ett matchande SID har användaråtkomst till databasen som databasanvändarobjektnamn.

Följande fråga kan användas för att se alla användarens huvudnamn och deras SID i en databas. Endast en medlem i db_owner databasroll eller serveradministratör kan köra den här frågan.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> De **INFORMATION_SCHEMA** och **sys-användarna** har *NULL-SID* och **gästen** SID är **0x00**. **DBO** SID kan börja med *0x010600000000164800000000048454*, om databasen skaparen var servern admin istället för en medlem av **DbManager**.

#### <a name="3-create-the-logins-on-the-target-server"></a>3. Skapa inloggningar på målservern

Det sista steget är att gå till målservern, eller servrar, och generera inloggningar med lämpliga SID. Den grundläggande syntaxen är följande.

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Om du vill ge användaren åtkomst till den sekundära, men inte till den primära, kan du göra det genom att ändra användarlogin på den primära servern med hjälp av följande syntax.
>
> ```sql
> ALTER LOGIN <login name> DISABLE
> ```
>
> DISABLE ändrar inte lösenordet, så du kan alltid aktivera det om det behövs.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du hanterar databasåtkomst och inloggningar finns i [SQL Database security: Hantera databasåtkomst och inloggningssäkerhet](sql-database-manage-logins.md).
* Mer information om innehållna databasanvändare finns i [Inneslutna databasanvändare - Göra databasen bärbar](https://msdn.microsoft.com/library/ff929188.aspx).
* Mer information om aktiv geo-replikering finns i [Aktiv geo-replikering](sql-database-active-geo-replication.md).
* Mer information om grupper för automatisk redundans finns i [Grupper för automatisk redundans](sql-database-auto-failover-group.md).
* Information om hur du använder geoåterställning finns i [geoåterställning](sql-database-recovery-using-backups.md#geo-restore)
