---
title: Guiden Kopiera aktivitets prestanda och skalbarhet
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder kopierings aktiviteten.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: aedb3df69821d1436b03b2eb1f12873b624d426e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414181"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guiden Kopiera aktivitets prestanda och skalbarhet

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du vill utföra en storskalig datamigrering från data Lake eller Enterprise Data Warehouse (ANALYSLÖSNINGAR) till Azure, eller om du vill mata in data i stor skala från olika källor i Azure för stor data analys, är det viktigt att uppnå optimala prestanda och skalbarhet.  Azure Data Factory ger en utförd, elastisk och kostnads effektiv mekanism för att mata in data i stor skala, vilket gör det lättare för data tekniker att skapa högpresterande och skalbara data inmatnings pipeliner.

När du har läst den här artikeln kan du svara på följande frågor:

- Vilken nivå av prestanda och skalbarhet kan jag använda för att använda ADF Copy-aktivitet för data migration och scenarier för data inmatning?

- Vilka steg ska jag utföra för att justera prestandan för en ADF Copy-aktivitet?
- Vilka videokanaligt prestanda optimerings rattar kan jag använda för att optimera prestanda för en enskild kopierings aktivitet?
- Vad är andra faktorer utanför ADF att överväga när du optimerar kopierings prestanda?

> [!NOTE]
> Om du inte är bekant med kopierings aktiviteten i allmänhet kan du läsa [översikten kopiera aktivitet](copy-activity-overview.md) innan du läser den här artikeln.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiera prestanda och skalbarhet som går att nå med ADF

ADF erbjuder en server lös arkitektur som gör det möjligt att använda parallellitet på olika nivåer, vilket gör att utvecklare kan bygga pipeliner för att fullt ut utnyttja din nätverks bandbredd samt lagrings-IOPS och bandbredd för att maximera data flödet för data flödet i din miljö.  Det innebär att du kan beräkna det data flöde som du kan uppnå genom att mäta det minsta data flöde som erbjuds av käll data lagret, mål data lagret och nätverks bandbredden mellan källan och målet.  I tabellen nedan beräknas kopieringens varaktighet baserat på data storleken och bandbredds gränsen för din miljö. 

| Data storlek/ <br/> bredd | 50 Mbit/s    | 100 Mbps  | 500 Mbps  | 1 Gbit/s   | 5 Gbit/s   | 10 Gbit/s  | 50 Gbit/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 timmar    | 2,3 timmar   | 0,5 timmar   | 0,2 timmar  | 0,05 timmar | 0,02 timmar | 0,0 timmar   |
| **1 TB**                    | 46,6 timmar   | 23,3 timmar  | 4,7 timmar   | 2,3 timmar  | 0,5 timmar  | 0,2 timmar  | 0,05 timmar  |
| **10 TB**                   | 19,4 dagar  | 9,7 dagar  | 1,9 dagar  | 0,9 dagar | 0,2 dagar | 0,1 dagar | 0,02 dagar |
| **100 TB**                  | 194,2 dagar | 97,1 dagar | 19,4 dagar | 9,7 dagar | 1,9 dagar | 1 dag    | 0,2 dagar  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 Mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |

ADF-kopian är skalbar på olika nivåer:

![så här skalar ADF-kopiering](media/copy-activity-performance/adf-copy-scalability.png)

