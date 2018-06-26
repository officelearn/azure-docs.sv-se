---
title: Kom igång med Azure SQL database auditing | Microsoft Docs
description: Att Azure SQL database auditing spårar databashändelser till en granskningslogg.
services: sql-database
author: giladmit
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: conceptual
ms.date: 06/24/2018
ms.author: giladm
ms.openlocfilehash: 0646667caab594556cc3c2043bc36905acef6e54
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751051"
---
# <a name="get-started-with-sql-database-auditing"></a>Kom igång med SQL-databasgranskning
Azure SQL database auditing spårar databashändelser och skriver dem till en granskningslogg logga i Azure storage-konto. Granskning också:

* Hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

* Aktiverar och underlättar anslutning till efterlevnadsstandarder, även om den inte garantera efterlevnad. Mer information om Azure-program som stöd för överensstämmelse med standarder, finns det [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/compliance/).


## <a id="subheading-1"></a>Azure SQL database granskning: översikt
Du kan använda SQL database auditing till:


* **Behåll** redovisningsspårning markerade händelser. Du kan definiera typer av databasåtgärder som ska granskas.
* **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktivitet och rapportera händelser.
* **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

Konfigurera granskning för olika typer av kategorier, enligt beskrivningen i den [ställa in granskning för databasen](#subheading-2) avsnitt.

> [!IMPORTANT]
> Granskningsloggar skrivs till **Tilläggsblobbar** i ett Azure Blob storage på din Azure-prenumeration.
>
> * **Premium-lagring** är för närvarande **stöds inte** av Tilläggsblobbar.
> * **Lagring i VNet** är för närvarande **stöds inte**.

## <a id="subheading-8"></a>Definiera servernivå kontra databasnivå granskningsprincip

En granskningsprincip kan definieras för en viss databas eller som en standardprincip för servern:

* En Serverprincipen gäller för alla befintliga och nya databaser på servern.

* Om *server blobbgranskning är aktiverat*, den *databasen gäller alltid*. Databasen granskas, oavsett databasen granskningsinställningar.

* Aktiverar blobbgranskning på databasen, samt för att aktivera det på servern, har *inte* åsidosätta eller ändra inställningar för server-blob för granskning. Båda granskningar kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger i parallell; en gång av serverprincip och en gång av principen för databasen.

   > [!NOTE]
   > Du bör undvika att aktivera både server blob gransknings- och databasen blobbgranskning tillsammans, såvida inte:
    > * Du vill använda en annan *lagringskonto* eller *kvarhållningsperioden* för en viss databas.
    > * Du vill granska händelsetyper eller kategorier för en viss databas som skiljer sig från resten av databaser på servern. Du kan till exempel ha tabellen infogningar som måste granskas endast för en viss databas.
   >
   > I annat fall rekommenderar vi att du aktiverar blobbgranskning endast servernivå och lämna databasnivå granskning inaktiveras för alla databaser.


## <a id="subheading-2"></a>Konfigurera granskning för databasen
I följande avsnitt beskrivs konfigurationen av granskning med Azure-portalen.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Gå till den **inställningar** bladet SQL-databas/SQL-Server som du vill granska. I den **inställningar** bladet väljer **Auditing & Threat detection**.

    <a id="auditing-screenshot"></a>![Navigeringsfönstret][1]
3. Om du vill konfigurera en granskningsprincip för server, kan du välja den **visa inställningarna för** länken i databasbladet för granskning. Du kan visa eller ändra inställningar för servergranskning. Servern granskningsprinciper gäller för alla befintliga och nya databaser på servern.

    ![Navigeringsfönster][2]
4. Om du vill aktivera blobbgranskning på databasnivå för **granskning**väljer **ON**, och för **granskning typen**väljer **Blob**.

    Om servern blobbgranskning är aktiverat, kommer att finnas audit databasen konfigurerad sida vid sida med blob-audit server.

    ![Navigeringsfönster][3]
5. Öppna den **granska loggarna lagring** bladet väljer **lagringsinformation**. Välj Azure storage-konto där loggar sparas, och välj sedan kvarhållningsperioden. Kommer att ta bort de gamla loggarna. Klicka sedan på **OK**.
    >[!TIP]
    >Använd samma lagringskonto för alla granskad databaser för att få mest av granskning rapporter mallarna.

    <a id="storage-screenshot"></a>![Navigeringsfönstret][4]
6. Om du vill anpassa granskade händelser kan du göra detta via [PowerShell-cmdlets](#subheading-7) eller [REST API](#subheading-9).
7. När du har konfigurerat inställningarna för granskning, kan du aktivera funktionen för identifiering av nya hot och konfigurera e-postmeddelanden för att ta emot säkerhetsaviseringar. När du använder hotidentifiering får proaktiva varningar på avvikande databasaktiviteter som kan indikera potentiella hot mot säkerheten. Mer information finns i [komma igång med hotidentifiering](sql-database-threat-detection-get-started.md).
8. Klicka på **Spara**.





## <a id="subheading-3"></a>Analysera granskningsloggar och rapporter
Granskningsloggar samman i Azure storage-konto som du valde under installationen. Du kan utforska granskningsloggar genom att använda ett verktyg som [Azure Lagringsutforskaren](http://storageexplorer.com/).

BLOB-granskningsloggar sparas som en samling blob-filer i en behållare med namnet **sqldbauditlogs**.

Mer information om hierarkin för lagringsmappen namnkonventioner och loggformatet, finns det [Blobbreferens till granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

Det finns flera metoder du kan använda för att visa blob granskningsloggar:

* Använd den [Azure-portalen](https://portal.azure.com).  Öppna den aktuella databasen. Överst i databasens **Auditing & Threat detection** bladet, klickar du på **visa granskningsloggarna**.

    ![Navigeringsfönster][7]

    En **granskningsloggarna** öppnas bladet som kommer du att kunna visa loggfilerna.

    - Du kan visa specifika datum genom att klicka på **Filter** överst i den **granskningsloggarna** bladet.
    - Du kan växla mellan granskningsposter som har skapats av en server princip- eller princip för granskning.

       ![Navigeringsfönster][8]

* Använd systemfunktionen **sys.fn_get_audit_file** (T-SQL) att returnera granskningsdata i tabellformat. Mer information om hur du använder den här funktionen finns i [sys.fn_get_audit_file dokumentationen](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).


* Använd **sammanfoga granskningsfilerna** i SQL Server Management Studio (startar med SSMS 17):
    1. Välj menyn SSMS **filen** > **öppna** > **sammanfoga granskningsfilerna**.

        ![Navigeringsfönster][9]
    2. Den **lägga till granskningsfilerna** öppnas. Välj en av de **Lägg till** alternativ att välja om du vill koppla granskningsfilerna från en lokal disk eller importera dem från Azure Storage. Du måste ange dina Azure Storage-information och kontonyckel.

    3. När alla filer som ska kopplas har lagts till, klickar du på **OK** att slutföra sammanslagningsåtgärden.

    4. Den kopplade filen öppnas i SSMS, där du kan visa och analysera den, samt exportera den till en XEL eller CSV-fil eller en tabell.

* Använd den [synkronisera programmet](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration) som vi har skapat. Den körs i Azure och använder logganalys offentliga API: er för att skicka SQL-granskningsloggar till logganalys. Synkronisera program push-meddelanden SQL granskningsloggar i logganalys för förbrukning via instrumentpanelen logganalys.

* Använd Powerbi. Du kan visa och analysera granskningsdata i Power BI. Lär dig mer om [Power BI och åtkomst till en mall för nedladdningsbar](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/).

* Hämta loggfilerna från din Azure Storage blob-behållare via portalen eller genom att använda ett verktyg som [Azure Lagringsutforskaren](http://storageexplorer.com/).
    * När du har hämtat en loggfil lokalt, dubbelklickar du på filen för att öppna, visa och analysera loggar i SSMS.
    * Du kan också hämta flera filer samtidigt via Azure Lagringsutforskaren. Högerklicka på en viss undermapp och välj **Spara som** ska sparas i en lokal mapp.

* Ytterligare metoder:
   * När du har hämtat flera filer eller en undermapp som innehåller loggfiler kan du kombinera dem lokalt enligt beskrivningen i SSMS Merge granskningsfilerna anvisningarna ovan.

   * Visa blobbgranskning loggar programmässigt:

     * Använd den [utökade händelser Reader](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C#-biblioteket.
     * [Fråga utökade händelser filer](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.




## <a id="subheading-5"></a>Produktionsmetoder för
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Granskning geo-replikerade databaser</a>
Med geo-replikerade databaser när du aktiverar granskning på den primära databasen har den sekundära databasen en identisk granskningsprincip. Det är också möjligt att ställa in granskning på den sekundära databasen genom att aktivera granskning av **sekundär server**, oberoende av den primära databasen.

* Servernivå (**rekommenderas**): aktivera granskning på både den **primära servern** samt de **sekundär server** -primära och sekundära databaser varje granskas oberoende baserat på deras respektive princip för servernivå.

* Databasnivå: Databasnivå granskning för sekundära databaser kan endast konfigureras från primära databasen granskningsinställningar.
   * Blobbgranskning måste vara aktiverat på den *primära databasen själva*, inte på servern.
   * När blobbgranskning är aktiverat på den primära databasen kan aktiveras den också på den sekundära databasen.

    >[!IMPORTANT]
    >Med databasnivå granskning måste lagringsinställningarna för den sekundära databasen vara samma som den primära databasen orsakar mellan regionala trafik. Vi rekommenderar att du aktiverar granskning endast servernivå och lämna databasnivå granskning inaktiveras för alla databaser.
<br>

### <a id="subheading-6">Sessionsnycklar för lagring</a>
Det är sannolikt att uppdatera din lagringsnycklar regelbundet i produktionen. När du uppdaterar dina nycklar, måste du spara om granskningsprincipen. Processen är följande:

1. Öppna den **lagringsinformation** bladet. I den **Lagringsåtkomstnyckel** väljer **sekundära**, och klicka på **OK**. Klicka på **spara** längst upp på bladet granskning konfiguration.

    ![Navigeringsfönster][5]
2. Gå till bladet storage-konfiguration och återskapa den primära åtkomstnyckeln.

    ![Navigeringsfönster][6]
3. Gå tillbaka till bladet granskning configuration växla lagringsåtkomstnyckel från sekundär till primär och klicka sedan på **OK**. Klicka på **spara** längst upp på bladet granskning konfiguration.
4. Gå tillbaka till bladet storage-konfiguration och återskapa den sekundära åtkomstnyckeln (som förberedelse för nyckeln nästa uppdateringscykeln).

## <a name="additional-information"></a>Ytterligare information

* Mer information om loggen formatera hierarki av lagringsmappen och namngivningskonventioner, finns det [Blobbreferens till granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL-databasen och Audit lagrar 4 000 tecken för Teckenfält i en granskningspost. När den **instruktionen** eller **data_sensitivity_information** värden som returneras från en granskningsbar åtgärd innehåller fler än 4 000 tecken blir alla data utöver de första 4 000 tecken  **trunkerad och inte granskas**.

* Granskningsloggar skrivs till **Tilläggsblobbar** i ett Azure Blob storage på din Azure-prenumeration:
    * **Premium-lagring** är för närvarande **stöds inte** av Tilläggsblobbar.
    * **Lagring i VNet** är för närvarande **stöds inte**.

* Granska standardprincipen innehåller följande uppsättning åtgärdsgrupper som ska granska alla frågor och lagrade procedurer som körs mot databasen samt lyckade och misslyckade inloggningar och alla åtgärder:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Du kan konfigurera granskning för olika typer av åtgärder och åtgärdsgrupper med hjälp av PowerShell som beskrivs i den [hantera SQL database auditing med hjälp av Azure PowerShell](#subheading-7) avsnitt.

## <a id="subheading-7"></a>Hantera SQL database auditing med hjälp av Azure PowerShell

**PowerShell-cmdlets**:

* [Skapa eller uppdatera databasen Blob granskningsprincip (Set-AzureRMSqlDatabaseAuditing)][105]
* [Skapa eller uppdatera Server Blob granskningsprincip (Set-AzureRMSqlServerAuditing)][106]
* [Hämta databasen granskningsprincip (Get-AzureRMSqlDatabaseAuditing)][101]
* [Hämta Server Blob granskningsprincip (Get-AzureRMSqlServerAuditing)][102]

Ett exempel på skript finns [konfigurera granskning och hotidentifiering identifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Hantera SQL database auditing med hjälp av REST API

**REST API - blobbgranskning**:

* [Skapa eller uppdatera databasen Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/database%20auditing%20settings/createorupdate)
* [Skapa eller uppdatera Server Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/server%20auditing%20settings/createorupdate)
* [Hämta databasen Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/database%20auditing%20settings/get)
* [Hämta Server Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/server%20auditing%20settings/get)
* [Hämta Server Blob granskning resultat](https://msdn.microsoft.com/library/azure/mt771862.aspx)

Utökad princip med där satsen stöd för ytterligare filtrering:
* [Skapa eller uppdatera databasen *utökad* Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
* [Skapa eller uppdatera Server *utökad* Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/server%20extended%20auditing%20settings/createorupdate)
* [Hämta databasen *utökad* Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/database%20extended%20auditing%20settings/get)
* [Hämta Server *utökad* Blob granskningsprincip](https://docs.microsoft.com/en-us/rest/api/sql/server%20extended%20auditing%20settings/get)

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
