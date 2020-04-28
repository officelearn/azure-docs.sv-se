---
title: Funktioner för flera modeller
description: Med Azure SQL Database kan du arbeta med flera data modeller i samma databas.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "73802821"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Funktioner i flera modeller i Azure SQL Database

Med flera modell databaser kan du lagra och arbeta med data som representeras i flera data format, till exempel Relations data, grafer, JSON/XML-dokument, nyckel/värde-par osv.

## <a name="when-to-use-multi-model-capabilities"></a>När du ska använda funktioner för flera modeller

Azure SQL Database är utformad för att fungera med den relations modell som ger bästa möjliga prestanda i de flesta fall för en rad generella program. Azure SQL Database är dock inte begränsat till endast Relations data. Azure SQL Database gör att du kan använda en mängd olika icke-relationella format som är nära integrerade i Relations modellen.
Du bör överväga att använda flera modell funktioner i Azure SQL Database i följande fall:
- Du har en del information eller strukturer som passar bättre för NoSQL-modeller och du inte vill använda separata NoSQL-databaser.
- En majoritet av dina data lämpar sig för Relations modeller och du behöver modellera vissa delar av dina data i NoSQL-format.
- Du vill använda Rich-SQL-språk för att fråga och analysera både Relations-och NoSQL data och integrera den med en rad olika verktyg och program som kan använda SQL-språket.
- Du vill använda databas funktioner som [minnes intern teknik](sql-database-in-memory.md) för att förbättra prestandan för din analys eller bearbetning av dina NoSQL data strukturer [, använda Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) eller [läsbara repliker](sql-database-read-scale-out.md) för att skapa en kopia av dina data på den andra platsen och avlasta vissa analytiska arbets belastningar från den primära databasen.

## <a name="overview"></a>Översikt

