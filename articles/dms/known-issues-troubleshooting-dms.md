---
title: Artikel om kända felsökning av vanliga problem/fel som är associerade med Azure Database Migration Service | Microsoft Docs
description: Läs mer om hur du felsöker vanliga kända problem/fel som är associerade med Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/09/2019
ms.openlocfilehash: 7b470c20397aac456d34d5e3b877c7d4126d8279
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65465116"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Felsöka vanliga Azure Database Migration Service problem och fel

Den här artikeln beskriver några vanliga problem och fel som Azure Database Migration Service-användare kan stöta på. Artikeln innehåller även information om hur du löser dessa problem och fel.

## <a name="migration-activity-in-queued-state"></a>Migreringsaktivitet i kö

När du skapar nya aktiviteter i Azure Database Migration Service-projekt, förblir aktiviteterna i kö.

| Orsak         | Matchning |
| ------------- | ------------- |
| Det här problemet inträffar när Azure Database Migration Service-instansen har nått maximal kapacitet för pågående aktiviteter som körs samtidigt. Alla nya aktiviteten placeras i kö tills kapaciteten blir tillgänglig. | Verifiera Data Migration Service som instansen har köra om aktiviteter i projekt. Du kan fortsätta att skapa nya aktiviteter som automatiskt läggs till i kön för körning. När någon av de befintliga körs aktiviteterna Slutför nästa köade aktivitet börjar köras och status ändras till körs automatiskt. Du behöver inte vidta några ytterligare åtgärder för att starta migreringen av köade aktiviteter.<br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Maximalt antal databaser som valts för migrering

Följande fel inträffar när du skapar en aktivitet för en database migration-projekt för att flytta till Azure SQL Database eller en Azure SQL Database-hanterad instans:

* **Fel**: Verifieringsfel för migreringsinställningar ”,” errorDetail ”:” mer än max antal ”4” objekt ”databaser” har valts för migrering ”.

| Orsak         | Matchning |
| ------------- | ------------- |
| Det här felet visas när du har valt fler än fyra databaser för en enda migreringsaktivitet. För närvarande är varje migreringsaktiviteten begränsad till fyra databaser. | Välj fyra eller färre databaser per migreringsaktivitet. Om du vill migrera fler än fyra databaser parallellt kan tillhandahålla en annan instans av Azure Database Migration Service. Varje prenumeration stöder för närvarande upp till två instanser av Azure Database Migration Service.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Fel för MySQL-migrering till Azure MySQL med Återställningsfel

När du migrerar från MySQL till Azure Database for MySQL med Azure Database Migration Service misslyckas migreringsaktiviteten med följande fel:

* **Fel**: Fel: Databasmigreringsfel - uppgiften ”TaskID' pausades på grund av [n] efterföljande Återställningsfel.

