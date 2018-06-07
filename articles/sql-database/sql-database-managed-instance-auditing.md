---
title: Azure SQL Database hanteras instans granskning | Microsoft Docs
description: Lär dig hur du kommer igång med Azure SQL Database hanteras instans Auditing med T-SQL
services: sql-database
author: giladm
manager: craigg
ms.reviewer: carlrab
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: giladm
ms.openlocfilehash: 71929be456de4b798da48bb202969deb71e1c371
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648861"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Kom igång med Azure SQL Database hanteras instans Auditing

[Azure SQL-hanterade databasinstans](sql-database-managed-instance.md) -databas Auditing spårar databashändelser och skriver dem till en granskningslogg logga i Azure storage-konto. Granskning också:
- Hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Aktiverar och underlättar anslutning till efterlevnadsstandarder, även om den inte garantera efterlevnad. Mer information om Azure-program som stöd för överensstämmelse med standarder, finns det [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/compliance/).


## <a name="set-up-auditing-for-your-server"></a>Konfigurera granskning för servern

I följande avsnitt beskrivs konfigurationen av granskning på din hanteras instans.
1. Gå till [Azure-portalen](https://portal.azure.com).
2. Följande steg skapar du ett Azure Storage **behållare** där granskningsloggarna lagras.

   - Gå till Azure Storage där du vill lagra din granskningsloggar.

     > [!IMPORTANT]
     > Ett lagringskonto i samma region som hanteras instans server Undvik att använda mellan region läsning/skrivning.

   - I storage-konto går du till **översikt** och på **Blobbar**.

     ![Navigeringsfönster][1]

   - Klicka på den översta menyn **+ behållare** att skapa en ny behållare.

     ![Navigeringsfönster][2]

   - Ange en behållare **namn**, anger offentlig åtkomst till **privata**, och klicka sedan på **OK**.

     ![Navigeringsfönster][3]

   - Klicka på den nyligen skapade behållaren i listan över behållare, och klicka sedan på **behållaregenskaperna**.

     ![Navigeringsfönster][4]

   - Kopiera URL som behållare genom att klicka på Kopiera-ikonen och spara URL (t.ex, i Anteckningar) för framtida användning. URL-format för behållaren ska vara `https://<StorageName>.blob.core.windows.net/<ContainerName>`

     ![Navigeringsfönster][5]

3. Följande steg generera ett Azure Storage **SAS-Token** används för att ge hanterad instans granskning åtkomsträttigheter till lagringskontot.

   - Gå till Azure Storage-konto där du skapade behållaren i föregående steg.

   - Klicka på **signatur för delad åtkomst** på menyn Inställningar för lagring.

     ![Navigeringsfönster][6]

   - Konfigurera SAS enligt följande:
     - **Tillåtna tjänster**: Blob
     - **Startdatum**: för att undvika problem med tidszon, rekommenderas att använda igårs datum.
     - **Slutdatum**: Välj det datum då den här SAS-Token upphör att gälla. 

       > [!NOTE]
       > Förnya token vid utgången för att undvika fel för granskning.

     - Klicka sedan på **Generera signatur för delad åtkomst (SAS)**.

       ![Navigeringsfönster][7]

   - När du klickar på Generera SAS längst SAS-Token ned. Kopiera token genom att klicka på Kopiera-ikonen och spara den (till exempel Anteckningar) för framtida användning.

     > [!IMPORTANT]
     > Ta bort ett frågetecken (””?) tecken från början av token.

     ![Navigeringsfönster][8]

4. Ansluta till dina hanterade instansen via SQL Server Management Studio (SSMS).

5. Kör följande T-SQL-sats till **skapar en ny autentiseringsuppgift** med behållar-URL och SAS-Token som du skapade i föregående steg:

    ```SQL
    CREATE CREDENTIAL [<container_url>]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',
    SECRET = '<SAS KEY>'
    GO
    ```

6. Kör följande T-SQL-sats för att skapa en ny Server Audit (Välj ditt eget namn för granskning, använda behållarens Webbadress som du skapade i föregående steg):

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>]
    TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
    GO
    ```

    Om inget anges `RETENTION_DAYS` standardvärdet är 0 (obegränsat kvarhållning).

    Ytterligare information:
    - [Granska skillnaderna mellan hanterade instans, Azure SQL Database och SQL Server](#subheading-3)
    - [SKAPA SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
    - [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

7. Skapa en Server Audit Specification eller Databasgranskningsspecifikation som du skulle för SQL Server:
    - [Skapa guide för T-SQL Server audit specification](https://docs.microsoft.com/ sql/t-sql/statements/create-server-audit-specification-transact-sql)
    - [Skapa databas audit specification T-SQL guide](https://docs.microsoft.com/ sql/t-sql/statements/create-database-audit-specification-transact-sql)

8. Aktivera server audit som du skapade i steg 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="analyze-audit-logs"></a>Analysera granskningsloggar
Det finns flera metoder du kan använda för att visa blob granskningsloggar.

- Använd systemfunktionen `sys.fn_get_audit_file` (T-SQL) att returnera granskningsdata i tabellformat. Mer information om hur du använder den här funktionen finns i [sys.fn_get_audit_file dokumentationen](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- En fullständig lista över Granska loggen förbrukningsmetoder finns i den [Kom igång med SQL database auditing](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).

> [!IMPORTANT]
> Metoden för att visa granskningsposter från Azure-portalen ('Granskningsloggarna' rutan) är inte tillgänglig för hanterade instans.

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Granska skillnaderna mellan hanterade instans, Azure SQL Database och SQL Server

De viktigaste skillnaderna mellan SQL-granskning i hanterade instans, Azure SQL Database och SQL Server lokalt är:

- I hanteras-instansen SQL Audit fungerar på servernivå och lagrar `.xel` loggfiler på Azure blob storage-konto.
- I Azure SQL Database fungerar SQL Audit på databasnivå.
- I SQL Server på lokala eller virtuella nivå datorer, SQL Audit fungerar på servern, men lagrar händelser på filer system och windows händelseloggar.

XEvent granskning i hanterade instans stöder Azure blob storage-mål. Fil- och windows-loggar är **stöds inte**.

Nyckeln skillnader i den `CREATE AUDIT` syntaxen för granskning till Azure blob storage är:
- Ny syntax `TO URL` tillhandahålls och låter dig ange Webbadressen till Azure blob Storage-behållare där den `.xel` filerna placeras.
- Syntaxen `TO FILE` är **stöds inte** eftersom hanteras instansen inte kan komma åt filresurser för Windows.
- Avslutningsalternativ är **stöds inte**.
- `queue_delay` 0 är **stöds inte**.


## <a name="next-steps"></a>Nästa steg

- En fullständig lista över Granska loggen förbrukningsmetoder finns i den [Kom igång med SQL database auditing](https://docs.microsoft.com/ azure/sql-database/sql-database-auditing).
- Mer information om Azure-program som stöd för överensstämmelse med standarder, finns det [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/compliance/).


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
