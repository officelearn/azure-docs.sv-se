---
title: "Flytta data med hjälp av Kopieringsaktiviteten | Microsoft Docs"
description: "Lär dig mer om flytt av data i Data Factory pipelines: migrering av data mellan moln butiker och mellan ett lokalt Arkiv och en molnarkivet. Använd Kopieringsaktiviteten."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d6cddf54827d44e41add9e1e6b1a4323e03879e7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-by-using-copy-activity"></a>Flytta data med hjälp av Kopieringsaktiviteten
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1 – allmänt tillgänglig](data-factory-data-movement-activities.md)
> * [Version 2 – förhandsversion](../copy-activity-overview.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [Kopieringsaktiviteten i V2](../copy-activity-overview.md).

## <a name="overview"></a>Översikt
I Azure Data Factory, du kan använda Kopieringsaktiviteten för att kopiera data mellan lokala och moln datalager. När data kopieras, kan ytterligare omvandlas och analyseras. Du kan också använda Kopieringsaktiviteten för att publicera omvandling och analysresultat för business intelligence (BI) och förbrukning av programmet.

![Rollen för Kopieringsaktivitet](media/data-factory-data-movement-activities/copy-activity.png)

Kopieringsaktiviteten drivs av ett säkert, tillförlitligt, skalbara och och [globalt tillgänglig service](#global). Den här artikeln innehåller information om flytt av data i Data Factory och Kopieringsaktivitet.

Först ska vi se hur migrering av data sker mellan två molnet datalager och mellan ett lokalt datalager och ett datalager för molnet.

> [!NOTE]
> Mer information om aktiviteter i allmänhet, se [förstå pipelines och aktiviteter](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiera data mellan två moln-datalager
När både käll- och mottagarnoderna datalager finns i molnet, går Kopieringsaktiviteten igenom följande steg för att kopiera data från källan till sink. Den tjänst som används för Kopieringsaktiviteten:

1. Läser data från datalagret källa.
2. Utför serialisering/deserialisering, komprimering/dekomprimering, kolumn mappning och Skriv konvertering. Den gör dessa åtgärder baserat på konfigurationer av inkommande dataset, utdatauppsättningen och Kopieringsaktivitet.
3. Skriver data till datalagret mål.

Tjänsten väljer automatiskt den optimala regionen att utföra dataflytten. Den här regionen är vanligtvis en närmast sink-datalagret.

![Moln-to-cloud-kopia](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt datalager och ett datalager för molnet
På ett säkert sätt flytta data mellan ett lokalt datalager och ett datalager i molnet genom att installera Data Management Gateway på din lokala dator. Data Management Gateway är en agent som möjliggör hybrid dataförflyttning och bearbetning. Du kan installera den på samma dator som datalager själva, eller på en separat dator som har åtkomst till datalagret.

Data Management Gateway i det här scenariot utför serialisering/deserialisering, komprimering/dekomprimering, kolumnen mappning och Skriv konvertering. Data flödar inte via Azure Data Factory-tjänsten. Data Management Gateway skriver i stället direkt data till målarkivet.

![Kopiera på lokal-till-moln](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Se [flytta data mellan lokalt och i molnet datalager](data-factory-move-data-between-onprem-and-cloud.md) för en introduktion och genomgången. Se [Data Management Gateway](data-factory-data-management-gateway.md) detaljerad information om den här agenten.

Du kan också flytta data från/till stöd för datalager som finns på Azure IaaS-virtuella maskiner (VMs) med hjälp av Data Management Gateway. I det här fallet kan du installera Data Management Gateway på samma virtuella dator datalager sig själv eller på en separat virtuell dator som har åtkomst till datalagret.

## <a name="supported-data-stores-and-formats"></a>Lagrar data som stöds och format
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

> [!NOTE] 
> Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder kan du använda en **anpassad aktivitet** i Data Factory med din egen logik för att kopiera/flytta data. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="supported-file-formats"></a>Filformat som stöds
Du kan använda Kopieringsaktiviteten till **kopiera filer som-är** mellan två filbaserade datalager, kan du hoppa över den [formatera avsnitt](data-factory-create-datasets.md) i både inkommande och utgående dataset definitionerna. Data kopieras effektivt utan någon serialisering/deserialisering.

Kopieringsaktiviteten också läser från och skriver till filer i angivna format: **Text, JSON, Avro, ORC och parkettgolv**, och komprimerings-codec **GZip, Deflate, BZip2 och ZipDeflate** stöds. Se [stöds format och komprimering](data-factory-supported-file-and-compression-formats.md) med information.

Du kan till exempel göra kopiera följande aktiviteter:

* Kopierar data i lokal SQL Server och skriva till Azure Data Lake Store i ORC-format.
* Kopiera filerna i textformat (CSV) från lokala filsystemet och skriva till Azure Blob i Avro-formatet.
* Kopiera komprimerade filer från lokala filsystemet och expandera sedan mark till Azure Data Lake Store.
* Kopiera data i GZip komprimerade (CSV)-format från Azure Blob och skriva till Azure SQL Database.

## <a name="global"></a>Globalt tillgänglig dataflyttning
Azure Data Factory är endast tillgänglig i USA, västra och östra USA Nordeuropa regioner. Dock är tjänsten som används för Kopieringsaktiviteten tillgängligt globalt i följande regioner och geografiska områden. Globalt tillgänglig topologin garanterar effektiv dataflyttning som vanligtvis undviker mellan region hopp. Se [tjänster efter region](https://azure.microsoft.com/regions/#services) tillgänglighet för Data Factory och flytt av Data i en region.

### <a name="copy-data-between-cloud-data-stores"></a>Kopiera data mellan moln datalager
När både käll- och mottagarnoderna datalager finns i molnet, använder Data Factory en tjänstdistribution i den region som ligger närmast sink i samma geografiska att flytta data. Se tabellen nedan för mappning:

| Geografisk plats för mål-datalager | Region för mål-datalagret | Region som används för dataflyttning |
|:--- |:--- |:--- |
| USA | Östra USA | Östra USA |
| &nbsp; | Östra USA 2 | Östra USA 2 |
| &nbsp; | Centrala USA | Centrala USA |
| &nbsp; | Norra centrala USA | Norra centrala USA |
| &nbsp; | Södra centrala USA | Södra centrala USA |
| &nbsp; | Västra centrala USA | Västra centrala USA |
| &nbsp; | Västra USA | Västra USA |
| &nbsp; | Västra USA 2 | Västra USA |
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
| Korea | Centrala Korea | Centrala Korea |
| &nbsp; | Sydkorea | Centrala Korea |

Du kan också explicit anger du regionen Data Factory-tjänsten som används för att utföra kopieringen genom att ange `executionLocation` egenskap under Kopieringsaktiviteten `typeProperties`. Värden som stöds för den här egenskapen är ovan i **Region används för dataflyttning** kolumn. Observera att data går igenom den regionen via kabel under kopia. Om du vill kopiera mellan Azure lagras i Sydkorea, kan du ange `"executionLocation": "Japan East"` att vidarebefordra via Japan region (se [exempel JSON](#by-using-json-scripts) som referens).

> [!NOTE]
> Om regionen för mål-datalagret är inte i föregående listan eller oidentifierbart, som standard Kopieringsaktiviteten misslyckas i stället för att gå via en annan region om `executionLocation` har angetts. Listan stöds region ska expanderas över tid.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt datalager och ett datalager för molnet
När data kopieras mellan lokalt (eller Azure virtuella datorer/IaaS) och molntjänster butiker [Data Management Gateway](data-factory-data-management-gateway.md) utför dataflytt på en lokal dator eller virtuell dator. Data flödar inte via tjänsten i molnet, såvida du inte använder den [mellanlagrad kopiera](data-factory-copy-activity-performance.md#staged-copy) kapaciteten. I det här fallet flödar data genom fristående Azure Blob storage innan de skrivs i datalagret mottagare.

## <a name="create-a-pipeline-with-copy-activity"></a>Skapa en pipeline med Kopieringsaktiviteten
Du kan skapa en pipeline med Kopieringsaktiviteten på ett par olika sätt:

### <a name="by-using-the-copy-wizard"></a>Med hjälp av guiden Kopiera
Guiden Kopiera Data Factory hjälper dig att skapa en pipeline med Kopieringsaktiviteten. Denna pipeline kan du kopiera data från källor som stöds till mål *utan att behöva skriva JSON* definitioner för länkade tjänster, datauppsättningar och rörledningar. Se [Data Factory kopiera guiden](data-factory-copy-wizard.md) för ytterligare information om guiden.  

### <a name="by-using-json-scripts"></a>Med hjälp av JSON-skript
Du kan använda Data Factory-redigeraren i Azure-portalen, Azure PowerShell eller Visual Studio för att skapa en JSON-definitionen för en pipeline (med hjälp av Kopieringsaktiviteten). Du kan sedan distribuera det för att skapa pipelinen i Data Factory. Se [Självstudier: Använd Kopieringsaktivitet i ett Azure Data Factory-pipelinen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) en självstudiekurs med stegvisa instruktioner.    

JSON-egenskaper (till exempel namn, beskrivning, ingående och utgående tabeller och principer) är tillgängliga för alla typer av aktiviteter. Egenskaper som är tillgängliga i den `typeProperties` delen av aktiviteten varierar med varje aktivitetstyp.

För Kopieringsaktivitet kan den `typeProperties` avsnittet varierar beroende på vilka typer av datakällor och egenskaperna. Klicka på källor/mottagare i den [källor och sänkor stöds](#supported-data-stores-and-formats) avsnittet om du vill veta mer om egenskaper som Kopieringsaktiviteten stöder för att lagra data.

Här är ett exempel JSON-definitionen:

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
Det schema som definieras i datamängd för utdata avgör när aktiviteten körs (till exempel: **dagliga**, frekvens som **dag**, och intervall som **1**). Aktiviteten kopierar data från en inkommande datauppsättning (**källa**) till en datamängd för utdata (**sink**).

Du kan ange mer än en datauppsättning som indata till Kopieringsaktiviteten. De används för att verifiera beroenden innan aktiviteten körs. Dock kopieras endast data från den första datauppsättningen till mål-dataset. Mer information finns i [schemaläggning och körning](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Prestanda- och justering
Finns det [prestandajustering guide och Kopieringsaktivitet prestanda](data-factory-copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestandan för flytt av data (Kopieringsaktiviteten) i Azure Data Factory. Den visar observerade prestanda under interna tester och beskrivs olika sätt för att optimera prestanda för Kopieringsaktivitet.

## <a name="fault-tolerance"></a>Feltolerans
Som standard kopieringsaktiviteten stoppas kopiera data och returnera fel uppstår när inkompatibla data mellan käll- och mottagarnoderna; medan du uttryckligen konfigurera för att hoppa över och logga inkompatibla rader och kopiera endast dessa kompatibel data så att kopieringen har slutförts. Finns det [Kopieringsaktiviteten feltolerans](data-factory-copy-activity-fault-tolerance.md) för mer information.

## <a name="security-considerations"></a>Säkerhetsöverväganden
Finns det [säkerhetsaspekter](data-factory-data-movement-security-considerations.md), som beskriver säkerhetsinfrastruktur flytt datatjänster i Azure Data Factory använder för att skydda dina data.

## <a name="scheduling-and-sequential-copy"></a>Schemaläggning och sekventiella kopia
Se [schemaläggning och körning](data-factory-scheduling-and-execution.md) detaljerad information om hur schemaläggning och körning fungerar i Data Factory. Det är möjligt att köra flera kopieringsåtgärder efter varandra på ett sätt som sekventiella/sorterade. Finns det [kopiera sekventiellt](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) avsnitt.

## <a name="type-conversions"></a>Typkonverteringar
Olika datalager har en inbyggd typ i olika system. Kopieringsaktiviteten utför automatisk konverteringar från källtyper att registrera typer med följande metod i två steg:

1. Konvertera till en .NET-typ typer av inbyggda datakällor.
2. Konvertera från en .NET-typ till en intern Mottagartypen.

Mappning från en inbyggd typ till en .NET-typ för ett datalager finns i respektive data store artikeln. (Klicka på länken i den [stöds datalager](#supported-data-stores) tabell). Du kan använda dessa mappningar för att avgöra lämpliga typer när du skapar tabeller, så att Kopieringsaktiviteten utför rätt konverteringar.

## <a name="next-steps"></a>Nästa steg
* Mer information om aktiviteten kopiera, se [kopiera data från Azure blobblagring till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Läs om hur du flyttar data från ett lokalt datalager i ett moln-datalager i [flytta data från lokalt till molnet datalager](data-factory-move-data-between-onprem-and-cloud.md).
