---
title: Funktioner i Azure SQL Database Multi-modell | Microsoft Docs
description: Azure SQL Database kan du arbeta med flera datamodeller i samma databas.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: 84efdb0297a2dc69497baee5fb746fb51d02b1b7
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939522"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Flera datamodeller funktionerna i Azure SQL Database

Databaser för flera datamodeller kan du lagra och arbeta med data som visas i flera dataformat som relationsdata, diagram, JSON/XML-dokument, nyckel / värde-par, osv.

## <a name="when-to-use-multi-model-capabilities"></a>När du ska använda funktioner för flera datamodeller

Azure SQL Database har utformats för att arbeta med relationsmodellen som tillhandahåller bästa möjliga prestanda i de flesta fall för en mängd olika Allmänt program. Azure SQL Database är dock inte begränsat till – relationsdata endast. Azure SQL Database kan du använda en mängd icke-relationella format som är nära integrerade i relationsmodellen.
Du bör överväga att använda funktionerna för flera modeller i Azure SQL Database i följande fall:
- Du har viss information eller strukturer som bättre passa för NoSQL-modeller och du vill inte använda separata NoSQL-databas.
- En majoritet av dina data är lämpligt för relationsmodellen och behöver du skapa vissa delar av dina data med NoSQL-stil.
- Du vill dra nytta av omfattande Transact-SQL-språket att fråga efter och analysera både Relations- och NoSQL-data och integrera det med en mängd olika verktyg och program som kan använda SQL-språket.
- Du vill tillämpa databasfunktioner som [minnesinterna tekniker](sql-database-in-memory.md) att förbättra prestanda för din analys eller använda bearbetning av dina data strucutres NoSQL [Transaktionsreplikering](sql-database-managed-instance-transactional-replication.md) eller [läsbara repliker](sql-database-read-scale-out.md) att skapa kopia av dina data på annan plats och avlasta vissa analytiska arbetsbelastningar från den primära databasen.

## <a name="overview"></a>Översikt

