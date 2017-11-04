---
title: "Flytta data från Cassandra med hjälp av Data Factory | Microsoft Docs"
description: "Läs mer om hur du flyttar data från en lokal Cassandra-databas med Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6e5c859d13ea8a10e1fa38340df52f189ec6cd4e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Flytta data från en lokal Cassandra-databas med Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-onprem-cassandra-connector.md)
> * [Version 2 – förhandsversion](../connector-cassandra.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Cassandra connector i V2](../connector-cassandra.md).

Den här artikeln förklarar hur du använder aktiviteten kopiera i Azure Data Factory för att flytta data från en lokal Cassandra databas. Den bygger på den [Data Movement aktiviteter](data-factory-data-movement-activities.md) artikel som presenterar en allmän översikt över dataflyttning med copy-aktivitet.

Du kan kopiera data från ett lokalt Cassandra dataarkiv till alla stöds sink-datalagret. En lista över datakällor som stöds som sänkor av kopieringsaktiviteten, finns det [stöds datalager](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flytta data från ett dataarkiv som Cassandra till andra databaser, men inte för att flytta data från andra datalager till en Cassandra dataarkiv. 

## <a name="supported-versions"></a>Versioner som stöds
Stöder följande versioner av Cassandra för Cassandra-anslutningen: 2.X.

## <a name="prerequisites"></a>Krav
För Azure Data Factory-tjänsten för att kunna ansluta till din lokala Cassandra databas måste du installera en Data Management Gateway på samma dator som värd för databasen eller på en separat dator för att undvika konkurrerar om resurser med databasen. Data Management Gateway är en komponent som ansluter lokala datakällor till molntjänster i en säker och hanterad sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) artikeln för information om Data Management Gateway. Se [flytta data från lokalt till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner om hur du konfigurerar gatewayen som en pipeline för data att flytta data.

Gatewayen måste du använda för att ansluta till en databas för Cassandra även om databasen finns i molnet, till exempel på en Azure IaaS-VM. Y du kan ha gatewayen på samma virtuella dator som är värd för databasen eller på en separat virtuell dator så länge som gatewayen kan ansluta till databasen.  

När du installerar gateway installeras automatiskt en Microsoft Cassandra ODBC-drivrutinen används för att ansluta till Cassandra-databasen. Därför behöver du inte manuellt installera en drivrutin på gateway-datorn när du kopierar data från databasen Cassandra. 

> [!NOTE]
> Se [felsökning av problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning /-gateway relaterade problem.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopia-aktivitet som flyttar data från en lokal Cassandra data store med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **guiden Kopiera**. Finns [Självstudier: skapa en pipeline med hjälp av guiden Kopiera](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och **REST API**. Se [kopiera aktivitet kursen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er, kan du utföra följande steg för att skapa en pipeline som flyttar data från ett dataarkiv som källa till ett dataarkiv som mottagare:

1. Skapa **länkade tjänster** att länka inkommande och utgående data lagras till din data factory.
2. Skapa **datauppsättningar** att representera inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en kopia-aktivitet som tar en datamängd som indata och en dataset som utdata. 

När du använder guiden skapas JSON definitioner för dessa Data Factory-enheter (länkade tjänster, datauppsättningar och pipelinen) automatiskt för dig. När du använder Verktyg/API: er (utom .NET API), kan du definiera dessa Data Factory-enheter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett lokalt Cassandra dataarkiv finns [JSON-exempel: kopiera data från Cassandra till Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory entiteter till datakällan Cassandra:

## <a name="linked-service-properties"></a>Länkad tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Cassandra länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen type måste anges till: **OnPremisesCassandra** |Ja |
| värden |En eller flera IP-adresser eller värdnamn Cassandra servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-porten som används av Cassandra-server för att lyssna efter anslutningar. |Nej, standardvärde: 9042 |
| AuthenticationType |Grundläggande eller anonym |Ja |
| användarnamn |Ange användarnamnet för användarkontot. |Ja, om authenticationType anges till Basic. |
| lösenord |Ange lösenordet för användarkontot. |Ja, om authenticationType anges till Basic. |
| gatewayName |Namnet på den gateway som används för att ansluta till lokalt Cassandra-databasen. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats med gatewayen. |Nej |

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över egenskaper som är tillgängliga för att definiera datauppsättningarna & avsnitt finns i [skapa datauppsättningar](data-factory-create-datasets.md) artikel. Avsnitt som struktur, tillgänglighet och princip på en datamängd JSON är liknande för alla typer av dataset (Azure SQL Azure blob, Azure-tabellen, osv.).

Den **typeProperties** avsnitt är olika för varje typ av dataset och innehåller information om placeringen av data i datalagret. TypeProperties avsnittet för dataset av typen **CassandraTable** har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| keyspace |Namnet på keyspace eller schema i Cassandra databasen. |Ja (om **frågan** för **CassandraSource** har inte definierats). |
| tableName |Namnet på tabellen i Cassandra databas. |Ja (om **frågan** för **CassandraSource** har inte definierats). |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i [skapar Pipelines](data-factory-create-pipelines.md) artikel. Egenskaper som namn, beskrivning, ingående och utgående tabeller och principen är tillgängliga för alla typer av aktiviteter.

De egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar beroende på varje aktivitetstyp. För Kopieringsaktivitet kan variera de beroende på vilka typer av datakällor och sänkor.

När datakällan är av typen **CassandraSource**, följande egenskaper finns i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 frågan eller CQL frågan. Se [CQL referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-frågan anger **keyspace name.table namn** som representerar den tabell som du vill fråga. |Nej (om tabellnamn och keyspace för datauppsättningen har definierats). |
| consistencyLevel |Nivå för konsekvenskontroll anger hur många repliker måste svara på en läsbegäran innan den returnerar data till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data att uppfylla read-förfrågan. |EN, TVÅ, TRE, KVORUM, ALL, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Se [konfigurera datakonsekvens](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) mer information. |Nej. Standardvärdet är en. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-exempel: kopiera data från Cassandra till Azure-Blob
Det här exemplet innehåller exempel JSON definitioner som du kan använda för att skapa en pipeline med [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en lokal Cassandra databas till en Azure Blob Storage. Dock datan kan kopieras till någon av sänkor anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av aktiviteten kopiera i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON kodavsnitt. Stegvisa instruktioner för att skapa datafabriken inkluderas inte. Se [flytta data mellan lokala platser och moln](data-factory-move-data-between-onprem-and-cloud.md) artikel stegvisa instruktioner.

Exemplet har följande data factory enheter:

* En länkad tjänst av typen [OnPremisesCassandra](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [dataset](data-factory-create-datasets.md) av typen [CassandraTable](#dataset-properties).
* Utdata [dataset](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med Kopieringsaktiviteten som använder [CassandraSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra länkade tjänsten:**

Det här exemplet används den **Cassandra** länkade tjänsten. Se [Cassandra länkade tjänsten](#linked-service-properties) avsnittet för egenskaper som stöds av den här länkade tjänsten.  

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage länkade tjänsten:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Cassandra inkommande datauppsättningen:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datamängden är extern till data factory och inte tillverkas av en aktivitet i datafabriken.

**Azure Blob utdatauppsättningen:**

Data skrivs till en ny blob varje timme (frekvens: timme, intervall: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopiera aktivitet i en pipeline med Cassandra källa och mottagare för Blob:**

Pipelinen innehåller en kopia-aktivitet som är konfigurerad för att använda indata och utdata-datauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen av **källa** är inställd på **CassandraSource** och **sink** är inställd på **BlobSink**.

Se [RelationalSource Typegenskaper](#copy-activity-properties) lista över egenskaper som stöds av RelationalSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]    
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Mappning för Cassandra
| Cassandra typ | .NET baserat typ |
| --- | --- |
| ASCII |Sträng |
| BIGINT |Int64 |
| BLOB |byte] |
| BOOLESKT VÄRDE |Booleskt värde |
| DECIMAL |Decimal |
| DUBBEL |dubbla |
| FLYTTAL |Enskild |
| INET |Sträng |
| INT |Int32 |
| TEXT |Sträng |
| TIDSSTÄMPEL |Datum och tid |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sträng |
| VARINT |Decimal |

> [!NOTE]
> Insamling av typer (karta, set, lista, etc.), referera till [arbeta med Cassandra samlingstyper med virtuella tabellen](#work-with-collections-using-virtual-table) avsnitt.
>
> Användardefinierade typer stöds inte.
>
> Längden på Binary-kolumn och strängkolumn längd får inte vara större än 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med virtuella tabellen
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från databasen Cassandra. För samlingstyper inklusive karta, uppsättning och lista, renormalizes drivrutinen data i motsvarande virtuella tabeller. Om en tabell innehåller några kolumner i samlingen, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som verkliga tabell utom samling kolumner. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje samling-kolumn som utökar kapslade data. Virtuella tabeller som representerar samlingar namnges med namnet på tabellen verkliga avgränsare ”*vt*” och namnet på kolumnen.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt Avnormaliserade data. Se avsnittet för information. Du kan komma åt innehållet i Cassandra samlingar genom att fråga och ansluta till virtuella tabeller.

Du kan använda den [guiden Kopiera](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitivt visa listan över tabeller i Cassandra databasen, inklusive virtuella tabeller och förhandsgranska data som ingår. Du kan också skapa en fråga i guiden Kopiera och validera om du vill se resultatet.

### <a name="example"></a>Exempel
Till exempel är följande ”ExampleTable” en Cassandra databastabell som innehåller ett heltal primärnyckelkolumnen med namnet ”pk_int”, en textkolumn namngivet värde, en kolumn, en karta kolumn och en set-kolumn (med namnet ”StringSet”).

| pk_int | Värde | Visa lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |”exempelvärde 1” |["1", "2", "3"] |{”S1”: ”a”, ”S2”: ”b”} |{”A”, ”B”, ”C”} |
| 3 |”exempelvärde 3” |["100", "101", "102", "105"] |{”S1”: ”t”} |{”A”, ”E”} |

Drivrutinen skulle generera flera virtuella tabeller som representerar denna tabell. Sekundärnyckelskolumnerna i virtuella tabeller refererar till primärnyckelkolumnerna i tabellen verkliga och ange vilka verkliga tabellrad som motsvarar den virtuella tabellraden.

Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas i följande tabell. Bastabellen innehåller samma data som den ursprungliga databastabellen förutom de samlingar som utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

| pk_int | Värde |
| --- | --- |
| 1 |”exempelvärde 1” |
| 3 |”exempelvärde 3” |

Följande tabeller visar virtuella register som renormalize data från listan och mappa StringSet kolumner. Kolumner med namn som slutar med ”_index” eller ”_nyckelskyddsserverns” anger placeringen av data i den ursprungliga listan eller kartan. Kolumnerna med namn som slutar med ”_value” innehåller utökade data från samlingen.

#### <a name="table-exampletablevtlist"></a>Tabell ”ExampleTable_vt_List”:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>Tabell ”ExampleTable_vt_Map”:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |B |
| 3 |S1 |T |

#### <a name="table-exampletablevtstringset"></a>Tabell ”ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Karta källan till mottagare för kolumner
Mer information om mappning kolumner i datauppsättningen källan till kolumner i datauppsättning mottagare, se [mappa dataset kolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Tänk på att undvika oväntade resultat repeterbarhet när kopiering av data från relationella data lagras. I Azure Data Factory, kan du köra en sektor manuellt. Du kan också konfigurera i principen för en dataset så att ett segment som körs när ett fel uppstår. När ett segment körs på något sätt, måste du kontrollera att samma data läses oavsett hur många gånger ett segment körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och finjustering
Se [kopiera aktivitet prestanda och justera guiden](data-factory-copy-activity-performance.md) vill veta mer om viktiga faktorer som påverkan prestanda för flytt av data (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
