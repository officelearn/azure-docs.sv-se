---
title: Flytta data från Cassandra med Data Factory
description: Lär dig mer om hur du flyttar data från en lokal Cassandra-databas med Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f96680f1ea91434c84d6606e3637c68c1cb5a84
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991509"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Flytta data från en lokal Cassandra-databas med Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-onprem-cassandra-connector.md)
> * [Version 2 (aktuell version)](../connector-cassandra.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser [du Cassandra-anslutning i V2](../connector-cassandra.md).

I den här artikeln beskrivs hur du använder kopieringsaktiviteten i Azure Data Factory för att flytta data från en lokal Cassandra-databas. Den bygger på [datarörelseaktiviteter](data-factory-data-movement-activities.md) artikeln, som presenterar en allmän översikt över data förflyttning med kopian aktivitet.

Du kan kopiera data från ett lokalt Cassandra-datalager till alla sink-datalager som stöds. En lista över datalager som stöds som mottagare av kopieringsaktiviteten finns i tabellen [Datalager som stöds.](data-factory-data-movement-activities.md#supported-data-stores-and-formats) Datafabriken stöder för närvarande endast att flytta data från ett Cassandra-datalager till andra datalager, men inte för att flytta data från andra datalager till ett Cassandra-datalager.

## <a name="supported-versions"></a>Versioner som stöds
Cassandra-kontakten stöder följande versioner av Cassandra: 2.x och 3.x. För aktiviteter som körs på Självvärderade Integration Runtime stöds Cassandra 3.x sedan IR version 3.7 och senare.

## <a name="prerequisites"></a>Krav
För att Azure Data Factory-tjänsten ska kunna ansluta till din lokala Cassandra-databas måste du installera en datahanteringsgateway på samma dator som är värd för databasen eller på en separat dator för att undvika att konkurrera om resurser med databasen. Data Management Gateway är en komponent som ansluter lokala datakällor till molntjänster på ett säkert och hanterat sätt. Mer information om Data Management Gateway finns i artikeln Data [Management Gateway.](data-factory-data-management-gateway.md) Se [Flytta data från lokal till molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner om hur du konfigurerar gatewayen en datapipeline för att flytta data.

Du måste använda gatewayen för att ansluta till en Cassandra-databas även om databasen finns i molnet, till exempel på en Azure IaaS VM. Y Du kan ha gatewayen på samma virtuella dator som är värd för databasen eller på en separat virtuell dator så länge gatewayen kan ansluta till databasen.

När du installerar gatewayen installeras automatiskt en Microsoft Cassandra ODBC-drivrutin som används för att ansluta till Cassandra-databasen. Därför behöver du inte installera någon drivrutin manuellt på gateway-datorn när du kopierar data från Cassandra-databasen.

> [!NOTE]
> Se [Felsöka gatewayproblem](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) för tips om felsökning av anslutnings-/gatewayrelaterade problem.

## <a name="getting-started"></a>Komma igång
Du kan skapa en pipeline med en kopieringsaktivitet som flyttar data från ett lokalt Cassandra-datalager med hjälp av olika verktyg/API:er.

- Det enklaste sättet att skapa en pipeline är att använda **kopieringsguiden**. Se [självstudiekurs: Skapa en pipeline med hjälp av kopieringsguiden](data-factory-copy-data-wizard-tutorial.md) för en snabb genomgång när du skapar en pipeline med hjälp av guiden Kopiera data.
- Du kan också använda följande verktyg för att skapa en pipeline: **Visual Studio,** **Azure PowerShell,** **Azure Resource Manager-mall,** **.NET API**och REST **API**. Se [Kopiera aktivitetshandledning](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för steg-för-steg-instruktioner för att skapa en pipeline med en kopieringsaktivitet.

Oavsett om du använder verktygen eller API:erna utför du följande steg för att skapa en pipeline som flyttar data från ett källdatalager till ett sink-datalager:

1. Skapa **länkade tjänster** för att länka in- och utdatalager till datafabriken.
2. Skapa **datauppsättningar** för att representera in- och utdata för kopieringen.
3. Skapa en **pipeline** med en kopieringsaktivitet som tar en datauppsättning som indata och en datauppsättning som utdata.

När du använder guiden skapas JSON-definitioner för dessa datafabrikentiteter (länkade tjänster, datauppsättningar och pipelinen) automatiskt åt dig. När du använder verktyg/API:er (förutom .NET API) definierar du dessa datafabrikentiteter med hjälp av JSON-formatet. Ett exempel på ett exempel med JSON-definitioner för datafabrikentiteter som används för att kopiera data från ett lokalt Cassandra-datalager finns i [JSON-exempel: Kopiera data från Cassandra till Azure Blob](#json-example-copy-data-from-cassandra-to-azure-blob) i den här artikeln.

I följande avsnitt finns information om JSON-egenskaper som används för att definiera datafabrikentiteter som är specifika för ett Cassandra-datalager:

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper
I följande tabell beskrivs beskrivning av JSON-element som är specifika för Cassandra-länkad tjänst.

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| typ |Egenskapen Type måste ställas in **på: OnPremisesCassandra** |Ja |
| värd |En eller flera IP-adresser eller värdnamn för Cassandra-servrar.<br/><br/>Ange en kommaavgränsad lista med IP-adresser eller värdnamn som ska anslutas till alla servrar samtidigt. |Ja |
| port |TCP-porten som Cassandra-servern använder för att lyssna efter klientanslutningar. |Nej, standardvärde: 9042 |
| authenticationType |Grundläggande eller anonym |Ja |
| användarnamn |Ange användarnamn för användarkontot. |Ja, om authenticationType är inställt på Basic. |
| password |Ange lösenord för användarkontot. |Ja, om authenticationType är inställt på Basic. |
| gatewayName (gatewayName) |Namnet på gatewayen som används för att ansluta till den lokala Cassandra-databasen. |Ja |
| krypteradKnuren |Autentiseringsuppgifter krypteras av gatewayen. |Inga |

>[!NOTE]
>För närvarande stöds inte anslutning till Cassandra med TLS.

## <a name="dataset-properties"></a>Egenskaper för datamängd
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera datauppsättningar finns i artikeln [Skapa datauppsättningar.](data-factory-create-datasets.md) Avsnitt som struktur, tillgänglighet och princip för en datauppsättning JSON är liknande för alla datauppsättningstyper (Azure SQL, Azure blob, Azure-tabell osv.).

Avsnittet **typeProperties** är olika för varje typ av datauppsättning och ger information om platsen för data i datalagret. Avsnittet typeProperties för datauppsättning av typen **CassandraTable** har följande egenskaper

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| nyckelutrymme |Namn på nyckelutrymmet eller schemat i Cassandra-databasen. |Ja (Om **frågan** för **CassandraSource** inte har definierats). |
| tableName |Namnet på tabellen i Cassandra-databasen. |Ja (Om **frågan** för **CassandraSource** inte har definierats). |

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet
En fullständig lista över avsnitt & egenskaper som är tillgängliga för att definiera aktiviteter finns i artikeln [Skapa pipelines.](data-factory-create-pipelines.md) Egenskaper som namn, beskrivning, indata- och utdatatabeller och princip är tillgängliga för alla typer av aktiviteter.

Medan egenskaper som är tillgängliga i avsnittet typeProperties i aktiviteten varierar med varje aktivitetstyp. För kopieringsaktivitet varierar de beroende på vilka typer av källor och sänkor som finns.

När källan är av typen **CassandraSource**är följande egenskaper tillgängliga i avsnittet typeProperties:

| Egenskap | Beskrivning | Tillåtna värden | Krävs |
| --- | --- | --- | --- |
| DocumentDB |Använd den anpassade frågan för att läsa data. |SQL-92-fråga eller CQL-fråga. Se [CQL-referens](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>När du använder SQL-frågan anger du **namnet keyspace name.table** som ska representera den tabell som du vill fråga. |Nej (om tableName och keyspace på datauppsättning definieras). |
| konsekvensNivå |Konsekvensnivån anger hur många repliker som måste svara på en läsbegäran innan data returneras till klientprogrammet. Cassandra kontrollerar det angivna antalet repliker för data för att uppfylla läsbegäran. |ETT, TVÅ, TRE, KVORUM, ALLA, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Mer information [finns i Konfigurera datakonsekvens.](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) |Nej. Standardvärdet är ETT. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON exempel: Kopiera data från Cassandra till Azure Blob
Det här exemplet innehåller exempel på JSON-definitioner som du kan använda för att skapa en pipeline med hjälp av [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Den visar hur du kopierar data från en lokal Cassandra-databas till en Azure Blob Storage. Data kan dock kopieras till någon av de diskhoar som anges [här](data-factory-data-movement-activities.md#supported-data-stores-and-formats) med hjälp av kopieringsaktiviteten i Azure Data Factory.

> [!IMPORTANT]
> Det här exemplet innehåller JSON-utdrag. Den innehåller inte steg-för-steg-instruktioner för att skapa datafabriken. Se [flytta data mellan lokala platser och molnartikel](data-factory-move-data-between-onprem-and-cloud.md) för steg-för-steg-instruktioner.

Exemplet har följande datafabriksenheter:

* En länkad tjänst av typen [OnPremisesCassandra](#linked-service-properties).
* En länkad tjänst av typen [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* En [indatauppsättning](data-factory-create-datasets.md) av typen [CassandraTable](#dataset-properties).
* En [utdatauppsättning](data-factory-create-datasets.md) av typen [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* En [pipeline](data-factory-create-pipelines.md) med kopieringsaktivitet som använder [CassandraSource](#copy-activity-properties) och [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

**Cassandra länkad tjänst:**

I det här exemplet används den **Cassandra-länkade** tjänsten. Se [avsnittet Cassandra-länkad tjänst](#linked-service-properties) för de egenskaper som stöds av den länkade tjänsten.

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

**Azure Storage-länkad tjänst:**

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

**Cassandra indatauppsättning:**

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

Om du ställer **in externt** för **true** informerar datafabrikstjänsten om att datauppsättningen är extern till datafabriken och inte produceras av en aktivitet i datafabriken.

**Azure Blob-utdatauppsättning:**

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

**Kopiera aktivitet i en pipeline med Cassandra-källa och Blob-diskho:**

Pipelinen innehåller en kopieringsaktivitet som är konfigurerad för att använda in- och utdatauppsättningar och som är schemalagd att köras varje timme. I JSON-definitionen för pipelinen är **källtypen** inställd på **CassandraSource** och **sink-typen** är inställd på **BlobSink**.

Se Egenskaper för [RelationsSource-typ](#copy-activity-properties) för listan över egenskaper som stöds av Relationskällan.

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

### <a name="type-mapping-for-cassandra"></a>Typmappning för Cassandra
| Cassandra Typ | .NET-baserad typ |
| --- | --- |
| ASCII |Sträng |
| BIGINT |Int64 |
| Blob |Byte[] |
| Boolean |Boolesk |
| DECIMAL |Decimal |
| DOUBLE |Double |
| Flyta |Enkel |
| Inet |Sträng |
| INT |Int32 |
| TEXT |Sträng |
| TIMESTAMP |DateTime |
| TIMEUUID (PÅ LÄNGE) |GUID |
| Uuid |GUID |
| VARCHAR |Sträng |
| VARINT (OLIKA) |Decimal |

> [!NOTE]
> För samlingstyper (karta, uppsättning, lista osv.) finns [i Samlingstyper för Arbete med Cassandra med hjälp av avsnittet virtual table.](#work-with-collections-using-virtual-table)
>
> Användardefinierade typer stöds inte.
>
> Längden på längderna binärkolumn och strängkolumn kan inte vara större än 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Arbeta med samlingar med hjälp av virtuell tabell
Azure Data Factory använder en inbyggd ODBC-drivrutin för att ansluta till och kopiera data från cassandra-databasen. För samlingstyper, inklusive karta, uppsättning och lista, renormalizes drivrutinen till motsvarande virtuella tabeller. Om en tabell innehåller några samlingskolumner genererar drivrutinen följande virtuella tabeller:

* En **bastabell**som innehåller samma data som den verkliga tabellen utom samlingskolumnerna. Bastabellen använder samma namn som den verkliga tabellen som den representerar.
* En **virtuell tabell** för varje samlingskolumn, som utökar de kapslade data. De virtuella tabeller som representerar samlingar namnges med namnet på den verkliga tabellen, en avgränsare "*vt*" och namnet på kolumnen.

Virtuella tabeller refererar till data i den verkliga tabellen, vilket gör att drivrutinen kan komma åt denormaliserade data. Mer information finns i avsnittet Exempel. Du kan komma åt innehållet i Cassandra-samlingar genom att fråga och ansluta till de virtuella tabellerna.

Du kan använda [kopieringsguiden](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) för att intuitivt visa listan över tabeller i Cassandra-databasen, inklusive de virtuella tabellerna, och förhandsgranska data inuti. Du kan också skapa en fråga i kopieringsguiden och validera för att se resultatet.

### <a name="example"></a>Exempel
Följande "Exempeltabell" är till exempel en Cassandra-databastabell som innehåller en heltalsnyckelkolumn med namnet "pk_int", en textkolumn med namnet värde, en listkolumn, en kartkolumn och en uppsättningskolumn (med namnet "StringSet").

| pk_int | Värde | Visa lista | Karta | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"provvärde 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"provvärde 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Drivrutinen skulle generera flera virtuella tabeller för att representera den här enstaka tabellen. Kolumnerna för sekundärnyckel i de virtuella tabellerna refererar till primärnyckelkolumnerna i den verkliga tabellen och anger vilken verklig tabellrad den virtuella tabellraden motsvarar.

Den första virtuella tabellen är bastabellen "ExampleTable" visas i följande tabell. Bastabellen innehåller samma data som den ursprungliga databastabellen förutom samlingarna, som utelämnas från den här tabellen och expanderas i andra virtuella tabeller.

| pk_int | Värde |
| --- | --- |
| 1 |"provvärde 1" |
| 3 |"provvärde 3" |

I följande tabeller visas de virtuella tabeller som renormalize data från kolumnerna Lista, Karta och StringSet. Kolumnerna med namn som slutar med "_index" eller "_key" anger datas position i den ursprungliga listan eller kartan. Kolumnerna med namn som slutar med "_value" innehåller expanderade data från samlingen.

#### <a name="table-exampletable_vt_list"></a>Tabell "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabell "ExampleTable_vt_Map":
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabell "ExampleTable_vt_StringSet":
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Kartkälla för att sänka kolumner
Mer information om hur du mappar kolumner i källdatauppsättning till kolumner i sink-datauppsättning finns [i Mappa datauppsättningskolumner i Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Repeterbar läsning från relationskällor
När du kopierar data från relationsdatalager bör du tänka på repeterbarhet för att undvika oavsiktliga resultat. I Azure Data Factory kan du köra ett segment manuellt igen. Du kan också konfigurera återförsöksprincipen för en datauppsättning så att ett segment körs igen när ett fel inträffar. När ett segment körs på något sätt måste du se till att samma data läss oavsett hur många gånger ett segment körs. Se [Repeterbar läsning från relationskällor](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Prestanda och justering
Se [Kopiera aktivitetsprestanda & justeringsguide](data-factory-copy-activity-performance.md) om du vill veta mer om viktiga faktorer som påverkar prestanda för datarörelser (kopieringsaktivitet) i Azure Data Factory och olika sätt att optimera den.
