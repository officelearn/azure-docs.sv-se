---
title: Flytta data med hjälp av Kopiera aktivitet
description: 'Lär dig mer om dataförflyttning i Data Factory-pipelines: datamigrering mellan molnlager och mellan en lokal butik och en molnbutik. Använd kopieringsaktivitet.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281891"
---
# <a name="move-data-by-using-copy-activity"></a>Flytta data med hjälp av Kopiera aktivitet
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-data-movement-activities.md)
> * [Version 2 (aktuell version)](../copy-activity-overview.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Kopiera aktivitet i V2](../copy-activity-overview.md).

## <a name="overview"></a>Översikt
I Azure Data Factory kan du använda Kopiera aktivitet för att kopiera data mellan lokala och molnbaserade datalager. När data har kopierats kan de omvandlas och analyseras ytterligare. Du kan också använda Kopiera aktivitet för att publicera omvandlings- och analysresultat för Business Intelligence (BI) och programförbrukning.

![Roll för kopieringsaktivitet](media/data-factory-data-movement-activities/copy-activity.png)

Kopieringsaktivitet drivs av en säker, tillförlitlig, skalbar och [globalt tillgänglig tjänst](#global). Den här artikeln innehåller information om dataförflyttning i Data Factory och Copy Activity.

Låt oss först se hur datamigrering sker mellan två molndatalager och mellan ett lokalt datalager och ett molndatalager.

> [!NOTE]
> Mer information om aktiviteter i allmänhet finns i [Förstå pipelines och aktiviteter](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiera data mellan två molndatalager
När både käll- och sinkdatalager finns i molnet går kopiera aktivitet igenom följande steg för att kopiera data från källan till diskhon. Tjänsten som driver kopieringsaktivitet:

1. Läser data från källdatalagret.
2. Utför serialisering/deserialisering, komprimering/dekompression, kolumnmappning och typkonvertering. De här åtgärderna baseras på konfigurationerna för indatauppsättningen, utdatauppsättningen och kopieringsaktiviteten.
3. Skriver data till måldatalagret.

Tjänsten väljer automatiskt den optimala regionen för att utföra datarörelsen. Den här regionen är vanligtvis den som ligger närmast sink-datalagret.

![Moln-till-moln-kopia](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt datalager och ett molndatalager
Om du vill flytta data på ett säkert sätt mellan ett lokalt datalager och ett molndatalager installerar du Data Management Gateway på din lokala dator. Data Management Gateway är en agent som möjliggör hybriddata förflyttning och bearbetning. Du kan installera den på samma dator som själva datalagret eller på en separat dator som har åtkomst till datalagret.

I det här fallet utför Data Management Gateway serialisering/deserialization, komprimering/dekomprimering, kolumnmappning och typkonvertering. Data flödar inte via Azure Data Factory-tjänsten. I stället skriver Data Management Gateway data direkt till målarkivet.

![Lokal-till-moln-kopiering](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Se [Flytta data mellan lokala datalager och molndatalager](data-factory-move-data-between-onprem-and-cloud.md) för en introduktion och genomgång. Mer information om den här agenten finns i [Data Management Gateway.](data-factory-data-management-gateway.md)

Du kan också flytta data från/till datalager som stöds och som finns på virtuella Azure IaaS-datorer med hjälp av Data Management Gateway. I det här fallet kan du installera Data Management Gateway på samma virtuella dator som själva datalagret eller på en separat virtuell dator som har åtkomst till datalagret.

## <a name="supported-data-stores-and-formats"></a>Datalager och format som stöds
Kopieringsaktiviteten i Data Factory kopierar data från källans datalager till mottagarens datalager. Data Factory har stöd för följande datalager. Data kan skrivas från valfri källa till valfri mottagare. Klicka på ett datalager om du vill veta hur du kopierar data till och från det datalagret.

> [!NOTE] 
> Om du behöver flytta data till/från ett datalager som Kopiera aktivitet inte stöder kan du använda en **anpassad aktivitet** i Data Factory med din egen logik för att kopiera/flytta data. Mer information om att skapa och använda en anpassad aktivitet finns i [Use custom activities in an Azure Data Factory pipeline (Använda anpassade aktiviteter i en Azure Data Factory-pipeline)](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datalager med * kan finnas lokalt eller på Azure IaaS, och kräver att du installerar [Data Management Gateway](data-factory-data-management-gateway.md) på en lokal/Azure IaaS-dator.

### <a name="supported-file-formats"></a>Filformat som stöds
Du kan använda Kopiera aktivitet för att **kopiera filer som de är** mellan två filbaserade datalager, du kan hoppa över [formatavsnittet](data-factory-create-datasets.md) i både indata- och utdatauppsättningsdefinitionerna. Data kopieras effektivt utan serialisering/deserialisering.

Kopiera aktivitet läser också från och skriver till filer i angivna format: **Text, JSON, Avro, ORC och Parkett**, och komprimering codec **GZip, Deflate, BZip2 och ZipDeflate** stöds. Se [Fil- och komprimeringsformat](data-factory-supported-file-and-compression-formats.md) som stöds med information.

Du kan till exempel göra följande kopieringsaktiviteter:

* Kopiera data i lokal SQL Server och skriv till Azure Data Lake Store i ORC-format.
* Kopiera filer i CSV-format (Text) från lokalt filsystem och skriv till Azure Blob i Avro-format.
* Kopiera zippade filer från lokala filsystem och expandera sedan till Azure Data Lake Store.
* Kopiera data i CSV-format (GZip compressed text) från Azure Blob och skriv till Azure SQL Database.

## <a name="globally-available-data-movement"></a><a name="global"></a>Globalt tillgänglig datarörelse
Azure Data Factory är endast tillgängligt i regionerna Västra USA, Östra USA och Norra Europa. Tjänsten som driver kopieringsaktivitet är dock tillgänglig globalt i följande regioner och geografiska områden. Den globalt tillgängliga topologin säkerställer effektiv dataförflyttning som vanligtvis undviker hopp mellan regioner. Se [Tjänster efter region](https://azure.microsoft.com/regions/#services) för tillgänglighet för datafabrik och datarörelse i en region.

### <a name="copy-data-between-cloud-data-stores"></a>Kopiera data mellan molndatalager
När både käll- och sinkdatalager finns i molnet använder Data Factory en tjänstdistribution i den region som är närmast diskhon i samma geografi för att flytta data. Se tabellen nedan för mappning:

| Destinationsdatalagerens geografi | Region för måldatalagret | Region som används för datarörelser |
|:--- |:--- |:--- |
| USA | USA, östra | USA, östra |
| &nbsp; | USA, östra 2 | USA, östra 2 |
| &nbsp; | USA, centrala | USA, centrala |
| &nbsp; | USA, norra centrala | USA, norra centrala |
| &nbsp; | USA, södra centrala | USA, södra centrala |
| &nbsp; | USA, västra centrala | USA, västra centrala |
| &nbsp; | USA, västra | USA, västra |
| &nbsp; | USA, västra 2 | USA, västra 2 |
| Kanada | Kanada, östra | Kanada, centrala |
| &nbsp; | Kanada, centrala | Kanada, centrala |
| Brasilien | Brasilien, södra | Brasilien, södra |
| Europa | Europa, norra | Europa, norra |
| &nbsp; | Europa, västra | Europa, västra |
| Storbritannien | Storbritannien, västra | Storbritannien, södra |
| &nbsp; | Storbritannien, södra | Storbritannien, södra |
| Asien och stillahavsområdet | Sydostasien | Sydostasien |
| &nbsp; | Asien, östra | Sydostasien |
| Australien | Australien, östra | Australien, östra |
| &nbsp; | Australien, sydöstra | Australien, sydöstra |
| Indien | Indien, centrala | Indien, centrala |
| &nbsp; | Indien, västra | Indien, centrala |
| &nbsp; | Indien, södra | Indien, centrala |
| Japan | Japan, östra | Japan, östra |
| &nbsp; | Japan, västra | Japan, östra |
| Korea | Sydkorea, centrala | Sydkorea, centrala |
| &nbsp; | Sydkorea, södra | Sydkorea, centrala |

Du kan också uttryckligen ange vilken region för datafabrikstjänst som `executionLocation` ska användas `typeProperties`för att utföra kopian genom att ange egenskap under Kopiera aktivitet . Värden som stöds för den här egenskapen visas i ovanstående **region som används för dataförflyttningskolumnen.** Observera att dina data går igenom det området via tråden under kopieringen. Om du till exempel vill kopiera mellan `"executionLocation": "Japan East"` Azure-butiker i Korea kan du ange att cirkulera genom Japan -regionen (se [exempel JSON](#by-using-json-scripts) som referens).

> [!NOTE]
> Om området för måldatalagret inte finns i föregående lista eller inte kan upptäckas misslyckas som `executionLocation` standard Kopieringsaktivitet i stället för att gå igenom ett alternativt område, såvida inte anges. Regionlistan som stöds kommer att utökas med tiden.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiera data mellan ett lokalt datalager och ett molndatalager
När data kopieras mellan lokala (eller Virtuella Azure-datorer/IaaS) och molnlager utför [Data Management Gateway](data-factory-data-management-gateway.md) dataförflyttning på en lokal dator eller virtuell dator. Data flödar inte genom tjänsten i molnet, såvida du inte använder den [mellanlagrade](data-factory-copy-activity-performance.md#staged-copy) kopieringsfunktionen. I det här fallet flödar data via mellanlagringen av Azure Blob-lagring innan den skrivs in i datalagringslagringen för sink.

## <a name="create-a-pipeline-with-copy-activity"></a>Skapa en pipeline med kopieringsaktivitet
Du kan skapa en pipeline med Kopiera aktivitet på ett par olika sätt:

### <a name="by-using-the-copy-wizard"></a>Med hjälp av kopieringsguiden
Guiden Data Factory Copy hjälper dig att skapa en pipeline med kopieringsaktivitet. Med den här pipelinen kan du kopiera data från källor som stöds till mål utan att *skriva JSON-definitioner* för länkade tjänster, datauppsättningar och pipelines. Mer information om guiden finns i guiden Kopia för [datafabrik.](data-factory-copy-wizard.md)  

### <a name="by-using-json-scripts"></a>Genom att använda JSON-skript
Du kan använda Data Factory Editor i Visual Studio eller Azure PowerShell för att skapa en JSON-definition för en pipeline (med hjälp av Kopiera aktivitet). Sedan kan du distribuera den för att skapa pipelinen i Data Factory. Se [självstudiekurs: Använd kopieringsaktivitet i en Azure Data Factory-pipeline](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för en självstudiekurs med steg-för-steg-instruktioner.    

JSON-egenskaper (till exempel namn, beskrivning, indata- och utdatatabeller och principer) är tillgängliga för alla typer av aktiviteter. Egenskaper som är `typeProperties` tillgängliga i avsnittet i aktiviteten varierar beroende på varje aktivitetstyp.

För Kopieringsaktivitet `typeProperties` varierar avsnittet beroende på vilka typer av källor och sänkor som finns. Klicka på en källa/diskho i avsnittet [Källor och sänkor som stöds](#supported-data-stores-and-formats) om du vill veta mer om typegenskaper som Kopiera aktivitet stöder för det datalagret.

Här är ett exempel JSON definition:

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
Schemat som definieras i utdatauppsättningen avgör när aktiviteten körs (till exempel **dagligen**, frekvens som **dag**och intervall som **1**). Aktiviteten kopierar data från en indatauppsättning (**källa**) till en utdatauppsättning (**diskho**).

Du kan ange mer än en indatauppsättning för att kopiera aktivitet. De används för att verifiera beroenden innan aktiviteten körs. Det är dock bara data från den första datauppsättningen som kopieras till måldatauppsättningen. Mer information finns i [Schemaläggning och körning](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Prestanda- och justering
Se [prestanda- och justeringsguiden för kopieringsaktivitet](data-factory-copy-activity-performance.md), som beskriver viktiga faktorer som påverkar prestanda för dataflyttning (kopieringsaktivitet) i Azure Data Factory. Den listar också de observerade prestanda under interna tester och diskuterar olika sätt att optimera prestanda för kopieringsaktivitet.

## <a name="fault-tolerance"></a>Feltolerans
Som standard slutar kopieringsaktiviteten att kopiera data och returfel när inkompatibla data uppstår mellan källa och diskho. Medan du uttryckligen kan konfigurera för att hoppa över och logga de inkompatibla raderna och bara kopiera dessa kompatibla data för att göra kopian lyckades. Se [feltoleransen för kopieringsaktivitet](data-factory-copy-activity-fault-tolerance.md) på mer information.

## <a name="security-considerations"></a>Säkerhetsöverväganden
Se [säkerhetsövervägandena](data-factory-data-movement-security-considerations.md), som beskriver säkerhetsinfrastruktur som dataflyttningstjänster i Azure Data Factory använder för att skydda dina data.

## <a name="scheduling-and-sequential-copy"></a>Schemaläggning och sekventiell kopia
Mer information om hur schemaläggning och körning fungerar i Data Factory finns i [Schemaläggning och körning.](data-factory-scheduling-and-execution.md) Det är möjligt att köra flera kopieringsåtgärder en efter en i sekventiell/ordnad. Se avsnittet [Kopiera sekventiellt.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Typkonverteringar
Olika datalager har olika inbyggda typsystem. Kopieringsaktivitet utför automatiska typkonverteringar från källtyper till sinktyper med följande tvåstegsmetod:

1. Konvertera från ursprungliga källtyper till en .NET-typ.
2. Konvertera från en .NET-typ till en inbyggd sink-typ.

Mappningen från ett inbyggt typsystem till en .NET-typ för ett datalager finns i respektive datalagerartikel. (Klicka på den specifika länken i tabellen Datalager som stöds). Du kan använda dessa mappningar för att bestämma lämpliga typer när du skapar tabellerna, så att kopiera aktivitet utför rätt konverteringar.

## <a name="next-steps"></a>Nästa steg
* Mer information om kopieringsaktiviteten finns i [Kopiera data från Azure Blob-lagring till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Mer information om hur du flyttar data från ett lokalt datalager till ett molndatalager finns i [Flytta data från lokala till molndatalager](data-factory-move-data-between-onprem-and-cloud.md).
