---
title: Vanliga problem - Tjänsten för migrering av Azure-databas
description: Lär dig mer om hur du felsöker vanliga kända problem/fel som är associerade med hjälp av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: c5d2ad481124f5ae048d010cdf632ee661bbd6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649115"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Felsöka vanliga problem och fel i Tjänsten för azure-databasmigreringstjänst

I den här artikeln beskrivs några vanliga problem och fel som Azure Database Migration Service-användare kan stöta på. Artikeln innehåller också information om hur du löser dessa problem och fel.

## <a name="migration-activity-in-queued-state"></a>Migreringsaktivitet i köat tillstånd

När du skapar nya aktiviteter i ett Azure Database Migration Service-projekt förblir aktiviteterna i kö.

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här problemet uppstår när Azure Database Migration Service-instansen har nått maximal kapacitet för pågående aktiviteter som körs samtidigt. Alla nya aktiviteter köas tills kapaciteten blir tillgänglig. | Validera datamigreringstjänsten-instansen har pågående aktiviteter i projekt. Du kan fortsätta att skapa nya aktiviteter som automatiskt läggs till i kön för körning. Så snart någon av de befintliga aktiviteterna slutförs börjar nästa köade aktivitet köras och statusen ändras automatiskt till körtillstånd. Du behöver inte vidta några ytterligare åtgärder för att starta migreringen av köad aktivitet.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximalt antal databaser som valts för migrering

Följande fel uppstår när du skapar en aktivitet för ett databasmigreringsprojekt för att flytta till Azure SQL Database eller en hanterad Azure SQL-databas-hanterad instans:

* **Fel:** Valideringsfel för migreringsinställningar", "errorDetail":"Mer än maxnummer '4' objekt i 'Databaser' har valts för migrering."

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet visas när du har valt fler än fyra databaser för en enda migreringsaktivitet. För närvarande är varje migreringsaktivitet begränsad till fyra databaser. | Välj fyra eller färre databaser per migreringsaktivitet. Om du behöver migrera fler än fyra databaser parallellt etablerar du en annan instans av Azure Database Migration Service. För närvarande stöder varje prenumeration upp till två Azure Database Migration Service-instanser.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Fel för MySQL-migrering till Azure MySQL med återställningsfel

När du migrerar från MySQL till Azure Database for MySQL med hjälp av Azure Database Migration Service misslyckas migreringen med följande fel:

