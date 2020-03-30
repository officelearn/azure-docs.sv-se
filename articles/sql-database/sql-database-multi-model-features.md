---
title: Funktioner för flera modeller
description: Med Azure SQL Database kan du arbeta med flera datamodeller i samma databas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802821"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Funktioner för flera modeller i Azure SQL Database

Med multimodelldatabaser kan du lagra och arbeta med data som representeras i flera dataformat, till exempel relationsdata, diagram, JSON/XML-dokument, nyckelvärdespar etc.

## <a name="when-to-use-multi-model-capabilities"></a>När ska du använda funktioner med flera modeller

Azure SQL Database är utformad för att fungera med relationsmodellen som ger bäst prestanda i de flesta fall för en mängd olika generella program. Azure SQL Database är dock inte begränsat till relationsdata. Med Azure SQL Database kan du använda en mängd olika icke-relationsformat som är tätt integrerade i relationsmodellen.
Du bör överväga att använda funktioner för flera modeller i Azure SQL Database i följande fall:
- Du har viss information eller strukturer som passar bättre för NoSQL-modeller och du vill inte använda separat NoSQL-databas.
- En majoritet av dina data är lämpliga för relationsmodell och du måste modellera vissa delar av dina data i NoSQL-stil.
- Du vill utnyttja det rika Transact-SQL-språket för att fråga och analysera både relations- och NoSQL-data och integrera dem med en mängd olika verktyg och program som kan använda SQL-språk.
- Du vill använda databasfunktioner som [minnesteknik](sql-database-in-memory.md) för att förbättra prestanda för din analytiska eller bearbetning av nosql-datastrukturer, använda [transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) eller [läsbara repliker](sql-database-read-scale-out.md) för att skapa en kopia av dina data på den andra platsen och avlasta vissa analytiska arbetsbelastningar från den primära databasen.

## <a name="overview"></a>Översikt