Azure SQL innehåller följande funktioner för flera modeller:
- Med [Graph-funktioner](#graph-features) kan du Visa dina data som en uppsättning noder och kanter och använda standard Transact-SQL-frågor som har `MATCH` förbättrats med diagram operator för att fråga graf-data.
- Med [JSON-funktioner](#json-features) kan du använda JSON-dokument i tabeller, transformera Relations data till JSON-dokument och vice versa. Du kan använda standard språket för Transact-SQL med JSON-funktioner för att parsa dokument och använda icke-grupperade index, columnstore-index eller minnesoptimerade tabeller för att optimera dina frågor.
- Med [spatiala funktioner](#spatial-features) kan du lagra geografiska och geometriska data, indexera dem med hjälp av rums index och hämta data med hjälp av spatiala frågor.
- Med [XML-funktioner](#xml-features) kan du lagra och indexera XML-data i databasen och använda inbyggda XQuery/XPath-åtgärder för att arbeta med XML-data. Azure SQL Database har specialiserat inbyggd XML-frågemotor som bearbetar XML-data.
- [Nyckel/värde-par](#key-value-pairs) stöds inte uttryckligen som särskilda funktioner eftersom nyckel/värde-par kan modelleras internt som två kolumn tabeller.

  > [!Note]
  > Du kan använda JSON Path-uttryck, XQuery/XPath-uttryck, spatiala funktioner och Graph-frågeuttryck i samma Transact-SQL-fråga för att få åtkomst till alla data som du har lagrat i databasen. Dessutom kan alla verktyg eller programmeringsspråk som kan köra Transact-SQL-frågor även använda det Query-gränssnittet för att få åtkomst till data i flera modeller. Detta är den viktigaste skillnaden jämfört med databaser med flera modeller, till exempel [Azure Cosmos DB](/azure/cosmos-db/) som tillhandahåller specialiserat API för olika data modeller.

I följande avsnitt får du lära dig mer om de viktigaste funktionerna i flera modeller i Azure SQL Database.

## <a name="graph-features"></a>Diagramfunktioner

Azure SQL Database ger diagram över flera-till-många-relationer i databasen. Ett diagram är en samling av noder (eller hörn) och kanter (eller relationer). En nod representerar en entitet (till exempel en person eller en organisation) och en kant representerar en relation mellan de två noder som den ansluter till (till exempel gillar eller vänner). Här följer några funktioner som gör en graf-databas unik:
- Kanter eller relationer är första klass enheter i en graf-databas och kan ha attribut eller egenskaper som är kopplade till dem.
- En enda gräns kan ansluta flera noder i en diagram databas på ett flexibelt sätt.
- Du kan enkelt matcha mönster matchning och navigerings frågor med flera hopp.
- Du kan enkelt uttrycka transitiva och polymorfa frågor.

Graf-och Graph-funktionerna är integrerade i Transact-SQL och tar emot fördelarna med att använda SQL Server som grundläggande databas hanterings system.
[Diagram bearbetning](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) är den viktigaste SQL Server databas motor funktionen, så att du kan hitta mer information om grafen där.

### <a name="when-to-use-a-graph-capability"></a>När du ska använda en diagram funktion

Det finns ingen graf-databas som kan uppnås med hjälp av en Relations databas. En graf-databas kan dock göra det enklare att uttrycka vissa frågor. Ditt beslut om att välja ett över det andra kan baseras på följande faktorer:

- Modellera hierarkiska data där en nod kan ha flera överordnade, så HierarchyId kan inte användas
- Modellen har ett komplext antal-till-många-relationer i programmet. När programmet utvecklas läggs nya relationer till.
- Du måste analysera sammankopplade data och relationer.

## <a name="json-features"></a>JSON-funktioner

Med Azure SQL Database kan du analysera och fråga data som representeras i JavaScript Object Notation [(JSON)](https://www.json.org/) -format och exportera Relations data som JSON-text.

JSON är ett populärt data format som används för att utbyta data i moderna webb-och mobil program. JSON används också för att lagra halv strukturerade data i loggfiler eller i NoSQL-databaser som [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Många REST-webb tjänster returnerar resultat formaterade som JSON-text eller accepterar data som formaterats som JSON. De flesta Azure-tjänster som [azure kognitiv sökning](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)och [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) har rest-slutpunkter som returnerar eller använder JSON.

Med Azure SQL Database kan du enkelt arbeta med JSON-data och integrera databasen med moderna tjänster. Azure SQL Database innehåller följande funktioner för att arbeta med JSON-data:

![JSON-funktioner](./media/sql-database-json-features/image_1.png)

Om du har JSON-text kan du extrahera data från JSON eller kontrol lera att JSON är korrekt formaterad genom att använda de inbyggda funktionerna [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)och [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Med funktionen [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) kan du uppdatera värdet i JSON-text. För mer avancerade frågor och analyser kan [openjson](https://msdn.microsoft.com/library/dn921885.aspx) -funktionen omvandla en matris med JSON-objekt till en uppsättning rader. Alla SQL-frågor kan köras i den returnerade resultat uppsättningen. Slutligen finns det en [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) -sats som gör att du kan formatera data som lagras i dina Relations tabeller som JSON-text.

Mer information finns i [så här arbetar du med JSON-data i azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) är en Core SQL Server databas motor funktion, så att du kan hitta mer information om JSON-funktionen där.

### <a name="when-to-use-a-json-capability"></a>När du ska använda en JSON-funktion

Dokument modeller kan användas i stället för Relations modeller i vissa olika scenarier:
- Hög normalisering av schemat ger inte några betydande fördelar eftersom du får åtkomst till alla fält för objekt på en gång, eller om du aldrig uppdaterar normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten för dina frågor på grund av det stora antalet tabeller som du måste koppla för att hämta data.
- Du arbetar med de program som ursprungligen använder JSON-dokument är kommunikations-eller data modeller, och du vill inte införa fler lager som transformerar Relations data till JSON och vice versa.
- Du behöver förenkla din data modell genom att normalisera de underordnade tabellerna eller entitetens objekt värde mönster.
- Du måste läsa in eller exportera data som lagras i JSON-format utan ytterligare verktyg som analyserar data.

## <a name="spatial-features"></a>Spatiala funktioner

Spatialdata representerar information om den fysiska platsen och formen på geometriska objekt. Dessa objekt kan vara plats platser eller mer komplexa objekt, till exempel länder/regioner, vägar eller sjöar.

Azure SQL Database stöder två typer av spatialdata – data typen Geometry och data typen geografi.
- Geometri typen representerar data i ett Euclidean (flat)-koordinatsystem.
- Geografi typen representerar data i ett system för Round-jordens koordinater.

Det finns ett antal spatiala objekt som kan användas i Azure SQL Database, till exempel [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [lin Est ring](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)osv.

Azure SQL Database tillhandahåller också särskilda [rums index](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) som kan användas för att förbättra prestandan för dina spatiala frågor.

[Spatial support](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) är Core SQL Server Database Engine-funktion, så att du kan hitta mer information om den spatiala funktionen där.

## <a name="xml-features"></a>XML-funktioner

SQL Server är en kraftfull plattform för att utveckla omfattande program för en delvis strukturerad data hantering. Stöd för XML är integrerat i alla komponenter i SQL Server och innehåller följande:

- Data typen XML. XML-värden kan lagras internt i en kolumn av typen XML-datatyp som kan skrivas i enlighet med en samling XML-scheman eller lämnas utan Skriv. Du kan indexera XML-kolumnen.
- Möjlighet att ange en XQuery-fråga mot XML-data som lagras i kolumner och variabler i XML-typen. XQuery-funktioner kan användas i alla Transact-SQL-frågor som har åtkomst till alla data modeller som du använder i databasen.
- Indexera automatiskt alla element i XML-dokument med hjälp av [primärt XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) eller ange exakta sökvägar som ska indexeras med [sekundärt XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OpenRowSet som tillåter Mass inläsning av XML-data.
- Transformera Relations data till XML-format.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) är en Core SQL Server databas motor funktion, så att du kan hitta mer information om XML-funktionen där.

### <a name="when-to-use-an-xml-capability"></a>När du ska använda en XML-funktion

Dokument modeller kan användas i stället för Relations modeller i vissa olika scenarier:
- Hög normalisering av schemat ger inte några betydande fördelar eftersom du får åtkomst till alla fält för objekt på en gång, eller om du aldrig uppdaterar normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten för dina frågor på grund av det stora antalet tabeller som du måste koppla för att hämta data.
- Du arbetar med de program som ursprungligen använder XML-dokument är kommunikations-eller data modeller, och du vill inte införa fler lager som transformerar Relations data till XML och vice versa.
- Du behöver förenkla din data modell genom att normalisera de underordnade tabellerna eller entitetens objekt värde mönster.
- Du måste läsa in eller exportera data som lagras i XML-format utan ytterligare verktyg som analyserar data.

## <a name="key-value-pairs"></a>Nyckel/värde-par

Azure SQL Database inte har specialiserade typer eller strukturer som stöder nyckel/värde-par eftersom nyckel värdes strukturer kan visas internt som standard Relations tabeller:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Du kan anpassa den här nyckel värdes strukturen så att den passar dina behov utan begränsningar. Värdet kan till exempel vara XML-dokument i stället för `nvarchar(max)` typ, om värdet är JSON-dokument, kan du ange en `CHECK` begränsning som verifierar att JSON-innehåll är giltigt. Du kan placera ett valfritt antal värden som är relaterade till en nyckel i de ytterligare kolumnerna, lägga till beräknade kolumner och index för att förenkla och optimera data åtkomst, definiera tabellen som en minnes/optimerad schema tabell för att få bättre prestanda osv.

Se [hur bwin använder minnes intern OLTP för att uppnå oöverträffade prestanda och skalning](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) för sin ASP.net caching-lösning som nådde 1.200.000 batchar per sekund, som ett exempel på hur Relations modellen kan användas som nyckel värdes par lösning i praxis.

## <a name="next-steps"></a>Nästa steg
Funktioner för flera modeller i Azure SQL-databaser är också de viktigaste SQL Server databas motor funktioner som delas mellan Azure SQL Database och SQL Server. Mer information om dessa funktioner finns i dokumentations sidorna för SQL Relations databas:

* [Diagram bearbetning](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Spatial support](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-data](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