- ADF-kontroll flödet kan starta flera kopierings aktiviteter parallellt, till exempel använda [för varje slinga](control-flow-for-each-activity.md).
- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser: när du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](#data-integration-units) för varje kopierings aktivitet på ett Server lös sätt. När du använder Integration Runtime med egen värd kan du skala upp datorn manuellt eller skala ut till flera datorer ([upp till 4 noder](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) och en enda kopierings aktivitet partitionerar dess fil uppsättning på alla noder.
- En enskild kopierings aktivitet läser från och skriver till data lagret med hjälp av flera trådar [parallellt](#parallel-copy).

## <a name="performance-tuning-steps"></a>Prestanda justerings steg

Följ dessa steg för att justera prestandan för din Azure Data Factory-tjänst med kopierings aktiviteten.

1. **Hämta en test data uppsättning och upprätta en bas linje.** Under utvecklings fasen testar du din pipeline med hjälp av kopierings aktiviteten mot ett representativt data exempel. Den data uppsättning du väljer ska representera dina typiska data mönster (mappstruktur, fil mönster, data schema och så vidare) och är tillräckligt stor för att utvärdera kopierings prestanda, till exempel det tar 10 minuter eller mer för kopierings aktiviteten att slutföras. Samla in körnings information och prestanda egenskaper efter [Kopiera aktivitets övervakning](copy-activity-monitoring.md).

2. **Maximera prestanda för en enskild kopierings aktivitet**:

   För att börja med rekommenderar vi att du först maximerar prestandan med en enda kopierings aktivitet.

   - **Om kopierings aktiviteten körs på en Azure integration Runtime:** börja med standardvärden för [data integrerings enheter (DIU)](#data-integration-units) och inställningar för [parallell kopiering](#parallel-copy) . 

   - **Om kopierings aktiviteten körs på en egen värd integration Runtime:** vi rekommenderar att du använder en dedikerad dator separat från den server som är värd för integration Runtime. Börja med standardvärden för inställningen för [parallell kopiering](#parallel-copy) och Använd en enda nod för IR med egen värd.  

   Kör en prestandatest och anteckna de prestanda som uppnås samt de faktiska värdena som används som DIUs och parallella kopior. Mer information finns i avsnittet om att [Kopiera aktiviteter](copy-activity-monitoring.md) för att samla in prestanda inställningar och använda prestanda och lär dig hur du [felsöker kopierings aktivitetens prestanda](copy-activity-performance-troubleshooting.md) för att identifiera och lösa Flask halsen. 

   Iterera för att utföra ytterligare prestandatest körningar efter fel söknings-och justerings vägledningen. Det går inte att få bättre data flöde i en enskild kopierings aktivitet, Överväg att maximera mängd data flöde genom att köra flera kopior samtidigt som du refererar till steg 3.


3. **Maximera sammanställd genom strömning genom att köra flera kopior samtidigt:**

   Nu när du har maximerat prestandan för en enskild kopierings aktivitet, om du inte har uppnått de övre gränserna för din miljö – nätverk, käll data lager och mål data lager, kan du köra flera kopierings aktiviteter parallellt med hjälp av ett ADF-styrnings flöde, till exempel [för varje slinga](control-flow-for-each-activity.md). Läs mer om att [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md), [migrera data från Amazon S3 till ADLS Gen2](solution-template-migration-s3-azure.md)eller [Mass kopiering med en mall för kontroll tabells](solution-template-bulk-copy-with-control-table.md) lösningar som ett allmänt exempel.

5. **Expandera konfigurationen till hela data uppsättningen.** När du är nöjd med körnings resultatet och prestandan kan du expandera definitionen och pipelinen för att hantera hela data uppsättningen.

## <a name="troubleshoot-copy-activity-performance"></a>Felsöka prestanda för kopierings aktivitet

Följ [prestanda justerings stegen](#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. Och lär dig hur du felsöker varje kopierings aktivitets körnings prestanda problem i Azure Data Factory från [fel sökning av prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopiera funktioner för prestanda optimering

Azure Data Factory innehåller följande funktioner för prestanda optimering:

- [Dataintegrationsenheter](#data-integration-units)
- [Skalbarhet för integration runtime med egen värd](#self-hosted-integration-runtime-scalability)
- [Parallell kopia](#parallel-copy)
- [Mellanlagrad kopia](#staged-copy)

### <a name="data-integration-units"></a>Dataintegrationsenheter

En data integrerings enhet är ett mått som representerar styrkan (en kombination av processor, minne och tilldelning av nätverks resurser) för en enskild enhet i Azure Data Factory. Data integrerings enheten gäller endast för [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime), men inte för [integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime). [Läs mer](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Skalbarhet för integration runtime med egen värd

För att vara värd för att öka arbets belastningen eller uppnå högre prestanda kan du antingen skala upp eller skala ut den egna värdbaserade Integration Runtime. [Läs mer](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Parallell kopia

Du kan ställa in parallell kopiering för att ange vilken parallellitet som ska användas för kopierings aktiviteten. Du kan tänka på den här egenskapen som det maximala antalet trådar i kopierings aktiviteten som läser från din källa eller skriver till dina mottagar data lager parallellt. [Läs mer](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Mellanlagrad kopia

När du kopierar data från ett käll data lager till ett data lager för mottagare kan du välja att använda Blob Storage som ett interimistiskt mellanlagrings lager. [Läs mer](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över kopierings aktivitet](copy-activity-overview.md)
- [Felsöka prestanda för kopierings aktivitet](copy-activity-performance-troubleshooting.md)
- [Kopiera aktivitets prestanda optimerings funktioner](copy-activity-performance-features.md)
- [Använd Azure Data Factory för att migrera data från data Lake eller data lager till Azure](data-migration-guidance-overview.md)
- [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)
