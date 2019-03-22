---
title: Vad är nytt i Azure Data Catalog
description: Den här artikeln innehåller en översikt över nya funktioner som har lagts till Azure Data Catalog.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 0f328c4edeaa35210c5379d84fa47980cb2f07cf
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317093"
---
# <a name="whats-new-in-azure-data-catalog"></a>Vad är nytt i Azure Data Catalog
Uppdaterar till **Azure Data Catalog** släpps regelbundet. Inte alla versionen innehåller nya funktioner för användarinriktade, eftersom vissa utgåvor av fokuserar på funktioner för backend-tjänst. Den här sidan visar nya användarinriktade funktioner som lagts till i Azure Data Catalog-tjänsten.

## <a name="whats-new-for-november-2017"></a>Vad är nytt för November 2017 
Från och med November 2017 kan har följande funktioner lagts till Azure Data Catalog:

* Stöd för att länka direkt till specifika företagstermer i Data Catalog-portalen. Användare kan kopiera länkar från en företagsordlista och bädda in dem i dokument, e-postmeddelanden, rapporter eller andra platser för att länka direkt till definition av termer i ordlistan.
* Stöd för Azure Active Directory-tjänstobjekt. Data Catalog-administratörer kan tillåta klientprogram som använder tjänstens huvudnamn med åtkomst till katalogen och kan ge programmen specifika behörigheter precis som de kan ge behörigheter till användare och säkerhetsgrupper. Mer information finns i [program och tjänstobjekt i Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).
* Stöd för Azure Active Directory-autentisering när du ansluter till Azure SQL Database och Azure SQL Data Warehouse-datakällor med hjälp av registreringsverktyget Data Catalog. Mer information finns i [Använd Azure Active Directory-autentisering för autentisering med SQL Database eller SQL Data Warehouse](../sql-database/sql-database-aad-authentication.md).


## <a name="whats-new-for-september-2017"></a>Vad är nytt för September 2017 
Från och med September 2017, har följande funktioner lagts till Azure Data Catalog:

* Stöd för att extrahera delta relationsmetadata från DB2 datakällor när du registrerar relaterade tabeller med registreringsverktyget för källan.
* Stöd för att registrera MongoDB version 3.4 datakällorna med registreringsverktyget för källan.
* Stöd för att ta bort alla metadata för objekten i en enda åtgärd när du tar bort en databas eller annan behållare från datakatalogen.
* Stöd för att visa upp till 1 000 taggar, företagstermer eller andra sökningar aspekter, när förfina din sökning i Data Catalog-portalen.


## <a name="whats-new-for-august-2017"></a>Vad är nytt för augusti 2017 
Från och med augusti 2017 har följande funktioner lagts till Azure Data Catalog:

