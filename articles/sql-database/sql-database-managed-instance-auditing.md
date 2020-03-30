---
title: Granskning av hanterad instans
description: Lär dig hur du kommer igång med Azure SQL Database-granskning av hanterade instanser med T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387775"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Komma igång med granskning av hanterad Azure SQL Database-instans

[Granskning av hanterade instanser](sql-database-managed-instance.md) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto. Granskning gör även följande:

- Det hjälper dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Det främjar och underlättar uppfyllandet av efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure-program som stöder efterlevnad av standarder finns i [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Konfigurera granskning för servern till Azure-lagring

I följande avsnitt beskrivs konfigurationen av granskning på din hanterade instans.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Skapa en Azure **Storage-behållare** där granskningsloggar lagras.

   1. Navigera till Azure Storage där du vill lagra dina granskningsloggar.

      > [!IMPORTANT]
      > Använd ett lagringskonto i samma region som den hanterade instansen för att undvika läsningar/skrivningar mellan regioner.

   1. Gå till **Översikt** i lagringskontot och klicka på **Blobbar**.

      ![Widgeten Azure Blob](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Klicka på + **Behållare** på den övre menyn om du vill skapa en ny behållare.

      ![Ikon för behållare för blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Ange ett **behållarnamn,** ange offentlig åtkomstnivå till **Privat**och klicka sedan på **OK**.

      ![Skapa konfiguration av blob-behållare](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > Kunden som vill konfigurera ett oföränderligt loggarkiv för sina [granskningshändelser](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) på server- eller databasnivå bör följa instruktionerna från Azure Storage (Se till att du har valt Tillåt ytterligare tillägg när du **konfigurerar** den oföränderliga blob-lagringen)
  
3. När du har skapat behållaren för granskningsloggarna finns det två sätt att konfigurera den som mål för granskningsloggarna: [använda T-SQL](#blobtsql) eller använda [SSMS-användargränssnittet (SQL Server Management Studio):](#blobssms)

   - <a id="blobtsql"></a>Konfigurera blogglagring för granskningsloggar med T-SQL:

     1. Klicka på den nyskapade behållaren i behållarelistan och klicka sedan på **Behållaregenskaper**.

        ![Knappen Egenskaper för Blob-behållare](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Kopiera behållaradressen genom att klicka på kopieringsikonen och spara WEBBADRESSEN (till exempel i Anteckningar) för framtida bruk. Behållarens URL-format ska`https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL för kopia av Blob-behållare](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Generera en Azure Storage **SAS-token** för att bevilja åtkomsträttigheter för hanterade instansgranskninger till lagringskontot:

        - Navigera till Azure Storage-kontot där du skapade behållaren i föregående steg.

        - Klicka på **Signaturen Delad åtkomst** på menyn Lagringsinställningar.

          ![Signaturikon för delad åtkomst på menyn lagringsinställningar](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Konfigurera SAS enligt följande:

          - **Tillåtna tjänster**: Blob

          - **Startdatum:** För att undvika tidszonsrelaterade problem rekommenderas att gårdagens datum används

          - **Slutdatum**: välj det datum då den här SAS-token upphör att gälla

            > [!NOTE]
            > Förnya token vid utgången för att undvika granskningsfel.

          - Klicka sedan på **Generera signatur för delad åtkomst (SAS)**.
            
            ![SAS-konfiguration](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - När du har klickat på Generera SAS visas SAS-token längst ned. Kopiera token genom att klicka på kopieringsikonen och spara den (till exempel i Anteckningar) för framtida bruk.

          ![Kopiera SAS-token](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Ta bort frågetecknet ("?") från början av token.

     1. Anslut till din hanterade instans via SQL Server Management Studio (SSMS) eller något annat verktyg som stöds.

     1. Kör följande T-SQL-uttryck för att **skapa en ny autentiseringsdag** med behållar-URL:en och SAS-token som du skapade i föregående steg:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Kör följande T-SQL-uttryck för att skapa en ny servergranskning (välj ditt eget granskningsnamn, använd behållar-URL:en som du skapade i föregående steg). Om inget anges `RETENTION_DAYS` är standardvärdet 0 (obegränsad kvarhållning):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Fortsätt genom [att skapa en specifikation för servergranskning eller databasgranskningsspecifikation](#createspec)

   - <a id="blobssms"></a>Konfigurera blob-lagring för granskningsloggar med hjälp av SQL Server Management Studio (SSMS) 18 (förhandsversion):

     1. Anslut till den hanterade instansen med hjälp av SSMS-användargränssnittet (SQL Server Management Studio).

     1. Expandera rotanteckningen i Objektutforskaren.

     1. Expandera **noden Säkerhet,** högerklicka på noden **Granskningar** och klicka på "Ny granskning":

        ![Expandera säkerhets- och granskningsnod](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Kontrollera att "URL" är markerat i **Granskningsmål** och klicka på **Bläddra:**

        ![Bläddra i Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Valfritt) Logga in på ditt Azure-konto:

        ![Logga in på Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Välj en prenumeration, ett lagringskonto och en Blob-behållare i listrutan eller skapa en egen behållare genom att klicka på **Skapa**. När du är klar klickar du på **OK:**

        ![Välj Azure-prenumeration, lagringskonto och blob-behållare](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Klicka på **OK** i dialogrutan "Skapa granskning".

4. <a id="createspec"></a>När du har konfigurerat Blob-behållaren som mål för granskningsloggarna skapar och aktiverar du en specifikation för servergranskning eller databasgranskning på samma sätt som för SQL Server:

   - [Skapa T-SQL-guide för servergranskningsspecifikation](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Skapa T-SQL-guide för databasgranskning](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Aktivera servergranskningen som du skapade i steg 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Ytterligare information:

- [Granska skillnader mellan enskilda databaser, elastiska pooler och hanterade instanser i Azure SQL Database och databaser i SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [SKAPA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ÄNDRA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Konfigurera granskning för servern till Event Hub- eller Azure Monitor-loggar

Granskningsloggar från en hanterad instans kan skickas till Even Hubs- eller Azure Monitor-loggar. I det här avsnittet beskrivs hur du konfigurerar detta:

1. Navigera i [Azure Portal](https://portal.azure.com/) till den hanterade instansen.

2. Klicka på **Diagnostikinställningar**.

3. Klicka på **Aktivera diagnostik**. Om diagnostik redan är aktiverat visas *inställningen +Lägg till diagnostik* i stället.

4. Välj **SQLSecurityAuditEvents** i listan över loggar.

5. Välj ett mål för granskningshändelserna - Event Hub, Azure Monitor-loggar eller båda. Konfigurera för varje mål de parametrar som krävs (t.ex. log analytics-arbetsyta).

6. Klicka på **Spara**.

    ![Konfigurera diagnostikinställningar](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Anslut till den hanterade instansen med **SQL Server Management Studio (SSMS)** eller någon annan klient som stöds.

8. Kör följande T-SQL-uttryck för att skapa en servergranskning:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Skapa och aktivera en servergranskningsspecifikation eller databasgranskningsspecifikation på samma sätt som för SQL Server:

   - [Skapa T-SQL-guide för servergranskningsspecifikation](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Skapa T-SQL-guide för databasgranskning](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Aktivera servergranskningen som skapats i steg 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Använda granskningsloggar

### <a name="consume-logs-stored-in-azure-storage"></a>Använda loggar som lagras i Azure Storage

Det finns flera metoder som du kan använda för att visa blob granskningsloggar.

- Använd systemfunktionen `sys.fn_get_audit_file` (T-SQL) för att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns i [dokumentationen för sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Du kan utforska granskningsloggar med hjälp av ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). I Azure-lagring sparas granskningsloggar som en samling blob-filer i en behållare som har definierats för att lagra granskningsloggarna. Mer information om lagringsmappens hierarki, namngivningskonventioner och loggformat finns i referensen för [Blob Audit Log-format](https://go.microsoft.com/fwlink/?linkid=829599).

- En fullständig lista över metoder för granskningsloggförbrukning finns i granskningen av [Komma igång med SQL-databasgranskning](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Använda loggar som lagras i eventhubben

Om du vill använda granskningsloggar från Event Hub måste du konfigurera en ström för att använda händelser och skriva dem till ett mål. Mer information finns i Dokumentationen för Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Använda och analysera loggar som lagras i Azure Monitor-loggar

Om granskningsloggar skrivs till Azure Monitor-loggar är de tillgängliga på log analytics-arbetsytan, där du kan köra avancerade sökningar på granskningsdata. Som utgångspunkt navigerar du till log analytics-arbetsytan och klickar på *Loggar* under `search "SQLSecurityAuditEvents"` *Allmänt* och anger en enkel fråga, till exempel: för att visa granskningsloggarna.  

Azure Monitor-loggar ger dig operativa insikter i realtid med hjälp av integrerade sök- och anpassade instrumentpaneler för att enkelt analysera miljontals poster över alla dina arbetsbelastningar och servrar. Mer användbar information om sökspråk och kommandon för Azure Monitor loggar finns i [sökreferens för Azure Monitor-loggar](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Granska skillnader mellan databaser i Azure SQL Database och databaser i SQL Server

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL Database och databaser i SQL Server är:

- Med distributionsalternativet för hanterade instanser i Azure SQL Database `.xel` fungerar granskning på servernivå och lagrar loggfiler i Azure Blob-lagring.
- I SQL Server lokalt/virtuella datorer fungerar granskning på servernivå, men lagrar händelser på filsystem-/windows-händelseloggar.

XEvent-granskning i hanterad instans stöder Azure Blob-lagringsmål. Fil- och windowsloggar **stöds inte**.

De viktigaste skillnaderna `CREATE AUDIT` i syntaxen för granskning till Azure Blob-lagring är:

- En ny `TO URL` syntax tillhandahålls och gör att du kan ange URL `.xel` för Azure blob Storage-behållaren där filerna placeras.
- En ny `TO EXTERNAL MONITOR` syntax finns för att aktivera even hub och Azure Monitor loggar mål.
- Syntaxen `TO FILE` **stöds inte** eftersom SQL Database inte kan komma åt Windows-filresurser.
- Avstängningsalternativet **stöds inte**.
- `queue_delay`av 0 **stöds inte**.

## <a name="next-steps"></a>Nästa steg

- En fullständig lista över metoder för granskningsloggförbrukning finns i granskningen av [Komma igång med SQL-databasgranskning](sql-database-auditing.md).
- Mer information om Azure-program som stöder efterlevnad av standarder finns i [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

<!--Image references-->









