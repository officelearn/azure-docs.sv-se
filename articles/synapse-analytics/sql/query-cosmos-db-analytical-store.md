---
title: Fråga Azure Cosmos DB data med Server lös SQL-pool i Azure Synapse Link (för hands version)
description: I den här artikeln får du lära dig hur du frågar Azure Cosmos DB att använda Server lös SQL-pool i Azure Synapse Link (för hands version).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 9f57d435134bffbb8e7576adffeacb92bf687124
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310309"
---
# <a name="query-azure-cosmos-db-data-with-serverless-sql-pool-in-azure-synapse-link-preview"></a>Fråga Azure Cosmos DB data med Server lös SQL-pool i Azure Synapse Link (för hands version)

Med Synapse server utan SQL-pool kan du analysera data i Azure Cosmos DB behållare som är aktiverade med [Azure Synapse-länk](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) i nära real tid utan att påverka prestandan för dina transaktions arbets belastningar. Den erbjuder en välkänd T-SQL-syntax för att fråga data från [analys lagret](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) och integrerad anslutning till en mängd olika bi-och ad hoc-frågemeddelanden via T-SQL-gränssnittet.

För att skicka frågor till Azure Cosmos DB stöds det fullständiga [Select](/sql/t-sql/queries/select-transact-sql?view=sql-server-ver15) -området via funktionen [OpenRowSet](develop-openrowset.md) , inklusive majoriteten av [SQL Functions och operatorer](overview-features.md). Du kan också lagra resultat från frågan som läser data från Azure Cosmos DB tillsammans med data i Azure Blob Storage eller Azure Data Lake Storage med hjälp av [skapa extern tabell som Välj](develop-tables-cetas.md#cetas-in-serverless-sql-pool). För närvarande kan du inte lagra frågeresultat för SQL-pooler för att Azure Cosmos DB med CETAS. 

I den här artikeln får du lära dig hur du skriver en fråga med en server lös SQL-pool som kommer att fråga efter data från Azure Cosmos DB behållare som är Synapse-länk aktiverade. Du kan sedan lära dig mer om hur du skapar SQL-pooler utan server över Azure Cosmos DB behållare och ansluter dem till Power BI modeller i [den här](./tutorial-data-analyst.md) självstudien. 

> [!IMPORTANT]
> I den här självstudien används en behållare med [Azure Cosmos DB väldefinierat schema](../../cosmos-db/analytical-store-introduction.md#schema-representation). Frågan som server lös SQL-poolen tillhandahåller för [Azure Cosmos DB full Fidelity schema](#full-fidelity-schema) är ett tillfälligt beteende som ändras baserat på förhands granskningen. Förlita dig inte på schemat för resultat uppsättningen för `OPENROWSET` Function utan `WITH` sats som läser data från en behållare med fullständigt åter användnings schema eftersom frågekörning kan ändras och justeras med väldefinierat schema. Publicera feedback om [Azure Synapse Analytics feedback-forumet](https://feedback.azure.com/forums/307516-azure-synapse-analytics) eller kontakta [Synapse länka produkt teamet](mailto:cosmosdbsynapselink@microsoft.com) för att ge feedback.

## <a name="overview"></a>Översikt

För att stödja frågor och analys av data i Azure Cosmos DB analys lager, använder SQL-poolen utan server följande `OPENROWSET` syntax:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

Anslutnings strängen Azure Cosmos DB anger Azure Cosmos DB konto namnet, databas namnet, databas kontots huvud nyckel och ett valfritt region namn som ska `OPENROWSET` fungera. 

> [!IMPORTANT]
> Se till att du använder alias efter `OPENROWSET` . Det finns ett [känt problem](#known-issues) som orsakar anslutnings problem till Synapse-server utan SQL-slutpunkt om du inte anger aliaset efter `OPENROWSET` funktion.

Anslutnings strängen har följande format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Namnet på Azure Cosmos DB containern anges utan citat tecken i `OPENROWSET` syntaxen. Om behållar namnet innehåller specialtecken (till exempel ett bindestreck "-"), ska namnet omslutas inom `[]` (hakparenteser) i `OPENROWSET` syntaxen.

> [!NOTE]
> SQL-poolen utan Server stöder inte frågor om Azure Cosmos DB transaktions lager.

## <a name="sample-data-set"></a>Exempel på datauppsättning

Exemplen i den här artikeln baseras på data från [Europeiska centrum för sjukdoms förebyggande och kontroll (ECDC) COVID-19 fall](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) och [COVID-19 Open Research data uppsättning (kabel-19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Du kan se licensen och strukturen för data på dessa sidor och hämta exempel data för [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) -och [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) -datauppsättningar.

Om du vill följa med i den här artikeln som demonstrerar hur du frågar Cosmos DB data med en server lös SQL-pool kontrollerar du att du skapar följande resurser:
* Ett Azure Cosmos DB databas konto som är [Synapse-länk aktiverat](../../cosmos-db/configure-synapse-link.md)
* En Azure Cosmos DB databas med namnet `covid`
* Två Azure Cosmos DB behållare med namnet `EcdcCases` och `Cord19` med ovanstående exempel data uppsättningar lästes in.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Utforska Azure Cosmos DB data med automatisk schema härledning

Det enklaste sättet att utforska data i Azure Cosmos DB är genom att dra nytta av funktionen för automatisk schema härledning. Genom att utelämna- `WITH` satsen från `OPENROWSET` instruktionen kan du instruera Server lös SQL-poolen att automatiskt identifiera (härleda) schemat för den Azure Cosmos DB behållarens analys arkiv.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
I ovanstående exempel instruerar vi SQL-poolen utan server att ansluta till `covid` databasen i Azure Cosmos DB konto som `MyCosmosDbAccount` autentiserats med hjälp av Azure Cosmos DBS nyckeln (dummy i exemplet ovan). Vi ansluter sedan till behållarens `EcdcCases` analys lager i `West US 2` regionen. Eftersom ingen projektion av vissa egenskaper visas, `OPENROWSET` returnerar funktionen alla egenskaper från Azure Cosmos DB objekt. 

Under förutsättning att objekten i Cosmos DB container har `date_rep` , `cases` , och `geo_id` egenskaper, visas resultatet av den här frågan i följande tabell:

| date_rep | fall | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Om du behöver utforska data från den andra behållaren i samma Azure Cosmos DB databas kan du använda samma anslutnings sträng och referens som krävs för behållare som tredje parameter:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Ange ett schema explicit

När den automatiska schema utlösaren i `OPENROWSET` tillhandahåller en enkel, lättanvänd querience kan dina affärs scenarier kräva att du uttryckligen anger schemat till skrivskyddade relevanta egenskaper från Azure Cosmos db data.

`OPENROWSET` med kan du uttryckligen ange vilka egenskaper du vill läsa från data i behållaren och ange deras data typer. Anta att vi har importerat några data från [ECDC COVID data uppsättning](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) med följande struktur till Azure Cosmos DB:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Dessa enkla JSON-dokument i Azure Cosmos DB kan representeras som en uppsättning rader och kolumner i Synapse SQL. `OPENROWSET` funktionen gör att du kan ange en delmängd av de egenskaper som du vill läsa och de exakta kolumn typerna i- `WITH` satsen:

```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Resultatet av den här frågan kan se ut så här:

| date_rep | fall | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Se [reglerna för SQL Type-mappningar](#azure-cosmos-db-to-sql-type-mappings) i slutet av artikeln för mer information om de SQL-typer som ska användas för Azure Cosmos DB värde.

## <a name="querying-nested-objects-and-arrays"></a>Fråga efter kapslade objekt och matriser

Azure Cosmos DB gör att du kan representera mer komplexa data modeller genom att skriva dem som kapslade objekt eller matriser. Funktionen för automatisk synkronisering av Synapse för Azure Cosmos DB hanterar schema representationen i den analytiska lagrings platsen, som omfattar hantering av kapslade data typer som tillåter omfattande frågor från SQL-poolen utan server.

Till exempel innehåller [kabel-19-](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) data uppsättningen JSON-dokument efter följande struktur:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

De kapslade objekten och matriserna i Azure Cosmos DB representeras som JSON-strängar i frågeresultatet när `OPENROWSET` funktionen läser dem. Ett alternativ för att läsa värdena från dessa komplexa typer som SQL-kolumner använder SQL JSON-funktioner:

```sql
SELECT
    title = JSON_VALUE(metadata, '$.title'),
    authors = JSON_QUERY(metadata, '$.authors'),
    first_author_name = JSON_VALUE(metadata, '$.authors[0].first')
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( metadata varchar(MAX) ) AS docs;
```

Resultatet av den här frågan kan se ut så här:

| title | författaren | first_autor_name |
| --- | --- | --- |
| Extra information ett eko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien |  

Som alternativ kan du också ange sökvägar till kapslade värden i objekten när du använder- `WITH` sats:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    WITH ( title varchar(1000) '$.metadata.title',
           authors varchar(max) '$.metadata.authors'
    ) AS docs;
```

Lär dig mer om att analysera [komplexa data typer i Synapse-länk](../how-to-analyze-complex-schema.md) och [kapslade strukturer i SQL-poolen utan server](query-parquet-nested-types.md).

> [!IMPORTANT]
> Om du ser oväntade tecken i din text `MÃƒÂ©lade` , t. ex. i stället för `Mélade` databas sorteringen, är inte inställd på [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Ändra sorteringen av databasen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) till viss utf8-sortering med en viss SQL-instruktion som `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flattening-nested-arrays"></a>Förenkla utkapslade matriser

Azure Cosmos DB data kan ha kapslade undermatriser som författarens matris från [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) data uppsättning:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

I vissa fall kan du behöva "koppla" egenskaperna från det översta objektet (metadata) med alla element i matrisen (författarna). Med Server lös SQL-pool kan du förenkla inkapslade strukturer genom att använda `OPENJSON` funktionen på den kapslade matrisen:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

Resultatet av den här frågan kan se ut så här:

| title | författaren | förstagångskörningen | pågå | anknytning |
| --- | --- | --- | --- | --- |
| Extra information ett eko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Extra information ett eko-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Extra information ett eko-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Beza | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Extra information ett eko-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Om du ser oväntade tecken i din text `MÃƒÂ©lade` , t. ex. i stället för `Mélade` databas sorteringen, är inte inställd på [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Ändra sorteringen av databasen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) till viss utf8-sortering med en viss SQL-instruktion som `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB mappningar av SQL-typ

Det är viktigt att först Observera att när Azure Cosmos DB transaktions lager är schema-oberoende, är analys lagret schematiserade för att optimera prestandan för analytiska frågor. Med funktionen för automatisk synkronisering av Synapse, hanterar Azure Cosmos DB schema representationen i den analytiska lagrings platsen som omfattar hantering av kapslade data typer. Eftersom SQL-poolen utan server frågar analys lagret är det viktigt att du förstår hur du mappar Azure Cosmos DB indata-typer till SQL-datatyper.

Azure Cosmos DB konton av SQL-API (Core) stöder JSON-egenskapsvärde av typen Number, String, Boolean, null, nested Object eller array. Du måste välja SQL-typer som matchar dessa JSON-typer om du använder- `WITH` satsen i `OPENROWSET` . Se under de SQL-kolumn typer som ska användas för olika egenskaps typer i Azure Cosmos DB.

| Azure Cosmos DB egenskaps typ | SQL-kolumn typ |
| --- | --- |
| Boolesk | bit |
| Integer | bigint |
| Decimal | flyt |
| Sträng | varchar (UTF8-databas sortering) |
| Datum tid (ISO-formaterad sträng) | varchar (30) |
| Datum tid (Unix-tidsstämpel) | bigint |
| Null | `any SQL type` 
| Kapslat objekt eller matris | varchar (max) (sortering av UTF8-databas), serialiserad som JSON-text |

## <a name="full-fidelity-schema"></a>Schema för fullständig åter givning

Azure Cosmos DB full Fidelity schema registrerar både värden och deras bästa matchnings typer för varje egenskap i en behållare.
`OPENROWSET` funktionen på en behållare med full Fidelity schema ger både typ och faktiskt värde i varje cell. Vi antar att följande fråga läser objekten från en behållare med schemat med fullständig åter givning:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) as rows
```

Resultatet av den här frågan returnerar typer och värden formaterade som JSON-text: 

| date_rep | fall | geo_id |
| --- | --- | --- |
| {"datum": "2020-08-13"} | {"Int32": "254"} | {"sträng": "RS"} |
| {"datum": "2020-08-12"} | {"Int32": "235"}| {"sträng": "RS"} |
| {"datum": "2020-08-11"} | {"Int32": "316"} | {"sträng": "RS"} |
| {"datum": "2020-08-10"} | {"Int32": "281"} | {"sträng": "RS"} |
| {"datum": "2020-08-09"} | {"Int32": "295"} | {"sträng": "RS"} |
| {"sträng": "2020/08/08"} | {"Int32": "312"} | {"sträng": "RS"} |
| {"datum": "2020-08-07"} | {"float64":"339.0"} | {"sträng": "RS"} |

För varje värde kan du se vilken typ som identifieras i Cosmos DB container Item. De flesta värden för `date_rep` egenskapen innehåller `date` värden, men en del av dem lagras felaktigt som strängar i Cosmos dB. Schemat för fullständig åter givning returnerar både korrekt skrivna `date` värden och felaktigt formaterade `string` värden.
Antalet ärenden är en information som lagras som ett `int32` värde, men det finns ett värde som anges som decimal tal. Det här värdet är av `float64` typen. Om det finns några värden som överstiger det största `int32` antalet, lagras de som `int64` typ. Alla `geo_id` värden i det här exemplet lagras som- `string` typer.

> [!IMPORTANT]
> `OPENROWSET` function utan `WITH` sats visar båda värdena med förväntade typer och värden med felaktigt angivna typer. Den här funcion är utformad för data utforskning och inte för rapportering. Parsa inte JSON-värden som returneras från den här funktionen för att skapa rapporter och använda explicit [with-sats](#querying-items-with-full-fidelity-schema) för att skapa dina rapporter.
> Du bör rensa värdena som har felaktiga typer i Azure Cosmos DB container för att kunna tillämpa corection i ett analys lager med fullständig åter givning. 

Om du vill fråga Azure Cosmos DB konton i Mongo DB API-typ kan du lära dig mer om schema representationen full Fidelity i analys lagret och de utökade egenskaps namn som ska användas [här](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

### <a name="querying-items-with-full-fidelity-schema"></a>Fråga objekt med full Fidelity schema

Vid frågor om schemat för fullständig åter givning måste du uttryckligen ange SQL-typ och förväntad Cosmos DB egenskaps typ i `WITH` satsen. Använd inte `OPENROWSET` utan `WITH` sats i rapporterna eftersom formatet för resultat uppsättningen kan ändras i för hands versionen, baserat på feedback.

I följande exempel antar vi att `string` är rätt typ för `geo_id` egenskap och `int32` korrekt typ för `cases` egenskap:

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

Värden för `geo_id` och `cases` som har andra typer kommer att returneras som `NULL` värden. Den här frågan refererar endast `cases` till den angivna typen i uttrycket ( `cases.int32` ).

Om du har värden med andra typer ( `cases.int64` , `cases.float64` ) som inte kan rensas i Cosmos DB container, måste du uttryckligen referera till dem i `WITH` satsen och kombinera resultaten. Följande fråga aggregerar både `int32` , `int64` , och `float64` lagras i `cases` kolumnen:

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

I det här exemplet lagras antalet fall som `int32` , `int64` eller `float64` värden, och alla värden måste extraheras för att beräkna antalet ärenden per land. 

## <a name="known-issues"></a>Kända problem

- Alias **måste** anges efter `OPENROWSET` funktion (till exempel `OPENROWSET (...) AS function_alias` ). Att utelämna alias kan orsaka anslutnings problem och Synapse SQL-slutpunkt kan vara tillfälligt otillgängligt. Det här problemet kommer att lösas i nov 2020.
- Frågan fungerar som en server lös SQL-pool för [Azure Cosmos DB full Fidelity schema](#full-fidelity-schema) är ett tillfälligt beteende som ändras baserat på förhands gransknings feedback. Förlita dig inte på det schema som `OPENROWSET` fungerar utan `WITH` satsen under den offentliga för hands versionen eftersom frågeresultaten kan vara justerade med väldefinierat schema baserat på kundfeedback. Kontakta [Synapse länka produkt teamet](mailto:cosmosdbsynapselink@microsoft.com) för att ge feedback.

Möjliga fel och fel söknings åtgärder visas i följande tabell:

| Fel | Rotorsak |
| --- | --- |
| Syntaxfel:<br/> -Felaktig syntax nära OpenRowSet<br/> - `...` är inte ett känt alternativ för OpenRowSet-providern.<br/> -Felaktig syntax nära `...` | Möjliga rotor orsaker<br/> – Använder inte ' CosmosDB ' som första parameter,<br/> – Använder sträng litteral i stället för identifierare i den tredje parametern,<br/> -Ange inte tredje parameter (container namn) |
| Ett fel uppstod i CosmosDB-anslutningssträngen | -Konto, databas, nyckel har inte angetts <br/> -Det finns ett alternativ i anslutnings strängen som inte känns igen.<br/> -Semikolon `;` placeras i slutet av anslutnings strängen |
| Det gick inte att matcha CosmosDB-sökvägen med felet "felaktigt konto namn" eller "felaktigt databas namn" | Det angivna konto namnet, databas namnet eller behållaren kan inte hittas, eller så har ingen analytisk lagring Aktiver ATS o den angivna samlingen|
| Det gick inte att matcha CosmosDB Sök väg med felet "felaktigt hemligt värde" eller "hemlighet är null eller tomt" | Konto nyckeln är inte giltig eller saknas. |
| Kolumn `column name` av typen `type name` är inte kompatibel med den externa data typen `type name` | Den angivna kolumn typen i `WITH` satsen matchar inte typen i Cosmos DB containern. Försök att ändra kolumn typen enligt beskrivningen i avsnittet [Azure Cosmos dB till SQL-typ mappningar](#azure-cosmos-db-to-sql-type-mappings) eller användnings `VARCHAR` typ. |
| Kolumnen innehåller `NULL` värden i alla celler. | Eventuellt felaktigt kolumn namn eller Sök vägs uttryck i `WITH` satsen. Kolumn namn (eller Sök vägs uttryck efter kolumn typen) i `WITH` satsen måste matcha ett egenskaps namn i Cosmos DB samlingen. Jämförelse är **SKIFT**  läges känslig (till exempel `productCode` och `ProductCode` är olika egenskaper). |

Du kan rapportera förslag och problem på [feedback-sidan för Azure Synapse](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Använd Power BI-och Server lös SQL-pool med Azure Synapse-länk](../../cosmos-db/synapse-link-power-bi.md)
- [Anvisningar för att skapa och använda vyer i SQL-poolen utan Server](create-use-views.md) 
- [Självstudie om hur du skapar SQL-pooler utan server över Azure Cosmos DB och ansluter dem till Power BI modeller via DirectQuery](./tutorial-data-analyst.md)
