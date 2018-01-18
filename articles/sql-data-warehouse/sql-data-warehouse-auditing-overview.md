---
title: Granskning i Azure SQL Data Warehouse | Microsoft Docs
description: "Kom igång med granskning i Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 01/16/2018
ms.author: rortloff;barbkess
ms.openlocfilehash: 5400f29d8c7579809ef7b2a084115473df7baa85
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Granskning i Azure SQL Data Warehouse

SQL Data Warehouse granskning kan du till post i databasen till en granskningslogg händelselogg i Azure Storage-konto. Granskning kan hjälpa dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser. SQL Data Warehouse granskning är integrerat med Microsoft Power BI för rapportering och analys.

Granskningsverktyg aktivera och underlätta att efterlevnadsstandarder men garantera inte efterlevnad. Mer information om Azure-program som stöd för överensstämmelse med standarder, finns det <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure Säkerhetscenter</a>.

## <a id="subheading-1"></a>Granskning grunderna
Granskning av SQL Data Warehouse-databas kan du:

* **Behåll** redovisningsspårning markerade händelser. Du kan definiera typer av databasåtgärder som ska granskas.
* **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktivitet och rapportera händelser.
* **Analysera** rapporter. Du kan hitta misstänkta händelser, ovanliga aktiviteter och trender.

Du kan konfigurera granskning för kategorier i följande:

**Vanlig SQL** och **parametriserade SQL** som samlats in granskningsloggarna klassificeras som  

* **Åtkomst till data**
* **Schemaändringar (DDL)**
* **Dataändringar (DML)**
* **Konton, roller och behörigheter (DCL)**
* **Lagrade proceduren**, **inloggning** och **transaktionshantering**.

För varje händelsekategori granskning av **lyckade** och **fel** åtgärder har konfigurerats separat.

Mer information om aktiviteter och händelser som granskas finns i <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">referens till granskningslogg Format (doc Filhämtning)</a>.

Granskningsloggar lagras i Azure storage-konto. Du kan definiera en Granska loggen Bevarandeperiod.

Du kan definiera en granskningsprincip för en viss databas eller som en standardprincip för servern. Granskningsprincip för servern som ska användas som standard gäller för alla databaser på en server som inte har en specifik åsidosättning databasen granskning princip definierad.

Innan du konfigurerar audit granskning kontrollera om du använder en [”meddelandeklienter”.](sql-data-warehouse-auditing-downlevel-clients.md)

## <a id="subheading-2"></a>Konfigurera granskning för databasen
1. Starta den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a>.
2. Gå till **inställningar** för SQL Data Warehouse som du vill granska. Välj **Auditing & Threat detection**.
   
    ![][1]
3. Därefter aktiverar granskning genom att klicka på den **ON** knappen.
   
    ![][3]
4. Välj i panelen granskning configuration **LAGRINGSINFORMATION** att öppna panelen Granska loggarna lagring. Välj Azure storage-konto för loggarna och kvarhållningsperioden. 
>[!TIP]
>Använda samma lagringskonto för alla granskad databaser för att få ut mesta av förkonfigurerade rapporter mallar.
   
    ![][4]
5. Klicka på den **OK** för att spara konfigurationen av information.
6. Under **loggning av HÄNDELSEN**, klickar du på **lyckade** och **fel** vill logga alla händelser eller enskilda kategorier.
7. Om du konfigurerar granskning för en databas som du kan behöva ändra anslutningssträngen för din klient så data granskning korrekt avbildas. Kontrollera den [ändra Server FDQN i anslutningssträngen](sql-data-warehouse-auditing-downlevel-clients.md) avsnittet för äldre klientanslutningar.
8. Klicka på **OK**.

## <a id="subheading-3"></a>Analysera granskningsloggar och rapporter
Granskningsloggar aggregeras i en samling av Store-tabeller med en **SQLDBAuditLogs** prefix i Azure storage-konto som du valde under installationen. Du kan visa loggfiler med hjälp av ett verktyg som <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Lagringsutforskaren</a>.

Rapportmall förkonfigurerade instrumentpanelen är tillgänglig som en <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">nedladdningsbara Excel-kalkylblad</a> som hjälper dig att snabbt analysera loggdata. Om du vill använda mallen på din granskningsloggar, behöver du Excel 2013 eller senare och Power Query, som du kan hämta <a href="http://www.microsoft.com/download/details.aspx?id=39379">här</a>.

Mallen har fiktiva exempeldata i den och du kan ställa in Power Query för att importera din granskningsloggen direkt från Azure storage-konto.

## <a id="subheading-4"></a>Sessionsnycklar för lagring
Det är sannolikt att uppdatera din lagringsnycklar regelbundet i produktionen. När du uppdaterar dina nycklar, måste du spara principen. Processen är följande:

1. Granskning Kontrollpanelen konfigurationen som beskrivs i föregående inställningarna granskning avsnitt, ändra den **Lagringsåtkomstnyckel** från *primära* till *sekundära* och  **Spara**.

   ![][4]
2. Gå till panelen konfiguration för lagring och **återskapa** den *primära åtkomstnyckeln*.
3. Gå tillbaka till panelen granskning konfiguration 
4. Växla den **Lagringsåtkomstnyckel** från *sekundära* till *primära* och tryck på **spara**.
4. Gå tillbaka till lagringen UI och **återskapa** den *sekundära åtkomstnyckel* (som förberedelse för nästa nycklar uppdateringscykel.

## <a id="subheading-5"></a>Automation (PowerShell/REST API)
Du kan också konfigurera granskning i Azure SQL Data Warehouse med hjälp av följande verktyg för automatisering:

* **PowerShell-cmdlets**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>Stöd för äldre klienter för granskning och dynamisk datamaskning
Granskning fungerar med SQL-klienter som stöder TDS-omdirigering.

Alla klienter som implementerar TDS 7.4 bör också stöd för omdirigering. Undantag till detta inkluderar JDBC 4.0 där funktionen för omdirigering av stöds inte fullt ut och Tedious för Node.JS i vilka omdirigering har inte implementerats.

För ”äldre klienter” som stöder TDS version 7.3 och nedan, ändra serverns FQDN i anslutningssträngen på följande sätt:

- Ursprungliga serverns FQDN i anslutningssträngen: <*servernamn*>. database.windows.net
- Ändrade serverns FQDN i anslutningssträngen: <*servernamn*> .database. **säker**. windows.net

Innehåller en lista över ”klientversioner”:

* .NET 4.0 och nedan.
* ODBC-10.0 och nedan.
* JDBC (medan JDBC stöder TDS 7.4, funktion för TDS-omdirigering fullständigt stöds inte)
* Tråkigt (för Node.JS)

**Kommentar:** föregående servern FDQN ändring kan vara användbara också för att tillämpa en princip för SQL Server-nivå granskning utan behov av en konfiguration steg i varje databas (tillfällig lösning).     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