| Orsak         | Matchning |
| ------------- | ------------- |
| Det här felet kan inträffa när användaren som utför migreringen saknar ReplicationAdmin-rollen och/eller behörighet av REPLIKERINGSKLIENT och REPLIKERINGSREPLIK SUPER (tidigare versioner än MySQL 5.6.6).<br> <br><br><br> <br> <br> <br> <br> <br> <br> | Kontrollera att den [krävs behörighet](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#prerequisites) användarens konto har konfigurerats korrekt på Azure MySQL-instans. Till exempel kan följande steg följas för att skapa en användare med namnet 'migrateuser' med de behörigheter som krävs:<br>1. Skapa användare migrateuser@'%' identifieras av hemlighet; <br>2. bevilja alla privilegier på db_name.* till 'migrateuser'@'%' identifieras av hemlighet; Upprepa det här steget för att bevilja åtkomst på fler databaser <br>3. bevilja replikering underordnad på *.* att 'migrateuser'@'%' identifieras av hemlighet;<br>4. bevilja replikeringsklient på *.* att 'migrateuser'@'%' identifieras av hemlighet;<br>5. tömma rättigheter. |

## <a name="error-when-attempting-to-stop-the-azure-database-migration-service-instance"></a>Fel vid försök att stoppa Azure Database Migration Service-instans

Du får följande fel när stoppar Azure Database Migration Service-instans:

* **Fel**: Det gick inte att stoppa tjänsten. Fel: {”fel”: {”code”: InvalidRequest, ”message” ”: en eller flera aktiviteter körs just nu. Om du vill stoppa tjänsten, vänta tills aktiviteterna har slutförts eller stoppa dem manuellt och försök igen ”.}}

| Orsak         | Matchning |
| ------------- | ------------- |
| Det här felet visas när den tjänstinstans som du försöker att stoppa innehåller aktiviteter som körs fortfarande eller finns i migreringsprojekt. <br><br><br><br><br><br> | Se till att det finns inga aktiviteter som körs i instansen av Azure Database Migration Service som du försöker att stoppa. Du kan också ta bort aktiviteter eller projekt innan du försöker att stoppa tjänsten. Följande steg illustrerar hur du tar bort projekt att rensa tjänstinstansen migreringen genom att ta bort alla aktiviteter som körs:<br>1. Install-Module -Name AzureRM.DataMigration <br>2. Login-AzureRmAccount <br>3 Select-AzureRmSubscription -SubscriptionName "<subName>" <br> 4. Remove-AzureRmDataMigrationProject -Name <projectName> -ResourceGroupName <rgName> -ServiceName <serviceName> -DeleteRunningTask |

## <a name="error-restoring-database-while-migrating-from-sql-server-to-an-azure-sql-database-managed-instance"></a>Ett fel uppstod när databasen medan migreringen från SQL Server till en Azure SQL Database-hanterad instans

När du utför en online-migrering från SQL Server till en Azure SQL Database managed instance misslyckas den startpunkt med följande fel:

* **Fel**: Återställningsåtgärden misslyckades för åtgärds-ID: ”åtgärds-ID”. Kod AuthorizationFailed, meddelandet ”klienten 'clientId' med objekt-id” objectId ”har inte behörighet att utföra åtgärden” Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/read' omfånget ”/subscriptions/ prenumerations-ID '.'.

| Orsak         | Matchning    |
| ------------- | ------------- |
| Det här felet indikerar att programmet-huvudnamn som används för onlinemigrering från SQLServer till en Azure SQL Database managed instance inte bidrar behörighet i prenumerationen. Vissa API-anrop med hanterad instans för närvarande kräver den här behörigheten för prenumerationen för återställningen. <br><br><br><br><br><br><br><br><br><br> | Använd den `Get-AzureADServicePrincipal` PowerShell-cmdlet med `-ObjectId` tillgängliga från felmeddelandet att lista visningsnamnet på det program-ID som används.<br><br> Verifiera behörigheter till det här programmet och se till att den har den [deltagarrollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) på prenumerationsnivån. <br><br> Azure Database Migration Service teknikerna arbetar för att begränsa de nödvändiga åtkomst från aktuella bidra rollen på prenumerationen. Om du har ett behov i verksamheten som inte tillåter användning av bidra roll, kontakta Azure-supporten om du behöver ytterligare hjälp. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Ett fel inträffade när du tar bort nätverkskort som är associerade med Azure Database Migration Service

När du försöker ta bort ett nätverkskort som är associerade med Azure Database Migration Service misslyckas borttagning försöket med felet:

* **Fel**: Det går inte att ta bort nätverkskortet som är kopplad till Azure Database Migration Service på grund av DMS-tjänsten använder nätverkskortet

| Orsak         | Matchning    |
| ------------- | ------------- |
| Det här problemet inträffar när Azure Database Migration Service-instans kan fortfarande finnas och förbrukar nätverkskortet. <br><br><br><br><br><br> | Ta bort den DMS-tjänstinstansen som tar automatiskt bort det nätverkskort som används av tjänsten för att ta bort det här nätverkskortet.<br><br> **Viktiga**: Kontrollera att Azure Database Migration Service-instans som tas bort har inga pågående aktiviteter.<br><br> Efter att alla projekt och aktiviteter som är associerade till Azure Database Migration Service-instansen tas bort, kan du ta bort tjänstinstansen. Det nätverkskort som används av tjänstinstansen rensas automatiskt som en del av borttagning av. |

## <a name="connection-error-when-using-expressroute"></a>Anslutningsfel när du använder ExpressRoute

När du försöker ansluta till datakällan i guiden Azure Database Migration service-projekt, upprättas inte anslutningen när tidsgränsen för långvarig om källa använder ExpressRoute för anslutning.

| Orsak         | Matchning    |
| ------------- | ------------- |
| När du använder [ExpressRoute](https://azure.microsoft.com/services/expressroute/), Azure Database Migration Service [kräver](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) etablering tre tjänstslutpunkter i virtuella nätverkets undernät som är kopplade till tjänsten:<br> --Service Bus-slutpunkt<br> --Slutpunkt för lagring av<br> --Rikta database-slutpunkten (t.ex. SQL-slutpunkt, Cosmos DB-slutpunkt)<br><br><br><br> | [Aktivera](https://docs.microsoft.com/azure/dms/tutorial-sql-server-azure-sql-online) krävs Tjänsteslutpunkter för ExpressRoute-anslutning mellan käll- och Azure Database Migration Service. <br><br><br><br><br><br><br><br> |

## <a name="timeout-error-when-migrating-a-mysql-database-to-azure-database-for-mysql"></a>Tidsgränsfel när du migrerar en MySQL-databas till Azure Database for MySQL

När du migrerar en MySQL-databas till en Azure Database for MySQL-instans via Azure Database Migration Service, misslyckas migreringen med följande fel:

    * **Fel**: Fel: Det gick inte att starta inläsningen för filen databasmigreringsfel - det gick inte att läsa in filen - n RetCode: SQL_ERROR SqlState: HY000 NativeError: 1205 meddelande: [MySQL] [ODBC-drivrutinen] [mysqld] Lås vänta tidsgräns har överskridits; Försök att starta om transaktionen

| Orsak         | Matchning    |
| ------------- | ------------- |
| Det här felet uppstår när migreringen misslyckas på grund av timeout för lås vänta under migreringen.<br><br> | Överväg att öka värdet för Serverparametern **'innodb_lock_wait_timeout'**. Det högsta tillåtna värdet är 1073741824. |

## <a name="additional-known-issues"></a>Ytterligare kända problem

* [Kända problem/migreringsbegränsningar med onlinemigrering till Azure SQL DB](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Begränsningar för kända problem/migrering med online migreringar till Azure DB för MySQL](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Begränsningar för kända problem/migrering med online migreringar till Azure DB för PostgreSQL](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="additional-resources"></a>Ytterligare resurser

* [Azure Database Migration Service PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)
* [Hur du konfigurerar serverparametrar i Azure Database för MySQL med hjälp av Azure portal](https://docs.microsoft.com/azure/mysql/howto-server-parameters)
* [Översikt över krav för att använda Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
* [Vanliga frågor och svar om hur du använder Azure Database Migration Service](https://docs.microsoft.com/azure/dms/faq)
