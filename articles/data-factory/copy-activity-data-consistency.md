---
title: Konsekvens kontroll av data i kopierings aktivitet
description: Lär dig mer om hur du aktiverar data konsekvens verifiering i kopierings aktivitet i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: a386c7d44cf5ba7eda895006cda7ce1fa9b798ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664979"
---
#  <a name="data-consistency-verification-in-copy-activity-preview"></a>Konsekvens kontroll av data i kopierings aktiviteten (förhands granskning)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När du flyttar data från käll-till mål lagret innehåller Azure Data Factory kopierings aktivitet ett alternativ som gör att du kan utföra ytterligare konsekvens kontroll för data för att säkerställa att data inte bara har kopierats från käll platsen till mål butiken, men också verifierade att de är konsekventa mellan käll-och mål lagret. När inkonsekventa data har hittats under data flyttningen kan du antingen avbryta kopierings aktiviteten eller fortsätta att kopiera resten genom att aktivera fel tolerans inställningen för att hoppa över inkonsekventa data. Du kan hämta de överhoppade objekt namnen genom att aktivera logg inställningen för sessionen i kopierings aktiviteten. 

> [!IMPORTANT]
> Den här funktionen är för närvarande en för hands version med följande begränsningar som vi arbetar aktivt med:
>- Verifiering av data konsekvens är bara tillgängligt för binära filer som kopieras mellan filbaserade butiker med beteendet "PreserveHierarchy" i kopierings aktiviteten. För att kopiera tabell data är data konsekvens kontrollen inte tillgänglig i kopierings aktiviteten ännu.
>- När du aktiverar logg inställningen för sessionen i kopierings aktiviteten för att logga inkonsekventa filer som hoppas över, får inte logg filens fullständighet vara 100% garanterad om kopierings aktiviteten misslyckades.
>- Loggen för sessionen innehåller inkonsekventa filer, där filer som har kopierats inte loggas hittills.

## <a name="supported-data-stores"></a>Datalager som stöds

### <a name="source-data-stores"></a>Käll data lager

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Filsystem](connector-file-system.md)
-   [HDFS](connector-hdfs.md)

### <a name="destination-data-stores"></a>Mål data lager

-   [Azure Blob Storage](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)
-   [Filsystem](connector-file-system.md)


## <a name="configuration"></a>Konfiguration
I följande exempel visas en JSON-definition för att aktivera data konsekvens kontroll i kopierings aktiviteten: 

```json
"typeProperties": { 
"source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
        } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
}, 
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
    "logStorageSettings": { 
        "linkedServiceName": { 
            "referenceName": "ADLSGen2_storage", 
            "type": "LinkedServiceReference" 
        }, 
        "path": "/sessionlog/" 
} 
} 
```

Egenskap | Beskrivning | Tillåtna värden | Obligatorisk
-------- | ----------- | -------------- | -------- 
validateDataConsistency | Om du anger sant för den här egenskapen kontrollerar kopierings aktiviteten fil storlek, lastModifiedDate och MD5-kontrollsumma för varje objekt som kopieras från käll platsen till mål lagret för att säkerställa data konsekvensen mellan käll-och mål arkivet. Tänk på att kopierings prestandan kommer att påverkas genom att aktivera det här alternativet.  | Sant<br/>False (standard) | Inga
dataInconsistency | Ett nyckel/värde-par i skipErrorFile egenskaps uppsättning för att avgöra om du vill hoppa över inkonsekventa data.<br/> – Sant: du vill kopiera resten genom att hoppa över inkonsekventa data.<br/> -Falskt: du vill avbryta kopierings aktiviteten när inkonsekventa data har hittats.<br/>Tänk på att den här egenskapen endast är giltig när du anger validateDataConsistency som true.  | Sant<br/>False (standard) | Inga
logStorageSettings | En grupp egenskaper som kan anges för att aktivera sessionsinformation för att logga överhoppade objekt. | | Inga
linkedServiceName | Den länkade tjänsten för [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) eller [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) för att lagra loggfilerna för sessionen. | Namnen på en `AzureBlobStorage` `AzureBlobFS` länkad eller typ länkad tjänst, som refererar till den instans som du använder för att lagra loggfilerna. | Inga
path | Sökvägen till loggfilerna. | Ange den sökväg där du vill lagra loggfilerna. Om du inte anger en sökväg skapar tjänsten en behållare åt dig. | Inga

>[!NOTE]
>- Data konsekvens stöds inte i utvecklings kopierings scenariot. 
>- När du kopierar binära filer från lagrings lager till Azure Blob Storage eller Azure Data Lake Storage Gen2, gör kopierings aktiviteten fil storlek och verifiering av MD5-kontroll för att säkerställa data konsekvensen mellan käll-och mål arkiv. 
>- När du kopierar binärfiler från lagrings lager till andra lagrings platser än Azure Blob Storage eller Azure Data Lake Storage Gen2, verifierar kopierings aktiviteten fil storleks verifieringen för att säkerställa data konsekvensen mellan käll-och mål arkivet.


## <a name="monitoring"></a>Övervakning

### <a name="output-from-copy-activity"></a>Utdata från kopierings aktivitet
När kopierings aktiviteten körs fullständigt kan du se resultatet av data konsekvens verifieringen från utdata för varje kopierings aktivitet som körs:

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```
Du kan se information om verifiering av data konsekvens från "dataConsistencyVerification Property".

Värde för **VerificationResult**: 
-   **Verifierad**: dina kopierade data har verifierats för att vara konsekventa mellan käll-och mål arkivet. 
-   **NotVerified**: dina kopierade data har inte verifierats vara konsekventa eftersom du inte har aktiverat validateDataConsistency i kopierings aktiviteten. 
-   **Stöds**inte: dina kopierade data har inte verifierats vara konsekventa eftersom konsekvens kontroll av data inte stöds för detta specifika kopierings par. 

Värde för **InconsistentData**: 
-   **Hittades**: ADF Copy-aktiviteten hittade inkonsekventa data. 
-   **Hoppade över**: ADF Copy-aktivitet har hittat och hoppat över inkonsekventa data. 
-   **Ingen**: ADF Copy-aktivitet har inte hittat inkonsekventa data. Det kan antingen bero på att dina data har verifierats vara konsekventa mellan käll-och mål lagret, eller att du har inaktiverat validateDataConsistency i kopierings aktiviteten. 

### <a name="session-log-from-copy-activity"></a>Sessionsbiljett från kopierings aktivitet

Om du konfigurerar för att logga den inkonsekventa filen kan du hitta logg filen från den här sökvägen: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Loggfilerna är CSV-filerna. 

Schemat för en loggfil är följande:

Kolumn | Description 
-------- | -----------  
Tidsstämpel | Tids stämplingen när ADF hoppar över inkonsekventa filer.
Nivå | Logg nivån för det här objektet. Den kommer att vara i varnings nivån för objektet som visar att filen hoppas över.
OperationName | Funktions sätt för automatisk kopierings aktivitet på varje fil. Det blir "FileSkip" för att ange den fil som ska hoppas över.
OperationItem | Fil namnet som ska hoppas över.
Meddelande | Mer information för att illustrera varför filer hoppas över.

Exemplet på en loggfil är följande: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
Från logg filen ovan kan du se att sample1. csv har hoppats över eftersom det inte kunde verifieras att vara konsekvent mellan käll-och mål arkivet. Du kan få mer information om varför sample1. csv blir inkonsekvent beror på att den har ändrats av andra program när ADF Copy-aktivitet kopieras på samma gång. 



## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Fel tolerans för kopierings aktivitet](copy-activity-fault-tolerance.md)


