---
title: "Vad är nytt i Azure Data Catalog | Microsoft Docs"
description: "Den här artikeln innehåller en översikt över nya funktioner som lagts till i Azure Data Catalog."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/06/2017
ms.author: maroche
ms.openlocfilehash: 7259505b68202f3741e64e86555d2e3f968b8951
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-azure-data-catalog"></a>Vad är nytt i Azure Data Catalog
Uppdateringar för **Azure Data Catalog** släpps regelbundet. Inte alla viktig innehåller nya funktioner för användarinriktad, vissa versioner fokuserar på funktioner för backend-tjänst. Den här sidan visar nya funktioner som användarinriktad har lagts till tjänsten Azure Data Catalog.

## <a name="whats-new-for-september-2017"></a>Vad är nytt för September 2017 
Från och med September 2017 har följande funktioner lagts till Azure Data Catalog:

* Stöd för att extrahera Anslut relationen metadata från DB2 datakällor när du registrerar relaterade tabeller med registreringsverktyget för datakällor.
* Stöd för att registrera MongoDB version 3.4 datakällorna med registreringsverktyget för datakällor.
* Stöd för att ta bort alla metadata för objekten i en enda åtgärd när du tar bort en databas eller annan behållare från Data Catalog.
* Stöd för upp till 1 000 taggar, business ordlista eller andra sökningar visa aspekter, när du förfina din sökning i Data Catalog-portalen.


## <a name="whats-new-for-august-2017"></a>Vad är nytt för augusti 2017 
Från och med augusti 2017 har följande funktioner lagts till Azure Data Catalog:

