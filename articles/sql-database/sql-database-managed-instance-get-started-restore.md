---
title: Återställa en säkerhets kopia till en hanterad instans
description: Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans med SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 7b7d635a45929ead1ef67e10fef75e502c5005f7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770099"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Snabb start: återställa en databas till en hanterad instans

I den här snabbstarten ska du använda SQL Server Management Studio (SSMS) för att återställa en databas (Wide World Importers – standardsäkerhetskopian) från Azure Blob Storage till en [hanterad instans](sql-database-managed-instance.md) för Azure SQL Database.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Mer information om migrering med hjälp av Azure Database Migration Service (DMS) finns i avsnittet [Migrering av hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md).
> Mer information om de olika migreringsmetoderna finns i [SQL Server-instansmigrering till Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Krav

Den här snabbstarten:

- Använder resurser från snabbstarten [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Kräver att datorn har den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) installerad.
- Kräver att du använder SSMS för att kunna ansluta till din hanterade instans. Läs följande snabbstarter om hur man ansluter:
  - [Aktivera offentlig slut punkt](sql-database-managed-instance-public-endpoint-configure.md) på hanterad instans – det här tillvägagångs sättet rekommenderas för den här självstudien.
  - [Connect to an Azure SQL Database Managed Instance from an Azure VM](sql-database-managed-instance-configure-vm.md) (Ansluta till en hanterad Azure SQL Database-instans från en virtuell Azure-dator)
  - [Konfigurera en punkt-till-plats-anslutning till en Azure SQL Database Hanterad instans från den lokala platsen](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> Mer information om hur du säkerhetskopierar och återställer en SQL Server-databas med hjälp av Azure Blob Storage och en [SAS-nyckel](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) finns i [SQL Server-säkerhetskopiering till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017).

## <a name="restore-the-database-from-a-backup-file"></a>Återställ databasen från en säkerhetskopia

Återställ Wide World Importers-databasen till din hanterade instans i SSMS enligt dessa steg. Säkerhetskopian av databasfilen lagras i ett förkonfigurerat Azure Blob Storage-konto.

1. Öppna SSMS och Anslut till din hanterade instans.
2. Högerklicka på din hanterade instans på den vänstra menyn och välj **Ny fråga** så öppnas ett nytt frågefönster.
3. Använd följande skript, som använder ett förkonfigurerat lagringskonto och SAS-nyckel, för att [skapa en autentiseringsuppgift](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) i den hanterade instansen.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![skapa autentiseringsuppgift](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. Kontrollera dina autentiseringsuppgifter genom att köra följande skript som använder en [container](https://azure.microsoft.com/services/container-instances/)-URL för att hämta en lista över säkerhetskopior.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![file list](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Återställ Wide World Importers-databasen genom att köra följande skript.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![återställ](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Spåra återställningsstatusen genom att köra följande skript.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. När återställningen är klar kan du Visa databasen i Object Explorer. Du kan kontrol lera att databas återställningen har slutförts med hjälp av [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) vyn.

> [!NOTE]
> Databas återställnings åtgärden är asynkron och återförsöksbar. Du kan få ett fel SQL Server Management Studio om anslutningen bryts eller om tids gränsen upphör att gälla. Azure SQL Database fortsätter att försöka återställa databasen i bakgrunden och du kan följa förloppet för återställningen med hjälp av vyerna [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) och [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) .
> I vissa faser av återställnings processen kommer du att se unik identifierare i stället för det faktiska databas namnet i systemvyerna. Lär dig mer om `RESTORE` instruktions skillnader [här](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Nästa steg

- Information om hur du felsöker säkerhetskopiering till en URL finns i avsnittet [Metodtips och felsökning för säkerhetskopiering av SQL-server till en URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- En översikt över appanslutningsalternativ finns i [Ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Om du vill fråga med hjälp av dina favorit verktyg eller språk, se [snabb starter: Azure SQL Database Anslut och fråga](sql-database-connect-query.md).
