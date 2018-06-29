---
title: Schemamappning i en Kopieringsaktivitet | Microsoft Docs
description: Läs mer om hur kopieringsaktiviteten i Azure Data Factory mappar scheman och datatyper från källdata för att registrera data vid kopiering av data.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 338df0e258f66b6639e59a4fe31b6cfb6c283dd3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045535"
---
# <a name="schema-mapping-in-copy-activity"></a>Schemamappning i en Kopieringsaktivitet
Den här artikeln beskrivs hur Azure Data Factory-kopieringsaktiviteten hanterar schemamappning och datatypmappningen från källdata till sink data när kopiera data.

## <a name="column-mapping"></a>Kolumnmappningen

Som standard kopieringsaktiviteten **mappa källdata till mottagare av kolumnnamn**, såvida inte [explicit kolumnmappningen](#explicit-column-mapping) har konfigurerats. Mer specifikt kopieringsaktiviteten:

1. Läsa data från källan och fastställa datakällans schema

    * För datakällor med fördefinierade schemat i data store/filformat, till exempel databasfiler med metadata (Avro/ORC/parkettgolv/Text med rubriken), extraheras datakällans schema från frågemetadata resultatet eller filen.
    * För datakällor med flexibelt schema, till exempel Azure tabellen/Cosmos DB härleda datakällans schema från frågeresultatet. Du kan skriva över den genom att tillhandahålla ”strukturen” i dataset.
    * Textfilen utan huvud skapas standardkolumnvärdena med mönstret ”Prop_0”, ”Prop_1”... Du kan skriva över den genom att tillhandahålla ”strukturen” i dataset.
    * Du måste ange schemainformation i avsnittet ”struktur” dataset för Dynamics källa.

2. Tillämpa explicit kolumnmappningen om anges.

3. Skriva data till mottagare

    * För datalager med fördefinierade schema skrivs data till kolumner med samma namn.
    * Datalager utan fasta schemat och filformat genereras namn/kolumnmetadata baserat på käll-schema.

### <a name="explicit-column-mapping"></a>Explicit kolumnmappning

Du kan ange **columnMappings** i den **typeProperties** avsnittet för aktiviteten kopiera sköta explicit kolumnmappningen. I det här scenariot krävs ”struktur” avsnittet för både inkommande och utgående datauppsättningar. Stöd för mappning av kolumn **alla mappnings- eller delmängd med kolumner i datauppsättningen källa ”struktur” för alla kolumner i datauppsättningen sink ”struktur”**. Följande är felvillkor som resulterar i ett undantag:

* Källdata lagra frågan resultatet inte har ett kolumnnamn som anges i avsnittet ”struktur” inkommande dataset.
* Sink-datalagret (med fördefinierade schema) har inte ett kolumnnamn som anges i avsnittet ”struktur” utdata dataset.
* Färre kolumner eller fler kolumner i ”struktur” sink dataset än anges i mappningen.
* Duplicera mappning.

#### <a name="explicit-column-mapping-example"></a>Explicit kolumnen Mappningsexempel

I det här exemplet indatatabellen har en struktur och pekar på en tabell i en lokal SQL-databas.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

I det här exemplet utdatatabellen har en struktur och pekar på en tabell i Azure SQL-databas.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Följande JSON definierar en kopia aktivitet i en pipeline. Kolumner från källan som är mappade till kolumnerna i kanalmottagare (**columnMappings**) med hjälp av den **översättare** egenskapen.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Om du använder syntaxen för `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` om du vill ange kolumnmappningen den fortfarande stöds som-är.

**Kolumnen mappning flöde:**

![Kolumnen mappning flöde](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="data-type-mapping"></a>Datatypsmappningen

Kopieringsaktiviteten utför typer av datakällor om du vill registrera typer mappning med följande metod i steg 2:

1. Konvertera från interna källtyper till Azure Data Factory tillfälliga datatyper
2. Konvertera från Azure Data Factory tillfälliga-datatyper till interna Mottagartypen

Du kan hitta mappningen mellan inbyggd typ till tillfälliga typ i avsnittet ”datatypen mappning” i avsnittet för varje koppling.

### <a name="supported-data-types"></a>Datatyper som stöds

Data Factory stöder följande datatyper av mellanliggande: du kan ange under värden när de tillhandahåller typinformation i [datauppsättningsstrukturen](concepts-datasets-linked-services.md#dataset-structure) konfiguration:

* Byte]
* Boolesk
* DateTime
* DateTimeOffset
* Decimal
* Dubbel
* GUID
* Int16
* Int32
* Int64
* Enkel
* Sträng
* Tidsintervall

### <a name="explicit-data-type-conversion"></a>Explicit konverteringen av datatyp

När data kopieras till data lagras med fasta schemat, till exempel SQL Server/Oracle, när källa och mottagare har en annan typ i samma kolumn anges explicit typkonvertering i käll-sida:

* För källa, till exempel CSV/Avro typen konvertering anges via datakällans struktur med fullständig kolumnlista (källa på klientsidan namn och en mottagare sida kolumntypen)
* För relationella källa (till exempel SQL/Oracle), bör typkonvertering uppnås genom att explicit typ. omvandling i frågeuttrycket.

## <a name="when-to-specify-dataset-structure"></a>När du ska ange ”datauppsättningsstrukturen”

I nedan scenarier krävs ”struktur” i dataset:

* Tillämpa [explicit konverteringen av datatyp](#explicit-data-type-conversion) för filen källor vid kopiering (inkommande dataset)
* Tillämpa [explicit kolumnmappningen](#explicit-column-mapping) vid kopiering (både inkommande och utgående dataset)
* Kopiera från Dynamics 365 / CRM källa (inkommande dataset)
* Kopiera till Cosmos DB som kapslade objekt när källan inte är JSON-filer (datamängd för utdata)

I nedan scenarier föreslås ”struktur” i dataset:

* Kopiera från textfil utan huvud (inkommande dataset). Du kan ange kolumnnamn för textfil justera med motsvarande sink-kolumner, spara från att tillhandahålla explicit kolumnmappningen.
* Kopiera från data lagrar med flexibelt schema, till exempel Azure-tabell/Cosmos DB (inkommande dataset), för att garantera att förväntade data (kolumner) kopieras över i stället för att låta kopiera aktivitet Skapa schema baserat på översta raderna under varje aktivitet körs.


## <a name="next-steps"></a>Nästa steg
Finns de andra Kopieringsaktiviteten artiklarna:

- [Aktiviteten-kopia – översikt](copy-activity-overview.md)
- [Kopiera aktivitet feltolerans](copy-activity-fault-tolerance.md)
- [Kopiera aktivitet prestanda](copy-activity-performance.md)
