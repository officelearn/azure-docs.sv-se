---
title: Filformat som stöds i Azure Data Factory (äldre)
description: I det här avsnittet beskrivs de filformat och komprimeringskoder som stöds av filbaserade kopplingar i Azure Data Factory.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: b1f11a1ff25117c07e61475e7e83fc0c170cd552
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414652"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Filformat och komprimeringskoder som stöds i Azure Data Factory (äldre)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Den här artikeln gäller följande kopplingar: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)och [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory introducerade ny formatbaserad datauppsättningsmodell, se motsvarande formatartikel med information: <br>- [Avro-format](format-avro.md)<br>- [Binärt format](format-binary.md)<br>- [Avgränsat textformat](format-delimited-text.md)<br>- [JSON-format](format-json.md)<br>- [ORC-format](format-orc.md)<br>- [Parkettformat](format-parquet.md)<br>Resten konfigurationer som nämns i den här artikeln stöds fortfarande som-är för bakåt compabitility. Du föreslås använda den nya modellen framöver. 

## <a name="text-format-legacy"></a><a name="text-format"></a>Textformat (äldre)

>[!NOTE]
>Lär dig den nya modellen från artikel [i avgränsat textformat.](format-delimited-text.md) Följande konfigurationer på filbaserade datalagringsdatauppsättning stöds fortfarande som är för bakåtkompilering. Du föreslås använda den nya modellen framöver.

Om du vill läsa från en textfil eller skriva `type` till `format` en textfil anger du egenskapen i avsnittet i datauppsättningen till **TextFormat**. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [TextFormat-exempel](#textformat-example).

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| columnDelimiter |Det tecken som används för att avgränsa kolumner i en fil. Du kan överväga att använda ett sällsynt oskrivbart tecken som kanske inte finns i dina data. Ange till exempel "\u0001", som representerar Början av rubrik (SOH). |Endast ett tecken är tillåtet. **Standardvärdet** är **kommatecken (,)**. <br/><br/>Om du vill använda ett Unicode-tecken läser du [Unicode-tecken](https://en.wikipedia.org/wiki/List_of_Unicode_characters) för att hämta motsvarande kod för det. |Inga |
| rowDelimiter |Det tecken som används för att avgränsa rader i en fil. |Endast ett tecken är tillåtet. **Standardvärdet** är något av följande värden vid läsning: **["\r\n", "\r", "\n"]** och **"\r\n"** vid skrivning. |Inga |
| escapeChar |Det specialtecken som används för att undanta en kolumnavgränsare i innehållet i indatafilen. <br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Exempel: om du har kommatecken (',') som kolumn avgränsare men du vill ha kommatecknet i texten (exempel: "Hej, världen"), kan du definiera '$' som escape-tecken och använda strängen "Hej$, världen" i källan. |Inga |
| quoteChar |Det tecken som används för att referera till ett strängvärde. Kolumn- och radavgränsarna innanför citattecknen behandlas som en del av strängvärdet. Den här egenskapen gäller både in- och utdatauppsättningar.<br/><br/>Du kan inte ange både escapeChar och quoteChar för en tabell. |Endast ett tecken är tillåtet. Inget standardvärde. <br/><br/>Om du till exempel använder kommatecken (,) som kolumnavgränsare, men vill använda ett kommatecken i texten (till exempel <Hello, world>), kan du definiera " (dubbla citattecken) som citattecknet och använda strängen "Hello, world" i källan. |Inga |
| nullValue |Ett eller flera tecken som används för att representera ett null-värde. |Ett eller flera tecken. **Standardvärdena** är **"\N" och "NULL"** vid läsning och **"\N"** vid skrivning. |Inga |
| encodingName |Ange kodningsnamnet. |Ett giltigt kodningsnamn. Se [Egenskapen Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exempel: windows-1250 or shift_jis. **Standardvärdet** är **UTF-8**. |Inga |
| firstRowAsHeader |Anger om den första raden ska behandlas som en rubrik. För en indatauppsättning läser Data Factory den första raden som en rubrik. För en utdatauppsättning skriver Data Factory den första raden som en rubrik. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (standard)</b> |Inga |
| skipLineCount |Anger hur många **rader som inte är tomma** att hoppa över när du läser data från indatafiler. Om både skipLineCount och firstRowAsHeader anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen. <br/><br/>Exempelscenarier finns i avsnittet med [användningsscenarier för `firstRowAsHeader` och `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Inga |
| treatEmptyAsNull |Anger om du vill hantera null-strängar eller tomma strängar som ett null-värde vid läsning av data från en indatafil. |**True (standard)**<br/>False |Inga |

### <a name="textformat-example"></a>TextFormat-exempel

I följande JSON-definition för en datauppsättning anges några av de valfria egenskaperna.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Om du vill använda ett `escapeChar` i stället för `quoteChar` ersätter du raden med `quoteChar` med följande escapeChar:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Användningsscenarier för firstRowAsHeader och skipLineCount

* Du kopierar från en källa som inte är filbaserad till en textfil och vill lägga till en rubrikrad som innehåller schemametadata (till exempel: SQL-schema). Ange `firstRowAsHeader` som true i utdatauppsättningen för det här scenariot.
* Du kopierar från en textfil som innehåller en rubrikrad till en mottagare som inte är filbaserad och vill ignorera raden. Ange `firstRowAsHeader` som true i indatauppsättningen.
* Du kopierar från en textfil och vill hoppa över några rader i början som antingen inte innehåller några data eller som innehåller rubrikinformation. Ange `skipLineCount` för att ange antalet rader som ska hoppas över. Om resten av filen innehåller en rubrikrad kan du också ange `firstRowAsHeader`. Om både `skipLineCount` och `firstRowAsHeader` anges hoppas raderna över först, varefter rubrikinformationen läses från indatafilen

## <a name="json-format-legacy"></a><a name="json-format"></a>JSON-format (äldre)

>[!NOTE]
>Lär dig den nya modellen från [JSON-format](format-json.md) artikeln. Följande konfigurationer på filbaserade datalagringsdatauppsättning stöds fortfarande som är för bakåtkompilering. Du föreslås använda den nya modellen framöver.

Om du vill **importera/exportera en JSON-fil som den är i/från Azure Cosmos DB**läser du avsnittet Importera/exportera JSON-dokument i flytta [data till/från Azure Cosmos](connector-azure-cosmos-db.md) DB-artikel.

Om du vill tolka JSON-filerna eller skriva data i JSON-format `format` anger du egenskapen `type` i avsnittet till **JsonFormat**. Du kan också ange följande **valfria** egenskaper i avsnittet `format`. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example).

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| filePattern |Ange mönstret för de data som lagras i varje JSON-fil. Tillåtna värden är: **setOfObjects** och **arrayOfObjects**. **Standardvärdet** är **setOfObjects**. Detaljerad information om dessa mönster finns i avsnittet om [JSON-filmönster](#json-file-patterns). |Inga |
| jsonNodeReference | Om du vill iterera och extrahera data från objekten i ett matrisfält med samma mönster anger du JSON-sökvägen för matrisen. Den här egenskapen stöds bara när data kopieras **från** JSON-filer. | Inga |
| jsonPathDefinition | Ange JSON-sökvägsuttrycket för varje kolumnmappning med ett anpassat kolumnnamn (inled med liten bokstav). Den här egenskapen stöds bara när du kopierar data **från** JSON-filer och du kan extrahera data från objekt eller matris. <br/><br/> För fält under rotobjektet börjar du med $; för fält inuti matrisen som väljs av egenskapen `jsonNodeReference` börjar du från matriselementet. Konfigurationsinformation finns i avsnittet med [JsonFormat-exempel](#jsonformat-example). | Inga |
| encodingName |Ange kodningsnamnet. En lista över giltiga kodningsnamn finns i avsnittet om egenskapen [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Exempel: windows-1250 or shift_jis. **Standardvärdet** är: **UTF-8**. |Inga |
| nestingSeparator |Tecken som används för att avgränsa kapslingsnivåer. Standardvärdet är ”.” (punkt). |Inga |

>[!NOTE]
>När det gäller korsförstörjande data i matrisen i flera rader (fall 1 -> exempel 2 i `jsonNodeReference` [JsonFormat)](#jsonformat-example)kan du bara välja att expandera en enda matris med egenskapen .

### <a name="json-file-patterns"></a>JSON-filmönster

Kopieringsaktivitet kan tolka följande mönster för JSON-filer:

- **Typ I: setOfObjects**

    Varje fil som innehåller ett enskilt objekt eller flera radavgränsade/sammanfogade objekt. När det här alternativet väljs i en utdatauppsättning genererar kopieringsaktiviteten en enskild JSON-fil med ett objekt per rad (radavgränsade).

    * **Exempel på JSON med enskilda objekt**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Exempel med radavgränsad JSON**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exempel med sammanfogad JSON**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Varje fil innehåller en matris med objekt.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat-exempel

**Fall 1: Kopiera data från JSON-filer**

**Exempel 1: hämta data från objektet och matrisen**

I det här exemplet mappas ett JSON-rotobjekt till en enskild post i tabellformat. Om du har en JSON-fil med följande innehåll:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

och du vill kopiera den till en Azure SQL-tabell i följande format, genom att extrahera data från både objekten och matrisen:

| ID | deviceType | targetResourceType | resursHanagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i [Mappa källdatauppsättningskolumner till måldatauppsättningskolumner](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. Om du vill kopiera data `array[x].property` från matrisen kan du `xth` använda för att `array[*].property` extrahera värdet för den angivna egenskapen från objektet, eller så kan du använda för att hitta värdet från ett objekt som innehåller en sådan egenskap.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Exempel 2: korstillämpa flera objekt med samma mönster från en matris**

I det här exemplet omvandlas ett JSON-rotobjekt till flera poster i tabellform. Om du har en JSON-fil med följande innehåll:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

som du vill kopiera till en Azure SQL-tabell i följande format genom att förenkla data i matrisen och korskoppla med den gemensamma rotinformationen:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Indatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt:

- Avsnittet `structure` definierar de anpassade kolumnnamnen och den motsvarande datatypen vid konverteringen till data i tabellformat. Det här avsnittet är **valfritt** såvida inte kolumnmappning krävs. Mer information finns i [Mappa källdatauppsättningskolumner till måldatauppsättningskolumner](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference`anger att iterera och extrahera data från objekten med samma mönster under **matrisen** `orderlines`.
- `jsonPathDefinition` anger JSON-sökvägen för varje kolumn och anger var data ska extraheras från. I det `ordernumber`här `orderdate`exemplet `city` , , och är under `$.`rotobjekt med JSON-bana som börjar med , medan `order_pd` och `order_price` definieras med bana som härleds från matriselementet utan `$.`.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Observera följande punkter:**

* Om `structure` och `jsonPathDefinition` inte har definierats i Data Factory-datauppsättningen identifierar kopieringsaktiviteten schemat från det första objektet och förenklar hela objektet.
* Om JSON-indata har en matris konverterar kopieringsaktiviteten som standard hela matrisvärdet till en sträng. Du kan välja att extrahera data från den med hjälp av `jsonNodeReference` och/eller `jsonPathDefinition`, eller hoppa över det genom att inte ange den i `jsonPathDefinition`.
* Om det finns dubblettnamn på samma nivå väljer kopieringsaktiviteten det sista.
* Egenskapsnamn är skiftlägeskänsliga. Två egenskaper med samma namn men med olika skiftlägen behandlas som två olika egenskaper.

**Fall 2: Skriva data till JSON-fil**

Om du har följande tabell i SQL Database:

| ID | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

och för varje post förväntar du dig att skriva till ett JSON-objekt i följande format:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Utdatauppsättningen med typen **JsonFormat** definieras så här: (partiell definition med endast de relevanta delarna). Mer specifikt `structure` definierar avsnittet de anpassade egenskapsnamnen i målfilen `nestingSeparator` (standard är ".") används för att identifiera kapslingslagret från namnet. Det här avsnittet är **valfritt** såvida du inte vill ändra egenskapsnamnet som jämförs med källkolumnnamnet, eller kapsla vissa av egenskaperna.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a>Parkettformat (äldre)

>[!NOTE]
>Lär dig den nya modellen från [Parquet format](format-parquet.md) artikel. Följande konfigurationer på filbaserade datalagringsdatauppsättning stöds fortfarande som är för bakåtkompilering. Du föreslås använda den nya modellen framöver.

Om du vill parsa Parquet-filer eller skriva data i Parquet-format anger du egenskapen `format` `type` till **ParquetFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Observera följande punkter:

* Komplexa datatyper stöds inte (MAP, LIST).
* Blanksteg i kolumnnamnet stöds inte.
* Parquet-filer har följande komprimeringsrelaterade alternativ: NONE, SNAPPY, GZIP och LZO. Data Factory stöder läsdata från Parquet-filen i något av dessa komprimerade format utom LZO - den använder komprimeringskodningen i metadata för att läsa data. Men vid skrivning till en Parquet-fil väljer Data Factory SNAPPY, som är standard för Parquet-formatet. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

> [!IMPORTANT]
> För kopiering som styrs av Självvärdförd Integration Runtime t.ex. **as-is** **64-bit JRE 8 (Java Runtime Environment) or OpenJDK** Se följande stycke med mer information.

För kopia som körs på Self-hosted IR med Parquet fil serialisering / deserialization, ADF lokaliserar Java runtime genom att först kontrollera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om inte hittas, andra kontroll systemvariabel *`JAVA_HOME`* för OpenJDK.

- **För att använda JRE:** 64-bitars IR kräver 64-bitars JRE. Du hittar den [härifrån.](https://go.microsoft.com/fwlink/?LinkId=808605)
- **Så här använder du OpenJDK**: det stöds sedan IR version 3.13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK i självvärdad IR-dator och ställ in systemmiljövariabeln JAVA_HOME i enlighet med detta.

>[!TIP]
>Om du kopierar data till/från Parquet-format med självvärderade Integration Runtime och trycker på fel som säger "Ett fel uppstod när du anropar java, `_JAVA_OPTIONS` meddelande: **java.lang.OutOfMemoryError:Java heap space**", kan du lägga till en miljövariabel i maskinen som är värd för den självvärderade IR för att justera min / max heap-storleken för JVM för att ge en sådan kopia och kör sedan om pipelinen.

![Ange JVM-heap-storlek på självvärd ir](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Exempel: ange `_JAVA_OPTIONS` variabel `-Xms256m -Xmx16g`med värde . Flaggan `Xms` anger den första minnesallokeringspoolen för en `Xmx` Java Virtual Machine (JVM), medan den anger den maximala minnesallokeringspoolen. Detta innebär att JVM `Xms` kommer att startas med mängden minne `Xmx` och kommer att kunna använda maximalt mängd minne. Som standard använder ADF min 64MB och max 1G.

### <a name="data-type-mapping-for-parquet-files"></a>Mappning av datatyp för parkettfiler

| Data fabrik interim datatyp | Parkett primitiv typ | Parkett originaltyp (Deserialize) | Parkett originaltyp (serialisera) |
|:--- |:--- |:--- |:--- |
| Boolesk | Boolesk | Ej tillämpligt | Ej tillämpligt |
| SByte (SByte) | Int32 | Int8 (int8) | Int8 (int8) |
| Byte | Int32 | UInt8 (på andra) | Int16 (int16) |
| Int16 (int16) | Int32 | Int16 (int16) | Int16 (int16) |
| UInt16 (storbritannien) | Int32 | UInt16 (storbritannien) | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 (Storbritannien) | Int64 | UInt32 (Storbritannien) | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 (storbritannien) | Int64/Binär | UInt64 (storbritannien) | Decimal |
| Enkel | Float (Flyttal) | Ej tillämpligt | Ej tillämpligt |
| Double | Double | Ej tillämpligt | Ej tillämpligt |
| Decimal | Binär | Decimal | Decimal |
| Sträng | Binär | Utf8 (olika) | Utf8 (olika) |
| DateTime | Int96 (int96) | Ej tillämpligt | Ej tillämpligt |
| TimeSpan | Int96 (int96) | Ej tillämpligt | Ej tillämpligt |
| DateTimeOffset | Int96 (int96) | Ej tillämpligt | Ej tillämpligt |
| Bytearray | Binär | Ej tillämpligt | Ej tillämpligt |
| GUID | Binär | Utf8 (olika) | Utf8 (olika) |
| Char | Binär | Utf8 (olika) | Utf8 (olika) |
| Mer från CharArray | Stöds inte | Ej tillämpligt | Ej tillämpligt |

## <a name="orc-format-legacy"></a><a name="orc-format"></a>ORC-format (äldre)

>[!NOTE]
>Lär dig den nya modellen från [ORC-format](format-orc.md) artikeln. Följande konfigurationer på filbaserade datalagringsdatauppsättning stöds fortfarande som är för bakåtkompilering. Du föreslås använda den nya modellen framöver.

Om du vill parsa ORC-filerna eller skriva data i ORC-format ange du egenskapen `format` `type` till **OrcFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "OrcFormat"
}
```

Observera följande punkter:

* Komplexa datatyper stöds inte (STRUCT, MAP, LIST, UNION).
* Blanksteg i kolumnnamnet stöds inte.
* ORC-filen har tre [komprimeringsrelaterade alternativ](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB och SNAPPY. Data Factory stöder läsning av data från ORC-filer i alla dessa komprimerade format. Data Factory använder komprimerings-codec i metadata för att läsa data. Men vid skrivning till en ORC-fil väljer Data Factory ZLIB, som är standard för ORC. För närvarande finns det inget alternativ för att åsidosätta det här beteendet.

> [!IMPORTANT]
> För kopiering som styrs av Självvärdförd integrationskörning, t.ex. **as-is** **64-bit JRE 8 (Java Runtime Environment) or OpenJDK** Se följande stycke med mer information.

För kopiering som körs på Självvärd iR med ORC-fil serialisering /deserialization, ADF lokaliserar Java runtime genom att först kontrollera registret *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* för JRE, om inte hittas, andra kontroll systemvariabel *`JAVA_HOME`* för OpenJDK.

- **För att använda JRE:** 64-bitars IR kräver 64-bitars JRE. Du hittar den [härifrån.](https://go.microsoft.com/fwlink/?LinkId=808605)
- **Så här använder du OpenJDK**: det stöds sedan IR version 3.13. Paketera jvm.dll med alla andra nödvändiga sammansättningar av OpenJDK i självvärdad IR-dator och ställ in systemmiljövariabeln JAVA_HOME i enlighet med detta.

### <a name="data-type-mapping-for-orc-files"></a>Mappning av datatyp för ORC-filer

| Data fabrik interim datatyp | ORC-typer |
|:--- |:--- |
| Boolesk | Boolesk |
| SByte (SByte) | Byte |
| Byte | Kort |
| Int16 (int16) | Kort |
| UInt16 (storbritannien) | Int |
| Int32 | Int |
| UInt32 (Storbritannien) | Lång |
| Int64 | Lång |
| UInt64 (storbritannien) | Sträng |
| Enkel | Float (Flyttal) |
| Double | Double |
| Decimal | Decimal |
| Sträng | Sträng |
| DateTime | Tidsstämpel |
| DateTimeOffset | Tidsstämpel |
| TimeSpan | Tidsstämpel |
| Bytearray | Binär |
| GUID | Sträng |
| Char | Tecken(1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a>AVRO-format (äldre)

>[!NOTE]
>Lär dig den nya modellen från [Avro-formatartikeln.](format-avro.md) Följande konfigurationer på filbaserade datalagringsdatauppsättning stöds fortfarande som är för bakåtkompilering. Du föreslås använda den nya modellen framöver.

Om du vill parsa Avro-filerna eller skriva data i Avro-format anger du egenskapen `format` `type` till **AvroFormat**. Du behöver inte ange några egenskaper i avsnittet Format i avsnittet typeProperties. Exempel:

```json
"format":
{
    "type": "AvroFormat",
}
```

Om du vill använda Avro-format i en Hive-tabell kan du referera till [Apache Hives handledning](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Observera följande punkter:

* [Komplexa datatyper](https://avro.apache.org/docs/current/spec.html#schema_complex) stöds inte (poster, uppräkningar, matriser, kartor, fackföreningar och fasta).

## <a name="compression-support-legacy"></a><a name="compression-support"></a>Komprimeringsstöd (äldre)

Azure Data Factory stöder komprimera/expandera data under kopiering. När du `compression` anger egenskap i en indatauppsättning läser kopieringsaktiviteten de komprimerade data från källan och expanderar den. Och när du anger egenskapen i en utdatauppsättning skriver kopieringsaktiviteten sedan data till diskhon. Här är några exempelscenarier:

* Läs GZIP-komprimerade data från en Azure-blob, expandera den och skriva resultatdata till en Azure SQL-databas. Du definierar indata Azure Blob-datauppsättningen med egenskapen `compression` `type` som GZIP.
* Läs data från en oformaterad textfil från lokalt filsystem, komprimera den med GZip-format och skriv komprimerade data till en Azure-blob. Du definierar en utdata-Azure `compression` `type` Blob-datauppsättning med egenskapen som GZip.
* Läs ZIP-filen från FTP-servern, expandera den för att hämta filerna och landa filerna i Azure Data Lake Store. Du definierar en indata-FTP-datauppsättning med egenskapen `compression` `type` Som ZipDeflate.
* Läs en GZIP-komprimerad data från en Azure-blob, expandera den, komprimera den med BZIP2 och skriva resultatdata till en Azure-blob. Du definierar indata Azure Blob-datauppsättningen med `compression` `type` inställd på `compression` `type` GZIP och utdatauppsättningen med inställd på BZIP2.

Om du vill ange komprimering för en datauppsättning använder du **komprimeringsegenskapen** i datauppsättningen JSON som i följande exempel:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Komprimeringsavsnittet** har två egenskaper:

* **Typ:** komprimeringskodningen, som kan vara **GZIP**, **Deflate**, **BZIP2**eller **ZipDeflate**. Observera att när du använder kopieringsaktivitet för att expandera ZipDeflate-filer och skriva till filbaserat `<path specified in dataset>/<folder named as source zip file>/`sink-datalager extraheras filerna till mappen: .
* **Nivå:** kompressionsförhållandet, som kan vara **optimalt** eller **snabbast**.

  * **Snabbast:** Komprimeringsåtgärden bör slutföras så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad.
  * **Optimal**: Komprimeringsoperationen ska komprimeras optimalt, även om operationen tar längre tid att slutföra.

    Mer information finns i avsnittet [Komprimeringsnivå.](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx)

> [!NOTE]
> Komprimeringsinställningar stöds inte för data i **AvroFormat,** **OrcFormat**eller **ParquetFormat**. När du läser filer i dessa format identifierar och använder Data Factory komprimeringskodningen i metadata. När du skriver till filer i dessa format väljer Data Factory standardkomprimeringskodifierare för det formatet. Till exempel ZLIB för OrcFormat och SNAPPY för ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Filtyper och komprimeringsformat som inte stöds

Du kan använda utökningsbarhetsfunktionerna i Azure Data Factory för att omvandla filer som inte stöds.
Två alternativ inkluderar Azure-funktioner och anpassade uppgifter med hjälp av Azure Batch.

Du kan se ett exempel som använder en Azure-funktion för att [extrahera innehållet i en tar-fil](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Mer information finns i [Azure Functions-aktivitet](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Du kan också skapa den här funktionen med hjälp av en anpassad dotnet-aktivitet. Mer information finns [här](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity)

## <a name="next-steps"></a>Nästa steg

Lär dig de senaste filformaten och komprimeringarna som stöds från [filformat och komprimeringar som stöds](supported-file-formats-and-compression-codecs.md).
