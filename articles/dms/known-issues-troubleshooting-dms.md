---
title: Artikel om kända fel sökning av vanliga problem/fel som är associerade med att använda Azure Database Migration Service | Microsoft Docs
description: Läs om hur du felsöker vanliga kända problem/fel som är associerade med att använda Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/18/2019
ms.openlocfilehash: a72a0e26c2af34942faaa6a7e62feef86082e196
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69034831"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Felsök vanliga Azure Database Migration Service problem och fel

I den här artikeln beskrivs några vanliga problem och fel som Azure Database Migration Service användare kan komma över. Artikeln innehåller också information om hur du löser problemen och felen.

## <a name="migration-activity-in-queued-state"></a>Migrering i kö-tillstånd

När du skapar nya aktiviteter i ett Azure Database Migration Service-projekt behålls aktiviteterna i ett köat tillstånd.

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här problemet uppstår när Azure Database Migration Service-instansen har nått maximal kapacitet för pågående aktiviteter som körs samtidigt. Alla nya aktiviteter placeras i kö tills kapaciteten blir tillgänglig. | Verifiera att instansen för datamigrering har aktiviteter som körs mellan projekt. Du kan fortsätta att skapa nya aktiviteter som automatiskt läggs till i kön för körning. När någon av de pågående aktiviteterna är slutförd börjar nästa köade aktivitet att köras och statusen ändras till kör tillstånd automatiskt. Du behöver inte vidta några ytterligare åtgärder för att starta migreringen av köade aktiviteter.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Högsta antal databaser som valts för migrering

Följande fel inträffar när du skapar en aktivitet för ett databas migreringsjobb för att flytta till Azure SQL Database eller en Azure SQL Database Hanterad instans:

* **Fel**: Verifierings fel för migreringsjobb "," errorDetail ":" fler än Max antal objekt i "databaser" har marker ATS för migrering. "

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet visas när du har valt fler än fyra databaser för en enda migrerings aktivitet. Vid tillfället är varje migreringsjobb begränsad till fyra databaser. | Välj fyra eller färre databaser per migrerings aktivitet. Om du behöver migrera fler än fyra databaser parallellt etablerar du en annan instans av Azure Database Migration Service. För närvarande stöder varje prenumeration upp till två Azure Database Migration Service instanser.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Fel för MySQL-migrering till Azure MySQL med återställnings fel

När du migrerar från MySQL till Azure Database for MySQL med Azure Database Migration Service, Miss lyckas migreringen med följande fel:

