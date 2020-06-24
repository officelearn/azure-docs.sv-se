---
title: Analysera schema med matriser och kapslade strukturer
description: Analysera matriser och kapslade strukturer med Apache Spark och SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84908173"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Analysera komplexa data typer i Synapse

Den här artikeln är relevant för Parquet-filer och behållare i **Azure Synapse-länken för Azure Cosmos DB**. Det förklarar hur användare kan använda Spark eller SQL för att läsa eller transformera data med ett komplext schema, till exempel matriser eller kapslade strukturer. Exemplet nedan görs med ett enda dokument, men kan enkelt skala till miljarder dokument med Spark eller SQL. I koden nedan används PySpark (python).

## <a name="use-case"></a>Användnings fall

Med moderna data typer är komplexa data typer ofta vanliga för att hantera och representera en utmaning för data tekniker. Analys av nästlat schema och matriser innehåller några utmaningar:
* Komplex för att skriva SQL-frågor
* Svårt att byta namn/Cast-datatype för kapslade kolumner
* Träffa prestanda problem med djupt kapslade objekt

Data tekniker måste förstå hur man effektivt bearbetar dessa data typer och gör dem lättillgängliga för alla.

I exemplet nedan används Synapse Spark för att läsa och transformera objekt via data ramar till en planad struktur. Synapse SQL Server-lös används för att fråga direkt sådana objekt och returnera resultaten som en vanlig tabell.

## <a name="what-are-arrays-and-nested-structures"></a>Vad är matriser och kapslade strukturer?

Följande objekt kommer från appens insikter. I det här objektet finns det kapslade strukturer, men även matriser som innehåller kapslade strukturer.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Schema exempel på matriser och kapslade strukturer
När du skriver ut schemat för data ramen i objektet (kallas **DF**) med kommandot **DF. printschema**, ser vi följande åter givning:

* den gula färgen representerar kapslad struktur
* den gröna färgen representerar en matris med två element

[![Schema ursprung](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts och _etag har lagts till i systemet när dokumentet skrevs in i Azure Cosmos DB transaktions lager.

Data ramen ovan räknas bara för 5 kolumner och 1 rad. Efter omvandling har den granskade data ramen 13 kolumner och 2 rader i tabell format.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Förenkla inkapslade strukturer och expandera matriser med Apache Spark

Med Synapse Spark är det enkelt att omvandla kapslade strukturer i kolumner och array-element till flera rader. Stegen nedan kan användas av alla för sin egen implementering.

[![Steg för Spark-omvandling](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Steg 1**: definiera en funktion för att förenkla det kapslade schemat. Den här funktionen kan användas utan ändring. Skapa en cell i en Pyspark-anteckningsbok med den funktionen:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Steg 2**: Använd funktionen för att förenkla det kapslade schemat i data ramen **DF** till en ny data Rams **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Funktionen display ska returnera 10 kolumner och 1 rad. Matrisen och dess kapslade element finns fortfarande där.

**Steg 3**: omvandla nu matrisen **context_custom_dimensions** i data ramen **df_flat** till en ny dataframe- **df_flat_explode**. I koden nedan definierar vi även vilken kolumn vi väljer:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Funktionen display ska returnera följande resultat: 10 kolumner och 2 rader. Nästa steg är att förenkla inkapslade scheman med funktionen som definieras i steg 1.

**Steg 4**: Använd funktionen för att förenkla det kapslade schemat för data ramen **df_flat_explode** till en ny data ram **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Funktionen display ska Visa 13 kolumner och 2 rader:

PrintSchema för data ramen df_flat_explode_flat returnerar följande resultat:

[![Schema slut](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Läs matriser och kapslade strukturer direkt med SQL Server-lös

Att fråga, skapa vyer och tabeller över sådana objekt är möjligt med SQL Server-lös.

Först, beroende på hur data har lagrats, bör användarna använda följande taxonomi. Allting VERSALer är speciellt för ditt användnings fall:

| Mass              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; konto = ACCOUNTNAME; databas = databasename; samling = samlings namn; region = REGIONTOQUERY, Secret = ' YOURSECRET ' |' CosmosDB ' (Synapse länk)|



**SQL Server** stöder länkad tjänst för Azure Synapse-länkar för Azure Cosmos DB och AAD-genomströmning. Kapaciteten är för närvarande underhåller för hands versionen av Synapse-länken.

Ersätt nedan:
* "Din BULK över" av anslutnings strängen för den data källa som du ansluter till
* "Din typ ovanför" med det format som du använder för att ansluta till källan

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Det finns två olika typer av åtgärder gjorda:
* Kodraden nedan definierar kolumnen med namnet contextdataeventTime som refererar till det kapslade elementet: context. data. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Den här raden definierar kolumnen med namnet contextdataeventTime som refererar till kapslings elementet: kontext>data>eventTime

* **kors Apply** används för att skapa nya rader för varje element under matrisen och sedan med definierar varje kapslat objekt som liknar den första punktens punkt: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Om matrisen hade 5 element med 4 kapslade strukturer, returnerar SQL Server, 5 rader och 4 kolumner.

SQL Server-funktionen kan fråga på plats, mappa matrisen i 2 rader och Visa alla kapslade strukturer i kolumner.

## <a name="next-steps"></a>Nästa steg

* [Lär dig att fråga Azure Synapse-länken för Azure Cosmos DB med Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Fråga Parquet kapslade typer](./sql/query-parquet-nested-types.md) 