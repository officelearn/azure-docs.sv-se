---
title: Kom igång med Azure SQL database-granskning | Microsoft Docs
description: Använda Azure SQL database-granskning för att spåra databashändelser till en granskningslogg.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 2a0bacaf0405a5223afedcd3897e2a1514f7128b
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466689"
---
# <a name="get-started-with-sql-database-auditing"></a>Kom igång med SQL-databasgranskning

Azure SQL-databasgranskning spårar databashändelser och skriver dem till en granskningslogg i ditt Azure storage-konto. Granskning också:

- Hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

- Aktiverar och underlättar infört efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Azure SQL database auditing översikt

Du kan använda SQL database auditing till att:

- **Behåll** granskningsspårning av valda händelser. Du kan definiera kategorier av databasåtgärder som ska granskas.
- **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktiviteten och rapportera händelser.
- **Analysera** rapporter. Du hittar misstänkta händelser, ovanliga aktiviteter och trender.

Du kan konfigurera granskning för olika typer av händelsekategorier, enligt beskrivningen i den [konfigurera granskning för din databas](#subheading-2) avsnittet.

> [!IMPORTANT]
> Granskningsloggarna skrivs till **Tilläggsblobbar** i Azure Blob storage på din Azure-prenumeration.
>
> - **Premium Storage** är för närvarande **stöds inte** av Tilläggsblobbar.
> - **Storage i det virtuella nätverket** är för närvarande **stöds inte**.

## <a id="subheading-8"></a>Definiera på servernivå och databasnivå granskningsprincip

En granskningsprincip kan definieras för en specifik databas eller som en standardprincip för servern:

- En Serverprincipen gäller för alla befintliga och nya databaser på servern.

- Om *blobgranskning är aktiverat*, den *alltid gäller för databasen*. Databasen granskas oavsett databasen granskningsinställningar.

- Att aktivera blobbgranskning på databasen, samt för att aktivera den på servern har *inte* åsidosätta eller ändra några av inställningarna för den blobgranskning. Båda granskningar kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger i parallella; en gång av princip för server och en gång av princip för databasen.

   > [!NOTE]
   > Du bör undvika att aktivera både blobgranskning och databasen blobbgranskning tillsammans, såvida inte:
    > - Du vill använda en annan *lagringskonto* eller *kvarhållningsperioden* för en viss databas.
    > - Du vill granska händelsetyper eller kategorier för en viss databas som skiljer sig från resten av databaser på servern. Du kan till exempel ha tabellen infogningar som behöver granskas endast för en viss databas.
   >
   > I annat fall rekommenderar vi att du aktiverar bara servernivå blobbgranskning och lämna granskning på databasnivå inaktiverad för alla databaser.

## <a id="subheading-2"></a>Konfigurera granskning för din databas

I följande avsnitt beskrivs konfigurationen av granskning med Azure portal.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Gå till **granskning** under rubriken säkerheten i din SQL database-server-fönstret.

    <a id="auditing-screenshot"></a>![Navigeringsfönstret][1]

3. Om du vill ställa in en granskningsprincip för server, kan du välja den **visa serverinställningar** länk på granskning database-sidan. Du kan sedan visa eller ändra serverns granskningsinställningar. Server granska principer gäller för alla befintliga och nya databaser på den här servern.

    ![Navigeringsfönster][2]

4. Om du vill aktivera granskning på databasnivå kan växla **granskning** till **på**.

    Om servergranskning är aktiverat, kommer att finnas sida vid sida med server audit databasens konfigurerade granskning.

    ![Navigeringsfönster][3]

5. **Nya** – nu har du flera alternativ för att konfigurera där granskningsloggar ska skrivas. Du kan skriva loggar till ett Azure storage-konto, till en Log Analytics-arbetsyta för användning av Log Analytics eller till event hub för förbrukning med händelsehubben. Du kan konfigurera en kombination av dessa alternativ och granskningsloggar skrivs till var och en.

    ![lagringsalternativ](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Konfigurera granskning för skrivning loggar till ett lagringskonto, väljer **Storage** och öppna **lagringsinformation**. Välj Azure-lagringskontot där loggarna sparas och välj sedan kvarhållningsperioden. De gamla loggarna tas bort. Klicka sedan på **OK**.

    ![storage account](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Konfigurera granskning för skrivning loggar till en Log Analytics-arbetsyta, väljer **Log Analytics (förhandsversion)** och öppna **Log Analytics information**. Välj eller skapa Log Analytics-arbetsyta där loggarna sparas, och klicka sedan på **OK**.

    ![Log Analytics](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Konfigurera granskning för skrivning loggar till en händelsehubb, Välj **Event Hub (förhandsversion)** och öppna **information om Händelsehubb**. Välj händelsehubb där loggarna sparas, och klicka sedan på **OK**. Var noga med att event hub finns i samma region som din databas och server.

    ![Händelsehubb](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Klicka på **Spara**.
10. Om du vill anpassa de granskade händelserna, kan du göra detta via [PowerShell-cmdletar](#subheading-7) eller [REST API](#subheading-9).
11. När du har konfigurerat inställningarna för granskning kan du aktivera funktionen för identifiering av nya hot och konfigurera e-postmeddelanden om du vill få säkerhetsaviseringar. När du använder hotidentifiering kan få du proaktiva varningar på avvikande databasaktiviteter som kan innebära potentiella säkerhetshot. Mer information finns i [komma igång med hotidentifiering](sql-database-threat-detection-get-started.md).

## <a id="subheading-3"></a>Analysera granskningsloggar och rapporter

Om du har valt att skriva granskningsloggar till Log Analytics:

- Använd den [Azure-portalen](https://portal.azure.com).  Öppna den aktuella databasen. Överst på databasens **granskning** klickar du på **visa granskningsloggar**.

    ![Visa granskningsloggar](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Klicka på **öppna i OMS** överst i den **granskningsposter** vyn loggar öppnas i Log Analytics, där du kan anpassa tidsintervallet och sökfrågan.

    ![Öppna i Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Du kan också du kan också komma åt granskningsloggarna från Log Analytics-bladet. Öppna Log Analytics-arbetsytan och under **Allmänt** klickar du på **loggar**. Du kan börja med en enkel fråga, till exempel: *Sök ”SQLSecurityAuditEvents”* att visa granskningen loggar.
    Härifrån kan du också använda [Log Analytics](../log-analytics/log-analytics-log-search.md) att köra avancerade sökningar på din granskningsloggdata. Log Analytics ger dig operational realtidsinsikter med integrerad sökning och anpassade instrumentpaneler för snabb analys av miljontals poster över alla dina arbetsbelastningar och servrar. Ytterligare användbar information om Log Analytics-frågespråket och kommandon finns i [Log Analytics Sök referens](../log-analytics/log-analytics-log-search.md).

Om du har valt att skriva granskningsloggar till Event Hub:

- Om du vill använda granskning loggar data från Event Hub, behöver du ställer in en dataström som förbrukar händelser och skriva dem till ett mål. Mer information finns i [dokumentation om Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).

Om du väljer att skriva granskningsloggar till ett Azure storage-konto, finns det flera metoder som du kan använda för att visa loggfilerna:

- Granskningsloggar räknas samman på kontot du valde i installationsprogrammet. Du kan utforska granskningsloggar genom att använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/). I Azure storage granskningsloggarna sparas som en samling av blobfiler i en behållare med namnet **sqldbauditlogs**. Ytterligare information om hierarkin för mappen storage namngivningskonventioner och loggformat, finns det [referens till Blob granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

- Använd den [Azure-portalen](https://portal.azure.com).  Öppna den aktuella databasen. Överst på databasens **granskning** klickar du på **visa granskningsloggar**.

    ![Navigeringsfönster][7]

    **Granskningsposter** öppnas, där du kommer att kunna visa loggarna.

  - Du kan visa specifika datum genom att klicka på **Filter** överst i den **granskningsposter** sidan.
  - Du kan växla mellan granskningsposter som har skapats av den *server granskningsprincip* och *databasen granskningsprincip* genom att ändra **granska källa**.
  - Du kan visa endast SQL-inmatning relaterade granskningsposter genom att kontrollera **visa enbart granskningsposter för SQL-inmatningar** kryssrutan.

       ![Navigeringsfönster][8]

- Använd systemfunktionen **sys.fn_get_audit_file** (T-SQL) att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns i [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Använd **sammanfoga granskningsfilerna** i SQL Server Management Studio (startar med SSMS 17):
    1. SSMS-menyn väljer **filen** > **öppna** > **sammanfoga granskningsfilerna**.

        ![Navigeringsfönster][9]
    2. Den **lägga till granskningsfilerna** öppnas dialogrutan. Välj en av de **Lägg till** alternativ att välja om du vill slå samman granskningsfilerna från en lokal disk eller importera dem från Azure Storage. Du måste ange din information för Azure Storage och kontonyckel.

    3. När alla filer att slå samman har lagts till, klickar du på **OK** att slutföra merge-operation.

    4. Sammanfogade filen öppnas i SSMS, där du kan visa och analysera dem, samt exportera den till en XEL eller CSV-fil, eller till en tabell.

- Använda Powerbi. Du kan visa och analysera granskningsloggdata i Power BI. Mer information och för att komma åt en nedladdningsbar mall finns i [Analyzie granskningsloggdata i Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Ladda ned loggfiler från dina Azure Storage blob-behållare via portalen eller genom att använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/).
  - När du har hämtat en loggfil lokalt, dubbelklicka på filen för att öppna, visa och analysera loggar i SSMS.
  - Du kan också hämta flera filer samtidigt via Azure Storage Explorer. Du gör detta genom att högerklicka på en viss undermapp och välj **Spara som** att spara i en lokal mapp.

- Ytterligare metoder:

  - När du hämtat flera filer eller en undermapp som innehåller loggfiler, kan du slå samman dem lokalt enligt beskrivningen i SSMS Merge granskningsfilerna anvisningarna som beskrivs ovan.
  - Visa blobbgranskning loggar programmässigt:

    - Använd den [utökade händelser läsare](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C#-biblioteket.
    - [Frågefiler utökade händelser](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.

## <a id="subheading-5"></a>Produktionsmetoder

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Granskning geo-replikerade databaser</a>

Med geo-replikerade databaser när du aktiverar granskning på den primära databasen har den sekundära databasen en identisk granskningsprincip. Det är också möjligt att ställa in granskning på den sekundära databasen genom att aktivera granskning på **sekundär server**, oberoende av den primära databasen.

- På servernivå (**rekommenderas**): aktivera granskning på både den **primärservern** samt de **sekundär server** -primära och sekundära databaserna var granskas oberoende av varandra utifrån deras respektive princip på servernivå.
- Databasnivå: Databasnivå granskning för sekundära databaser kan bara konfigureras från den primära databasen granskningsinställningar.
  - Granskning måste vara aktiverat på den *primära databasen själva*, inte på servern.
  - När granskning har aktiverats på den primära databasen kan aktiveras det också på den sekundära databasen.

    >[!IMPORTANT]
    >Med granskning på databasnivå, ska lagringsinställningarna för den sekundära databasen vara identiskt för den primära databasen orsakar tvärregional trafik. Vi rekommenderar att du aktiverar endast serverniv och lämna granskning på databasnivå inaktiverad för alla databaser.

### <a id="subheading-6">Storage åtkomstnyckeln återskapades</a>

I produktion förmodligen du uppdatera dina storage-nycklar med jämna mellanrum. När du skriver granskningsloggar till Azure storage, måste du spara om granskningsprincipen när du uppdaterar dina nycklar. Processen är följande:

1. Öppna **lagringsinformation**. I den **Lagringsåtkomstnyckel** väljer **sekundära**, och klicka på **OK**. Klicka sedan på **spara** överst på konfigurationssidan för granskning.

    ![Navigeringsfönster][5]
2. Gå till konfigurationssidan för lagring och återskapa den primära åtkomstnyckeln.

    ![Navigeringsfönster][6]
3. Gå tillbaka till konfigurationssidan granskning växla lagringsåtkomstnyckel från sekundär till primär och klicka sedan på **OK**. Klicka sedan på **spara** överst på konfigurationssidan för granskning.
4. Gå tillbaka till konfigurationssidan för lagring och återskapa den sekundära åtkomstnyckeln (som förberedelse inför nästa tangent uppdateringscykeln).

## <a name="additional-information"></a>Ytterligare information

- Mer information om loggen formatera hierarki på lagringsmappen och namngivningskonventioner, finns i den [referens till Blob granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL-databasen och Audit lagrar 4 000 tecken av data för Teckenfält i en granskningspost. När den **instruktionen** eller **data_sensitivity_information** värden som returneras från en granskningsbar åtgärd innehålla fler än 4 000 tecken kommer alla data utöver de första 4 000 tecken att  **förkortas och inte granskas**.

- Granskningsloggarna skrivs till **Tilläggsblobbar** i Azure Blob storage på din Azure-prenumeration:
  - **Premium Storage** är för närvarande **stöds inte** av Tilläggsblobbar.
  - **Storage i det virtuella nätverket** är för närvarande **stöds inte**.

- Granskning standardprincipen innehåller följande uppsättning åtgärdsgrupper som granskar alla frågor och lagrade procedurer som körs mot databasen, samt lyckade och misslyckade inloggningar och alla åtgärder:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Du kan konfigurera granskning för olika typer av åtgärder och åtgärdsgrupper med hjälp av PowerShell, enligt beskrivningen i den [hantera SQL database-granskning med Azure PowerShell](#subheading-7) avsnittet.

## <a id="subheading-7"></a>Hantera SQL database-granskning med Azure PowerShell

**PowerShell-cmdletar**:

- [Skapa eller uppdatera databasen Blob granskningsprincip (Set-AzureRMSqlDatabaseAuditing)][105]
- [Skapa eller uppdatera Server Blob granskningsprincip (Set-AzureRMSqlServerAuditing)][106]
- [Hämta databasen granskningsprincip (Get-AzureRMSqlDatabaseAuditing)][101]
- [Hämta Server Blob granskningsprincip (Get-AzureRMSqlServerAuditing)][102]

Ett skript-exempel finns i [konfigurera granskning och hotidentifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Hantera SQL database-granskning med REST API

**REST-API – blobbgranskning**:

- [Skapa eller uppdatera databasen Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera Server Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databasen Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Hämta Server Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Utökade princip med där satsen stöd för ytterligare filtrering:

- [Skapa eller uppdatera databasen *utökade* Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera Server *utökade* Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databas *utökade* Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Hämta Server *utökade* Blob granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9

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

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing
