---
title: Granskning i Azure SQL Data Warehouse | Microsoft Docs
description: Läs mer om granskning och hur du ställer in granskning i Azure SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/11/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 701c5c175e22556aefa95d1ac3836d52cd19b845
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342312"
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Granskning i Azure SQL Data Warehouse

Läs mer om granskning och hur du ställer in granskning i Azure SQL Data Warehouse.

## <a name="what-is-auditing"></a>Vad granskning?
Granskning av SQL Data Warehouse kan du till post i din databas till en granskningslogg händelselogg i Azure Storage-kontot. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

Granskningsverktyg aktivera och underlätta infört efterlevnadsstandarder men garanterar inte efterlevnad. Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://azure.microsoft.com/support/trust-center/compliance/).

## <a id="subheading-1"></a>Granskning grunderna
Granskning av SQL Data Warehouse-databas kan du:

* **Behåll** granskningsspårning av valda händelser. Du kan definiera kategorier av databasåtgärder som ska granskas.
* **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktiviteten och rapportera händelser.
* **Analysera** rapporter. Du hittar misstänkta händelser, ovanliga aktiviteter och trender.

Granskningsloggar lagras i Azure storage-kontot. Du kan definiera en loggkvarhållningsperiod för granskning.


## <a id="subheading-4"></a>Definiera på servernivå och databasnivå granskningsprincip

En granskningsprincip kan definieras för en specifik databas eller som en standardprincip för servern:

* En serverprincip för **gäller för alla befintliga och nya databaser** på servern.

* Om *blobgranskning är aktiverat*, den *alltid gäller för databasen*. Databasen granskas oavsett databasen granskningsinställningar.

* Aktivera granskning på databasen, samt för att aktivera den på servern har *inte* åsidosätta eller ändra några av inställningarna för den blobgranskning. Båda granskningar kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger i parallella; en gång av princip för server och en gång av princip för databasen.

> [!NOTE]
> Vi rekommenderar att du aktiverar **bara servernivå blobbgranskning** och lämna granskning på databasnivå inaktiverad för alla databaser.
> Du bör undvika att aktivera både servergranskning och databasgranskning tillsammans, såvida inte:
> * Du vill använda en annan *lagringskonto* eller *kvarhållningsperioden* för en viss databas.
> * Du vill granska händelsetyper eller kategorier för en viss databas som skiljer sig från resten av databaser på servern. Du kan till exempel ha tabellen infogningar som behöver granskas endast för en viss databas.
> * Du vill använda Hotidentifiering, vilket är för närvarande stöds endast med granskning på databasnivå.

> [!IMPORTANT]
>Aktivera granskning på en Azure SQL Data Warehouse eller på en server som har en Azure SQL Data Warehouse, **leder till datalagret återtas**, även i de fall där det tidigare pausades. **Glöm inte att pausa informationslagret igen när du har aktiverat granskning**.

## <a id="subheading-5"></a>Konfigurera serverniv för alla databaser

En server granskningsprincip som gäller för **alla befintliga och nya databaser** på servern.

I följande avsnitt beskrivs konfigurationen av granskning med Azure portal.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Gå till den **SQLServer** som du vill granska (viktigt, se till att du visar SQL-servern, inte en specifik databas/DW). I den **Security** menyn och välj **granskning och Hotidentifiering**.

    ![Navigeringsfönster][6]
4. I den *granskning och Hotidentifiering* bladet för **granskning** Välj **på**. Den här granskningsprincip gäller för alla befintliga och nya databaser på den här servern.

    ![Navigeringsfönster][7]
5. Öppna den **granska loggarna Storage** bladet väljer **lagringsinformation**. Välj eller skapa Azure-lagringskontot där loggarna sparas och välj sedan kvarhållningsperioden (gamla loggarna tas bort). Klicka sedan på **OK**.

    ![Navigeringsfönster][8]

    > [!IMPORTANT]
    > Granskningsloggarna skrivs till **Tilläggsblobbar** i Azure Blob storage på din Azure-prenumeration.
    >
    > - Alla storage-typer (v1, v2, blob) stöds.
    > - Alla lagringskonfigurationer replikering stöds.
    > - **Premiumlagring** är för närvarande **stöds inte**.
    > - **Storage i det virtuella nätverket** är för närvarande **stöds inte**.
    > - **Storage bakom en brandvägg** är för närvarande **stöds inte**

