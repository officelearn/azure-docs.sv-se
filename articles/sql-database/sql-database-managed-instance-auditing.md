---
title: Azure SQL Database Managed Instance granskning | Microsoft Docs
description: Lär dig hur du kommer igång med Azure SQL Database hanterad instans-granskning med T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: giladm
ms.author: giladm
ms.reviewer: vanto
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 38dee577ff57e6ba440fb4062315b57e4eb7957b
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162261"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Kom igång med Azure SQL Database hanterad instans-granskning

[Azure SQL Database Managed Instance](sql-database-managed-instance.md) granskning spårar databasen händelser och skriver dem till en granskningslogg i ditt Azure storage-konto. Granskning också:
- Hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Aktiverar och underlättar infört efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Konfigurera granskning för din server

I följande avsnitt beskrivs konfigurationen av granskning på din hanterade instans.
1. Gå till [Azure-portalen](https://portal.azure.com).
2. Följande steg skapar ett Azure Storage **behållare** där granskningsloggar lagras.

   - Gå till Azure Storage där du vill lagra dina granskningsloggar.

     > [!IMPORTANT]
     > Använd ett lagringskonto i samma region som hanterad instans-server om du vill undvika läsningar/skrivningar över flera regioner.

   - I storage-konto går du till **översikt** och klicka på **Blobar**.

     ![Navigeringsfönster][1]

   - I den översta menyn klickar du på **+ behållare** att skapa en ny behållare.

     ![Navigeringsfönster][2]

   - Ange en behållare **namn**, anger offentlig åtkomst till **privata**, och klicka sedan på **OK**.

     ![Navigeringsfönster][3]

   - Klicka på den nyligen skapade behållaren i listan med behållare och klicka sedan på **behållaregenskaperna**.

     ![Navigeringsfönster][4]

   - Kopiera URL: en för behållaren genom att klicka på ikonen för kopiera och spara URL: en (till exempel i Anteckningar) för framtida användning. URL-format för behållaren ska vara `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Navigeringsfönster][5]

3. Följande steg generera ett Azure Storage **SAS-Token** används för att ge hanterad instans granskning åtkomstbehörigheter till lagringskontot.

   - Gå till Azure Storage-konto där du skapade behållaren i föregående steg.

   - Klicka på **signatur för delad åtkomst** på menyn Inställningar.

     ![Navigeringsfönster][6]

   - Konfigurera SAS enligt följande:
     - **Tillåtna tjänster**: Blob
     - **Startdatum**: för att undvika tidszon-relaterade problem rekommenderar vi att du använder gårdagens datum.
     - **Slutdatum**: Välj det datum då den här SAS-Token upphör att gälla. 

       > [!NOTE]
       > Förnya token vid utgången att undvika fel för granskning.

     - Klicka sedan på **Generera signatur för delad åtkomst (SAS)**.

       ![Navigeringsfönster][7]

   - När du klickar på Generera SAS, längst SAS-Token ned. Kopiera token genom att klicka på ikonen för kopiera och spara den (till exempel i Anteckningar) för framtida användning.

     > [!IMPORTANT]
     > Ta bort frågetecknet (””?) tecken från början av token.

     ![Navigeringsfönster][8]

4. Anslut till din hanterade instans via SQL Server Management Studio (SSMS).

5. Kör följande T-SQL-instruktion till **skapar du en ny autentiseringsuppgift** med hjälp av behållarens Webbadress och SAS-Token som du skapade i föregående steg:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Kör följande T-SQL-instruktion för att skapa en ny Server Audit (Välj granskningsnamnet på din egen, använda behållarens Webbadress som du skapade i föregående steg):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Om inte anges `RETENTION_DAYS` standardvärdet är 0 (obegränsad kvarhållning).

    Ytterligare information:
    - [Granskning skillnaderna mellan hanterad instans, Azure SQL DB och SQL Server](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
    - [SKAPA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Skapa en Server Audit Specification eller Databasgranskningsspecifikation precis som för SQL Server:
    - [Skapa Server audit specification T-SQL-guide](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Skapa databas audit specification T-SQL-guide](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Aktivera servergranskning som du skapade i steg 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analysera granskningsloggar
Det finns flera metoder som du kan använda för att visa blob granskningsloggar.

- Använd systemfunktionen `sys.fn_get_audit_file` (T-SQL) att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns i den [sys.fn_get_audit_file dokumentation](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- En fullständig lista över audit log förbrukningsmetoder finns i den [Kom igång med SQL-databasgranskning](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Metoden för visning av granskningsposter från Azure portal (”granskningsposter”-rutan) är inte tillgänglig för hanterad instans.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Granskning skillnaderna mellan hanterad instans, Azure SQL Database och SQL Server

De viktigaste skillnaderna mellan SQL-granskning på hanterad instans, Azure SQL Database och SQL Server lokalt är:

- I Managed Instance fungerar SQL-granskning på servernivå och lagrar `.xel` loggfiler på en Azure blob storage-konto.
- I Azure SQL Database fungerar SQL-granskning på databasnivå.
- I SQL Server lokalt / virtuella nivå datorer, SQL-granskning fungerar på servern, men lagrar händelser på filer system/windows händelseloggar.

XEvent granskning i Managed Instance stöder prestandamål i Azure blob storage. Fil- och windows-loggar är **stöds inte**.

Nyckeln skillnader i den `CREATE AUDIT` syntaxen för granskning till Azure blob storage är:
- En ny syntax `TO URL` tillhandahålls och kan du ange URL: en för Azure blob Storage-behållare där de `.xel` filerna är placerade.
- Syntaxen `TO FILE` är **stöds inte** eftersom hanterad instans inte kan komma åt Windows-filresurser.
- Stäng av alternativet är **stöds inte**.
- `queue_delay` 0 är **stöds inte**.


## <a name="next-steps"></a>Nästa steg

- En fullständig lista över audit log förbrukningsmetoder finns i den [Kom igång med SQL-databasgranskning](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).


<!--Anchors-->
[Set up auditing for your server]: #subheading-1
[Analyze audit logs]: #subheading-2
[Auditing differences between Managed Instance, Azure SQL DB and SQL Server]: #subheading-3

<!--Image references-->
[1]: ./media/sql-managed-instance-auditing/1_blobs_widget.png
[2]: ./media/sql-managed-instance-auditing/2_create_container_button.png
[3]: ./media/sql-managed-instance-auditing/3_create_container_config.png
[4]: ./media/sql-managed-instance-auditing/4_container_properties_button.png
[5]: ./media/sql-managed-instance-auditing/5_container_copy_name.png
[6]: ./media/sql-managed-instance-auditing/6_storage_settings_menu.png
[7]: ./media/sql-managed-instance-auditing/7_sas_configure.png
[8]: ./media/sql-managed-instance-auditing/8_sas_copy.png
