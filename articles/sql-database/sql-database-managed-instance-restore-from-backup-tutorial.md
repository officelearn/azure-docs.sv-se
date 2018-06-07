---
title: Återställa en säkerhetskopia till Azure SQL Database Managed Instance | Microsoft Docs
description: Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans med SSMS.
keywords: sql database tutorial, create a sql database managed instance
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 04/10/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: ff605b7512a27f81b111560f5d151010dbb62273
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "31426210"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Återställa en databassäkerhetskopia till en hanterad Azure SQL Database-instans

Den här självstudien visar hur du återställer en säkerhetskopia av en databas som har lagrats i Azure Blob Storage till den hanterade instansen med Wide World Importers – standardsäkerhetskopieringsfil. Den här metoden kräver vissa avbrott. En självstudie om hur du använder Azure Database Migration Service (DMS) för migrering finns i avsnittet om [migrering av hanterade instanser med DMS](../dms/tutorial-sql-server-to-managed-instance.md). En diskussion om de olika migreringsmetoderna finns i [SQL Server instance migration to Azure SQL Database Managed Instance](sql-database-managed-instance-migrate.md) (SQL Server-migrering till Azure SQL Database Managed Instance).

> [!div class="checklist"]
> * Ladda ned Wide World Importers – standardsäkerhetskopian
> * Skapa ett Azure lagringskonto och ladda upp säkerhetskopian
> * Återställa databasen Wide World Importers från en säkerhetskopia

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien används som startpunkt resurser som skapades i självstudien: [Create an Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md) (Skapa en hanterad Azure SQL Database-instans).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>Ladda ned Wide World Importers – standardsäkerhetskopian

Använd följande steg för att ladda ned Wide World Importers – standardsäkerhetskopian.

Öppna Internet Explorer och skriv in https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak i webbadressfältet. När du uppmanas göra det klickar du på **Spara** och sparar filen i mappen **Hämtade filer**.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Skapa ett Azure lagringskonto och ladda upp säkerhetskopian

1. Klicka på **Skapa en resurs** längst upp till vänster i Azure Portal.
2. Leta upp **Lagring** och klicka sedan på **Lagringskonto** att öppna formuläret för lagringskontot.

   ![storage account](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Fyll i formuläret för lagringskontot med den begärda informationen, med hjälp av informationen i följande tabell:

   | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Distributionsmodell**|Resursmodell||
   |**Typ av konto**|Blob Storage||
   |**Prestanda**|Standard eller premium|Magnetiska hårddiskar eller SSD-enheter|
   |**Replikering**|Lokalt redundant lagring||
   |\*\*Åtkomstnivå (standard)|Frekvent eller lågfrekvent||
   |**Säker överföring krävs**|Disabled||
   |**Prenumeration**|Din prenumeration|Mer information om dina prenumerationer finns i [Prenumerationer](https://account.windowsazure.com/Subscriptions).|
   |**Resursgrupp**|Den resursgrupp som du skapade tidigare|| 
   |**Plats**|Den plats som du tidigare valt||
   |**Virtuella nätverk**|Disabled||

4. Klicka på **Skapa**.

   ![storage account details](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. När distributionen av lagringskontot har slutförts öppnar du det nya lagringskontot.
6. Under **Inställningar** klickar du på **Signatur för delad åtkomst** för att öppna formuläret för signatur för delad åtkomst (SAS).

   ![sas form](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Ändra standardinställningarna efter behov i SAS-formuläret. Observera att förfallodatumet/tiden som standard bara är 8 timmar.
8. Klicka sedan på **Generera signatur för delad åtkomst (SAS)**.

   ![sas form completed](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Kopiera och spara **SAS-token** och **SAS-URL:en för blobservern**.
10. Under **Inställningar** klickar du på **Behållare**.

    ![behållare](./media/sql-database-managed-instance-tutorial/containers.png)

11. Klicka på **+ Behållare** för att skapa en behållare för att lagra säkerhetskopian.
12. Fyll i formuläret för behållaren med den begärda informationen, med hjälp av informationen i följande tabell:

    | Inställning| Föreslaget värde | Beskrivning |
   | ------ | --------------- | ----------- |
   |**Namn**|Valfritt giltigt namn|Giltiga namn finns i [Namngivningsregler och begränsningar](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).|
   |**Offentlig åtkomstnivå**|Privat (ingen anonym åtkomst)||

    ![container detail](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Klicka på **OK**.
14. När behållaren har skapats öppnar du behållaren.

    ![container](./media/sql-database-managed-instance-tutorial/container.png)

15. Klicka på **Egenskaper för behållare** och kopiera URL:en till behållaren.

    ![container URL](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Välj **Ladda upp** för att öppna formuläret **Ladda upp blob**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload.png)

17. Bläddra till mappen med nedladdade filer och välj filen **WideWorldIimporters-Standard.bak**.

    ![upload](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Klicka på **Överför**.
19. Fortsätt inte förrän uppladdningen är klar.

    ![upload complete](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Återställa databasen Wide World Importers från en säkerhetskopia

Med SSMS utför du följande steg för att återställa databasen Wide World Importers till din hanterade instans från säkerhetskopian.

1. Öppna ett nytt frågefönster i SSMS.
2. Använd följande skript för att skapa SAS-autentiseringsuppgifter – ange URL:en för behållaren för lagringskontot och SAS-nyckeln enligt anvisningarna.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![credential](./media/sql-database-managed-instance-tutorial/credential.png)

3. Använd följande skript för att skapa och kontrollera SAS-autentiseringsuppgifter och giltigheten för säkerhetskopian – ange URL:en för behållaren med säkerhetskopian:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![file list](./media/sql-database-managed-instance-tutorial/file-list.png)

4. Använd följande skript för att återställa databasen Adventure Works 2012 från en säkerhetskopia – ange URL:en för behållaren med säkerhetskopian:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![restore executing](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. Kör följande fråga i en ny frågesession för att spåra status för återställningen:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![restore percent complete](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. När återställningen är klar kan du visa den i Object Explorer. 

    ![restore complete](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du återställer en säkerhetskopia av en databas som har lagrats i Azure Blob Storage till den hanterade instansen med Wide World Importers – standardsäkerhetskopieringsfil. Du har lärt dig att: 

> [!div class="checklist"]
> * Ladda ned Wide World Importers – standardsäkerhetskopian
> * Skapa ett Azure lagringskonto och ladda upp säkerhetskopian
> * Återställa databasen Wide World Importers från en säkerhetskopia

Gå vidare till nästa självstudie och lär dig att migrera SQL Server till Azure SQL Database Managed Instance med DMS.

> [!div class="nextstepaction"]
>[Migrera SQL Server till Azure SQL Database Managed Instance med DMS](../dms/tutorial-sql-server-to-managed-instance.md)