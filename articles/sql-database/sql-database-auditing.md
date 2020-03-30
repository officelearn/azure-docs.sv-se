---
title: Azure SQL-granskning
description: Använd Granskning av Azure SQL-databas för att spåra databashändelser i en granskningslogg.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 8b50cb95e51ef36ed4436a6eb9c9143c9c613cc7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346446"
---
# <a name="azure-sql-auditing"></a>Azure SQL-granskning

Granskning för Azure [SQL Database](sql-database-technical-overview.md) och [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) spårar databashändelser och skriver dem till en granskningslogg i ditt Azure-lagringskonto, Log Analytics-arbetsyta eller Event Hubs. 

Granskning gör även följande:

- Det hjälper dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

- Det främjar och underlättar uppfyllandet av efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure-program som stöder efterlevnad av standarder finns i [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan över SQL Database-efterlevnadscertifieringar.

> [!NOTE] 
> Det här avsnittet gäller både Azure SQL Database och Azure Synapse Analytics-databaser. För enkelhetens skull används SQL Database när du refererar till både Azure SQL Database och Azure Synapse Analytics.

## <a name="overview"></a><a id="subheading-1"></a>Översikt

Du kan använda SQL-databasgranskning för att:

- **Behåll** en granskningshistorik för valda händelser. Du kan definiera kategorier med databasåtgärder som ska granskas.
- **Rapport om** databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att snabbt komma igång med aktivitets- och händelserapportering.
- **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

> [!IMPORTANT]
> - Granskning av Azure SQL Database är optimerad för tillgänglighet & prestanda. Under mycket hög aktivitet Azure SQL Database tillåter åtgärder för att fortsätta och kanske inte registrera vissa granskade händelser.
   
## <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="subheading-8"></a>Definiera granskningsprincip på servernivå jämfört med databasnivå

En granskningsprincip kan definieras för en viss databas eller som standardserverprincip:

- En serverprincip gäller för alla befintliga och nyskapade databaser på servern.

- Om *granskning av serverblobb är aktiverat*gäller den alltid för *databasen*. Databasen granskas, oavsett databasgranskningsinställningarna.

- Om du aktiverar blob-granskning i databasen eller informationslagret åsidosätts eller ändras *inte* någon av inställningarna för granskningen av serverblobben, förutom att aktivera den på servern. Båda revisionerna kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger parallellt. en gång av serverprincipen och en gång av databasprincipen.

   > [!NOTE]
   > Du bör undvika att aktivera både granskning av serverblobb och granskning av databasblobb tillsammans, såvida inte:
    > - Du vill använda ett annat *lagringskonto,* *kvarhållningsperiod* eller *Logganalysarbetsyta* för en viss databas.
    > - Du vill granska händelsetyper eller kategorier för en viss databas som skiljer sig från resten av databaserna på servern. Du kan till exempel ha tabellinfogningar som behöver granskas endast för en viss databas.
   >
   > Annars rekommenderar vi att du endast aktiverar blobgranskning på servernivå och låter granskningen på databasnivå inaktiveras för alla databaser.

## <a name="set-up-auditing-for-your-server"></a><a id="subheading-2"></a>Konfigurera granskning för servern

I följande avsnitt beskrivs konfigurationen av granskning med Hjälp av Azure-portalen.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Navigera till **Granskning** under rubriken Säkerhet i sql-databasen/serverfönstret.
3. Om du föredrar att konfigurera en servergranskningsprincip kan du välja länken **Visa serverinställningar** på sidan databasgranskning. Du kan sedan visa eller ändra servergranskningsinställningarna. Servergranskningsprinciper gäller för alla befintliga och nyskapade databaser på den här servern.

    ![Navigeringsfönster][2]

4. Om du föredrar att aktivera granskning på databasnivå växlar du **Granskning** till **ON**. Om servergranskning är aktiverat kommer den databaskonfigurerade granskningen att finnas sida vid sida med servergranskningen.

5. Du har flera alternativ för att konfigurera var granskningsloggar ska skrivas. Du kan skriva loggar till ett Azure-lagringskonto, till en Log Analytics-arbetsyta för förbrukning av Azure Monitor-loggar (förhandsversion) eller till händelsehubb för förbrukning med hjälp av händelsehubb (förhandsversion). Du kan konfigurera valfri kombination av dessa alternativ och granskningsloggar skrivs till var och en.
  
   ![lagringsalternativ](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name=""></a><a id="audit-storage-destination">Granskning till lagringsmål</a>

Om du vill konfigurera skriva granskningsloggar till ett lagringskonto väljer du **Lagring** och öppnar **Lagringsinformation**. Välj det Azure-lagringskonto där loggar ska sparas och välj sedan kvarhållningsperioden. Klicka sedan på **OK**. Loggar som är äldre än kvarhållningsperioden tas bort.

  ![storage account](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="log-audits-to-storage-account-behind-vnet-or-firewall"></a>Logga granskningar till lagringskonto bakom VNet eller brandvägg

Du kan skriva granskningsloggar till ett Azure Storage-konto bakom ett virtuella nätverk eller en brandvägg. Specifika instruktioner finns i [Skriv granskning till ett lagringskonto bakom VNet och brandvägg](create-auditing-storage-account-vnet-firewall.md).

#### <a name="remarks"></a>Anmärkningar

- Alla lagringstyper (v1, v2, blob) stöds.
- Alla konfigurationer för lagringsreplikering stöds.
- Lagring bakom ett virtuellt nätverk och en brandvägg stöds.
- **Premiumlagring** stöds för närvarande **inte**.
- **Hierarkiskt namnområde** för **Azure Data Lake Storage Gen2-lagringskonto** stöds för närvarande **inte**.
- Det går inte att aktivera granskning på ett pausat **Azure SQL Data Warehouse.** Om du vill aktivera granskning återupptar du datalagret.
- Standardvärdet för kvarhållningsperioden är 0 (obegränsad kvarhållning). Du kan ändra det här värdet genom att flytta skjutreglaget **Kvarhållning (Dagar)** i **Lagringsinställningar när** du konfigurerar lagringskontot för granskning.
  - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till något annat värde, observera att kvarhållning endast gäller för loggar skrivna efter att kvarhållningsvärdet har ändrats (loggar skrivna under den period då kvarhållningen var inställd på obegränsad bevaras, även efter kvarhållning är aktiverat).
- Kunden som vill konfigurera ett oföränderligt loggarkiv för sina [granskningshändelser](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes) på server- eller databasnivå bör följa instruktionerna från Azure Storage (Se till att du har valt Tillåt ytterligare tillägg när du **konfigurerar** den oföränderliga blob-lagringen).
- När du har konfigurerat granskningsinställningarna kan du aktivera den nya hotidentifieringsfunktionen och konfigurera e-postmeddelanden för att ta emot säkerhetsaviseringar. När du använder hotidentifiering får du proaktiva aviseringar om avvikande databasaktiviteter som kan indikera potentiella säkerhetshot. Mer information finns i [Komma igång med hotidentifiering](sql-database-threat-detection-get-started.md).
- Mer information om loggformat, lagringsmappens hierarki och namngivningskonventioner finns i [referensen för Blob Audit Log-format](https://go.microsoft.com/fwlink/?linkid=829599).
- Azure SQL Database Audit lagrar 4 000 tecken data för teckenfält i en granskningspost. När **utdraget** eller **data_sensitivity_information** värden som returneras från en granskningsbar åtgärd innehåller mer än 4 000 tecken, trunkeras alla data utöver de första 4 000 tecknen **och granskas inte.**
- Granskningsloggar skrivs till **Tilläggsblobar** i en Azure Blob-lagring på din Azure-prenumeration
- Standardgranskningsprincipen innehåller alla åtgärder och följande uppsättning åtgärdsgrupper, som granskar alla frågor och lagrade procedurer som körs mot databasen, samt lyckade och misslyckade inloggningar:
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
- Du kan konfigurera granskning för olika typer av åtgärder och åtgärdsgrupper med PowerShell, enligt beskrivningen i [avsnittet Hantera SQL-databasgranskning med hjälp av Azure PowerShell.You](#subheading-7) can configure auditing for different types of actions and action groups using PowerShell, as described in the Manage SQL database auditing using Azure PowerShell section.
- När du använder AAD-autentisering visas *inte* misslyckade inloggningsposter i SQL-granskningsloggen. Om du vill visa misslyckade inloggningsgranskningsposter måste du besöka [Azure Active Directory-portalen]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), som loggar information om dessa händelser.

### <a name=""></a><a id="audit-log-analytics-destination">Granskning till logganalysmål</a>
  
Om du vill konfigurera skriva granskningsloggar till en Log Analytics-arbetsyta väljer du **Log Analytics (Preview)** och öppnar **Log Analytics-information**. Markera eller skapa arbetsytan Log Analytics där loggar ska skrivas och klicka sedan på **OK**.
    
  > [!WARNING]
   > Om du aktiverar granskning i Log Analytics medför du kostnader baserat på inmatningshastigheter. Tänk på den associerade kostnaden med att använda det här [alternativet](https://azure.microsoft.com/pricing/details/monitor/)eller överväg att lagra granskningsloggarna i ett Azure-lagringskonto.
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name=""></a><a id="audit-event-hub-destination">Granskning till händelsehubbmål</a>

> [!WARNING]
> Om du aktiverar granskning på en server som har en SQL-pool på **den återupptas SQL-poolen och pausas igen,** vilket kan medföra faktureringsavgifter.
> Det går inte att aktivera granskning på en pausad SQL-pool. Om du vill aktivera den avmarkerar du SQL-poolen.

Om du vill konfigurera skriva granskningsloggar till en händelsehubb väljer du **Event Hub (Förhandsversion)** och öppnar **information om händelsehubb .** Välj händelsehubben där loggar ska skrivas och klicka sedan på **OK**. Kontrollera att händelsehubben är i samma region som databasen och servern.

   ![Eventhub (På efter och med)](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analysera granskningsloggar och rapporter

Om du väljer att skriva granskningsloggar till Azure Monitor-loggar:

- Använd [Azure-portalen](https://portal.azure.com).  Öppna relevant databas. Klicka på **Visa granskningsloggar**högst upp på databasens **granskningssida** .

    ![visa granskningsloggar](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Sedan har du två sätt att visa loggarna:
    
    Om du klickar på **Logganalys** högst upp på sidan **Granskningsposter** öppnas loggvyn på logganalysarbetsytan, där du kan anpassa tidsintervallet och sökfrågan.
    
    ![öppna i Log Analytics-arbetsyta](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Om du klickar på **Visa instrumentpanel** högst upp på sidan **Granska poster** öppnas en instrumentpanel som visar granskningsloggar, där du kan öka detaljnivån i Säkerhetsstatistik, Åtkomst till känsliga data med mera. Den här instrumentpanelen är utformad för att hjälpa dig att få säkerhetsinsikter för dina data.
    Du kan också anpassa tidsintervallet och sökfrågan. 
    ![Visa instrumentpanelen För logganalys](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Instrumentpanel för logganalys](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Säkerhetsstatistik för logganalys](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Alternativt kan du även komma åt granskningsloggarna från Log Analytics-bladet. Öppna arbetsytan Log Analytics och klicka på **Loggar**under avsnittet **Allmänt.** Du kan börja med en enkel fråga, till exempel: *sök på "SQLSecurityAuditEvents"* för att visa granskningsloggarna.
    Härifrån kan du också använda [Azure Monitor-loggar](../log-analytics/log-analytics-log-search.md) för att köra avancerade sökningar på dina granskningsloggdata. Azure Monitor-loggar ger dig operativa insikter i realtid med hjälp av integrerade sök- och anpassade instrumentpaneler för att enkelt analysera miljontals poster över alla dina arbetsbelastningar och servrar. Mer användbar information om sökspråk och kommandon för Azure Monitor loggar finns i [sökreferens för Azure Monitor-loggar](../log-analytics/log-analytics-log-search.md).

Om du väljer att skriva granskningsloggar till Event Hub:

- Om du vill använda granskningsloggar från Event Hub måste du konfigurera en ström för att använda händelser och skriva dem till ett mål. Mer information finns i [Dokumentationen för Azure Event Hubs](../event-hubs/index.yml).
- Granskningsloggar i Event Hub fångas in i brödtexten i [Apache Avro-händelser](https://avro.apache.org/) och lagras med JSON-formatering med UTF-8-kodning. Om du vill läsa granskningsloggarna kan du använda [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) eller liknande verktyg som bearbetar det här formatet.

Om du väljer att skriva granskningsloggar till ett Azure-lagringskonto finns det flera metoder som du kan använda för att visa loggarna:

> [!NOTE] 
> Granskning av [skrivskyddade repliker](sql-database-read-scale-out.md) aktiveras automatiskt. Mer information om hierarkin för lagringsmappar, namngivningskonventioner och loggformat finns i [LOGGFORMATET FÖR SQL Database Audit](sql-database-audit-log-format.md). 

- Granskningsloggar sammanställs i det konto du valde under installationen. Du kan utforska granskningsloggar med hjälp av ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/). I Azure-lagring sparas granskningsloggar som en samling blob-filer i en behållare med namnet **sqldbauditlogs**. Mer information om hierarkin för lagringsmappar, namngivningskonventioner och loggformat finns i [LOGGFORMATET FÖR SQL Database Audit](https://go.microsoft.com/fwlink/?linkid=829599).

- Använd [Azure-portalen](https://portal.azure.com).  Öppna relevant databas. Klicka på **Visa granskningsloggar**högst upp på databasens **granskningssida** .

    ![Navigeringsfönster][7]

    **Granskningsposter** öppnas, där du kan visa loggarna.

  - Du kan visa specifika datum genom att klicka på **Filter** högst upp på sidan **Granska poster.**
  - Du kan växla mellan granskningsposter som har skapats av *servergranskningsprincipen* och *databasgranskningsprincipen* genom att växla **granskningskälla**.
  - Du kan bara visa SQL-injektionsrelaterade granskningsposter genom att markera **kryssrutan Visa endast granskningsposter för SQL-injektioner.**

       ![Navigeringsfönster][8]

- Använd systemfunktionen **sys.fn_get_audit_file** (T-SQL) för att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns [i sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Använda **kopplingsgranskningsfiler** i SQL Server Management Studio (från och med SSMS 17):
    1. Välj Öppna**granskningsfiler**för > **filuppfogning** > på SSMS-menyn . **File**

        ![Navigeringsfönster][9]
    2. Dialogrutan **Lägg till granskningsfiler** öppnas. Välj ett av alternativen **Lägg till** för att välja om granskningsfiler ska kopplas från en lokal disk eller importera dem från Azure Storage. Du måste ange dina Azure Storage-information och kontonyckel.

    3. När alla filer som ska sammanfogas har lagts till klickar du på **OK** för att slutföra kopplingen.

    4. Den kopplade filen öppnas i SSMS, där du kan visa och analysera den, samt exportera den till en XEL- eller CSV-fil eller till en tabell.

- Använd Power BI. Du kan visa och analysera granskningsloggdata i Power BI. Mer information och åtkomst till en nedladdningsbar mall finns [i Analysera granskningsloggdata i Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Hämta loggfiler från din Azure Storage-blob-behållare via portalen eller med hjälp av ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/).
  - När du har hämtat en loggfil lokalt dubbelklickar du på filen för att öppna, visa och analysera loggarna i SSMS.
  - Du kan också hämta flera filer samtidigt via Azure Storage Explorer. Om du vill göra det högerklickar du på en viss undermapp och väljer **Spara som** för att spara i en lokal mapp.

- Ytterligare metoder:

  - När du har hämtat flera filer eller en undermapp som innehåller loggfiler kan du sammanfoga dem lokalt enligt beskrivningen i instruktionerna för SSMS Merge Audit Files som beskrivits tidigare.
  - Visa blob granskningsloggar programmässigt:

    - [Fråga utökade händelser filer](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.

## <a name="production-practices"></a><a id="subheading-5"></a>Produktionsmetoder

<!--The description in this section refers to preceding screen captures.-->

### <a name=""></a><a id="subheading-6">Granska geo-replikerade databaser</a>

Med geo-replikerade databaser har den sekundära databasen en identisk granskningsprincip när du aktiverar granskning i den primära databasen. Det är också möjligt att ställa in granskning i den sekundära databasen genom att aktivera granskning på den **sekundära servern**, oberoende av den primära databasen.

- Servernivå **(rekommenderas):** Aktivera granskning på både den **primära servern** och den **sekundära servern** - de primära och sekundära databaserna granskas oberoende av varandra baserat på deras respektive princip på servernivå.
- Databasnivå: Granskning på databasnivå för sekundära databaser kan bara konfigureras från primära databasgranskningsinställningar.
  - Granskning måste vara aktiverat på *själva den primära databasen*, inte servern.
  - När granskning har aktiverats i den primära databasen aktiveras den också i den sekundära databasen.

    >[!IMPORTANT]
    >Med granskning på databasnivå är lagringsinställningarna för den sekundära databasen identiska med de primära databasens, vilket orsakar gränsöverskridande trafik. Vi rekommenderar att du endast aktiverar granskning på servernivå och låter granskningen på databasnivå inaktiveras för alla databaser.

### <a name=""></a><a id="subheading-6">Regenerering av lagringsnyckel</a>

I produktion är det troligt att du uppdaterar lagringsnycklarna med jämna mellanrum. När du skriver granskningsloggar till Azure-lagring måste du spara din granskningsprincip igen när du uppdaterar dina nycklar. Processen är följande:

1. Öppna **lagringsinformation**. Välj **Sekundär**i rutan **Lagringsåtkomstnyckel** och klicka på **OK**. Klicka sedan på **Spara** högst upp på granskningskonfigurationssidan.

    ![Navigeringsfönster][5]
2. Gå till lagringskonfigurationssidan och återskapa den primära åtkomstnyckeln.

    ![Navigeringsfönster][6]
3. Gå tillbaka till granskningskonfigurationssidan, växla åtkomstnyckeln för lagring från sekundär till primär och klicka sedan på **OK**. Klicka sedan på **Spara** högst upp på granskningskonfigurationssidan.
4. Gå tillbaka till lagringskonfigurationssidan och återskapa den sekundära åtkomstnyckeln (som förberedelse för nästa nyckels uppdateringscykel).

## <a name="manage-azure-sql-server-and-database-auditing-using-azure-powershell"></a><a id="subheading-7"></a>Hantera Granskning av Azure SQL Server och databas med Azure PowerShell

**PowerShell-cmdlets (inklusive WHERE-satsstöd för ytterligare filtrering):**

- [Skapa eller uppdatera databasgranskningsprincip (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Skapa eller uppdatera servergranskningsprincip (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Hämta databasrevisionspolicy (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Hämta policy för servergranskning (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Ta bort databasgranskningsprincip (ta bort AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Ta bort servergranskningsprincip (ta bort AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Ett skriptexempel finns i [Konfigurera granskning och hotidentifiering med PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="manage-azure-sql-server-and-database-auditing-using-rest-api"></a><a id="subheading-8"></a>Hantera Granskning av Azure SQL Server och databas med REST API

**REST API**:

- [Skapa eller uppdatera databasgranskningsprincip](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera servergranskningsprincip](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databasgranskningsprincip](/rest/api/sql/database%20auditing%20settings/get)
- [Hämta princip för servergranskning](/rest/api/sql/server%20auditing%20settings/get)

Utökad princip med WHERE-satsstöd för ytterligare filtrering:

- [Skapa eller uppdatera *databas utökad* granskningsprincip](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera *serverns utökade* granskningsprincip](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta *databas utökad* granskningsprincip](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Hämta princip *för utökad* granskning av servern](/rest/api/sql/server%20auditing%20settings/get)

## <a name="manage-azure-sql-server-and-database-auditing-using-azure-resource-manager-templates"></a><a id="subheading-9"></a>Hantera Granskning av Azure SQL Server och databas med Azure Resource Manager-mallar

Du kan hantera Granskning av Azure SQL-databaser med Azure Resource Manager-mallar, vilket visas i följande exempel: [Azure Resource Manager](../azure-resource-manager/management/overview.md)

- [Distribuera en Azure SQL Server med granskning aktiverad för att skriva granskningsloggar till Azure Blob storage-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuera en Azure SQL Server med granskning aktiverad för att skriva granskningsloggar till Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuera en Azure SQL Server med granskning aktiverad för att skriva granskningsloggar till eventhubbar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De länkade exemplen finns i ett externt offentligt arkiv och tillhandahålls "i befintligt fall", utan garanti och stöds inte under något Microsoft-supportprogram/-tjänst.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage Azure SQL Server and Database auditing using Azure PowerShell]: #subheading-7
[Manage SQL database auditing using REST API]: #subheading-8
[Manage Azure SQL Server and Database auditing using ARM templates]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png 