*   Ett exempel på en ny utvecklare är tillgänglig för att skapa och hantera relationen metadata med hjälp av Data Catalog REST-API: et. Den *importera relationsinformation till Data Catalog* prov är tillgängligt på den [Data Catalog teckentabellen exempel](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Stöd för att extrahera Anslut relationen metadata från Teradata-datakällor när du registrerar relaterade tabeller med registreringsverktyget för datakällor.
* Stöd för SQL Server Tabellvärdesfunktionen (Tabellvärdesfunktion) objekt vid registrering av SQL Server-datakällorna med registreringsverktyget för datakällor.
* Flera uppdateringar och förfining att öka prestanda och användningsmöjligheter Data Catalog-portalen.

## <a name="whats-new-for-july-2017"></a>Vad är nytt för juli 2017 
Från och med juli 2017 har följande funktioner lagts till Azure Data Catalog:
*   Stöd för mer detaljerad kontroll över tillåts metadataåtgärder inklusive:
    - Katalogadministratörer kan begränsa användarnas möjlighet att bidra med taggar och relaterade metadata till katalogen för att aktivera skrivskyddad åtkomst till katalogen.
    - Katalogadministratörer kan begränsa användarnas möjlighet att registrera nya datakällor i katalogen.
    - Katalogadministratörer kan begränsa användarnas möjlighet att överta ägarskapet för data tillgångens metadata i katalogen.
    - Behörighet kan beviljas för Azure Active Directory-säkerhetsgrupper och användare för att underlätta hanteringen av behörigheter.
* Stöd för relationer mellan registrerade datatillgångar och identifierande relaterade datatillgångar i Data Catalog-portalen, inklusive:
    - Extrahering av relationen metadata från SQL Server (inklusive Azure SQL Database), Oracle och MySQL datakällor när du använder registreringsverktyget för datakällor i Data Catalog.
    - Identifiering av relaterade datatillgångar när du visar tillgångens metadata i Data Catalog-portalen.
    - Åtgärder för att definiera, identifiera och hantera relationer mellan datatillgångar med hjälp av Data Catalog REST-API: et.

Mer information om hur du hanterar behörigheter i Data Catalog finns [så säker åtkomst till data catalog och datatillgångar](data-catalog-how-to-secure-catalog.md).
Ytterligare information om relationer i Data Catalog finns [så att visa relaterade datatillgångar i Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Vad är nytt för juni 2017 
Från och med juni 2017 har följande funktioner lagts till Azure Data Catalog:
*   Stöd för Sybase och Apache Cassandra MongoDB-datakällor. Användare kan nu registrera och identifiera Cassandra och MongoDB databaser och tabeller och Sybase-databaser tabeller och vyer.

> [!NOTE]
> När du registrerar MongoDB och Cassandra tabeller som innehåller kolumner med komplexa datatyper, till exempel poster och matriser inkluderas dessa kolumner inte i metadata som lagts till i Data Catalog.

## <a name="whats-new-for-may-2017"></a>Vad är nytt för maj 2017 
Från och med kan 2017 har följande funktioner lagts till Azure Data Catalog:
*   Ett exempel på en ny utvecklare är tillgänglig för massimport för företag-ordlista. Ordlista massimport prov är tillgängligt på den [exempelsida för Data Catalog developer](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Stöd för att redigera anslutningsinformationen för ODBC i Azure Data Catalog-portalen. Data tillgångens ägare och administratörer för Data Catalog kan nu redigera anslutningsinformationen för registrerade ODBC-datakällor utan att behöva registrera datakällor.
*   Stöd för klickbara URL: er i företag ordlista definitioner och beskrivningar. När URL: er finns i egenskaperna beskrivning och definition för business ordlista, ska URL: er visas som hyperlänkar i Data Catalog-portalen.
*   Stöd för att visa expert namn förutom expert e-postadresser. När du visar experter i egenskaperna för en datatillgång i Data Catalog-portalen, visas handledarens fullständiga namn från Azure Active Directory i tooltip.

## <a name="whats-new-for-april-2017"></a>Vad är nytt för April 2017 
Från och med April 2017 har följande funktioner lagts till Azure Data Catalog:
*   Stöd för ODBC-datakällor. Användare kan nu registrera och identifiera ODBC-databaser, tabeller och vyer med hjälp av registreringsverktyget för datakällor i Data Catalog.

## <a name="whats-new-for-march-2017"></a>Vad är nytt för mars 2017 
Från och med mars 2017 har följande funktioner lagts till Azure Data Catalog:
*   Stöd för användning av AAD-säkerhetsgrupper för Data Catalog-administratörer.
*   Azure Data Catalog är nu tillgänglig i en ny Azure-region. Kunder kan etablera Azure Data Catalog i West centrala oss region, förutom östra USA, västra USA, västra Europa och östra, Norra Europa och Sydostasien. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Vad är nytt för februari 2017 
Från och med februari 2017 har följande funktioner lagts till Azure Data Catalog:
*   Stöd för avancerade inställningar i registreringsverktyget för datakällor i Data Catalog. Användarna kan ange timeout-värdena för kommandot vid registrering av stora datakällor.
*   Stöd för FTP- och PostgreSQL-datakällor. Användare kan nu registrera och identifiera FTP-filer och kataloger, och PostgreSQL-databaser, tabeller och vyer.

## <a name="whats-new-for-january-2017"></a>Vad är nytt för januari 2017 
Från och med januari 2017 har följande funktioner lagts till Azure Data Catalog:
*   Azure Data Catalog är nu [CSA STAR](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) kompatibla.
*   Integrering med [Hämta & transformeringen i Excel 2016 och Power Query för Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Excel-användare kan dela frågor och identifiera frågor med Azure Data Catalog från i Excel. Den här funktionen är tillgänglig för användare med Power BI Pro licenser.

## <a name="whats-new-for-december-2016"></a>Vad är nytt för December 2016
Följande funktioner har lagts till Azure Data Catalog December 2016:
*   Azure Data Catalog är nu [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) och [EU-standardavtalsklausuler](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses) kompatibla.
*   Stöd för att redigera anslutningsinformationen för datakällan. Data tillgångens ägare och administratörer för Data Catalog kan nu redigera anslutningsinformationen för registrerade datakällor utan att behöva registrera datakällor.
*   Stöd för Salesforce.com-datakällor. Användare kan nu registrera och identifiera Salesforce-objekt.


## <a name="whats-new-for-november-2016"></a>Vad är nytt för November 2016
Följande funktioner har lagts till Azure Data Catalog November 2016:
*   Azure Data Catalog är nu [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) och [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) kompatibla.
*   Stöd för manuell registrering av ODBC-datakällor med Data Catalog-portalen och REST-API.

## <a name="whats-new-for-september-2016"></a>Vad är nytt för September 2016
Från och med September 2016 kommer har följande funktioner lagts till Azure Data Catalog:

* Stöd för IBM DB2-datakällor. Användare kan nu registrera och identifiera DB2 databaser, tabeller och vyer.
* Stöd för Azure Cosmos DB-datakällor. Användare kan nu registrera och identifiera Cosmos-DB-databaser och samlingar.
* Stöd för att anpassa katalognamnet i Data Catalog-portalen. Katalogadministratörer kan nu ange text som visas i portalen rubrik, till exempel organisationens namn.

## <a name="whats-new-for-august-2016"></a>Vad är nytt för augusti 2016
Följande funktioner har lagts till Azure Data Catalog från och med augusti 2016:

* Förbättringar för registrering av SQL Server Master Data Services (MDS) datakällor. Användare kan nu inkludera profiler för förhandsversioner och data när du registrerar MDS-enheter med hjälp av registreringsverktyget för datakällor i Data Catalog.
* Stöd för administratörsdefinierad organisationens sparade sökningar. När du sparar en sökning i Data Catalog-portalen, kan Data Catalog-administratörer nu välja att spara sökningar för personligt bruk eller för alla användare i katalogen. Organisationens sparade sökningar delas med alla användare i katalogen och kan erbjuda standardiserad startpunkter för upptäckt av datakälla.
* Uppdateringar till egenskapsvyn i Data Catalog-portalen. Egenskaper för resurs för alla data är nu visas och hanteras i en enda, ändringsbara fönstret att ge en mer konsekvent och identifierbart upplevelse.

## <a name="whats-new-for-july-2016"></a>Vad är nytt för juli 2016
Följande funktioner har lagts till Azure Data Catalog juli 2016:

* Stöd för SQL Server Master Data Services (MDS)-datakällor. Användare kan nu registrera och identifiera MDS-modeller och entiteter.
* Stöd för SQL-lagrade procedurer. Användare kan nu registrera och identifiera lagrade proceduren objekt i SQL Server-datakällor.
* Stöd för ytterligare språk i Azure Data Catalog-portalen och registreringsverktyget för datakällor, totalt 18 språk som stöds. Användarupplevelsen för Azure Data Catalog är lokaliserade utifrån de språkinställningar som i Windows eller i webbläsaren.
* Uppdateringar och förfining för Data Catalog startsidan, inklusive prestandaförbättringar och en effektiviserad upplevelse.

## <a name="whats-new-for-june-2016"></a>Vad är nytt för juni 2016
Från och med juni 2016 kommer följande funktioner har lagts till Azure Data Catalog:

* Stöd för att ändra storlek på kolumner i listvyn när identifiera datatillgångar i Data Catalog-portalen. Användare kan nu ändra storlek på enskilda kolumner för att det blir lättare att läsa långa tillgångens metadata, till exempel taggar och beskrivningar.
* Power Query för Excel har lagts till ”öppna i”-menyn i Data Catalog-portalen. Nu kan du öppna datakällor som stöds i Excel 2016 eller i Excel 2010 och Excel 2013 med den [Power Query för Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) installerat.
* Stöd för Azure Table Storage-datakällor. Användare kan nu registrera och identifiera tabellobjekt i Azure Storage-datakällor.

## <a name="whats-new-for-may-2016"></a>Vad är nytt för maj 2016
Följande funktioner har lagts till Azure Data Catalog maj 2016:

* En Företagsordlista som hjälper katalogadministratörer att fastställa visningsinställning och hierarkier för att skapa en gemensam ordlista för företag. Användare kan tagga registrerade datatillgångar med termer från ordlistan för att göra det lättare att identifiera och förstå innehållet i katalogen. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md)  
* Förbättringar av Data Catalog Företagsordlista som användarna kan uppdatera flera ordlista i en enda åtgärd. Användare kan välja flera villkor för att redigera följande fält:
  * Överordnad Term: Användaren kan välja en ny överordnad term och alla valda villkor har uppdaterats för att vara underordnade platser till den valda överordnade termen. Om den valda villkor alla har samma överordnade och sedan det överordnat visas i textrutan, annars överordnade termen har värdet tomt.   
  * Taggar och intressenter: användare kan lägga till och ta bort taggar och intressenter för flera ordlista med samma upplevelse som taggning flera datatillgångar.

> [!NOTE]
> En företagsordlista är endast tillgänglig i Standard Edition av Azure Data Catalog. Den kostnadsfria versionen ger inte några funktioner för styrda märkning eller en företagsordlista.

## <a name="whats-new-for-march-2016"></a>Vad är nytt för mars 2016
Följande funktioner har lagts till Azure Data Catalog: g för mars 2016:

* Konsoliderade REST API-slutpunkt för att komma åt sökfunktioner och katalogen tillgången funktioner för hantering av tjänsten Azure Data Catalog. Den här sökningen API-slutpunkten och katalogen API-slutpunkten har föråldrad och upphöra att gälla den 21 mars 2016. Det finns inga ändringar av API-semantik. Endast slutpunkten URI: N ändras. Mer information finns i [Azure Data Catalog REST API-referens](https://msdn.microsoft.com/library/azure/mt267595.aspx). API-exempel finns [Azure Data Catalog developer exempel](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Vad är nytt för februari 2016
Från och med februari 2016 kommer följande funktioner har lagts till Azure Data Catalog:

* Källan för nyligen omdesignat dataurval upplevelse i registreringsverktyget för datakällor i Azure Data Catalog. Registreringsverktyget för datakällor har uppdaterats för att göra det lättare att leta upp och välj datakällorna som stöds av Azure Data Catalog.
* Stöd för 10 ytterligare språk i Azure Data Catalog-portalen och registreringsverktyget för datakällor. Azure Data Catalog-upplevelse är nu tillgänglig i tyska, spanska, franska, italienska, japanska, koreanska, portugisiska (Brasilien), ryska, förenklad och traditionell kinesiska än engelska. Användarupplevelsen för Azure Data Catalog är lokaliserade utifrån de språkinställningar som anges i Windows eller i användarens webbläsare.
* Stöd för geo-replikering av data i Azure Data Catalog för verksamhetskontinuitet och katastrofåterställning. Allt innehåll i Azure Data Catalog, inklusive data käll-metadata och användargenererade anteckningar, replikeras nu mellan två Azure-regioner utan extra kostnad för kunder. Azure-regioner är före länkas till minst 500 mil bort, och följ mappningen enligt beskrivningen i [Business affärskontinuitet och haveriberedskap återställning (BCDR): parad Azure-regioner](../best-practices-availability-paired-regions.md).
* Stöd för att ändra Azure-prenumerationen används av Azure Data Catalog. Azure Data Catalog-administratörer kan använda inställningssidan i Azure Data Catalog-portalen för att välja en annan Azure-prenumeration för fakturering.

## <a name="whats-new-for-january-2016"></a>Vad är nytt för januari 2016
Följande funktioner har lagts till Azure Data Catalog januari 2016:

* Stöd för att manuellt registrera ytterligare datakällor. Du kan nu använda ”skapa manuella transaktionen” i Azure Data Catalog-portalen eller använda Azure Data Catalog REST API för att registrera följande datakällor:
  * OData - funktionen, Entitetsuppsättning och Entitetsbehållaren
  * HTTP - fil, slutpunkt, rapporten och plats
  * Filsystem - fil
  * SharePoint - lista
  * FTP - filer och kataloger
  * Salesforce.com - objekt
  * DB2 - tabellen, vyn och databas
  * PostgreSQL - tabellen, vyn och databas
* Stöd för ”öppna i SQL Server Data Tools” för SQL Server (inklusive Azure SQL DB och Azure SQL Data Warehouse)-datakällor.  
* Stöd för registrering och identifiera SAP HANA-vyer och paket. Du kan registrera SAP HANA-datakällor med hjälp av Azure Data Catalog datakälla registreringsverktyget och kan lägga till anteckningar och identifiera registrerade datakällor för SAP HANA med hjälp av Azure Data Catalog-portalen.
* Möjligheten att fästa och ta bort datatillgångar i Azure Data Catalog-portalen. Du kan välja att fästa datatillgångar för att göra dem lättare att identifiera och återanvända.
* En nyligen omdesignat startsida i Azure Data Catalog-portalen. Den nya hemsidan innehåller insyn i den aktuella aktiviteten för användare – inklusive nyligen publicerade tillgångar, Fäst tillgångar och sparade sökningar- och insyn i aktiviteten i katalogen som helhet.
* Stöd för beständig användarinställningar i Azure Data Catalog-portalen. Inställningar användargränssnitt - inklusive rutnät eller panelen Visa antalet resultat per sida och träffar eller inaktivera markering - beständiga mellan sessioner.
* Azure Data Catalog finns nu i två nya Azure-regioner. Kunder kan etablera Azure Data Catalog i Norra Europa och Sydostasien områden, förutom östra USA, västra USA, västra Europa och östra. Mer information finns i [Azure-regioner](https://azure.microsoft.com/regions/).

> [!NOTE]
> ”Öppna i SQL Server Data Tools” kräver Visual Studio 2013 med Update 4 och SQL Server Tooling för att installeras. Du kan installera den senaste versionen av SQL Server Data Tools [Hämta SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Vad är nytt för December 2015
Från och med December 2015 är har följande funktioner lagts till Azure Data Catalog:

* Stöd för data-profiler för Azure SQL Data Warehouse-datakällor. När du registrerar Azure SQL Data Warehouse-tabeller och vyer, kan användarna välja att inkludera data profil mått med metadata som extraherats från datakällan.
* Stöd för registrering och identifiera MySQL-objekt och databaser. Användarna kan registrera MySQL datakällor med hjälp av Azure Data Catalog datakälla registreringsverktyget och kan lägga till anteckningar och identifiera registrerade MySQL-datakällor med hjälp av Azure Data Catalog-portalen.
* Stöd för SPNEGO och Windows-autentisering för Teradata-datakällor. När du registrerar Teradata-tabeller och vyer, kan användare välja att ansluta till Teradata med SPNEGO och Windows, och LDAP- och TD2 autentisering.
* Stöd för Azure Data Lake Store-datakällor. Användare kan nu registrera och identifiera datakällor i Azure Data Lake Store med hjälp av Azure Data Catalog.
* Stöd för att manuellt ange proxyinställningar för nätverk i registreringsverktyget för datakällor i Azure Data Catalog. Användare kan välja ”Ändra proxyinställningar” verktygets välkomstsidan och kan ange proxyadress och port som ska användas av verktyget.

## <a name="whats-new-for-november-2015"></a>Vad är nytt för November 2015
Från och med November 2015 har följande funktioner lagts till Azure Data Catalog:

* Möjligheten att visa och kopiera anslutningssträngar i Azure Data Catalog-portalen för SQL Server (inklusive Azure SQL Database) och Oracle-datakällor. Användarna kan klicka på länken ”Visa anslutningssträngar” i anslutningsinformationen för en SQL Server eller Oracle tabeller, vyer och databasen för att se anslutningssträngar som används för att ansluta till datakällan. ADO.NET, ODBC, OLEDB och JDBC-anslutningssträngar har angetts för SQL Server-datakällor. ODBC- och OLEDB-anslutningssträngar tillhandahålls för Oracle-datakällor.
* Stöd för inklusive data profiler när du registrerar Teradata-tabeller och vyer.
* Stöd för ”öppna i Power BI Desktop” för SQL Server (inklusive Azure SQL DB och Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage och HDFS källor.  
* Stöd för LDAP-autentisering för Teradata-datakällor. När du registrerar Teradata-tabeller och vyer som användarna kan välja att ansluta till Teradata LDAP, och TD2 autentisering.
* Stöd för ”öppna i Excel” för Teradata-datakällor.
* Stöd för senaste sökorden i Azure Data Catalog-portalen. När du söker i portalen kan välja användare från senast använda söktermer som påskyndar upplevelsen av identifieringen.
* Stöd för förhandsgranskning för Teradata-datakällor. När du registrerar Teradata-tabeller och vyer, kan användare välja att inkludera ögonblicksbild poster med metadata som extraherats från datakällan.
* Stöd för ”öppna i Excel” för Azure SQL Data Warehouse-datakällor.
* Stöd för att definiera och redigera kolumnnivå scheman för manuellt registrerade datatillgångar. När du har skapat en datatillgång med hjälp av Azure Data Catalog-portalen manuellt, användare kan lägga till kolumndefinitionerna dataegenskaper för tillgången.
* Stöd för ”har” frågor när du söker Azure Data Catalog, om du vill aktivera identifiering av registrerade datatillgångar som har specifika metadata. Azure Data Catalog frågesyntaxen innehåller nu:

| Frågesyntaxen | Syfte |
| --- | --- |
| `has:previews` |Söker efter datatillgångar som innehåller en förhandsgranskning |
| `has:documentation` |Söker efter datatillgångar som dokumentationen har angetts |
| `has:tableDataProfiles` |Söker efter datatillgångar med data på tabellen nivå profilinformation |
| `has:columnsDataProfiles` |Söker efter datatillgångar med kolumnnivå data profilinformation |


> [!NOTE]
> ”Öppna i Power BI Desktop” kräver en aktuell version av Power BI Desktop-program ska installeras. Om det uppstår problem eller fel med den här funktionen kan du se till att du har den senaste versionen av Power BI Desktop från [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Vad är nytt för oktober 2015
Från och med oktober 2015 har följande funktioner lagts till Azure Data Catalog:

* Stöd för kryptering av vilande data förhandsversioner och data profiler för registrerade datakällor. Azure Data Catalog krypterar transparent alla poster för förhandsgranskning och data profiler datakällor som har registrerats för tjänsten, utan något behov för hantering av nycklar av katalogadministratörer.
* Stöd för Teradata-datakällor. Användare kan nu registrera och identifiera Teradata-tabeller och vyer.
* Stöd för lokala Hive-datakällor. Användare kan nu registrera och identifierar Hive-tabeller för Apache Hive i Hadoop lokala datakällor.
* Stöd för sparade sökningar i Azure Data Catalog-portalen. Användare kan spara sökvillkoren och filtrera val enkelt Upprepa föregående sökningar och definiera användbar vyer för katalogens innehåll. Användaren kan också markera en sparad sökning som standardsökningen. När användaren klickar på ”” Sök förstoringsglaset från Azure Data Catalog portalens startsida eller från sidan ”komma igång”, tas användaren direkt till den sparade sökningen som flaggats som standard.
* Stöd för RTF-dokumentation för registrerade datatillgångar och behållare i Azure Data Catalog-portalen. Användare kan nu tillhandahålla dokumentation för datatillgångar som tabeller, vyer och rapporter, och för behållare, till exempel databaser och modeller för scenarier där taggar och beskrivningar inte är tillräckliga.
* Stöd för manuell registrering av kända typer av datakällor. Användare kan manuellt ange information om datakälla med hjälp av Azure Data Catalog-portalen för alla typer av datakällor stöds av Azure Data Catalog.
* Stöd för auktorisering av Azure Active Directory-säkerhetsgrupper. Katalogadministratörer kan aktivera katalog åtkomst till säkerhetsgrupper, användarkonton, vilket gör det enklare att hantera åtkomst till Azure Data Catalog.
* Stöd för att öppna Hive-datakällor i Excel från Azure Data Catalog-portalen.

> [!NOTE]
> För den aktuella versionen stöds endast Teradata TD2 autentisering. Ytterligare autentisering autentiseringsmetoder som stöds i framtida versioner.

> [!NOTE]
> Om du vill använda funktionen ”Öppna i Excel” för Hive-datakällor, måste användare ha installerat ODBC-drivrutinen för Hive.

## <a name="whats-new-for-september-2015"></a>Vad är nytt för September 2015
Från och med September 2015 har följande funktioner lagts till Azure Data Catalog:

* Stöd för inklusive data profiler när du registrerar Hive-datakällor.
* Stöd för identifiering av programmässigt Catalog-API, vilket gör det enklare för program att integrera med Azure Data Catalog.
* En ny ”komma igång” datakälla identifiering upplevelse i Azure Data Catalog-portalen. När användarna skriver in sidan ”identifiera” i Azure Data Catalog-portalen utan att ange en sökterm, visas en översikt över katalogen innehållet inklusive används mest taggar, experter, typer av datakällor och objekttyper.
* Stöd för registrering och identifiera Azure SQL Data Warehouse-objekt och databaser. Ytterligare information om Azure SQL Data Warehouse finns [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Stöd för registrering och identifiering av SQL Server Analysis Services-modeller och SQL Server Reporting Services-servrar som behållare. När registrering SSAS och SSRS-objekt, skapas en post för SSAS-modell och SSRS-servern och för rapporter och andra objekt i Azure Data Catalog. Behållarna kan identifieras och kommenterats med hjälp av Azure Data Catalog-portalen. Användare kan också söka och filtrera innehållet i en modell eller server förutom sökning och filtrering innehållet i katalogen.
* Stöd för registrering och identifiera SQL Server Analysis Services-objekt via HTTP eller HTTPS. Användare kan ansluta till SSAS-servrar med hjälp av en URL (t.ex https://servername/olap/msmdpump.dll) i stället för ett servernamn nu och kan använda grundläggande autentisering och anonyma anslutningar förutom Windows-autentisering. Ytterligare information om HTTP/HTTPS-anslutningar till SSAS finns [konfigurera HTTP-åtkomst till Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Stöd för Hive-datakällor på HDInsight. Användare kan nu registrera och identifierar Hive-tabeller för Apache Hive i Hadoop på HDInsight-datakällor. Ytterligare information om Hive i HDInsight finns i [HDInsight Dokumentationscenter](../hdinsight/hdinsight-use-hive.md).
* Stöd för registrering och identifiering av Oracle-databaser och HDFS-kluster som behållare. När du registrerar Oracle-tabeller och vyer eller HDFS skapas Azure Data Catalog en post för databasen, tabeller och vyer. Databasen kan identifieras och kommenterats med hjälp av Azure Data Catalog-portalen. Användare kan också söka och filtrera innehållet i en databas eller ett kluster utöver sökning och filtrering innehållet i katalogen.
* Stöd för manuell registrering av okända typer av datakällor. Användare kan manuellt ange information om datakälla med hjälp av Azure Data Catalog-portalen så att de datakällor som inte stöds av registreringsverktyget för datakällor kan kommenterats och identifieras.
* Stöd för registrering och identifiering av SQL Server-databaser som behållare. När registrering SQL Server-tabeller och vyer, skapas en post för databasen, tabeller och vyer i Azure Data Catalog. Databasen kan identifieras och kommenterats med hjälp av Azure Data Catalog-portalen. Användare kan också söka och filtrera innehållet i en databas utöver sökning och filtrering innehållet i katalogen.

> [!NOTE]
> SSAS och SSRS-objekt som har varit registrerade före den 18 September versionen måste registreras igen med hjälp av registreringsverktyget för datakällor innan transaktionen modell eller server har lagts till i katalogen. Registrera en datakälla påverkar inte eventuella kommentarer som lagts till av användare i Azure Data Catalog-portalen.

> [!NOTE]
> Oracle-tabeller och vyer och HDFS filer och kataloger som är registrerade före den 11 September versionen måste registreras igen med hjälp av registreringsverktyget för datakällor innan transaktionen databas eller ett kluster läggs till i katalogen. Registrera en datakälla påverkar inte eventuella kommentarer som lagts till av användare i Azure Data Catalog-portalen.

> [!NOTE]
> SQL Server-tabeller och vyer som är registrerade före September 4 versionen måste registreras igen med hjälp av registreringsverktyget för datakällor innan transaktionen databas läggs till i katalogen. Registrera en datakälla påverkar inte eventuella kommentarer som lagts till av användare i Azure Data Catalog-portalen.

## <a name="whats-new-for-august-2015"></a>Vad är nytt för augusti 2015
Från och med augusti 2015 har följande funktioner lagts till Azure Data Catalog:

* Stöd för data profilering av SQL Server och Oracle-datakällor. När du registrerar SQL Server och Oracle-tabeller och vyer, kan användare välja att inkludera data profilinformation för objekt som registreras. Profilen data innehåller statistik på objektnivå och på kolumnnivå.
* Stöd för Hadoop HDFS-datakällor. Användare kan nu registrera och identifiera HDFS filer och kataloger.
* Stöd för att tillhandahålla information om åtkomstbegäran för registrerade datakällor. För alla registrerade datatillgångar kan användare nu anvisningar för begär åtkomst, inklusive e-länkar eller URL: er, lätt kan integrera med befintliga verktyg och processer.
* Knappbeskrivning för taggar och experter som gör det lättare att identifiera vad användare har angett vilka metadata för registrerade datatillgångar.
* Vi har lagt till en ny ”användare”-knappen och menyn till vår högsta navigeringsfältet. Den här menyn användaren kan se det konto som används för att logga in på Azure Data Catalog och för att logga ut om du vill. Den här menyn visar även katalognamn som är värdefull för utvecklare som använder Azure Data Catalog REST API.
* Standard Edition: När du lägger till ägare till datatillgångar Azure Data Catalog stöder nu både användarkonton och säkerhetsgrupper som ägare. Om du vill lägga till en säkerhetsgrupp som en ägare för valda datatillgångar och kan du ange gruppens namn eller gruppens UPN-e-postadress, om det finns någon.
* Stöd för Azure Blob Storage-datakällor. Användare kan nu registrera och identifiera Azure Storage-blobbar och kataloger.