* **Fel**: Fel i databasreplikering-aktiviteten ' TaskID ' pausades på grund av [n] fel vid misslyckad återställning.

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet kan inträffa när användaren som utför migreringen saknar ReplicationAdmin-roll och/eller behörighet för REPLIKERINGSTJÄNSTEN, replikering och SUPER (versioner som är äldre än MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Se till att de [nödvändiga behörigheterna](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) för användar kontot har kon figurer ATS korrekt på Azure Database for MySQL-instansen. Följande steg kan till exempel följas för att skapa en användare med namnet "MigrateUser" med de behörigheter som krävs:<br>1. Skapa ANVÄNDAREns MigrateUser @ '% ' som identifieras av ' Secret '; <br>2. Bevilja alla behörigheter för DB_NAME. * till ' MigrateUser ' @ '% ' som identifieras av ' Secret '; Upprepa det här steget om du vill bevilja åtkomst för fler databaser <br>3. Bevilja replikering slaven på *.* till ' MigrateUser ' @ '% ' identifieras av ' Secret ';<br>4. Bevilja replikerings klient på *.* till ' MigrateUser ' @ '% ' identifieras av ' Secret ';<br>5. Rensa privilegier; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Fel vid försök att stoppa Azure Database Migration Service

Du får följande fel meddelande när du stoppar Azure Database Migration Service-instansen:

* **Fel**: Det gick inte att stoppa tjänsten. Fel: {'error':{'code':'InvalidRequest','message':'En eller flera aktiviteter körs för närvarande. Stoppa tjänsten genom att vänta tills aktiviteterna har slutförts eller stoppa aktiviteterna manuellt och försök igen.}}

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet visas när tjänst instansen som du försöker stoppa innehåller aktiviteter som fortfarande körs eller finns i migreringsjobb. <br><br><br><br><br><br> | Se till att det inte finns några aktiviteter som körs i instansen av Azure Database Migration Service som du försöker stoppa. Du kan också ta bort aktiviteterna eller projekten innan du försöker stoppa tjänsten. Följande steg illustrerar hur du tar bort projekt för att rensa migreringsprocessen genom att ta bort alla aktiviteter som körs:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-SubscriptionName "\<undernamn >" <br> 4. Remove-AzureRmDataMigrationProject-name \<projectName >-ResourceGroupName \<rgName >-ServiceName \<ServiceName >-DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Fel vid försök att starta Azure Database Migration Service

Du får följande fel meddelande när du startar Azure Database Migration Service-instansen:

* **Fel**: Det går inte att starta tjänsten. Fel: {' errorDetail ': ' tjänsten kunde inte starta, kontakta Microsoft-supporten '}

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet visar när den föregående instansen misslyckades internt. Det här felet uppstår sällan och teknik teamet är medveten om det. <br> | Ta bort instansen av tjänsten som du inte kan starta och etablera sedan en ny för att ersätta den. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Fel vid återställning av databas vid migrering av SQL till Azure SQL DB-hanterad instans

När du utför en online-migrering från SQL Server till en Azure SQL Database Hanterad instans, Miss lyckas start punkt med följande fel:

* **Fel**: Det gick inte att återställa åtgärds-ID: t ' operationId '. Kod ' AuthorizationFailed ', meddelande ' klienten ' clientId ' med objekt-ID ' objectId ' har inte behörighet att utföra åtgärden ' Microsoft. SQL/locations/managedDatabaseRestoreAzureAsyncOperation/Read ' över omfattning '/subscriptions/ subscriptionId ".".

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här felet anger att det program objekt som används för online-migrering från SQL Server till en Azure SQL Database Hanterad instans inte har behörigheten delta i prenumerationen. Vissa API-anrop med hanterade instanser kräver den här behörigheten för prenumerationen för återställnings åtgärden. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Använd PowerShell-cmdleten `-ObjectId` som är tillgänglig i fel meddelandet för att Visa visnings namnet för det program-ID som används. `Get-AzureADServicePrincipal`<br><br> Verifiera behörigheterna för det här programmet och se till att det har [rollen deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) på prenumerations nivån. <br><br> Azure Database Migration Service teknik teamet arbetar för att begränsa den nödvändiga åtkomsten från den aktuella Contribute-rollen för prenumerationen. Om du har ett affärs krav som inte tillåter användning av Contribute-rollen kan du kontakta Azure-supporten om du vill ha mer hjälp. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Fel vid borttagning av NIC som är associerat med Azure Database Migration Service

När du försöker ta bort ett nätverkskort som är kopplat till Azure Database Migration Service, Miss lyckas borttagnings försöket med det här felet:

* **Fel**: Det går inte att ta bort NÄTVERKSKORTet som är kopplat till Azure Database Migration Service på grund av den DMS-tjänst som använder NÄTVERKSKORTet

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här problemet uppstår när Azure Database Migration Service-instansen kanske fortfarande finns och utnyttjar NÄTVERKSKORTet. <br><br><br><br><br><br><br><br> | Ta bort det här NÄTVERKSKORTet genom att ta bort DMS-tjänstinstansen som automatiskt tar bort NÄTVERKSKORTet som används av tjänsten.<br><br> **Viktigt**: Kontrol lera att Azure Database Migration Service-instansen som tas bort inte innehåller några aktiviteter som körs.<br><br> När alla projekt och aktiviteter som är kopplade till Azure Database Migration Service-instansen har tagits bort kan du ta bort tjänst instansen. NÄTVERKSKORTet som används av tjänst instansen rensas automatiskt som en del av borttagningen av tjänsten. |

## <a name="connection-error-when-using-expressroute"></a>Anslutnings fel vid användning av ExpressRoute

När du försöker ansluta till källan i guiden Azure Database Migration Service-projekt misslyckas anslutningen efter en lång timeout om källan använder ExpressRoute för anslutning.

| Orsak         | Lösning    |
| ------------- | ------------- |
| När du använder [ExpressRoute](https://azure.microsoft.com/services/expressroute/) [kräver](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) Azure Database migration service etablering av tre tjänst slut punkter i det Virtual Network undernät som är associerat med tjänsten:<br> --Service Bus slut punkt<br> --Lagrings slut punkt<br> --Slut punkt för mål databas (t. ex. SQL-slutpunkt, Cosmos DB slut punkt)<br><br><br><br><br> | [Aktivera](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) de tjänst slut punkter som krävs för ExpressRoute-anslutning mellan källa och Azure Database migration service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Timeout-fel vid låsning vid migrering av en MySQL-databas till Azure DB för MySQL

När du migrerar en MySQL-databas till en Azure Database for MySQL-instans via Azure Database Migration Service, Miss lyckas migreringen med följande timeout-värde för låsnings vänte tid:

* **Fel**: Fel vid databas migrering – det gick inte att läsa in filen – det gick inte att starta inläsnings processen för filen ' n ' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 meddelande: [MySQL] [ODBC-drivrutin] [mysqld] lås vänte tid har överskridits; försök att starta om transaktionen

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här felet uppstår när migreringen Miss lyckas på grund av timeout vid lås väntan under migreringen. | Överväg att öka värdet för Server parametern **"innodb_lock_wait_timeout"** . Det högsta tillåtna värdet är 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Fel vid anslutning till käll SQL Server när dynamisk port eller namngiven instans används

När du försöker ansluta Azure Database Migration Service till SQL Server källa som körs på antingen namngiven instans eller en dynamisk port, Miss lyckas anslutningen med det här felet:

* **Fel**:-1-SQL-anslutning misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrol lera att instans namnet är rätt och att SQL Server har kon figurer ATS för att tillåta fjärr anslutningar. CSP SQL-nätverks gränssnitt, fel: 26-fel vid sökning efter angiven Server/instans)

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här problemet uppstår när käll SQL Servers instansen som Azure Database Migration Service försöker ansluta till antingen har en dynamisk port eller använder en namngiven instans. Tjänsten SQL Server Browser lyssnar på UDP-port 1434 för inkommande anslutningar till en namngiven instans eller när du använder en dynamisk port. Den dynamiska porten kan ändras varje gången SQL Server tjänsten startas om. Du kan kontrol lera den dynamiska port som tilldelats till en instans via nätverks konfiguration i Konfigurationshanteraren för SQL Server.<br><br><br> |Kontrol lera att Azure Database Migration Service kan ansluta till käll SQL Server Browsers tjänsten på UDP-port 1434 och SQL Server-instansen via den dynamiskt tilldelade TCP-porten enligt vad som är tillämpligt. |

## <a name="additional-known-issues"></a>Ytterligare kända problem

* [Kända problem/migrerings begränsningar med online-migreringar till Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Kända problem/migrerings begränsningar med online-migreringar till Azure Database for MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Kända problem/migrerings begränsningar med online-migreringar till Azure Database for PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Nästa steg

* Visa artikeln [Azure Database migration service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Se artikeln [så här konfigurerar du Server parametrar i Azure Database for MySQL med hjälp av Azure Portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Se artikeln [Översikt över krav för att använda Azure Database migration service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Se [vanliga frågor och svar om hur du använder Azure Database migration service](https://docs.microsoft.com/azure/dms/faq).