*   Ett nytt developer-exempel finns för att skapa och hantera relationsmetadata med Data Catalog REST-API: et. Den *importera relationsinformation till Data Catalog* exempel finns på den [Data Catalog sidan med kodexempel](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Stöd för att extrahera delta relationsmetadata från Teradata-datakällor när du registrerar relaterade tabeller med registreringsverktyget för källan.
* Stöd för SQL Server tabellvärdesfunktion (TVF) objekt när du registrerar SQL Server-datakällor med hjälp av registreringsverktyget för källan.
* Flera uppdateringar och sökvillkoren för att öka prestanda och användbarhet för Data Catalog-portalen.

## <a name="whats-new-for-july-2017"></a>Vad är nytt för juli 2017 
Från och med juli 2017, har följande funktioner lagts till Azure Data Catalog:
*   Stöd för mer detaljerad kontroll över tillåtna metadataåtgärder, inklusive:
    - Katalogadministratörer kan begränsa användarnas möjlighet att bidra med taggar och tillhörande metadata till katalogen att aktivera skrivskyddad åtkomst till katalogen.
    - Katalogadministratörer kan begränsa användarnas möjlighet att registrera nya datakällor i katalogen.
    - Katalogadministratörer kan begränsa användarnas möjlighet att överta ägarskapet för data tillgångens metadata i katalogen.
    - Behörighet kan beviljas till Azure Active Directory-säkerhetsgrupper och användare för att underlätta hanteringen av behörigheter.
* Stöd för relationer mellan registrerade datatillgångar och identifierande relaterade datatillgångar i Data Catalog-portalen, inklusive:
    - Extrahering av relationsmetadata från SQL Server (inklusive Azure SQL Database), Oracle och MySQL datakällor när du använder registreringsverktyget Data Catalog.
    - Identifiering av relaterade datatillgångar när du visar tillgångens metadata i Data Catalog-portalen.
    - Åtgärder för att definiera, identifiera och hantera relationer mellan datatillgångar med hjälp av Data Catalog REST-API: et.

Mer information om hur du hanterar behörigheter i Data Catalog finns i [hur du skyddar åtkomsten till data catalog och datatillgångar](data-catalog-how-to-secure-catalog.md).
Ytterligare information om relationer i Data Catalog finns i [Visa relaterade datatillgångar i Azure Data Catalog](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Nyheter för juni 2017 
Från och med juni 2017, har följande funktioner lagts till Azure Data Catalog:
*   Stöd för Sybase, Apache Cassandra och MongoDB-datakällor. Användare kan nu registrera och identifiera Cassandra och MongoDB-databaser och tabeller och Sybase-databaser, tabeller och vyer.

> [!NOTE]
> När du registrerar MongoDB och Cassandra-tabeller som innehåller kolumner med komplexa datatyper, till exempel poster och matriser, inkluderas inte dessa kolumner i metadata läggs till Data Catalog.

## <a name="whats-new-for-may-2017"></a>Vad är nytt för maj 2017 
Från och med maj 2017, har följande funktioner lagts till Azure Data Catalog:
*   Ett nytt developer-exempel finns för massimport av företagstermer. Ordlista massimport-exempel finns på den [sidan för Data Catalog developer-exempel](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Stöd för att redigera anslutningsinformationen för ODBC i Azure Data Catalog-portalen. Tillgångsägare för data och Data Catalog-administratörer kan nu redigera anslutningsinformationen för registrerade ODBC-datakällor utan att behöva registrera datakällor.
*   Stöd för klickbara URL: er i företag ordlista definitioner och beskrivningar. När URL: er ingår i egenskaperna beskrivning och definitionen för företagstermer, visas webbadresserna som hyperlänkar i Data Catalog-portalen.
*   Stöd för att visa experten namn förutom experten e-postadresser. När du visar experter i egenskaperna för en datatillgång i Data Catalog-portalen, visas handledarens fullständigt namn från Azure Active Directory i knappbeskrivningen.

## <a name="whats-new-for-april-2017"></a>Vad är nytt för April 2017 
Från och med April 2017, har följande funktioner lagts till Azure Data Catalog:
*   Stöd för ODBC-datakällor. Användare kan nu registrera och identifiera ODBC-databaser, tabeller och vyer med Data Catalog registreringsverktyget.

## <a name="whats-new-for-march-2017"></a>Vad är nytt för mars 2017 
Från och med mars 2017 kommer har följande funktioner lagts till Azure Data Catalog:
*   Stöd för användning av AAD-säkerhetsgrupper för Data Catalog-administratörer.
*   Azure Data Catalog finns nu i en ny Azure-region. Kunder kan etablera Azure Data Catalog i USA, västra centrala regionen, förutom östra USA, västra USA, Europa, västra och Östra Australien, Norra Europa och Asien, sydöstra. Mer information finns i [Azure-regionerna](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Vad är nytt för februari 2017 
Från och med februari 2017, har följande funktioner lagts till Azure Data Catalog:
*   Stöd för avancerade inställningar i Data Catalog registreringsverktyget. Användare kan ange timeout-värdena för kommandot när du registrerar stora datakällor.
*   Stöd för FTP- och PostgreSQL-datakällor. Användare kan nu registrera och identifiera FTP-filer och kataloger, och PostgreSQL-databaser, tabeller och vyer.

## <a name="whats-new-for-january-2017"></a>Vad är nytt för januari 2017 
Från och med januari 2017, har följande funktioner lagts till Azure Data Catalog:
*   Azure Data Catalog är nu [CSA STAR](https://www.microsoft.com/en-us/trustcenter/compliance/csa-star-certification) kompatibla.
*   Integrering med [Hämta & transformera i Excel 2016 och Power Query för Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Excel-användare kan dela frågor och identifiera frågor med Azure Data Catalog från i Excel. Den här funktionen är tillgänglig för användare med Power BI Pro-licenser.

## <a name="whats-new-for-december-2016"></a>Vad är nytt för December 2016
Från och med December 2016 har följande funktioner lagts till Azure Data Catalog:
*   Azure Data Catalog är nu [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) och [EU: s Modellklausuler](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses) kompatibla.
*   Stöd för att redigera anslutningsinformationen för datakällan. Tillgångsägare för data och Data Catalog-administratörer kan nu redigera anslutningsinformationen för registrerade datakällor utan att behöva registrera datakällor.
*   Stöd för Salesforce.com-datakällor. Användare kan nu registrera och identifiera Salesforce-objekt.


## <a name="whats-new-for-november-2016"></a>Vad är nytt för November 2016
Från och med November 2016 kommer har följande funktioner lagts till Azure Data Catalog:
*   Azure Data Catalog är nu [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) och [ISO/IEC 27018](https://www.microsoft.com/en-us/TrustCenter/Compliance/iso-iec-27018) kompatibla.
*   Stöd för manuell registrering av ODBC-datakällor med hjälp av Data Catalog-portalen och REST API.

## <a name="whats-new-for-september-2016"></a>Vad är nytt för September 2016
Från September 2016 är har följande funktioner lagts till Azure Data Catalog:

* Stöd för IBM DB2-datakällor. Användare kan nu registrera och identifiera DB2-databaser, tabeller och vyer.
* Stöd för Azure Cosmos DB-datakällor. Användare kan nu registrera och identifiera Cosmos DB-databaser och samlingar.
* Stöd för att anpassa katalognamnet i Data Catalog-portalen. Katalogadministratörer kan nu ange text som visas i portalen rubriken, till exempel organisationens namn.

## <a name="whats-new-for-august-2016"></a>Vad är nytt för augusti 2016
Följande funktioner har lagts till Azure Data Catalog från augusti 2016:

* Förbättringar för registrering av SQL Server Master Data Services (MDS)-datakällor. Användarna kan nu inkludera förhandsversioner och data profiler när du registrerar MDS-entiteter med hjälp av Data Catalog registreringsverktyget.
* Stöd för administratörsdefinierad organisationens sparade sökningar. När du sparar en sökning i Data Catalog-portalen, kan Data Catalog-administratörer nu välja att spara sökningar för personligt bruk eller för alla kataloganvändare. Organisationens sparade sökningar som delas med alla kataloganvändare och kan tillhandahålla en standardiserad startpunkter för identifiering av datakällor.
* Uppdateringar till egenskapsvyn i Data Catalog-portalen. Alla datatillgångarnas egenskaper är nu visas och hanteras i en enda kan ändra storlek i fönstret för att ge en mer konsekvent och identifierbart upplevelse.

## <a name="whats-new-for-july-2016"></a>Vad är nytt för juli 2016
Från och med juli 2016 kommer har följande funktioner lagts till Azure Data Catalog:

* Stöd för SQL Server Master Data Services (MDS)-datakällor. Användare kan nu registrera och identifiera MDS-modeller och entiteter.
* Stöd för SQL-lagrade procedurer. Användare kan nu registrera och identifiera objekt för lagrad procedur i SQL Server-datakällor.
* Stöd för ytterligare språk i Azure Data Catalog-portalen och registreringsverktyget, totalt 18 språk som stöds. Azure Data Catalog användarupplevelsen lokaliseras baserat på datorns språkinställningar i Windows eller i webbläsaren.
* Uppdateringar och förfining för Data Catalog startsidan, inklusive prestandaförbättringar och en smidig användarupplevelse.

## <a name="whats-new-for-june-2016"></a>Nyheter för juni 2016
Från och med juni 2016 har följande funktioner lagts till Azure Data Catalog:

* Stöd för att ändra storlek på kolumner i listvyn när identifiera datatillgångar i Data Catalog-portalen. Användare kan nu ändra storlek på enskilda kolumner för att enklare att läsa långa tillgångens metadata, till exempel taggar och beskrivningar.
* Power Query för Excel har lagts till på ”Öppna i”-menyn i Data Catalog-portalen. Användare kan nu öppna datakällor som stöds i Excel 2016 eller Excel 2010 och Excel 2013 med den [Power Query för Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) -tillägget installerat.
* Stöd för Azure Table Storage-datakällor. Användare kan nu registrera och identifiera tabellobjekt i Azure Storage-datakällor.

## <a name="whats-new-for-may-2016"></a>Vad är nytt för maj 2016
Följande funktioner har lagts till Azure Data Catalog maj 2016:

* En Företagsordlista som hjälper katalogadministratörer att definiera företagstermer och hierarkier för att skapa en gemensam företagsterminologi. Användare kan tagga registrerade datatillgångar med termer från ordlistan för att göra det lättare att identifiera och förstå innehållet i katalogen. Mer information finns i [Så här konfigurerar du en företagsordlista för hanterade taggar](data-catalog-how-to-business-glossary.md)  
* Förbättringar i Data Catalog Företagsordlista som hjälper användarna att uppdatera flera termer i ordlistan i en enda åtgärd. Användarna kan välja flera villkor för att redigera följande fält:
  * Överordnad Term: Användaren kan välja en ny överordnad term och alla valda villkor har uppdaterats för att vara underordnade platser till den valda överordnade termen. Om den valda villkor alla har samma överordnade och det överordnat visas i textrutan, annars den överordnade termen anges till tom.   
  * Taggar och intressenter: Användare kan lägga till och ta bort taggar och intressenter för flera termer i ordlistan med samma upplevelse som taggning flera datatillgångar.

> [!NOTE]
> En företagsordlista är endast tillgängliga i Standard Edition av Azure Data Catalog. Den kostnadsfria versionen ger inte funktioner för styrda märkning eller en företagsordlista.

## <a name="whats-new-for-march-2016"></a>Vad är nytt för mars 2016
Följande funktioner har lagts till Azure Data Catalog: g för mars 2016:

* En konsoliderad REST API-slutpunkt för att komma åt sökfunktioner och catalog tillgången hanteringsfunktionerna i Azure Data Catalog-tjänsten. Den här search API-slutpunkt och katalog-API-slutpunkten var inaktuellt och upphöra den 21 mars 2016. Det finns inga ändringar på semantiken för API: et. Endast slutpunkten URI ändrad. Mer information finns i den [Azure Data Catalog REST API-referens](https://msdn.microsoft.com/library/azure/mt267595.aspx). API-exempel finns [developer-exempel för Azure Data Catalog](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Vad är nytt för februari 2016
Från och med februari 2016 kommer har följande funktioner lagts till Azure Data Catalog:

* Källa för topprekommendationer dataurval användarupplevelsen i Azure Data Catalog registreringsverktyget. Registreringsverktyget har uppdaterats för att göra det enklare att leta upp och välj från datakällor som stöds av Azure Data Catalog.
* Stöd för 10 ytterligare språk i Azure Data Catalog-portalen och registreringsverktyget. Utöver engelska finns nu Azure Data Catalog-upplevelse på tyska, spanska, franska, italienska, japanska, koreanska, portugisiska (Brasilien), ryska, kinesiska (förenklad) och traditionell kinesiska. Azure Data Catalog användarupplevelsen lokaliseras baserat på datorns språkinställningar i Windows eller i användarens webbläsare.
* Stöd för geo-replikering av data i Azure Data Catalog för affärskontinuitet och haveriberedskap. Allt innehåll i Azure Data Catalog, inklusive data käll-metadata och crowdsourcade anteckningar, replikeras nu mellan två Azure-regioner utan extra kostnad för kunder. Azure-regioner har redan parats ihop, minst 500 miles från varandra, och följ mappningen enligt beskrivningen i [företag affärskontinuitet och haveriberedskap recovery (BCDR): Parade Azure-regioner](../best-practices-availability-paired-regions.md).
* Stöd för att ändra den Azure-prenumeration används av Azure Data Catalog. Azure Data Catalog-administratörer kan använda sidan Inställningar i Azure Data Catalog-portalen för att välja en annan Azure-prenumeration för fakturering.

## <a name="whats-new-for-january-2016"></a>Vad är nytt för januari 2016
Från och med januari 2016, har följande funktioner lagts till Azure Data Catalog:

* Stöd för att manuellt registrera ytterligare datakällor. Du kan nu använda ”skapa manuell post” i Azure Data Catalog-portalen eller Använd Azure Data Catalog REST API för att registrera följande datakällor:
  * OData - funktionen och Entitetsuppsättning Entitetsbehållare
  * HTTP - filen, slutpunkt, rapporten och plats
  * Filsystem - fil
  * SharePoint - List
  * FTP - fil och katalog
  * Salesforce.com - Object
  * DB2 - tabell, vy och databas
  * PostgreSQL - tabell, vy och databas
* Stöd för ”öppna i SQL Server Data Tools” för SQL Server (inklusive Azure SQL DB och Azure SQL Data Warehouse)-datakällor.  
* Stöd för att registrera och identifiera SAP HANA-vyer och paket. Du kan registrera SAP HANA-datakällor med hjälp av Azure Data Catalog datakälla registreringsverktyget, och kan lägga till anteckningar och identifiera registrerade datakällor för SAP HANA med hjälp av Azure Data Catalog-portalen.
* Möjligheten att fästa och ta bort datatillgångar i Azure Data Catalog-portalen. Du kan välja att Fäst datatillgångar så att de blir enklare att identifiera och återanvända.
* En topprekommendationer startsida i Azure Data Catalog-portalen. Ny startsida innehåller information om den pågående aktiviteten för användare – inklusive nyligen publicerade tillgångar, fästa tillgångar och sparade sökningar- och insyn i aktiviteten i katalogen som helhet.
* Stöd för beständiga användarinställningar i Azure Data Catalog-portalen. Inställningar användargränssnitt - inklusive rutnät eller panelen Visa antalet resultat per sida och markering eller inaktivera av träffar - sparas mellan användarsessioner.
* Azure Data Catalog finns nu i två nya Azure-regioner. Kunder kan etablera Azure Data Catalog i Norra Europa och Asien, sydöstra regioner, förutom östra USA, västra USA, Europa, västra och Östra Australien. Mer information finns i [Azure-regionerna](https://azure.microsoft.com/regions/).

> [!NOTE]
> ”Öppna i SQL Server Data Tools” kräver Visual Studio 2013 uppdatering 4 och verktyg för SQL Server installeras. Du kan installera den senaste versionen av SQL Server Data Tools [ladda ned SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Vad är nytt för December 2015
Från och med December 2015, har följande funktioner lagts till Azure Data Catalog:

* Stöd för data-profiler för Azure SQL Data Warehouse-datakällor. När du registrerar Azure SQL Data Warehouse-tabeller och vyer, kan användarna välja att inkludera data profil mått med metadata som extraherats från datakällan.
* Stöd för att registrera och identifiera MySQL-objekt och databaser. Användare kan registrera datakällor för MySQL med hjälp av Azure Data Catalog datakälla registreringsverktyget, och kan lägga till anteckningar och identifiera registrerade datakällor för MySQL med hjälp av Azure Data Catalog-portalen.
* Stöd för SPNEGO och Windows-autentisering för Teradata-datakällor. När du registrerar Teradata-tabeller och vyer, kan användare välja att ansluta till Teradata med SPNEGO och Windows, och LDAP och TD2 autentisering.
* Stöd för Azure Data Lake Store-datakällor. Användare kan nu registrera och identifiera datakällor i Azure Data Lake Store med hjälp av Azure Data Catalog.
* Stöd för att manuellt ange proxyinställningar för nätverk i Azure Data Catalog registreringsverktyget. Användare kan välja ”Ändra proxyinställningar” från verktygets välkomstsidan och kan ange proxyadress och port som ska användas av verktyget.

## <a name="whats-new-for-november-2015"></a>Vad är nytt för November 2015
Från och med November 2015, har följande funktioner lagts till Azure Data Catalog:

* Möjligheten att visa och kopiera anslutningssträngar i Azure Data Catalog-portalen för SQL Server (inklusive Azure SQL Database) och Oracle-datakällor. Användarna kan klicka på ”Visa anslutningssträngar”-länken i anslutningsinformationen för en SQL Server- eller Oracle-tabell, vy eller databasen för att se anslutningssträngar som används för att ansluta till datakällan. ADO.NET, ODBC, OLEDB och JDBC-anslutningssträngar tillhandahålls för SQL Server-datakällor. ODBC- och OLEDB-anslutningssträngar tillhandahålls för Oracle-datakällor.
* Stöd för att inkludera data profiler när du registrerar Teradata-tabeller och vyer.
* Stöd för ”öppna i Power BI Desktop” för SQL Server (inklusive Azure SQL DB och Azure SQL Data Warehouse), SQL Server Analysis Services, Azure Storage och HDFS källor.  
* Stöd för LDAP-autentisering för Teradata-datakällor. När du registrerar Teradata-tabeller och vyer som användare kan välja att ansluta till Teradata LDAP, och TD2 autentisering.
* Stöd för ”öppna i Excel” för Teradata-datakällor.
* Stöd för de senaste söktermer i Azure Data Catalog-portalen. När du söker i portalen kan kan användarna välja från nyligen använda söktermer snabbare upplevelsen av identifieringen.
* Stöd för förhandsgranskning för Teradata-datakällor. När du registrerar Teradata-tabeller och vyer, kan användarna välja att inkludera ögonblicksbild poster med metadata som extraherats från datakällan.
* Stöd för ”öppna i Excel” för Azure SQL Data Warehouse-datakällor.
* Stöd för att definiera och redigera på kolumnnivå scheman för manuellt registrerade datatillgångar. När du har skapat manuellt en datatillgång med hjälp av Azure Data Catalog-portalen, kan användarna lägga till kolumndefinitionerna i datatillgångarnas egenskaper.
* Stöd för ”har”-frågor när du söker Azure Data Catalog och aktivera identifiering av registrerade datatillgångar som har specifika metadata. Azure Data Catalog-frågesyntax innehåller nu:

| Frågesyntax | Syfte |
| --- | --- |
| `has:previews` |Söker efter datatillgångar som innehåller en förhandsgranskning |
| `has:documentation` |Söker efter datatillgångar som dokumentationen har angetts |
| `has:tableDataProfiles` |Söker efter datatillgångar med data på tabellen profilinformation |
| `has:columnsDataProfiles` |Söker efter datatillgångar med data på kolumnnivå profilinformation |


> [!NOTE]
> ”Öppna i Power BI Desktop” kräver en aktuell version av Power BI Desktop programmet installeras. Om du stöter på problem eller fel med den här funktionen kan du se till att du har den senaste versionen av Power BI Desktop från [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Vad är nytt för oktober 2015
Från och med oktober 2015, har följande funktioner lagts till Azure Data Catalog:

* Stöd för kryptering av vilande data förhandsversioner och data profiler för registrerade datakällor. Azure Data Catalog krypterar transparent alla poster i förhandsversion och data profiler datakällor som registrerats med tjänsten, utan som katalogadministratörer.
* Stöd för Teradata-datakällor. Användare kan nu registrera och identifiera Teradata-tabeller och vyer.
* Stöd för lokala Hive-datakällor. Användare kan nu registrera och identifiera Hive-tabeller för Apache Hive i Hadoop lokala datakällor.
* Stöd för sparade sökningar i Azure Data Catalog-portalen. Användare kan spara sökvillkoren och filtrera val enkelt Upprepa föregående sökningar och definiera användbara vyer för katalogens innehåll. Användaren kan också markera en sparad sökning som standardsökningen. När en användare klickar på ”” Sök förstoringsglaset från startsidan för Azure Data Catalog-portalen eller från ”komma igång”-sida, tas användaren direkt till den sparade sökningen som flaggats som standard.
* Stöd för RTF-dokumentationen finns registrerade datatillgångar och behållare i Azure Data Catalog-portalen. Användare kan nu ange dokumentation för datatillgångar som tabeller, vyer och rapporter, och för behållare, till exempel databaser och modeller för scenarier där taggar och beskrivningar inte är tillräckliga.
* Stöd för att manuellt registrera kända typer av datakällor. Användare kan ange information om datakällan med hjälp av Azure Data Catalog-portalen för alla typer av datakällor stöds av Azure Data Catalog manuellt.
* Stöd för auktorisering av Azure Active Directory-säkerhetsgrupper. Katalogadministratörer kan aktivera catalog åtkomst till säkerhetsgrupper, användarkonton, vilket gör det enklare att hantera åtkomst till Azure Data Catalog.
* Stöd för att öppna Hive-datakällor i Excel från Azure Data Catalog-portalen.

> [!NOTE]
> För den aktuella versionen kan stöds endast Teradata TD2 autentisering. Ytterligare autentisering mekanismer som stöds i framtida versioner.

> [!NOTE]
> Om du vill använda funktionen ”Öppna i Excel” för Hive-datakällor, måste användare vara installerad ODBC-drivrutinen för Hive.

## <a name="whats-new-for-september-2015"></a>Vad är nytt för September 2015
Från och med September 2015 kommer följande funktioner har lagts till Azure Data Catalog:

* Stöd för att inkludera data profiler när du registrerar Hive-datakällor.
* Stöd för identifiering av programmässigt Catalog-API, vilket gör det enklare för program att integreras med Azure Data Catalog.
* Nya ”komma igång” data source affärsanalyser i Azure Data Catalog-portalen. När användare anger sidan ”identifiera” i Azure Data Catalog-portalen utan att ange en sökterm kan se de en översikt över katalogens innehåll, inklusive de mest använda taggar, experter, typer av datakällor och objekttyper.
* Stöd för att registrera och identifiera Azure SQL Data Warehouse-objekt och databaser. Mer information om Azure SQL Data Warehouse och se [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Stöd för att registrera och identifiera SQL Server Analysis Services-modeller och SQL Server Reporting Services-servrar som behållare. När du registrerar SSAS och SSRS-objekt, skapar en post för SSAS-modellen och SSRS-servern och för rapporter och andra objekt i Azure Data Catalog. Behållarna som kan identifieras och kommenterats med hjälp av Azure Data Catalog-portalen. Användare kan också söka och filtrera innehållet i en modell eller server förutom söka och filtrera innehållet i katalogen.
* Stöd för att registrera och identifiera SQL Server Analysis Services-objekt via HTTP/HTTPS. Användare kan nu ansluta till SSAS-servrar med en URL (till exempel https:\//servername/olap/msmdpump.dll) i stället för en server namn och kan använda grundläggande autentisering och anonyma anslutningar förutom Windows-autentisering. Ytterligare information om HTTP/HTTPS-anslutningar till SSAS finns i [konfigurera HTTP-åtkomst till Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Stöd för datakällor för Hive på HDInsight. Användare kan nu registrera och identifiera Hive-tabeller för Apache Hive i Hadoop på HDInsight-datakällor. Ytterligare information om Hive på HDInsight finns i den [dokumentationscentret för HDInsight](../hdinsight/hadoop/hdinsight-use-hive.md).
* Stöd för att registrera och identifiera Oracle-databaser och HDFS-kluster som behållare. När du registrerar Oracle-tabeller och vyer eller HDFS, skapar en post för databasen, tabeller och vyer i Azure Data Catalog. Databasen kan identifieras och kommenterade med hjälp av Azure Data Catalog-portalen. Användare kan också söka och filtrera innehållet i en databas eller -kluster förutom söka och filtrera innehållet i katalogen.
* Stöd för att manuellt registrera okända typer av datakällor. Användare kan ange information om datakällan med hjälp av Azure Data Catalog-portalen så att inte uttryckligen stöds av registreringsverktyget för datakällor kan kommenterade och identifieras manuellt.
* Stöd för att registrera och identifiera SQL Server-databaser som behållare. När du registrerar SQL Server-tabeller och vyer, skapar en post för databasen, tabeller och vyer i Azure Data Catalog. Databasen kan identifieras och kommenterade med hjälp av Azure Data Catalog-portalen. Användare kan också söka och filtrera innehållet i en databas förutom söka och filtrera innehållet i katalogen.

> [!NOTE]
> SSAS och SSRS-objekt som är registrerade före den 18 September versionen måste registreras igen med hjälp av registreringsverktyget innan posten modell eller server har lagts till i katalogen. Registrera en datakälla påverkar inte eventuella kommentarer som lagts till av användare i Azure Data Catalog-portalen.

> [!NOTE]
> Oracle-tabeller och vyer och HDFS-filer och kataloger som är registrerade före den 11 September versionen måste registreras igen med hjälp av registreringsverktyget innan posten databas eller ett kluster har lagts till i katalogen. Registrera en datakälla påverkar inte eventuella kommentarer som lagts till av användare i Azure Data Catalog-portalen.

> [!NOTE]
> SQL Server-tabeller och vyer som är registrerade innan September 4-versionen måste registreras igen med hjälp av registreringsverktyget innan databasposten läggs till i katalogen. Registrera en datakälla påverkar inte eventuella kommentarer som lagts till av användare i Azure Data Catalog-portalen.

## <a name="whats-new-for-august-2015"></a>Vad är nytt för augusti 2015
Från och med augusti 2015 kommer har följande funktioner lagts till Azure Data Catalog:

* Stöd för data profilering av SQL Server- och Oracle-datakällor. När du registrerar SQL Server- och Oracle-tabeller och vyer, kan användarna välja att inkludera data profilinformation för objekt som registreras. Dataprofilen innehåller statistik på objektnivå och på kolumnnivå.
* Stöd för HDFS Hadoop-datakällor. Användare kan nu registrera och identifiera HDFS-filer och kataloger.
* Stöd för att tillhandahålla information om åtkomstbegäran för registrerade datakällor. För registrerade datatillgångar, kan användarna nu ge instruktioner man begär åtkomst, inklusive via e-post eller URL: er, lätt kan integrera med befintliga verktyg och processer.
* Knappbeskrivning för taggar och experter för att göra det enklare att identifiera vilka användare har lagt till vilka metadata för registrerade datatillgångar.
* Vi har lagt till en ny ”användare”-knappen och menyn vår övre navigeringsfältet. Den här menyn användaren kan se det konto som används för att logga in på Azure Data Catalog och för att logga ut om du vill. Den här menyn visar även katalognamnet är värdefullt för utvecklare som använder Azure Data Catalog REST-API.
* Standard Edition: När du lägger till ägare till datatillgångar, Azure Data Catalog nu stöder både användarkonton och säkerhetsgrupper som ägare. Om du vill lägga till en säkerhetsgrupp som ägare för valda datatillgångar, kan du ange gruppens visningsnamn eller gruppens UPN-e-postadress, om det finns en.
* Stöd för Azure Blob Storage-datakällor. Användare kan nu registrera och identifiera Azure Storage-blobbar och kataloger.