* **Fel:** Databasmigreringsfel - Task'TaskID avbröts på grund av [n] på varandra följande återställningsfel.

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet kan uppstå när användaren som utför migreringen saknar rollen ReplicationAdmin och/eller behörigheterna för REPLIKERINGSKLIENT, REPLIKERINGSREPLIK OCH SUPER (versioner tidigare än MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Kontrollera att de nödvändiga behörigheterna för användarkontot är korrekt konfigurerade i [Azure-databasen](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) för MySQL-instansen. Följande steg kan till exempel följas för att skapa en användare med namnet "migrateuser" med nödvändiga privilegier:<br>1. SKAPA ANVÄNDAR- MIGRATEUSER@ %' SOM IDENTIFIERATS MED "HEMLIG". <br>2. Bevilja alla privilegier på db_name.* till "migrerande"@'%' som identifieras med "hemlig". upprepa det här steget för att bevilja åtkomst i fler databaser <br>3. Bevilja replikering slav på *.* till "migrateuser"@'%' som identifieras med "hemlig".<br>4. Bevilja replikeringsklienten på *.* till "migrateuser"@'%' som identifieras med "hemlig".<br>5. Spolprivilegier. |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Fel vid försök att stoppa Azure Database Migration Service

Följande felmeddelande visas när du stoppar Azure Database Migration Service-instansen:

* **Fel:** Tjänsten kunde inte stoppas. Fel: {'error':{'code':'InvalidRequest','message':'En eller flera aktiviteter körs för närvarande. Om du vill stoppa tjänsten väntar du tills aktiviteterna har slutförts eller stoppar aktiviteterna manuellt och försöker igen.'}}

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet visas när tjänstinstansen som du försöker stoppa innehåller aktiviteter som fortfarande körs eller finns i migreringsprojekt. <br><br><br><br><br><br> | Kontrollera att det inte finns några aktiviteter som körs i instansen av Azure Database Migration Service som du försöker stoppa. Du kan också ta bort aktiviteter eller projekt innan du försöker stoppa tjänsten. Följande steg illustrerar hur du tar bort projekt för att rensa migreringstjänstinstansen genom att ta bort alla uppgifter som körs:<br>1. Installationsmodul -Namn AzureRM.DataMigration <br>2. Logga in-AzureRmAccount <br>3. Välj-AzureRmSubscription -SubscriptionName "\<undernamn>" <br> 4. Ta bort AzureRmDataMigrationProject -Name \<projectName> \<-ResourceGroupName rgName \<> -ServiceName serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Fel vid försök att starta Azure Database Migration Service

Följande fel visas när du startar Azure Database Migration Service-instansen:

* **Fel:** Tjänsten kan inte starta. Fel: {'errorDetail':'Tjänsten kunde inte startas, kontakta Microsoft support'}

| Orsak         | Lösning |
| ------------- | ------------- |
| Det här felet visas när den föregående instansen misslyckades internt. Det här felet uppstår sällan och ingenjörsteamet är medvetet om det. <br> | Ta bort instansen av tjänsten som du inte kan starta och etablera sedan en ny för att ersätta den. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Det gick inte att återställa databasen när SQL migreras till Azure SQL DB-hanterad instans

NÃ¤mtning nÃ¤mtning frÃ¤nstÃ¤nligger du frÃ¶n en onlinemigrering frÃ¶n SQL Server till en hanterad Azure SQL Database-instans, misslyckas cutovert med följande fel:

* **Fel:** Återställningsåtgärden misslyckades för åtgärds-ID 'operationId'. Kod "AuthorizationFailed", Meddelande 'Client 'clientId' med object id 'objectId' har inte behörighet att utföra åtgärden 'Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' over scope '/subscriptions/subscriptionId'.'.

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här felet anger att programmets huvudnamn som används för onlinemigrering från SQL Server till en Azure SQL Database-hanterad instans inte har contribute-behörighet för prenumerationen. Vissa API-anrop med hanterad instans kräver för närvarande den här behörigheten för prenumeration för återställningen. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Använd `Get-AzureADServicePrincipal` PowerShell-cmdleten med `-ObjectId` tillgängligt från felmeddelandet för att visa visningsnamnet för det program-ID som används.<br><br> Verifiera behörigheterna för det här programmet och se till att det har [deltagarrollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) på prenumerationsnivå. <br><br> Azure Database Migration Service Engineering Team arbetar för att begränsa den nödvändiga åtkomsten från den aktuella contribute-rollen på prenumerationen. Om du har ett affärskrav som inte tillåter användning av contribute-roll kontaktar du Azure-supporten för ytterligare hjälp. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Fel vid borttagning av nätverkskort som är associerat med Azure Database Migration Service

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ta bort ett nÃ¤rsÃ¤nsnitt som är associerat med Azure Database Migration Service misslyckas borttagningsförsöket med det fÃ¶rsÃ¥k. felet:

* **Fel:** Det går inte att ta bort nätverkskortet som är associerat till Azure Database Migration Service på grund av DMS-tjänsten som använder nätverkskortet

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här problemet uppstår när Azure Database Migration Service-instansen fortfarande kan finnas och förbruka nätverkskortet. <br><br><br><br><br><br><br><br> | Om du vill ta bort det här nätverkskortet tar du bort DMS-tjänstinstansen som automatiskt tar bort nätverkskortet som används av tjänsten.<br><br> **Viktigt**: Kontrollera att Azure Database Migration Service-instansen som tas bort inte har några aktiviteter som körs.<br><br> När alla projekt och aktiviteter som är associerade till Azure Database Migration Service-instansen har tagits bort kan du ta bort tjänstinstansen. Nätverkskortet som används av tjänstinstansen rensas automatiskt som en del av borttagningen av tjänsten. |

## <a name="connection-error-when-using-expressroute"></a>Anslutningsfel vid användning av ExpressRoute

När du försöker ansluta till källan i guiden Azure Database Migration Service-projekt misslyckas anslutningen efter en lång timeout om källan använder ExpressRoute för anslutning.

| Orsak         | Lösning    |
| ------------- | ------------- |
| När du använder [ExpressRoute](https://azure.microsoft.com/services/expressroute/) [kräver](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) Azure Database Migration Service att tre tjänstslutpunkter etableras i det virtuella nätverksundernätet som är associerat med tjänsten:<br> -- Service Bus slutpunkt<br> -- Slutpunkt för lagring<br> -- Måldatabasslutpunkt (t.ex. SQL-slutpunkt, Cosmos DB-slutpunkt)<br><br><br><br><br> | [Aktivera](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) de tjänstslutpunkter som krävs för ExpressRoute-anslutning mellan käll- och Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Lås tidsgränsen för väntetid när du migrerar en MySQL-databas till Azure DB för MySQL

När du migrerar en MySQL-databas till en Azure Database for MySQL-instans via Azure Database Migration Service misslyckas migreringen med följande låstidsgränsen för tidsgränsen:

* **Fel:** Databasmigreringsfel - Det gick inte att läsa in filen - Det gick inte att starta inläsningsprocessen för filen 'n' RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 Message: [MySQL][ODBC Driver][mysqld] Lock wait timeout exceeded; prova att starta om transaktionen

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här felet uppstår när migreringen misslyckas på grund av tidsgränsen för låstidsväntan under migreringen. | Överväg att öka värdet på serverparametern **"innodb_lock_wait_timeout".** Det högsta tillåtna värdet är 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Fel vid anslutning till källan SQL Server vid användning av dynamisk port eller namngiven instans

NÃ¤r du fÃ¶rsÃ¶k fÃ¶rsÃ¶k fÃ¶rsÃ¶k att ansluta Azure Database Migration Service till SQL Server-källa som fÃ¶rsÃ¶rs PÃ¶r en namngiven instans eller en dynamisk port, fÃ¶rsÃ¶ks anslutningen med det fÃ¶rsÃ¶r:

* **Fel:**-1 - SQL-anslutningen misslyckades. Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig. Kontrollera att instansnamnet är korrekt och att SQL Server är konfigurerat för att tillåta fjärranslutningar. (provider: SQL Network Interfaces, fel: 26 - Fel att hitta server/instans angiven)

| Orsak         | Lösning    |
| ------------- | ------------- |
| Det här problemet uppstår när källan SQL Server-instans som Azure Database Migration Service försöker ansluta till antingen har en dynamisk port eller använder en namngiven instans. Sql Server Browser-tjänsten lyssnar på UDP-port 1434 för inkommande anslutningar till en namngiven instans eller när du använder en dynamisk port. Den dynamiska porten kan ändras varje gång SQL Server-tjänsten startas om. Du kan kontrollera den dynamiska porten som tilldelats en instans via nätverkskonfiguration i SQL Server Configuration Manager.<br><br><br> |Kontrollera att Azure Database Migration Service kan ansluta till källan SQL Server Browser service på UDP-port 1434 och SQL Server-instansen via den dynamiskt tilldelade TCP-porten beroende på vad som är tillämpligt. |

## <a name="additional-known-issues"></a>Ytterligare kända problem

* [Kända problem/migreringsbegränsningar med onlinemigreringar till Azure SQL Database](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Kända problem/migreringsbegränsningar med onlinemigreringar till Azure Database för MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Kända problem/migreringsbegränsningar med onlinemigreringar till Azure Database för PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>Nästa steg

* Visa artikeln [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Visa artikeln [Så här konfigurerar du serverparametrar i Azure Database för MySQL med hjälp av Azure-portalen](https://docs.microsoft.com/azure/mysql/howto-server-parameters).
* Visa artikeln [Översikt över förutsättningar för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs).
* Se [vanliga frågor och svar om hur du använder Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq).
