---
title: Flytta data med hjälp av Kopieringsaktivitet | Microsoft Docs
description: 'Lär dig mer om dataförflyttning i Data Factory-pipelines: migrering av data mellan molnet och mellan en lokal databas och ett molnlager. Använda Kopieringsaktivitet.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3d0a3014fa224d6b5c85142e492afb0679f9f0b1
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014689"
---
# <a name="move-data-by-using-copy-activity"></a>Flytta data med hjälp av Kopieringsaktivitet
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-data-movement-activities.md)
> * [Version 2 (aktuell version)](../copy-activity-overview.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [Kopieringsaktivitet i V2](../copy-activity-overview.md).

## <a name="overview"></a>Översikt
I Azure Data Factory kan du använda Kopieringsaktivitet för att kopiera data mellan lokala och molnbaserade datalager. När data kopieras kan den ytterligare omvandlas och analyseras. Du kan också använda Kopieringsaktivitet för att publicera omvandling och analysresultat för business intelligence (BI) och förbrukning av programmet.

![Rollen för Kopieringsaktivitet](media/data-factory-data-movement-activities/copy-activity.png)

Kopieringsaktiviteten drivs av ett säkert, tillförlitligt och skalbart, och [globalt tillgänglig tjänst](#global). Den här artikeln innehåller information om dataförflyttning i Data Factory och Kopieringsaktiviteten.

Först ska vi se hur migrering av data sker mellan två molndatalager och mellan ett lokalt datalager och ett molndatalager.

> [!NOTE]
> Läs mer om aktiviteter som i allmänhet i [förstå pipelines och aktiviteter](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiera data mellan två molndatalager
När både käll-och mottagare finns i molnet, går Kopieringsaktivitet igenom följande steg för att kopiera data från källan till mottagaren. Den tjänst som används av Kopieringsaktiviteten:

1. Läser data från källdatalagret.
2. Utför serialisering/deserialisering, komprimering/dekomprimering, kolumn mappning och typkonvertering. Dessa åtgärder baserat på konfigurationerna för datauppsättningen för indata, utdata datauppsättningen och Kopieringsaktivitet sker.
3. Skriver data till måldatalagret.

Tjänsten väljer automatiskt regionen optimala för att utföra dataförflyttning. Den här regionen är vanligtvis den närmast datalager för mottagare.

![Moln-till-moln-kopia](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt datalager och ett molndatalager
Om du vill flytta på ett säkert sätt data mellan ett lokalt datalager och ett molndatalager, installera Data Management Gateway på din lokala dator. Data Management Gateway är en agent som möjliggör hybriddataflytt och bearbetning. Du kan installera den på samma dator som den datalagringen själva, eller på en separat dator som har åtkomst till datalagret.

I det här scenariot Data Management Gateway utför serialisering/deserialisering, komprimering/dekomprimering, kolumnen mappning och typkonvertering. Data flödar inte via Azure Data Factory-tjänsten. Data Management Gateway skriver i stället direkt data till målarkiv.

![Kopiera på-plats-till-moln](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Se [flytta data mellan lokala och molnbaserade datalager](data-factory-move-data-between-onprem-and-cloud.md) för en introduktion och en genomgång. Se [Data Management Gateway](data-factory-data-management-gateway.md) detaljerad information om den här agenten.

Du kan också flytta data från/till stöd för datalager som finns på Azure IaaS-datorer (VM) med hjälp av Data Management Gateway. I det här fallet kan du installera Data Management Gateway på samma Virtuella datalager själva eller på en separat virtuell dator som har åtkomst till datalagret.

## <a name="supported-data-stores-and-formats"></a>Datalager som stöds och format
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

> [!NOTE] 
> Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder kan du använda en **anpassad aktivitet** i Data Factory med din egen logik för att kopiera/flytta data. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="supported-file-formats"></a>Filformat som stöds
Du kan använda Kopieringsaktivitet som **kopiera filer som – är** mellan två filbaserade data kan du hoppa över den [formatera avsnitt](data-factory-create-datasets.md) i både inkommande och utgående datamängd definitionerna. Data kopieras effektivt utan någon serialisering/deserialisering.

Kopieringsaktivitet också läser från och skriver till filer i angivna format: **Text, JSON, Avro, ORC och Parquet**, och komprimerings-codec **GZip, Deflate, BZip2 och ZipDeflate** stöds. Se [stöds format och komprimering](data-factory-supported-file-and-compression-formats.md) med information.

Du kan exempelvis göra följande kopieringsaktiviteter:

* Kopiera data i en lokal SQL Server och skriva till Azure Data Lake Store i ORC-format.
* Kopiera filer i textformat (CSV) från den lokala filsystem och skriva till Azure Blob i Avro-format.
* Kopiera komprimerade filer från den lokala filsystem och expandera sedan mark till Azure Data Lake Store.
* Kopiera data i GZip-komprimerade textfiler (CSV)-format från Azure Blob och skriva till Azure SQL Database.

## <a name="global"></a>Globalt tillgänglig dataförflyttning
Azure Data Factory finns endast i regionerna västra USA, östra USA och Nordeuropa. Tjänsten som driver Kopieringsaktivitet är dock tillgängligt globalt i följande regioner och geografiska områden. Globalt tillgänglig topologin säkerställer effektiv dataförflyttning som vanligtvis undviker interregionala hopp. Se [tjänster efter region](https://azure.microsoft.com/regions/#services) för tillgänglighet för Data Factory och dataförflyttning i en region.

### <a name="copy-data-between-cloud-data-stores"></a>Kopieringsdata mellan molndatalager
När både käll-och mottagare finns i molnet, använder Data Factory en tjänstdistribution i den region som ligger närmast mottagaren på samma geografiska plats att flytta data. Se tabellen nedan för mappning:

| Geografisk plats för datalager som mål | Regionen för måldatalagret | Regionen som används för dataförflyttning |
|:--- |:--- |:--- |
| USA | Östra USA | Östra USA |
| &nbsp; | USA, östra 2 | USA, östra 2 |
| &nbsp; | Centrala USA | Centrala USA |
| &nbsp; | Norra centrala USA | Norra centrala USA |
| &nbsp; | Södra centrala USA | Södra centrala USA |
| &nbsp; | Västra centrala USA | Västra centrala USA |
| &nbsp; | Västra USA | Västra USA |
| &nbsp; | Västra USA 2 | Västra USA 2 |
| Kanada | Östra Kanada | Centrala Kanada |
| &nbsp; | Centrala Kanada | Centrala Kanada |
| Brasilien | Södra Brasilien | Södra Brasilien |
| Europa | Norra Europa | Norra Europa |
| &nbsp; | Västra Europa | Västra Europa |
| Storbritannien | Storbritannien, västra | Storbritannien, södra |
| &nbsp; | Storbritannien, södra | Storbritannien, södra |
| Asien och stillahavsområdet | Sydostasien | Sydostasien |
| &nbsp; | Östasien | Sydostasien |
| Australien | Östra Australien | Östra Australien |
| &nbsp; | Sydöstra Australien | Sydöstra Australien |
| Indien | Indien, centrala | Indien, centrala |
| &nbsp; | Indien, västra | Indien, centrala |
| &nbsp; | Södra Indien | Indien, centrala |
| Japan | Östra Japan | Östra Japan |
| &nbsp; | Västra Japan | Östra Japan |
| Korea | Sydkorea, centrala | Sydkorea, centrala |
| &nbsp; | Sydkorea, södra | Sydkorea, centrala |

Du kan också du uttryckligen ange regionen som Data Factory-tjänsten som används för att utföra kopieringen genom att ange `executionLocation` egenskapen under Kopieringsaktiviteten `typeProperties`. Värden som stöds för den här egenskapen visas i ovan **regionen används för dataförflyttning** kolumn. Observera att dina data passerar den regionen i rörelse vid kopiering. Till exempel att kopiera mellan Azure lagrar i Sydkorea, kan du ange `"executionLocation": "Japan East"` för dirigering via Japan region (se [exempel JSON](#by-using-json-scripts) som referens).

> [!NOTE]
> Om regionen för måldatalagret inte är i föregående lista eller inte kan identifieras som standard kopieringen misslyckas istället för att gå igenom en alternativ region, såvida inte `executionLocation` har angetts. Listan stöds område utökas över tid.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt datalager och ett molndatalager
När data kopieras mellan en lokal (eller Azure virtuella datorer/IaaS) och molnbaserade datalager, [Data Management Gateway](data-factory-data-management-gateway.md) utför dataförflyttning på en lokal dator eller virtuell dator. Data flödar via tjänsten i molnet, inte om du inte använder den [mellanlagrad kopiering](data-factory-copy-activity-performance.md#staged-copy) kapaciteten. I det här fallet flödar data genom mellanlagring Azure Blob storage innan de skrivs till de mottagande datalagren.

## <a name="create-a-pipeline-with-copy-activity"></a>Skapa en pipeline med en Kopieringsaktivitet
Du kan skapa en pipeline med Kopieringsaktivitet i ett par olika sätt:

### <a name="by-using-the-copy-wizard"></a>Med hjälp av guiden Kopiera
Data Factory-Kopieringsguiden hjälper dig att skapa en pipeline med en Kopieringsaktivitet. Den här pipelinen låter dig kopiera data från källor som stöds till mål *utan att skriva JSON* definitioner för länkade tjänster, datauppsättningar och pipeliner. Se [Data Factory-Kopieringsguide](data-factory-copy-wizard.md) mer information om guiden.  

### <a name="by-using-json-scripts"></a>Med hjälp av JSON-skript
Du kan använda Data Factory-redigeraren i Azure portal, Visual Studio eller Azure PowerShell för att skapa en JSON-definition för en pipeline (med hjälp av Kopieringsaktivitet). Du kan distribuera den sedan för att skapa pipelinen i Datafabriken. Se [självstudien: Använda Kopieringsaktivitet i en Azure Data Factory-pipeline](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) en självstudie med stegvisa instruktioner.    

JSON-egenskaper (till exempel namn, beskrivning, indata och utdata tabeller och principer) är tillgängliga för alla typer av aktiviteter. Egenskaper som är tillgängliga i den `typeProperties` avsnittet aktivitetens varierar med varje aktivitetstyp av.

För Kopieringsaktiviteten den `typeProperties` avsnittet varierar beroende på vilka typer av källor och egenskaperna. Klicka på källa/mottagare i den [stöds källor och mottagare](#supported-data-stores-and-formats) att ta reda på om egenskaperna för anslutningstypen som Kopieringsaktivitet har stöd för det datalagringen.

Här är en exempel-JSON-definition:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Det schema som har definierats i datauppsättningen för utdata anger när aktiviteten körs (till exempel: **dagliga**, frekvens som **dag**, och intervall som **1**). Aktiviteten kopierar data från en indatauppsättning (**källa**) till en datauppsättning för utdata (**mottagare**).

Du kan ange fler än en indatauppsättning till Kopieringsaktiviteten. De används för att verifiera beroenden innan aktiviteten körs. Dock kopieras endast data från den första datauppsättningen till måldatamängden som. Mer information finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se den [Kopieringsaktiviteten prestanda- och Justeringsguiden](data-factory-copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestandan för dataflytt (Kopieringsaktivitet) i Azure Data Factory. Den visar en lista över de observerade prestandan under interna tester och beskriver olika sätt att optimera prestandan för Kopieringsaktiviteten.

## <a name="fault-tolerance"></a>Feltolerans
Som standard Kopieringsaktivitet slutar att kopiera data och returnera fel uppstår när inkompatibla data mellan källa och mottagare; även om du explicit kan konfigurera för att gå vidare och logga inkompatibla rader och bara en kopia dessa kompatibel data att kopieringen har slutförts. Se den [Kopieringsaktiviteten feltolerans](data-factory-copy-activity-fault-tolerance.md) på mer information.

## <a name="security-considerations"></a>Säkerhetsöverväganden
Se den [säkerhetsöverväganden](data-factory-data-movement-security-considerations.md), som beskriver säkerhetsinfrastruktur dataförflyttning i Azure Data Factory använder för att skydda dina data.

## <a name="scheduling-and-sequential-copy"></a>Schemaläggning och sekventiella kopia
Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) detaljerad information om hur schemaläggning och körning fungerar i Data Factory. Det går att köra flera kopieringsåtgärder efter varandra i ett sekventiella/ordnat sätt. Se den [kopiera sekventiellt](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) avsnittet.

## <a name="type-conversions"></a>Typkonverteringar
Olika datalager har olika ursprunglig typ. system. Kopieringsaktiviteten utför automatisk konverteringar från typer av datakällor till mottagare typer med följande metod i två steg:

1. Konvertera från interna källtyper till en .NET.
2. Konvertera från en .NET-typ till en intern mottagare.

Mappningen från en ursprunglig typ. system till en .NET-typ för ett datalager finns i respektive data store artikeln. (Klicka på länken i den [datalager som stöds](#supported-data-stores) tabell). Du kan använda dessa mappningar för att avgöra lämpliga typer när du skapar tabeller, så att Kopieringsaktivitet utför rätt konverteringar.

## <a name="next-steps"></a>Nästa steg
* Mer information om Kopieringsaktiviteten, se [kopiera data från Azure Blob storage till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Läs om hur du flyttar data från ett lokalt datalager till ett datalager i molnet i [flytta data från mellan lokala och molnbaserade datalager](data-factory-move-data-between-onprem-and-cloud.md).
