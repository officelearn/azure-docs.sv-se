---
title: Fråga Azure Cosmos DB data med SQL på begäran i Azure Synapse-länk (för hands version)
description: I den här artikeln får du lära dig hur du frågar Azure Cosmos DB med SQL på begäran i Azure Synapse Link (för hands version).
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 09/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 8dd6ab5bcb42765c995e8cd767358be5e62aa0b6
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288401"
---
# <a name="query-azure-cosmos-db-data-using-sql-on-demand-in-azure-synapse-link-preview"></a>Fråga Azure Cosmos DB data med SQL på begäran i Azure Synapse-länk (för hands version)

SQL Server-lös (tidigare SQL på begäran) gör att du kan analysera data i Azure Cosmos DB behållare som är aktiverade med [Azure Synapse-länk](../../cosmos-db/synapse-link.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) i nära real tid utan att påverka prestandan för dina transaktions arbets belastningar. Den erbjuder en välkänd T-SQL-syntax för att fråga data från [analys lagret](../../cosmos-db/analytical-store-introduction.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) och integrerad anslutning till en mängd olika bi-och ad hoc-frågemeddelanden via T-SQL-gränssnittet.

> [!NOTE]
> Stöd för att skicka frågor till Azure Cosmos DB analys lager med SQL på begäran finns för närvarande i för hands version. 

För att skicka frågor till Azure Cosmos DB stöds det fullständiga [Select](/sql/t-sql/queries/select-transact-sql.md?view=sql-server-ver15&preserve-view=true) -området via funktionen [OpenRowSet](develop-openrowset.md) , inklusive majoriteten av [SQL Functions och operatorer](overview-features.md). Du kan också lagra resultat från frågan som läser data från Azure Cosmos DB tillsammans med data i Azure Blob Storage eller Azure Data Lake Storage med hjälp av [skapa extern tabell som Välj](develop-tables-cetas.md#cetas-in-sql-on-demand). Du kan för närvarande inte lagra SQL-frågeresultat på begäran för att Azure Cosmos DB med [CETAS](develop-tables-cetas.md#cetas-in-sql-on-demand).

I den här artikeln får du lära dig hur du skriver en fråga med SQL på begäran som kommer att fråga efter data från Azure Cosmos DB behållare som är Synapse-länk aktiverade. Du kan sedan lära dig mer om att skapa SQL-vyer på begäran över Azure Cosmos DB behållare och ansluta dem till Power BI modeller i [den här](./tutorial-data-analyst.md) självstudien. 

## <a name="overview"></a>Översikt

För att stödja frågor och analys av data i Azure Cosmos DB analys lager, använder SQL på begäran följande `OPENROWSET` syntax:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ]
```

Anslutnings strängen Azure Cosmos DB anger Azure Cosmos DB konto namnet, databas namnet, databas kontots huvud nyckel och ett valfritt region namn som ska `OPENROWSET` fungera. Anslutnings strängen har följande format:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

Namnet på Azure Cosmos DB containern anges utan citat tecken i `OPENROWSET` syntaxen. Om behållar namnet innehåller specialtecken (till exempel ett bindestreck "-"), ska namnet omslutas inom `[]` (hakparenteser) i `OPENROWSET` syntaxen.

> [!NOTE]
> SQL på begäran stöder inte frågor om Azure Cosmos DB transaktions lager.

## <a name="sample-data-set"></a>Exempel på datauppsättning

Exemplen i den här artikeln baseras på data från [Europeiska centrum för sjukdoms förebyggande och kontroll (ECDC) COVID-19 fall](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) och [COVID-19 Open Research data uppsättning (kabel-19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/). 

Du kan se licensen och strukturen för data på dessa sidor och hämta exempel data för [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) -och [Cord19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) -datauppsättningar.

Om du vill följa med i den här artikeln som demonstrerar hur du frågar Cosmos DB data med SQL på begäran kontrollerar du att du skapar följande resurser:
* Ett Azure Cosmos DB databas konto som är [Synapse-länk aktiverat](../../cosmos-db/configure-synapse-link.md)
* En Azure Cosmos DB databas med namnet `covid`
* Två Azure Cosmos DB behållare med namnet `EcdcCases` och `Cord19` med ovanstående exempel data uppsättningar lästes in.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Utforska Azure Cosmos DB data med automatisk schema härledning

Det enklaste sättet att utforska data i Azure Cosmos DB är genom att dra nytta av funktionen för automatisk schema härledning. Genom att utelämna- `WITH` satsen från `OPENROWSET` -instruktionen kan du instruera SQL on-demand to Autodetect (Härled) schemat för den Azure Cosmos DB containerns analytiska lagrings nivå.

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       EcdcCases) as documents
```
I exemplet ovan instruerar vi SQL on-demand att ansluta till `covid` databasen i Azure Cosmos DB konto som `MyCosmosDbAccount` autentiseras med hjälp av Azure Cosmos DBS nyckeln (dummy i exemplet ovan). Vi ansluter sedan till behållarens `EcdcCases` analys lager i `West US 2` regionen. Eftersom ingen projektion av vissa egenskaper visas, `OPENROWSET` returnerar funktionen alla egenskaper från Azure Cosmos DB objekt.

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

