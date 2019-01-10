---
title: Flytta data från Cassandra med Data Factory | Microsoft Docs
description: Läs mer om hur du flyttar data från en lokal Cassandra-databas med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: dd90834a2e112effbfd6876b84dfe8b3ca87fcf3
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015652"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Flytta data från en lokal Cassandra-databas med Azure Data Factory
> [!div class="op_single_selector" title1="Välj vilken version av Data Factory-tjänsten du använder:"]
> * [Version 1](data-factory-onprem-cassandra-connector.md)
> * [Version 2 (aktuell version)](../connector-cassandra.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Cassandra-anslutning i V2](../connector-cassandra.md).

Den här artikeln förklarar hur du använder Kopieringsaktivitet i Azure Data Factory för att flytta data från en lokal Cassandra-databas. Den bygger på den [Dataförflyttningsaktiviteter](data-factory-data-movement-activities.md) artikel som anger en allmän översikt över dataförflyttning med kopieringsaktiviteten.

Du kan kopiera data från ett datalager för lokal Cassandra till alla datalager för mottagare som stöds. En lista över datalager som stöds som mottagare av Kopieringsaktivitet finns i den [datalager som stöds](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabell. Data factory stöder för närvarande endast flyttar data från ett datalager för Cassandra till datalager, men inte för att flytta data från andra datalager till ett Cassandra-datalager. 

## <a name="supported-versions"></a>Versioner som stöds
Cassandra-anslutningsappen stöder följande versioner av Cassandra: 2.x och 3.x. För aktiviteter som körs på lokal Integration Runtime, Cassandra 3.x stöds sedan IR version 3.7 och senare.

## <a name="prerequisites"></a>Förutsättningar
Du måste installera en Gateway för datahantering på samma dator som är värd för databasen eller på en separat dator att undvika konkurrerar om resurser med databasen för Azure Data Factory-tjänsten för att kunna ansluta till din lokala Cassandra-databas. Data Management Gateway är en komponent som ansluter till lokala datakällor till molntjänster på ett säkert och hanterat sätt. Se [Data Management Gateway](data-factory-data-management-gateway.md) nedan för information om Data Management Gateway. Se [flytta data från lokal plats till molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner om hur du konfigurerar gatewayen en datapipeline att flytta data.

Du måste använda gatewayen för att ansluta till en Cassandra-databas, även om databasen finns i molnet, till exempel på en Azure IaaS-VM. Y du kan ha gatewayen på samma virtuella dator som är värd för databasen eller på en separat virtuell dator så länge som gatewayen kan ansluta till databasen.  

När du installerar gatewayen installeras automatiskt en Microsoft Cassandra ODBC-drivrutin som används för att ansluta till Cassandra-databasen. Därför behöver du inte manuellt installera en drivrutin på gatewaydatorn när du kopierar data från Cassandra-databasen. 

> [!NOTE]
> Se [felsöka problem med gateway](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tips om hur du felsöker anslutning/gateway-relaterade problem.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en Kopieringsaktivitet som flyttar data från ett datalager för lokal Cassandra med hjälp av olika verktyg/API: er. 

- Det enklaste sättet att skapa en pipeline är att använda den **Kopieringsguiden**. Se [självstudien: Skapa en pipeline med Copy Wizard](data-factory-copy-data-wizard-tutorial.md) en snabb genomgång om hur du skapar en pipeline med hjälp av guiden Kopiera data. 
- Du kan också använda följande verktyg för att skapa en pipeline: **Azure-portalen**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-mall**, **.NET API**, och  **REST-API**. Se [kopiera aktivitet självstudien](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för stegvisa instruktioner för att skapa en pipeline med en Kopieringsaktivitet. 

Om du använder verktyg eller API: er kan utföra du följande steg för att skapa en pipeline som flyttar data från källans datalager till mottagarens datalager:

1. Skapa **länkade tjänster** länka inkommande och utgående data du lagrar till din datafabrik.
2. Skapa **datauppsättningar** som representerar inkommande och utgående data för kopieringen. 
3. Skapa en **pipeline** med en Kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata. 

När du använder guiden skapas JSON-definitioner för dessa Data Factory-entiteter (länkade tjänster, datauppsättningar och pipeline) automatiskt åt dig. När du använder Verktyg/API: er (med undantag för .NET-API) kan definiera du dessa Data Factory-entiteter med hjälp av JSON-format.  Ett exempel med JSON-definitioner för Data Factory-entiteter som används för att kopiera data från ett datalager för lokal Cassandra finns [JSON-exempel: Kopiera data från Cassandra till Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) i den här artikeln. 

Följande avsnitt innehåller information om JSON-egenskaper som används för att definiera Data Factory-entiteter som är specifika för ett Cassandra-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
Följande tabell innehåller en beskrivning för JSON-element som är specifika för Cassandra länkad tjänst.

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Type-egenskapen måste anges till: **OnPremisesCassandra** |Ja |
| värd |En eller flera IP-adresser eller värdnamn för Cassandra-servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn för att ansluta till alla servrar samtidigt. |Ja |
| port |TCP-port som Cassandra-servern använder för att lyssna efter klientanslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Grundläggande eller anonym |Ja |
| användarnamn |Ange användarnamn för användarkontot. |Ja, om authenticationType anges till Basic. |
| lösenord |Ange lösenordet för användarkontot. |Ja, om authenticationType anges till Basic. |
| gatewayName |Namnet på den gateway som används för att ansluta till den lokala Cassandra-databasen. |Ja |
| encryptedCredential |Autentiseringsuppgifter har krypterats av gatewayen. |Nej |

>[!NOTE]
>För närvarande stöds inte anslutning till Cassandra med hjälp av SSL.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i den [skapar datauppsättningar](data-factory-create-datasets.md) artikeln. Avsnitt som struktur, tillgänglighet och princip av en datauppsättnings-JSON är liknande för alla datauppsättningstyper av (Azure SQL, Azure-blob, Azure-tabell osv.).

Den **typeProperties** avsnittet är olika för varje typ av datauppsättning och tillhandahåller information om platsen för data i datalagret. TypeProperties avsnittet för datauppsättningen av typen **CassandraTable** har följande egenskaper

| Egenskap  | Beskrivning | Krävs |
| --- | --- | --- |
| keyspace |Namnet på keyspace eller schema i Cassandra-databasen. |Ja (om **fråga** för **CassandraSource** har inte definierats). |
| tableName |Namnet på tabellen i Cassandra-databas. |Ja (om **fråga** för **CassandraSource** har inte definierats). |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i den [skapa Pipelines](data-factory-create-pipelines.md) artikeln. Egenskaper, till exempel namn, beskrivning, indata och utdata tabeller och principen är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties aktivitetens varierar med varje aktivitetstyp av. För kopieringsaktiviteten variera de beroende på vilka typer av källor och mottagare.

När källan är av typen **CassandraSource**, följande egenskaper är tillgängliga i avsnittet typeProperties:

| Egenskap  | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd anpassad fråga för att läsa data. |SQL-92 fråga eller CQL-fråga. Se [CQL referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-fråga, ange **keyspace name.table namn** som representerar den tabell som du vill fråga. |Nej (om tabellnamn och keyspace för datauppsättningen har definierats). |
| consistencyLevel |Konsekvensnivån som anger hur många kopior måste svara på en läsbegäran innan det returneras data till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data för att tillgodose läsförfrågan. |EN, TVÅ, TRE, KVORUM, ALL, LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Se [konfigurera datakonsekvens](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) mer information. |Nej. Standardvärdet är en. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-exempel: Kopiera data från Cassandra till Azure Blob
Det här exemplet innehåller exempel JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en lokal Cassandra-databas till Azure Blob Storage. Dock datan kan kopieras till någon av de mottagare som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) använda Kopieringsaktivitet i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-kodfragment. Stegvisa instruktioner för att skapa data factory omfattas inte. Se [flytta data mellan lokala platser och molnet](data-factory-move-data-between-onprem-and-cloud.md) artikeln stegvisa instruktioner.

Exemplet har följande data factory-entiteter:

* En länkad tjänst av typen [OnPremisesCassandra](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Indata [datauppsättning](data-factory-create-datasets.md) av typen [CassandraTable](#dataset-properties).
* Utdata [datauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med en Kopieringsaktivitet som använder [CassandraSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra-länkad tjänst:**

Det här exemplet används den **Cassandra** länkad tjänst. Se [Cassandra länkad tjänst](#linked-service-properties) avsnittet för egenskaper som stöds av den här länkade tjänsten.  

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

**Länkad Azure Storage-tjänst:**

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

**Indatauppsättning för Cassandra:**

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

Ange **externa** till **SANT** informerar Data Factory-tjänsten att datauppsättningen är extern till datafabriken och inte kommer från en aktivitet i data factory.

**Utdatauppsättning för Azure Blob:**

Data skrivs till en ny blob varje timme (frequency: timme, intervall: 1).

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

**Kopiera aktivitet i en pipeline med Cassandra käll- och Blob-mottagare:**

Pipelinen innehåller en Kopieringsaktivitet som har konfigurerats för användning av in- och utdatauppsättningar och är schemalagd att köras varje timme. I pipeline-JSON-definitionen i **källa** är **CassandraSource** och **mottagare** är **BlobSink**.

Se [RelationalSource typegenskaperna](#copy-activity-properties) lista över egenskaper som stöds av RelationalSource.

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
| Cassandra-typ | .NET-baserade typ |
| --- | --- |
| ASCII |Sträng |
| BIGINT |Int64 |
| BLOB |Byte] |
| BOOLESKT VÄRDE |Boolesk |
| DECIMALTAL |Decimal |
| DOUBLE-VÄRDE |Double-värde |
| FLYTTAL |Enkel |
| INET |Sträng |
| INT |Int32 |
| TEXT |Sträng |
| TIDSSTÄMPEL |DateTime |
| TIMEUUID |GUID |
| UUID |GUID |
| VARCHAR |Sträng |
| VARINT |Decimal |

> [!NOTE]
> Samlingen finns typer (karta, set, lista, osv.), [arbeta med Cassandra samlingstyper med hjälp av virtuella tabellen](#work-with-collections-using-virtual-table) avsnittet.
>
> Användardefinierade typer stöds inte.
>
> Längden på Binär kolumn och strängkolumn längd får inte vara större än 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med virtuella tabellen
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från Cassandra-databas. För samlingstyper inklusive kartan, uppsättning och lista, renormalizes drivrutinen data till motsvarande virtuella tabeller. Om en tabell innehåller några kolumner för samlingen, genererar drivrutinen mer specifikt kan följande virtuella tabeller:

* En **bastabellen**, som innehåller samma data som den verkliga tabellen utom samling kolumner. Bastabellen använder samma namn som den verkliga tabell som representerar.
* En **virtuella tabellen** för varje samling-kolumn som utökar den kapslade data. Virtuella tabeller som representerar samlingar namnges med namnet på tabellen verkliga avgränsare ”*vt*” och namnet på kolumnen.

Virtuella tabellerna hänvisar till data i tabellen verkliga aktiverar drivrutinen att komma åt den Avnormaliserade data. Se exempel avsnittet för information. Du kan komma åt innehållet i Cassandra samlingar genom att fråga och ansluta till virtuella tabeller.

Du kan använda den [Kopieringsguiden](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) att visa en lista över tabeller i Cassandra-databas, inklusive virtuella tabeller intuitivt och förhandsgranska data i. Du kan också skapa en fråga i guiden Kopiera och validera om du vill se resultatet.

### <a name="example"></a>Exempel
Till exempel är följande ”ExampleTable” en Cassandra-databastabell som innehåller ett heltal primärnyckelkolumnen med namnet ”pk_int”, en textkolumn namngivet värde, en kolumn, en karta kolumn och en uppsättning kolumn (med namnet ”StringSet”).

| pk_int | Värde | Visa lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |”exempelvärde 1” |["1", "2", "3"] |{”S1”: ”a”, ”S2”: ”b”} |{”A”, ”B”, ”C”} |
| 3 |”exempelvärde 3” |["100", "101", "102", "105"] |{”S1”: ”t”} |{”A”, ”E”} |

Drivrutinen skulle generera flera virtuella tabeller som representerar en enda tabell. Sekundärnyckelskolumnerna i virtuella tabeller refererar till primärnyckelskolumnerna i verkliga tabellen och anger vilka verkliga tabellrad som den virtuella tabellraden motsvarar.

Den första virtuella tabellen är bastabellen med namnet ”ExampleTable” visas i följande tabell. Bastabellen innehåller samma data som den ursprungliga databastabellen förutom de samlingar som är utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

| pk_int | Värde |
| --- | --- |
| 1 |”exempelvärde 1” |
| 3 |”exempelvärde 3” |

Följande tabeller visar de virtuella tabeller som renormera data från listan och kartan StringSet kolumner. Kolumner med namn som slutar med ”_index” eller ”_nyckel” anger data i den ursprungliga listan eller kartan position. Kolumner med namn som slutar med ”_value” innehåller utökade data från samlingen.

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
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>Tabell ”ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Kartkälla till kolumner för mottagare
Mer information om mappning av kolumner i datauppsättningen för källan till kolumner i datauppsättning för mottagare, se [mappning av kolumner för datauppsättningar i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Upprepbar läsning från relationella källor
Kom ihåg att undvika oväntade resultat repeterbarhet när kopiera data från relationsdata lagras. I Azure Data Factory kan du köra en sektor manuellt. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att en sektor som körs när ett fel uppstår. När ett segment ska köras på nytt på något sätt, måste du se till att samma data läses oavsett hur många gånger som en sektor körs. Se [Repeatable läsa från relationella källor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [kopiera aktivitet prestanda- och Justeringsguide](data-factory-copy-activity-performance.md) att lära dig om viktiga faktorer att påverka prestandan för dataförflyttning (Kopieringsaktiviteten) i Azure Data Factory och olika sätt att optimera den.
