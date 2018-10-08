---
title: Återställa en säkerhetskopia till Azure SQL Database Managed Instance | Microsoft Docs
description: Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans med SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6cad73c8b8fa6a2fa95a6ea0c1fdb5d4114ffd41
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180107"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans

Den här snabbstarten visar hur du återställer en säkerhetskopia av en databas som har lagrats i Azure Blob Storage till den hanterade instansen med Wide World Importers – standardsäkerhetskopieringsfil. Den här metoden kräver vissa avbrott. 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

En självstudie om hur du använder Azure Database Migration Service (DMS) för migrering finns i avsnittet om [migrering av hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md). En diskussion om de olika migreringsmetoderna finns i [SQL Server instance migration to Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md) (SQL Server-migrering till Azure SQL Database Managed Instance).

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här snabbstarten:
- Använder de resurser som har skapats i följande snabbstart som utgångspunkt: [Skapa en hanterad instans](sql-database-managed-instance-get-started.md).
- Kräver den senaste versionen av [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) på den lokala klientdatorn
- Kräver anslutning till din hanterade instans med hjälp av SQL Server Management Studio. Läs följande snabbstarter för anslutningsalternativ:
  - [Connect to an Azure SQL Database Managed Instance from an Azure VM](sql-database-managed-instance-configure-vm.md) (Ansluta till en hanterad Azure SQL Database-instans från en virtuell Azure-dator)
  - [Connect to an Azure SQL Database Managed Instance from on-premises using a Point-to-Site connection](sql-database-managed-instance-configure-p2s.md) (Ansluta till en hanterad Azure SQL Database-instans lokalt med hjälp av en Point-to-Site-anslutning).
- Använder ett förkonfigurerat Azure blob-lagringskonto som innehåller Wide World Importers – standardsäkerhetskopian (nedladdad från https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak).

> [!NOTE]
> Mer information om att säkerhetskopiera och återställa en SQL Server-databas med hjälp av Azure blob-lagring och en signatur för delad åtkomst (SAS) finns i [SQL Server-säkerhetskopiering till URL](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Återställa databasen Wide World Importers från en säkerhetskopia

Med SSMS utför du följande steg för att återställa databasen Wide World Importers till din hanterade instans från säkerhetskopian.

1. Öppna SQL Server Management Studio (SSMS) och anslut till din hanterade instans.
2. Öppna ett nytt frågefönster i SSMS.
3. Använd följande skript för att skapa en autentiseringsuppgift i den hanterade instansen med hjälp av det förkonfigurerade lagringskontot och SAS-nyckeln.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![skapa autentiseringsuppgift](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > Ta alltid bort inledande **?** från den genererade SAS-nyckeln.
  
3. Använd följande skript för att kontrollera SAS-autentiseringsuppgifter och giltigheten för säkerhetskopian – ange URL:en för containern med säkerhetskopian:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![file list](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. Använd följande skript för att återställa databasen World Wide Importers från en säkerhetskopia – ange URL:en för behållaren med säkerhetskopian:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![återställ](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. Kör följande fråga i en ny frågesession för att spåra status för återställningen:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. När återställningen är klar kan du visa den i Object Explorer. 

## <a name="next-steps"></a>Nästa steg

- Information om hur du felsöker säkerhetskopiering till URL finns i avsnittet om [metodtips och felsökning för säkerhetskopiering av SQL-server till URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- En översikt över anslutningsalternativen för program finns i [Ansluta dina program till hanterad instans](sql-database-managed-instance-connect-app.md).
- Information om hur du använder önskat verktyg eller språk finns i [ansluta och fråga](sql-database-connect-query.md).
