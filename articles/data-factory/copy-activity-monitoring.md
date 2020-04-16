---
title: Övervaka kopieringsaktivitet
description: Läs mer om hur du övervakar kopieringsaktivitetskörningen i Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417852"
---
# <a name="monitor-copy-activity"></a>Övervaka kopieringsaktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du övervakar kopieringsaktivitetskörningen i Azure Data Factory. Den bygger på [kopian aktivitet översikt](copy-activity-overview.md) artikeln som presenterar en allmän översikt över kopieringsaktivitet.

## <a name="monitor-visually"></a>Övervaka visuellt

När du har skapat och publicerat en pipeline i Azure Data Factory kan du associera den med en utlösare eller manuellt starta en ad hoc-körning. Du kan övervaka alla dina pipeline-körningar internt i azure data factory-användarupplevelsen. Lär dig mer om Azure Data Factory-övervakning i allmänhet från [Visually monitor Azure Data Factory](monitor-visually.md).

Om du vill övervaka kopieringsaktivitetskörningen går du till **datafabriken Author & Monitor** UI. På fliken **Övervakar** visas en lista över pipeline-körningar, klicka på **länken för pipelinenamn** för att komma åt listan över aktivitetskörningar i pipelinekörningen.

![Kör för övervakningskopieringsaktivitet](./media/copy-activity-overview/monitor-pipeline-run.png)

På den här nivån kan du se länkar för att kopiera aktivitetsindata, utdata och fel (om kopieringsaktivitetskörningen misslyckas) samt statistik som varaktighet/status. Om du klickar på **knappen Detaljer** (glasögon) bredvid kopieringsaktivitetsnamnet får du djup information om kopieringsaktivitetens utförande. 

![Kör för övervakningskopieringsaktivitet](./media/copy-activity-overview/monitor-copy-activity-run.png)

I den här grafiska övervakningsvyn visar Azure Data Factory information om kopieringsaktivitet, inklusive dataläs/skriftlig volym, antal filer/rader med data som kopieras från källa till diskho, dataflöde, konfigurationer som tillämpas för kopieringsscenariot, steg som kopieringsaktiviteten går igenom med motsvarande varaktigheter och information med mera. Se [den här tabellen](#monitor-programmatically) om varje möjligt mått och dess detaljerade beskrivning. 

I vissa fall visas **"Prestandajusteringstips"** högst upp i övervakningsvyn för kopieringsaktivitet som visas i exemplet när du kör en kopieringsaktivitet i Data Factory. Tipsen talar om för dig flaskhalsen som identifieras av ADF för den specifika kopieringskörningen, tillsammans med förslag på vad du ska ändra för att öka kopieringsflödet. Läs mer om [tips om automatisk prestandajustering](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

Den nedre **körningsinformationen och varaktigheten** beskriver de viktigaste stegen som kopieringsaktiviteten går igenom, vilket är särskilt användbart för felsökning av kopieringsprestandan. Flaskhalsen i kopieringskörningen är den med längst varaktighet. Se [Felsöka kopieringsaktivitetsprestanda](copy-activity-performance-troubleshooting.md) på för vad varje steg representerar och detaljerad felsökningsvägledning.

**Exempel: Kopiera från Amazon S3 till Azure Data Lake Storage Gen2**

![Information om övervakningskopieringsaktivitetskörning](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Övervaka programmässigt

Information om kopiera aktivitetskörning och prestandaegenskaper returneras också i avsnittet **Kopiera aktivitetskörningsresultatutdata,** > **Output** som används för att återge övervakningsvyn för användargränssnittet. Följande är en fullständig lista över egenskaper som kan returneras. Du ser bara de egenskaper som gäller för kopieringsscenariot. Information om hur du övervakar aktivitet körs programmässigt i allmänhet finns i [Programmatiskt övervaka en Azure-datafabrik](monitor-programmatically.md).

| Egenskapsnamn  | Beskrivning | Enhet i utdata |
|:--- |:--- |:--- |
| dataLäsa | Den faktiska mängden data som läss från källan. | Int64-värde, i byte |
| dataSkrivet | Den faktiska monteringen av data skrivna / åtagit sig att diskbänken. Storleken kan skilja `dataRead` sig från storleken, eftersom den relaterar hur varje datalager lagrar data. | Int64-värde, i byte |
| filerLäs | Antalet filer som läss från den filbaserade källan. | Int64-värde (ingen enhet) |
| filerSkrivna | Antalet filer som skrivits/bekräftats till den filbaserade diskbänken. | Int64-värde (ingen enhet) |
| källaUppkopplingar | Toppantal samtidiga anslutningar som upprättats till källdatalagret under kopieringsaktiviteten körs. | Int64-värde (ingen enhet) |
| sinkPeakAnslutningar | Toppantal samtidiga anslutningar som upprättats till sink-datalagret under kopieringsaktiviteten körs. | Int64-värde (ingen enhet) |
| raderLäs | Antal rader som läss från källan (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| raderKoperade | Antal rader som kopierats till diskho (gäller inte för binär kopia). | Int64-värde (ingen enhet) |
| raderSkipade | Antal inkompatibla rader som hoppades över. Du kan aktivera inkompatibla rader som `enableSkipIncompatibleRow` ska hoppas över genom att ställa in true. | Int64-värde (ingen enhet) |
| kopieraDuration | Varaktighet för kopieringen kör. | Int32-värde, i sekunder |
| dataflöde | Överföring av data. | Flyttalsnummer, i KBps |
| källaUppkopplingar | Toppantal samtidiga anslutningar som upprättats till källdatalagret under kopieringsaktiviteten körs. | Int32-värde (ingen enhet) |
| sinkPeakAnslutningar| Toppantal samtidiga anslutningar som upprättats till sink-datalagret under kopieringsaktiviteten körs.| Int32-värde (ingen enhet) |
| sqlDwPolyBase | Om PolyBase används när data kopieras till SQL Data Warehouse. | Boolesk |
| redshiftUnload | Om AVLASTNING används när data kopieras från Redshift. | Boolesk |
| hdfsDistcp (hdfsDistcp) | Om DistCp används när data kopieras från HDFS. | Boolesk |
| effektivtIntegrationRuntime | Integrationskörningen (IR) eller runtimes som används för `<IR name> (<region if it's Azure IR>)`att driva aktivitetskörningen i formatet . | Text (sträng) |
| usedDataIntegrationUnits | De effektiva dataintegrationsenheterna under kopieringen. | Int32-värde |
| användsParallelCopies | Den effektiva parallelCopies under kopia. | Int32-värde |
| omdirigeraRowPath | Sökväg till loggen över inkompatibla rader i blob-lagringen som du konfigurerar i egenskapen. `redirectIncompatibleRowSettings` Se [Feltolerans](copy-activity-overview.md#fault-tolerance). | Text (sträng) |
| executionDetails | Mer information om de stadier som kopieringsaktiviteten går igenom och motsvarande steg, varaktigheter, konfigurationer och så vidare. Vi rekommenderar inte att du tolkar det här avsnittet eftersom det kan ändras. Om du vill bättre förstå hur det hjälper dig att förstå och felsöka kopieringsprestanda läser du avsnittet [Övervaka visuellt.](#monitor-visually) | Matris |
| perfKommendation | Kopiera prestandajusteringstips. Mer information finns i [Tips för prestandajustering.](copy-activity-performance-troubleshooting.md#performance-tuning-tips) | Matris |

**Exempel:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna för kopieringsaktivitet:

\-[Kopiera aktivitetsöversikt](copy-activity-overview.md)

\- [Kopiera aktivitetsprestanda](copy-activity-performance.md)