Azure SQL innehåller följande funktioner för flera modeller:
- [Med diagramfunktioner](#graph-features) kan du representera dina data som en uppsättning noder och kanter `MATCH` och använda vanliga Transact-SQL-frågor som förbättrats med diagramoperatorn för att fråga grafdata.
- [Med JSON-funktioner](#json-features) kan du placera JSON-dokument i tabeller, omvandla relationsdata till JSON-dokument och vice versa. Du kan använda standardspråket Transact-SQL som förbättrats med JSON-funktioner för att tolka dokument och använda icke klustrade index, columnstore-index eller minnesoptimerade tabeller för att optimera dina frågor.
- [Med rumsliga funktioner](#spatial-features) kan du lagra geografiska och geometriska data, indexera dem med hjälp av rumsliga index och hämta data med hjälp av rumsliga frågor.
- [Med XML-funktioner](#xml-features) kan du lagra och indexera XML-data i databasen och använda inbyggda XQuery/XPath-åtgärder för att arbeta med XML-data. Azure SQL-databasen har specialiserad inbyggd XML-frågemotor som bearbetar XML-data.
- [Nyckelvärdespar](#key-value-pairs) stöds inte uttryckligen som specialfunktioner eftersom nyckelvärdespar kan modelleras som tabeller med två kolumner.

  > [!Note]
  > Du kan använda JSON Path-uttryck, XQuery/XPath-uttryck, rumsliga funktioner och diagramfrågeuttryck i samma Transact-SQL-fråga för att komma åt alla data som du har lagrat i databasen. Alla verktyg eller programmeringsspråk som kan köra Transact-SQL-frågor kan också använda frågegränssnittet för att komma åt data med flera modeller. Det här är den viktigaste skillnaden jämfört med multimodelldatabaser som [Azure Cosmos DB](/azure/cosmos-db/) som tillhandahåller specialiserat API för olika datamodeller.

I följande avsnitt kan du lära dig mer om de viktigaste multimodellfunktionerna i Azures SQL Database.

## <a name="graph-features"></a>Diagramfunktioner

Azure SQL Database erbjuder grafdatabasfunktioner för att modellera många till många relationer i databasen. Ett diagram är en samling noder (eller hörn) och kanter (eller relationer). En nod representerar en entitet (till exempel en person eller en organisation) och en kant representerar en relation mellan de två noderna som den ansluter (till exempel gilla-markeringar eller vänner). Här är några funktioner som gör en grafdatabas unik:
- Kanter eller relationer är första klass entiteter i en diagramdatabas och kan ha attribut eller egenskaper associerade med dem.
- En enda kant kan flexibelt ansluta flera noder i en diagramdatabas.
- Du kan enkelt uttrycka mönstermatchning och navigeringsfrågor med flera hopp.
- Du kan uttrycka transitiv stängning och polymorfa frågor enkelt.

Grafrelationerna och graffrågefunktionerna är integrerade i Transact-SQL och får fördelarna med att använda SQL Server som grundläggande databashanteringssystem.
[Grafbearbetning](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) är den centrala sql server-databasmotorfunktionen, så du kan hitta mer information om graph-bearbetningen där.

### <a name="when-to-use-a-graph-capability"></a>När ska du använda en grafkapacitet

Det finns inget en grafdatabas kan uppnå, vilket inte kan uppnås med hjälp av en relationsdatabas. En diagramdatabas kan dock göra det enklare att uttrycka vissa frågor. Ditt beslut att välja det ena över det andra kan baseras på följande faktorer:

- Modellhierarkiska data där en nod kan ha flera överordnade, så HierarchyId kan inte användas
- Modellen har Ditt program har komplexa många-till-många-relationer; i takt med att programmet utvecklas läggs nya relationer till.
- Du måste analysera sammankopplade data och relationer.

## <a name="json-features"></a>JSON funktioner

Med Azure SQL Database kan du tolka och fråga data som representeras i [JSON-format (JavaScript](https://www.json.org/) Object Notation) och exportera relationsdata som JSON-text.

JSON är ett populärt dataformat som används för att utbyta data i moderna webb- och mobilappar. JSON används också för att lagra semistrukturerade data i loggfiler eller i NoSQL-databaser som [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Många REST-webbtjänster returnerar resultat som är formaterade som JSON-text eller accepterar data som är formaterade som JSON. De flesta Azure-tjänster som [Azure Cognitive Search,](https://azure.microsoft.com/services/search/) [Azure Storage](https://azure.microsoft.com/services/storage/)och [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) har REST-slutpunkter som returnerar eller använder JSON.

Med Azure SQL Database kan du enkelt arbeta med JSON-data och integrera databasen med moderna tjänster. Azure SQL Database innehåller följande funktioner för att arbeta med JSON-data:

![JSON-funktioner](./media/sql-database-json-features/image_1.png)

Om du har JSON-text kan du extrahera data från JSON eller kontrollera att JSON är korrekt formaterat med hjälp av de inbyggda funktionerna [JSON_VALUE,](https://msdn.microsoft.com/library/dn921898.aspx) [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)och [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Med [JSON_MODIFY-funktionen](https://msdn.microsoft.com/library/dn921892.aspx) kan du uppdatera värdet i JSON-texten. För mer avancerade frågor och analyser kan [OPENJSON-funktionen](https://msdn.microsoft.com/library/dn921885.aspx) omvandla en matris med JSON-objekt till en uppsättning rader. Alla SQL-frågor kan köras på den returnerade resultatuppsättningen. Slutligen finns det en [FOR JSON-sats](https://msdn.microsoft.com/library/dn921882.aspx) som låter dig formatera data som lagras i relationstabellerna som JSON-text.

Mer information finns i [Så här arbetar du med JSON-data i Azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) är kärnfunktionen SQL Server Database Engine, så du kan hitta mer information om JSON-funktionen där.

### <a name="when-to-use-a-json-capability"></a>När du ska använda en JSON-funktion

Dokumentmodeller kan användas i stället för relationsmodellerna i vissa specifika scenarier:
- Hög normalisering av schemat medför inte betydande fördelar eftersom du kommer åt alla fält av objekt samtidigt, eller om du aldrig uppdaterar normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten i dina frågor på grund av det stora antalet tabeller som du behöver gå med för att hämta data.
- Du arbetar med de program som internt använder JSON-dokument är kommunikations- eller datamodeller, och du vill inte introducera ytterligare lager som omvandlar relationsdata till JSON och vice versa.
- Du måste förenkla datamodellen genom att de-normalisera underordnade tabeller eller mönster för entitetsobjektvärde.
- Du måste läsa in eller exportera data som lagras i JSON-format utan något ytterligare verktyg som tolkar data.

## <a name="spatial-features"></a>Rumsliga funktioner

Rumsliga data representerar information om geometriska objekts fysiska placering och form. Dessa objekt kan vara punktplatser eller mer komplexa objekt som länder/regioner, vägar eller sjöar.

Azure SQL Database stöder två rumsliga datatyper - geometridatatypen och geografidatatypen.
- Geometritypen representerar data i ett euklidiskt (platt) koordinatsystem.
- Geografitypen representerar data i ett koordinerat koordinssystem.

Det finns ett antal rumsliga objekt som kan användas i Azure SQL-databas som [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon), etc.

Azure SQL Database innehåller också specialiserade [spatiala index](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) som kan användas för att förbättra prestanda för dina rumsliga frågor.

[Spatial support](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) är kärnfunktionen SQL Server Database Engine, så du kan hitta mer information om den rumsliga funktionen där.

## <a name="xml-features"></a>XML-funktioner

SQL Server är en kraftfull plattform för att utveckla avancerade program för halvstrukturerad datahantering. Stöd för XML är integrerat i alla komponenter i SQL Server och innehåller följande:

- Xml-datatypen. XML-värden kan lagras inbyggt i en xml-datatypskolumn som kan skrivas enligt en samling XML-scheman eller lämnas otypade. Du kan indexera XML-kolumnen.
- Möjligheten att ange en XQuery-fråga mot XML-data som lagras i kolumner och variabler av xml-typen. XQuery-funktioner kan användas i alla Transact-SQL-frågor som kommer åt alla datamodeller som du använder i databasen.
- Indexera alla element i XML-dokument automatiskt med [primärt XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) eller ange de exakta sökvägar som ska indexeras med [sekundärt XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET som tillåter massinläsning av XML-data.
- Omvandla relationsdata till XML-format.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) är den centrala sql server-databasmotorfunktionen, så du kan hitta mer information om XML-funktionen där.

### <a name="when-to-use-an-xml-capability"></a>När ska en XML-funktion användas

Dokumentmodeller kan användas i stället för relationsmodellerna i vissa specifika scenarier:
- Hög normalisering av schemat medför inte betydande fördelar eftersom du kommer åt alla fält av objekt samtidigt, eller om du aldrig uppdaterar normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten i dina frågor på grund av det stora antalet tabeller som du behöver gå med för att hämta data.
- Du arbetar med de program som använder XML-dokument som används internt är kommunikations- eller datamodeller, och du vill inte introducera ytterligare lager som omvandlar relationsdata till XML och vice versa.
- Du måste förenkla datamodellen genom att de-normalisera underordnade tabeller eller mönster för entitetsobjektvärde.
- Du måste läsa in eller exportera data som lagras i XML-format utan något ytterligare verktyg som tolkar data.

## <a name="key-value-pairs"></a>Nyckel/värde-par

Azure SQL Database har inga specialiserade typer eller strukturer som stöder nyckel-värde-par eftersom nyckelvärdestrukturer kan representeras internt som standardrelationstabeller:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Du kan anpassa den här nyckelvärdesstrukturen så att den passar dina behov utan några begränsningar. Som ett exempel kan värdet vara `nvarchar(max)` XML-dokument i stället för typ, `CHECK` om värdet är JSON-dokument kan du ange villkor som verifierar giltigheten för JSON-innehåll. Du kan placera valfritt antal värden relaterade till en nyckel i ytterligare kolumner, lägga till beräknade kolumner och index för att förenkla och optimera dataåtkomst, definiera tabellen som tabellen minne /optimerad schema för att få bättre prestanda, etc.

Se [hur BWin använder IN-Memory OLTP för att uppnå oöverträffad prestanda och skalning](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) för sina ASP.NET cachelagringslösning som uppnådde 1.200.000 batchar per sekund, som ett exempel på hur relationsmodell effektivt kan användas som nyckel-värde parlösning i praktiken.

## <a name="next-steps"></a>Nästa steg
Multimodellfunktioner i Azure SQL-databaser är också de grundläggande SQL Server Database Engine-funktionerna som delas mellan Azure SQL Database och SQL Server. Mer information om de här funktionerna finns på dokumentationssidorna för SQL Relations-databasen:

* [Bearbetning av diagram](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Rumsligt stöd](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-data](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