Azure Cosmos DB gör att du kan representera mer komplexa data modeller genom att skriva dem som kapslade objekt eller matriser. Funktionen för automatisk synkronisering av Synapse för Azure Cosmos DB hanterar schema representationen i den analytiska lagrings platsen, som omfattar hantering av kapslade data typer som tillåter omfattande frågor från SQL på begäran.

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

Lär dig mer om att analysera [komplexa data typer i Synapse-länk](../how-to-analyze-complex-schema.md) och [kapslade strukturer i SQL på begäran](query-parquet-nested-types.md).

> [!IMPORTANT]
> Om du ser oväntade tecken i din text `MÃƒÂ©lade` , t. ex. i stället för `Mélade` databas sorteringen, är inte inställd på [utf8](https://docs.microsoft.com/sql/relational-databases/collations/collation-and-unicode-support#utf8) -sortering. 
> [Ändra sorteringen av databasen](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) till viss utf8-sortering med en viss SQL-instruktion som `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .


## <a name="flattening-nested-arrays"></a>Förenkla utkapslade matriser

Azure Cosmos DB data kan ha kapslade underordnade matriser som författarens matris från [Cord19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) data uppsättning:

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

I vissa fall kan du behöva "koppla" egenskaperna från det översta objektet (metadata) med alla element i matrisen (författarna). Med SQL på begäran kan du förenkla inkapslade strukturer genom `OPENJSON` att använda funktionen på den kapslade matrisen:

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

Det är viktigt att först Observera att när Azure Cosmos DB transaktions lager är schema-oberoende, är analys lagret schematiserade för att optimera prestandan för analytiska frågor. Med funktionen för automatisk synkronisering av Synapse, hanterar Azure Cosmos DB schema representationen i den analytiska lagrings platsen som omfattar hantering av kapslade data typer. Eftersom SQL på begäran skickar frågor till analys lagret är det viktigt att du förstår hur du mappar Azure Cosmos DB indata-typer till SQL-datatyper.

Azure Cosmos DB konton av SQL-API (Core) stöder JSON-egenskapsvärde av typen Number, String, Boolean, null, nested Object eller array. Du måste välja SQL-typer som matchar dessa JSON-typer om du använder- `WITH` satsen i `OPENROWSET` . Se under de SQL-kolumn typer som ska användas för olika egenskaps typer i Azure Cosmos DB.

| Azure Cosmos DB egenskaps typ | SQL-kolumn typ |
| --- | --- |
| Boolesk | bit |
| Heltal | bigint |
| Decimal | flyt |
| Sträng | varchar (UTF8-databas sortering) |
| Datum tid (ISO-formaterad sträng) | varchar (30) |
| Datum tid (Unix-tidsstämpel) | bigint |
| Null | `any SQL type` 
| Kapslat objekt eller matris | varchar (max) (sortering av UTF8-databas), serialiserad som JSON-text |

Om du vill fråga Azure Cosmos DB konton i Mongo DB API-typ kan du lära dig mer om schema representationen full Fidelity i analys lagret och de utökade egenskaps namn som ska användas [här](../../cosmos-db/analytical-store-introduction.md#analytical-schema).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Så här skapar du och använder vyer i SQL på begäran](create-use-views.md) 
- [Självstudie om att skapa SQL-vyer på begäran över Azure Cosmos DB och ansluta dem till Power BI modeller via DirectQuery](./tutorial-data-analyst.md)