Azure SQL innehåller följande funktioner för flera modeller:
- [Funktioner för Graph](#graph-features) gör att du kan visa data som uppsättning noder och kanter och använda standard Transact-SQL-frågor, förbättrad med graph `MATCH` operator för att fråga graph-data.
- [JSON-funktioner](#json-features) gör att du kan placera JSON-dokument i tabeller, omvandla relationella data till JSON-dokument och vice versa. Du kan använda standard Transact-SQL-språket, förbättrad med JSON-funktioner för parsning av dokument och använda icke-grupperade index eller kolumnlagringsindex minnesoptimerade tabeller för att optimera dina frågor.
- [Spatial funktioner](#spatial-features) kan du lagra geografiska och geometriska, index-dem med hjälp av spatialindex och hämta data med rumsliga förfrågningar.
- [XML-funktioner](#xml-features) gör det möjligt att lagra och indexera XML-data i din databas och använda inbyggda XQuery/XPath-åtgärder för att arbeta med XML-data. Azure SQL-databas har specialiserade inbyggda XML-frågemotor som bearbeta XML-data.
- [Nyckel / värde-par](#key-value-pairs) uttryckligen stöds inte som specialfunktioner eftersom nyckel / värde-paris modelleras internt som tabeller i två kolumner.

  > [!Note]
  > Du kan använda JSON-sökvägsuttrycket, XQuery/XPath-uttryck, spatial funktioner och graph-frågeuttryck i samma Transact-SQL-fråga för att komma åt alla data som du har lagrat i databasen. Alla verktyg och programmeringsspråk som kan köra Transact-SQL-frågor kan dessutom också att använda det-gränssnittet på dataåtkomst för flera datamodeller. Det här är den viktigaste skillnaden jämfört med databaser för flera datamodeller som [Azure Cosmos DB](/azure/cosmos-db/) som tillhandahåller specialiserade API för olika datamodeller.

I följande avsnitt kommer du lära dig om de viktigaste flermodells funktionerna i Azures SQL-databas.

## <a name="graph-features"></a>Diagramfunktioner

Azure SQL Database erbjuder graph-databasen till modellen många-till-många-relationer i databasen. Ett diagram är en samling noder (eller hörn) och kanter (eller relationer). En nod representerar en entitet (till exempel en person eller en organisation) och en gräns representerar en relation mellan de två noderna som den ansluter (till exempel, likes eller vänner). Här följer några funktioner som gör en grafdatabas unik:
- Kanter eller relationer är första klassens entiteter i en Grafdatabas och kan ha egenskaper eller attribut som är associerade med dem.
- En enda edge kan ett flexibelt sätt att ansluta flera noder i en Grafdatabas.
- Du kan uttrycka mönstermatchning och multihoppsnavigering frågor enkelt.
- Du kan uttrycka transitiv kommer att avslutas och polymorfisk frågor enkelt.

Graph relationer och graph-frågefunktioner är integrerade i Transact-SQL och få fördelarna med att använda SQL Server som grundläggande databashanteringssystemet.
[Bearbeta diagram](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) är funktionen core SQL Server Database Engine, så att du kan hitta mer information om diagrammet bearbetning av det.

### <a name="when-to-use-a-graph-capability"></a>När du ska använda en graph-funktion

Det finns inget en grafdatabas kan uppnå som inte kan tillgodoses med en relationsdatabas. En grafdatabas kan dock gör det enklare att uttrycka vissa frågor. Bestämma dig för att välja ett av alternativen kan baseras på följande faktorer:

- Modellen hierarkiska data där en nod kan ha flera överordnade så HierarchyId inte kan användas
- Modellen har ditt program har komplexa många-till-många-relationer; allteftersom programmet utvecklas, läggs nya relationer.
- Du behöver analysera sammankopplade data och relationer.

## <a name="json-features"></a>JSON-funktioner

Azure SQL Database kan du parsa och skicka frågor till data som visas i JavaScript Object Notation [(JSON)](https://www.json.org/) formatera och exportera din relationsdata som JSON-texten.

JSON är ett populärt dataformat som används för utbyte av data i moderna webbprogram och mobilappar. JSON används också för att lagra halvstrukturerade data i loggfiler eller i NoSQL-databaser som [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Många REST-webbtjänsterna sökresultaten formaterade som JSON-texten eller acceptera data formaterade som JSON. De flesta Azure-tjänster som [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/), och [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) har REST-slutpunkter som returnerar eller använda JSON.

Azure SQL Database kan du enkelt arbeta med JSON-data och få din databas med moderna tjänster. Azure SQL Database innehåller följande funktioner för att arbeta med JSON-data:

![JSON-funktioner](./media/sql-database-json-features/image_1.png)

Om du har JSON-text, du kan extrahera data från JSON eller kontrollera att JSON är korrekt formaterat med hjälp av de inbyggda funktionerna [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), och [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Den [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) funktionen låter dig uppdatera värdet i JSON-texten. För mer avancerade frågor och analys, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funktion kan omvandla en matris av JSON-objekt till en uppsättning rader. Alla SQL-frågor kan köras på den returnerade resultatuppsättningen. Slutligen finns en [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) satsen där du kan formatera data som lagras i din Relationstabeller som JSON-text.

Mer information finns i [hur du arbetar med JSON-data i azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) är grundläggande SQL Server Database Engine-funktion, så att du kan hitta mer information om JSON-funktionen det.

### <a name="when-to-use-a-json-capability"></a>När du ska använda en JSON-funktion

Dokumentet modeller kan användas i stället för relationella modeller i vissa specifika scenarier:
- Hög-normalisering av schemat inte ger dig betydande fördelar eftersom du åtkomst till alla fält med objekt på samma gång, eller så uppdatera aldrig normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten i dina frågor på grund av det stora antalet tabeller som du behöver för att ansluta till för att hämta data.
- Du arbetar med de program som internt Använd JSON-dokument är kommunikation eller datamodeller och du inte vill införa ytterligare lager som omvandlar relationella data till JSON och vice versa.
- Måste du förenkla din datamodell genom att ta bort normalisering underordnade tabellerna eller entitet Objektvärde mönster.
- Du behöver läsa in eller exportera data som lagras i JSON-format utan några ytterligare verktyg som analyserar data.

## <a name="spatial-features"></a>Spatial funktioner

Spatialdata representerar information om fysisk plats och formen på geometriska objekt. De här objekten kan vara platser eller mer komplexa objekt som länder/regioner, vägar eller sjöar.

Azure SQL Database stöder två rumsliga datatyper - datatypen geometri och geografidata.
- Typen geometri representerar data i ett Euclidean (fast) koordinatsystem.
- Typen geografi representerar data i en resursallokering earth koordinatsystem.

Det finns ett antal Spatial objekt som kan användas i Azure SQL database som [punkt](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Polygon](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)osv.

Azure SQL Database innehåller också specialiserade [spatialindex](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) som kan användas för att förbättra prestanda för dina rumsliga förfrågningar.

[Stöd för spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) är core SQL Server Database Engine-funktion, så att du kan hitta mer information om den spatial funktionen.

## <a name="xml-features"></a>XML-funktioner

SQL Server är en kraftfull plattform för utveckling av omfattande program för hantering av halvstrukturerade data. Stöd för XML är integrerad i alla komponenter i SQL Server och omfattar följande:

- Xml-datatypen. XML-värden kan lagras i en kolumn av XML-data-typ som kan skrivits enligt en uppsättning XML-scheman eller vänster frågans internt. Du kan indexera XML-kolumnen.
- Möjligheten att ange en XQuery-fråga mot XML-data som lagras i kolumner och variabler av typen xml. XQuery-funktioner kan användas i en Transact-SQL-fråga som har åtkomst till någon datamodell som du använder i din databas.
- Automatiskt indexerar alla element i XML-dokument med hjälp av [primärt XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) eller ange de exakta sökvägar som ska indexeras med [sekundärt XML-index](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET som tillåter massinläsning av XML-data.
- Omvandla relationsdata i XML-format.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) är core SQL Server Database Engine-funktion, så att du kan hitta mer information om det XML-funktionen.

### <a name="when-to-use-an-xml-capability"></a>När du ska använda en XML-funktion

Dokumentet modeller kan användas i stället för relationella modeller i vissa specifika scenarier:
- Hög-normalisering av schemat inte ger dig betydande fördelar eftersom du åtkomst till alla fält med objekt på samma gång, eller så uppdatera aldrig normaliserade delar av objekten. Den normaliserade modellen ökar dock komplexiteten i dina frågor på grund av det stora antalet tabeller som du behöver för att ansluta till för att hämta data.
- Du arbetar med de program som internt Använd XML-dokument är kommunikation eller datamodeller och du inte vill införa ytterligare lager som omvandlar relationella data till XML och vice versa.
- Måste du förenkla din datamodell genom att ta bort normalisering underordnade tabellerna eller entitet Objektvärde mönster.
- Du behöver läsa in eller exportera data som lagras i XML-format utan några ytterligare verktyg som analyserar data.

## <a name="key-value-pairs"></a>Nyckel/värde-par

Azure SQL Database har inte särskilda typer eller strukturer som har stöd för nyckel / värde-par eftersom nyckel / värde-strukturer kan internt visas som standard Relationstabeller:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Du kan anpassa den här nyckel / värde-strukturen så att de passar dina behov utan några begränsningar. Till exempel värdet kan vara XML-dokument i stället för `nvarchar(max)` typ, om värdet är JSON-dokument och du kan placera `CHECK` villkor som kontrollerar giltigheten för JSON-innehåll. Du kan placera valfritt antal värden som är relaterade till en nyckel i fler kolumner, lägga till beräknade kolumner och index för att förenkla och optimera dataåtkomst, definiera tabellen som minnesoptimerade/endast schema tabell för att få bättre prestanda, osv.

Se [hur BWin använder Minnesintern OLTP för att uppnå en oöverträffad prestanda och skalning](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) för sina cachelagring av ASP.NET-lösning som uppnås 1.200.000 slår ihop per sekunder, som exempel hur relationella modell kan användas som nyckel / värde-par lösning i praktiken.

## <a name="next-steps"></a>Nästa steg
Flera datamodeller funktioner i Azure SQL-databaser är även SQL Server Database Engine huvudfunktioner som delas mellan Azure SQL Database och SQL Server. Om du vill veta mer om dessa funktioner finns i SQL-relationsdatabas dokumentationssidorna för databasen:

* [Bearbeta diagram](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON-data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Spatial support](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML-data](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
