---
title: Kom igång med granskning
description: Använd Azure SQL Database-granskning för att spåra databas händelser i en Gransknings logg.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 58a446baaf15facab3a85d4d83defdeb5e6d2ef9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851493"
---
# <a name="get-started-with-sql-database-auditing"></a>Kom igång med SQL-databasgranskning

Granskning för Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) spårar databas händelser och skriver dem till en Gransknings logg i ditt Azure storage-konto, Log Analytics arbets yta eller Event Hubs. Granskning gör även följande:

- Det hjälper dig att upprätthålla regelefterlevnad, förstå databasaktiviteter och få insikter om i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

- Det främjar och underlättar uppfyllandet av efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure-program som stöder standardkompatibilitet finns i [Azure Säkerhetscenter](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den mest aktuella listan med SQL Database certifierings certifieringar.


> [!NOTE] 
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Översikt över Azure SQL Database-granskning

Du kan använda SQL-databasgranskning för att:

- **Behåll** en gransknings historik för markerade händelser. Du kan definiera kategorier med databasåtgärder som ska granskas.
- **Rapport** om databas aktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att snabbt komma igång med aktivitets- och händelserapportering.
- **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

> [!IMPORTANT]
> Gransknings loggar skrivs för att **lägga till blobar** i Azure Blob Storage i din Azure-prenumeration.
>
> - Alla lagrings typer (v1, v2, BLOB) stöds.
> - Alla konfigurationer av lagringsprovider stöds.
> - **Premium Storage** **stöds inte**för närvarande.
> - **Lagring i VNet** **stöds inte**för närvarande.
> - Det finns för närvarande **inte stöd**för **lagring bakom en brand vägg** .
> - Det finns för närvarande **inte stöd**för **hierarkiskt namn område** för **Azure Data Lake Storage Gen2 lagrings konto** .

## <a id="subheading-8"></a>Definiera server nivå kontra gransknings princip på databas nivå

En gransknings princip kan definieras för en viss databas eller som en standard princip för servern:

- En server princip gäller för alla befintliga och nyligen skapade databaser på servern.

- Om *Server-BLOB-granskning är aktiverat* *gäller den alltid för-databasen*. Databasen kommer att granskas, oavsett databas gransknings inställningar.

- Att aktivera BLOB-granskning på databasen eller data lagret, förutom att aktivera det på-servern, Åsidosätt eller ändra *inte* inställningarna för Server-BLOB-granskning. Båda granskningarna kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger parallellt. en gång av Server principen och en gång av databas principen.

   > [!NOTE]
   > Du bör undvika att aktivera både server-BLOB-granskning och databas-BLOB-granskning, om inte:
    > - Du vill använda ett annat *lagrings konto* eller en *kvarhållningsperiod* för en viss databas.
    > - Du vill granska händelse typer eller kategorier för en speciell databas som skiljer sig från resten av databaserna på servern. Du kan till exempel ha tabell infogningar som bara behöver granskas för en speciell databas.
   >
   > Annars rekommenderar vi att du bara aktiverar BLOB-granskning på server nivå och lämnar granskning på databas nivå inaktive rad för alla databaser.

## <a id="subheading-2"></a>Konfigurera granskning för databasen

I följande avsnitt beskrivs konfigurationen av granskning med hjälp av Azure Portal.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Navigera till **granskning** under säkerhets rubriken i fönstret SQL Database/Server.

    <a id="auditing-screenshot"></a>![Navigeringsfönstret][1]

3. Om du vill konfigurera en server gransknings princip kan du välja länken **Visa Server inställningar** på sidan databas granskning. Du kan sedan Visa eller ändra server gransknings inställningarna. Server gransknings principer gäller för alla befintliga och nyligen skapade databaser på den här servern.

    ![Navigeringsfönster][2]

4. Om du föredrar att aktivera granskning på databas nivån växlar du **granskning** till **på**.

    Om Server granskning är aktive rad, finns den databas-konfigurerade granskningen sida vid sida med Server granskningen.

    ![Navigeringsfönster][3]

5. **Ny** – nu har du flera alternativ för att konfigurera var gransknings loggar ska skrivas. Du kan skriva loggar till ett Azure Storage-konto till en Log Analytics arbets yta för användning genom att Azure Monitor loggar eller till Event Hub för användning med Event Hub. Du kan konfigurera valfri kombination av dessa alternativ och gransknings loggarna skrivs till var och en.

   > [!WARNING]
   > Om du aktiverar granskning till Log Analytics debiteras kostnaden baserat på inmatnings taxan. Var medveten om den associerade kostnaden med hjälp av det här [alternativet](https://azure.microsoft.com/pricing/details/monitor/)eller Överväg att lagra gransknings loggarna i ett Azure Storage-konto.

    ![lagrings alternativ](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Om du vill konfigurera att skriva gransknings loggar till ett lagrings konto väljer du **lagring** och öppna **lagrings information**. Välj det Azure Storage-konto där loggar ska sparas och välj sedan kvarhållningsperioden. De gamla loggarna tas bort. Klicka på **OK**.

   > [!IMPORTANT]
   > - Standardvärdet för kvarhållningsperiod är 0 (obegränsad kvarhållning). Du kan ändra det här värdet genom att flytta skjutreglaget för **kvarhållning (dagar)** i **lagrings inställningarna** när du konfigurerar lagrings kontot för granskning.
   > - Om du ändrar kvarhållningsperioden från 0 (obegränsad kvarhållning) till ett annat värde, Observera att kvarhållning endast kommer att gälla för loggar som skrivs efter att kvarhållning har ändrats (loggar som skrivits under perioden när kvarhållning hade värdet obegränsat bevaras, även efter kvarhållning är aktiverat)

    ![storage account](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Om du vill konfigurera att skriva gransknings loggar till en Log Analytics arbets yta väljer du **Log Analytics (förhands granskning)** och öppnar **Log Analytics information**. Välj eller skapa arbets ytan Log Analytics där loggarna ska skrivas och klicka sedan på **OK**.

    ![Log Analytics-arbetsyta](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Om du vill konfigurera att skriva gransknings loggar till en Event Hub väljer du **Event Hub (för hands version)** och **information om**att öppna händelsehubben. Välj den händelsehubben där loggar ska skrivas och klicka sedan på **OK**. Se till att händelsehubben är i samma region som din databas och server.

    ![Händelsehubb](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Klicka på **Save** (Spara).
10. Om du vill anpassa de granskade händelserna kan du göra detta via PowerShell- [cmdletar](#subheading-7) eller [REST API](#subheading-9).
11. När du har konfigurerat dina gransknings inställningar kan du aktivera den nya funktionen för hot identifiering och konfigurera e-postmeddelanden för att få säkerhets aviseringar. När du använder hot identifiering får du proaktiva aviseringar om avvikande databas aktiviteter som kan innebära potentiella säkerhetshot. Mer information finns i [komma igång med hot identifiering](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Det går inte att aktivera granskning på en pausad Azure SQL Data Warehouse. Om du vill aktivera det avbryter du data lagret.

> [!WARNING]
> Om du aktiverar granskning på en server som har en Azure SQL Data Warehouse på den **, kommer data lagret att återupptas och pausas igen,** vilket kan ådra sig vid fakturerings avgifter.

## <a id="subheading-3"></a>Analysera gransknings loggar och rapporter

Om du väljer att skriva gransknings loggar till Azure Monitor loggar:

- Använd [Azure-portalen](https://portal.azure.com).  Öppna relevant databas. Klicka på **Visa gransknings loggar**överst i databasens **gransknings** sida.

    ![Visa gransknings loggar](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- Sedan kan du visa loggarna på två sätt:
    
    Om du klickar på **Log Analytics** överst på sidan **gransknings poster** öppnas vyn loggar i Log Analytics arbets yta där du kan anpassa tidsintervallet och Sök frågan.
    
    ![Öppna i Log Analytics arbets ytan](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    Om du klickar på **Visa instrument panel** överst på sidan **gransknings poster** öppnas en instrument panel med information om gransknings loggar, där du kan öka detalj nivån i säkerhets insikter, komma åt känsliga data och mycket mer. Den här instrument panelen är utformad för att hjälpa dig att få säkerhets insikter om dina data.
    Du kan också anpassa tidsintervallet och Sök frågan. 
    ![Visa Log Analytics instrument panel](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics instrument panel](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics säkerhets insikter](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- Du kan också få åtkomst till gransknings loggarna från Log Analytics bladet. Öppna arbets ytan Log Analytics och klicka på **loggar**under **Allmänt** . Du kan börja med en enkel fråga, till exempel: *Sök efter "SQLSecurityAuditEvents"* för att Visa gransknings loggarna.
    Härifrån kan du också använda [Azure Monitor loggar](../log-analytics/log-analytics-log-search.md) för att köra avancerade sökningar på dina Gransknings logg data. Med Azure Monitor loggar får du operativa insikter i real tid med integrerad sökning och anpassade instrument paneler för att enkelt analysera miljon tals poster över alla dina arbets belastningar och servrar. Mer värdefull information om Azure Monitor loggar Sök språk och-kommandon finns i [Sök referens för Azure Monitor loggar](../log-analytics/log-analytics-log-search.md).

Om du väljer att skriva gransknings loggar till Händelsehubben:

- Om du vill använda gransknings loggar från Händelsehubben måste du konfigurera en data ström för att använda händelser och skriva dem till ett mål. Mer information finns i [Azure Event Hubs-dokumentationen](../event-hubs/index.yml).
- Gransknings loggar i Händelsehubben samlas in i bröd texten i [Apache Avro](https://avro.apache.org/) -händelser och lagras med JSON-FORMATERING med UTF-8-kodning. Om du vill läsa gransknings loggarna kan du använda [Avro-verktyg](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) eller liknande verktyg som bearbetar det här formatet.

Om du väljer att skriva gransknings loggar till ett Azure Storage-konto finns det flera metoder som du kan använda för att visa loggarna:

> [!NOTE] 
> Granskning på [skrivskyddade repliker](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-read-scale-out) aktive ras automatiskt. Mer information om hierarkin för lagrings-mappar, namngivnings konventioner och logg format finns i [SQL Database Gransknings logg format](sql-database-audit-log-format.md). 

- Gransknings loggar sammanställs i det konto som du valde under installationen. Du kan utforska gransknings loggar med hjälp av ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/). I Azure Storage sparas gransknings loggar som en samling BLOB-filer i en behållare med namnet **sqldbauditlogs**. Mer information om hierarkin för lagrings-mappar, namngivnings konventioner och logg format finns i [SQL Database Gransknings logg format](https://go.microsoft.com/fwlink/?linkid=829599).

- Använd [Azure-portalen](https://portal.azure.com).  Öppna relevant databas. Klicka på **Visa gransknings loggar**överst i databasens **gransknings** sida.

    ![Navigeringsfönster][7]

    **Gransknings poster** öppnas där du kan visa loggarna.

  - Du kan visa vissa datum genom att klicka på **filter** överst på sidan **gransknings poster** .
  - Du kan växla mellan gransknings poster som har skapats av *Server gransknings principen* och *databas gransknings principen* genom att växla **gransknings källa**.
  - Du kan bara visa SQL-injektering relaterade gransknings poster genom att markera kryss rutan **Visa endast gransknings poster för SQL-injektering** .

       ![Navigeringsfönster][8]

- Använd system funktionen **sys. fn_get_audit_file** (T-SQL) för att returnera Gransknings logg data i tabell format. Mer information om hur du använder den här funktionen finns i [sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Använda **sammanslagna gransknings filer** i SQL Server Management Studio (från och med SSMS 17):
    1. Från menyn SSMS väljer du **fil** > **öppnar** > **slå samman gransknings filer**.

        ![Navigeringsfönster][9]
    2. Dialog rutan **Lägg till gransknings filer** öppnas. Välj ett av alternativen **Lägg** till för att välja om du vill slå samman gransknings filer från en lokal disk eller importera dem från Azure Storage. Du måste ange Azure Storage information och konto nyckeln.

    3. När du har lagt till alla filer som ska sammanfogas klickar du på **OK** för att slutföra sammanslagnings åtgärden.

    4. Den sammanslagna filen öppnas i SSMS, där du kan visa och analysera den, samt exportera den till en XEL-eller CSV-fil eller till en tabell.

- Använd Power BI. Du kan visa och analysera Gransknings logg data i Power BI. Mer information och åtkomst till en hämtnings bar mall finns i [analysera Gransknings logg data i Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Ladda ned loggfiler från din Azure Storage BLOB-behållare via portalen eller med ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/).
  - När du har laddat ned en loggfil lokalt, dubbelklickar du på filen för att öppna, Visa och analysera loggarna i SSMS.
  - Du kan också hämta flera filer samtidigt via Azure Storage Explorer. Det gör du genom att högerklicka på en angiven undermapp och välja **Spara som** för att spara i en lokal mapp.

- Ytterligare metoder:

  - När du har laddat ned flera filer eller en undermapp som innehåller loggfiler kan du sammanfoga dem lokalt enligt beskrivningen i anvisningarna för SSMS-sammanslagning av filer som beskrivs ovan.
  - Visa BLOB-gransknings loggar program mässigt:

    - [Fråga om utökade Events-filer](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.

## <a id="subheading-5"></a>Produktions metoder

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Granska geo-replikerade databaser</a>

När du aktiverar granskning på den primära databasen med geo-replikerade databaser, kommer den sekundära databasen ha en identisk gransknings princip. Du kan också konfigurera granskning på den sekundära databasen genom att aktivera granskning på den **sekundära servern**, oberoende av den primära databasen.

- Server nivå (**rekommenderas**): aktivera granskning på både den **primära servern** och den **sekundära servern** – de primära och sekundära databaserna kommer att granskas oberoende av varandra baserat på deras respektive princip på server nivå.
- Databas nivå: granskning på databas nivå för sekundära databaser kan bara konfigureras från primära databas gransknings inställningar.
  - Granskning måste vara aktiverat på själva den *primära databasen*, inte på servern.
  - När granskning har Aktiver ATS på den primära databasen aktive ras den också på den sekundära databasen.

    >[!IMPORTANT]
    >Med granskning på databas nivå blir lagrings inställningarna för den sekundära databasen identiska med de som finns i den primära databasen, vilket orsakar en över-regional trafik. Vi rekommenderar att du bara aktiverar granskning på server nivå och låter granskning på databas nivå vara inaktive rad för alla databaser.

### <a id="subheading-6">Återskapa lagrings nyckel</a>

I produktion är det troligt att du uppdaterar dina lagrings nycklar med jämna mellanrum. När du skriver gransknings loggar till Azure Storage måste du spara om gransknings principen när du uppdaterar dina nycklar. Processen ser ut så här:

1. Öppna **lagrings information**. I rutan **lagrings åtkomst nyckel** väljer du **sekundär**och klickar på **OK**. Klicka sedan på **Spara** högst upp på sidan gransknings konfiguration.

    ![Navigeringsfönster][5]
2. Gå till sidan lagrings konfiguration och återskapa den primära åtkomst nyckeln.

    ![Navigeringsfönster][6]
3. Gå tillbaka till sidan gransknings konfiguration, växla lagrings åtkomst nyckeln från sekundär till primär och klicka sedan på **OK**. Klicka sedan på **Spara** högst upp på sidan gransknings konfiguration.
4. Gå tillbaka till sidan lagrings konfiguration och återskapa den sekundära åtkomst nyckeln (som förberedelse för nästa nyckels uppdaterings cykel).

## <a name="additional-information"></a>Ytterligare information

- Mer information om logg formatet, hierarkin för lagringsmappen och namngivnings konventioner finns i [referensen för logg format för BLOB-granskning](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL Database granskning lagrar 4000 tecken med data för tecken fält i en gransknings post. När **instruktionen** eller **data_sensitivity_information** värden som returneras från en gransknings bar åtgärd innehåller fler än 4000 tecken kommer data utöver de första 4000 tecknen att **trunkeras och inte granskas**.

- Gransknings loggar skrivs för att **bifoga blobbar** i en Azure Blob-lagring i din Azure-prenumeration:
  - **Premium Storage** stöds för närvarande **inte** av bifogade blobbar.
  - **Lagring i VNet** **stöds inte**för närvarande.

- Standard gransknings principen omfattar alla åtgärder och följande uppsättning åtgärds grupper, som granskar alla frågor och lagrade procedurer som körs mot databasen, samt lyckade och misslyckade inloggningar:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Du kan konfigurera granskning för olika typer av åtgärder och åtgärds grupper med hjälp av PowerShell, enligt beskrivningen i avsnittet [hantera granskning av SQL-databas med hjälp av Azure PowerShell](#subheading-7) .

- När du använder AAD-autentisering, kommer misslyckade inloggnings poster *inte* visas i SQL-gransknings loggen. Om du vill visa gransknings poster för misslyckad inloggning måste du gå till [Azure Active Directory Portal]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)som loggar information om dessa händelser.


## <a id="subheading-7"></a>Hantera granskning av SQL Database med hjälp av Azure PowerShell

**PowerShell-cmdletar (inklusive WHERE-sats stöd för ytterligare filtrering)** :

- [Skapa eller uppdatera databas gransknings princip (Set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Skapa eller uppdatera Server gransknings princip (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Hämta databas gransknings princip (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Hämta Server gransknings princip (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Ta bort databas gransknings princip (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Ta bort Server gransknings princip (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Ett skript exempel finns i [Konfigurera granskning och hot identifiering med PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Hantera granskning av SQL Database med hjälp av REST API

**REST-API**:

- [Skapa eller uppdatera databas gransknings princip](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera Server gransknings princip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databas gransknings princip](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Hämta Server gransknings princip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Utökad princip med stöd för WHERE-sats för ytterligare filtrering:

- [Skapa eller uppdatera *utökad* gransknings princip för databasen](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera *utökad* gransknings princip för Server](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta *utökad* gransknings princip för databas](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Hämta *utökad* gransknings princip för Server](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Hantera granskning av SQL Database med hjälp av Azure Resource Manager mallar

Du kan hantera Azure SQL Database-granskning med [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallar, som du ser i följande exempel:

- [Distribuera ett Azure-SQL Server med granskning aktiverat för att skriva gransknings loggar till Azure Blob Storage-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuera ett Azure-SQL Server med granskning aktiverat för att skriva gransknings loggar till Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuera ett Azure-SQL Server med granskning aktiverat för att skriva gransknings loggar till Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> De länkade exemplen finns på ett externt offentligt lager och tillhandahålls i befintligt skick, utan garanti, och stöds inte i Microsoft Support program/-tjänster.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

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