8. Klicka på **Spara**.



## <a id="subheading-2"></a>Konfigurera granskning databasnivå för en enskild databas

Du kan definiera en granskningsprincip för en specifik databas eller som en standardprincip för servern.

Vi rekommenderar starkt att använda serverniv och granskning inte databasnivå, enligt beskrivningen i [definiera på servernivå och databasnivå granskningsprincip](#subheading-4)

Innan du konfigurerar audit granskning kontrollera om du använder en [”meddelandeklienter”](sql-data-warehouse-auditing-downlevel-clients.md).


1. Starta den [Azure-portalen](https://portal.azure.com).
2. Gå till **inställningar** för SQL Data Warehouse som du vill granska. Välj **granskning och Hotidentifiering**.

    ![][1]
3. Därefter aktiverar granskning genom att klicka på den **på** knappen.

    ![][3]
4. I panelen granskning configuration väljer **LAGRINGSINFORMATION** att öppna panelen Granska loggarna lagring. Välj Azure storage-konto för loggarna och kvarhållningsperioden.
    >[!TIP]
    >Använd samma lagringskonto för alla granskade databaser för att få ut mest av de förkonfigurerade rapporter mallarna.

    ![][4]

5. Klicka på den **OK** för att spara information om lagringskonfiguration.
6. Under **loggning av händelse**, klickar du på **lyckades** och **fel** vill logga alla händelser eller enskilda händelsekategorier.
7. Om du konfigurerar granskning för en databas, kan du behöva ändra anslutningssträngen för din klient för att se till att datagranskning samlas in korrekt. Kontrollera den [ändra Server-FQDN i anslutningssträngen](sql-data-warehouse-auditing-downlevel-clients.md) avsnittet för äldre-klientanslutningar.
8. Klicka på **OK**.

## <a id="subheading-3"></a>Analysera granskningsloggar och rapporter

### <a name="server-level-policy-audit-logs"></a>Princip för servernivå granskningsloggar
På servernivå granskningsloggarna skrivs till **Tilläggsblobbar** i Azure Blob storage på din Azure-prenumeration. De sparas som en samling av blobfiler i en behållare med namnet **sqldbauditlogs**.

Ytterligare information om hierarkin för mappen storage namngivningskonventioner och loggformat, finns det [referens till Blob granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

Det finns flera metoder som du kan använda för att visa blob granskningsloggar:

* Använd **sammanfoga granskningsfilerna** i SQL Server Management Studio (startar med SSMS 17):
    1. SSMS-menyn väljer **filen** > **öppna** > **sammanfoga granskningsfilerna**.

    2. Den **lägga till granskningsfilerna** öppnas dialogrutan. Välj en av de **Lägg till** alternativ att välja om du vill slå samman granskningsfilerna från en lokal disk eller importera dem från Azure Storage. Du måste ange din information för Azure Storage och kontonyckel.

    3. När alla filer att slå samman har lagts till, klickar du på **OK** att slutföra merge-operation.

    4. Sammanfogade filen öppnas i SSMS, där du kan visa och analysera dem, samt exportera den till en XEL eller CSV-fil eller till en tabell.

* Använd den [synkronisera program](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) som vi har skapat. Den körs i Azure och använder Log Analytics offentliga API: er för att skicka SQL-granskningsloggar till Log Analytics. Synkronisera program skickar SQL-granskningsloggar till Log Analytics för användning via Log Analytics-instrumentpanelen.

* Använda Powerbi. Du kan visa och analysera granskningsloggdata i Power BI. Läs mer om [Power BI och åtkomst till en mall för nedladdningsbara](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Ladda ned loggfiler från dina Azure Storage blob-behållare via portalen eller genom att använda ett verktyg som [Azure Storage Explorer](http://storageexplorer.com/).
    * När du har hämtat en loggfil lokalt, kan du dubbelklicka på filen för att öppna, visa och analysera loggar i SSMS.
    * Du kan också hämta flera filer samtidigt via Azure Storage Explorer. Högerklicka på en viss undermapp och välj **Spara som** att spara i en lokal mapp.

* Ytterligare metoder:
   * När du hämtat flera filer eller en undermapp som innehåller loggfiler, kan du slå samman dem lokalt enligt beskrivningen i SSMS Merge granskningsfilerna anvisningarna ovan.

   * Visa blobbgranskning loggar programmässigt:

     * Använd den [utökade händelser läsare](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C#-biblioteket.
     * [Frågefiler utökade händelser](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.



<br>
### <a name="database-level-policy-audit-logs"></a>Loggar för granskning på databasnivå
Databasnivå granskningsloggar räknas samman i en samling med Store-tabeller med en **SQLDBAuditLogs** prefixet i Azure storage-kontot som du valde i installationsprogrammet. Du kan visa loggfiler med hjälp av ett verktyg som [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com).

En förkonfigurerad instrumentpanel rapportmall är tillgänglig som en [nedladdningsbara Excel-kalkylblad](https://go.microsoft.com/fwlink/?LinkId=403540) för att snabbt analysera loggdata. Om du vill använda mallen på dina granskningsloggar, behöver du Excel 2013 eller senare och Power Query, som du kan [ladda ned här](https://www.microsoft.com/download/details.aspx?id=39379).

Mallen innehåller fiktiva exempeldata och du kan ställa in Power Query för att importera din granskningslogg direkt från Azure storage-kontot.

## <a id="subheading-4"></a>Storage åtkomstnyckeln återskapades
I produktion förmodligen du uppdatera dina storage-nycklar med jämna mellanrum. När du uppdaterar dina nycklar måste du spara principen. Processen är följande:

1. I granskning panelen för konfigurationen som beskrivs i föregående installationen granskning avsnittet, ändra den **Lagringsåtkomstnyckel** från *primära* till *sekundära* och  **Spara**.

   ![][4]
2. Gå till panelen konfiguration för lagring och **återskapa** den *primära åtkomstnyckel*.
3. Gå tillbaka till panelen granskning konfiguration
4. Växla den **Lagringsåtkomstnyckel** från *sekundära* till *primära* och tryck på **spara**.
4. Gå tillbaka till lagringen Användargränssnittet och **återskapa** den *sekundära åtkomstnyckel* (som förberedelse inför nästa uppdatera nycklar cykel.

## <a id="subheading-5"></a>Automation (PowerShell/REST API)
Du kan också konfigurera granskning i Azure SQL Data Warehouse med hjälp av följande verktyg för automatisering:

* **PowerShell-cmdletar**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Äldre klienter har stöd för granskning och dynamisk datamaskning
Granskning fungerar med SQL-klienter som stöder TDS-omdirigering.

Alla klienter som implementerar TDS 7.4 bör också stöd för omdirigering. Undantag till detta inkluderar JDBC 4.0 där funktionen omdirigering stöds inte fullt ut och Tedious för Node.JS i vilka omdirigering har inte implementerats.

För ”äldre klienter” som stöder TDS version 7.3 och nedan, ändra serverns FQDN i anslutningssträngen på följande sätt:

- Ursprungliga serverns FQDN i anslutningssträngen: <*servernamn*>. database.windows.net
- Ändrade serverns FQDN i anslutningssträngen: <*servernamn*> .database. **säker**. windows.net

En lista över ”klientversioner” innehåller:

* .NET 4.0 och nedan.
* ODBC-10.0 och nedan.
* JDBC (medan JDBC stöder TDS 7.4, funktion för TDS-omdirigering fullständigt stöds inte)
* Tedious (för Node.JS)

**Kommentar:** Den föregående servern FQDN ändring kan vara användbart också för att tillämpa en princip för granskning för SQL Server-nivå utan att behöva ett konfigurationssteg i varje databas (tillfällig lösning).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Define server-level vs. database-level auditing policy]: #subheading-4
[Set up server-level auditing for all databases]: #subheading-5

<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png
[6]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_1_overview.png
[7]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_2_enable.png
[8]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-server_3_storage.png
