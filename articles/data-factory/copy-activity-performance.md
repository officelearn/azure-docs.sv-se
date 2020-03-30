---
title: Kopiera aktivitetsprestanda och skalbarhetsguide
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder kopieringsaktiviteten.
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
ms.openlocfilehash: 231b0d77dc441e70dc0ec8de313291bb6b4f9292
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261403"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Kopiera aktivitetsprestanda och skalbarhetsguide

> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)

Oavsett om du vill utföra en storskalig datamigrering från datasjö eller företagsdatalager (EDW) till Azure, eller om du vill använda data i stor skala från olika källor till Azure för stordataanalys, är det viktigt att uppnå optimal prestanda och Skalbarhet.  Azure Data Factory tillhandahåller en högpresterande, flexibel och kostnadseffektiv mekanism för att få tillgång till data i stor skala, vilket gör det till en utmärkt passform för datatekniker som vill skapa högpresterande och skalbara datainmatningspipelpipels.

Efter att ha läst den här artikeln kommer du att kunna svara på följande frågor:

- Vilken nivå av prestanda och skalbarhet kan jag uppnå med hjälp av ADF-kopieringsaktivitet för datamigrering och scenarier för datainmatning?

- Vilka åtgärder bör jag vidta för att justera prestanda för ADF-kopieringsaktivitet?
- Vilka ADF perf-optimeringsrattar kan jag använda för att optimera prestanda för en enda kopieringsaktivitetskörning?
- Vilka andra faktorer utanför ADF att tänka på när du optimerar kopieringsprestanda?

> [!NOTE]
> Om du inte känner till kopieringsaktiviteten i allmänhet läser du [kopians aktivitetsöversikt](copy-activity-overview.md) innan du läser den här artikeln.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiera prestanda och skalbarhet som kan uppnås med hjälp av ADF

ADF erbjuder en serverlös arkitektur som tillåter parallellism på olika nivåer, vilket gör det möjligt för utvecklare att bygga pipelines för att fullt ut utnyttja din nätverksbandbredd samt lagring IOPS och bandbredd för att maximera datarördataflödet för din miljö.  Det innebär att dataflödet du kan uppnå kan uppskattas genom att mäta det minsta dataflöde som erbjuds av källdatalagret, måldatalagret och nätverksbandbredden mellan källan och målet.  Tabellen nedan beräknar kopieringstiden baserat på datastorlek och bandbreddsgränsen för din miljö. 

| Datastorlek / <br/> Bandbredd | 50 Mbit/s    | 100 Mbps  | 500 Mbps  | 1 Gbit/s   | 5 Gbit/s   | 10 Gbit/s  | 50 Gbit/s   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 timmar    | 2,3 timmar   | 0,5 timmar   | 0,2 timmar  | 0,05 timmar | 0,02 timmar | 0,0 timmar   |
| **1 TB**                    | 46,6 timmar   | 23,3 timmar  | 4,7 timmar   | 2,3 timmar  | 0,5 timmar  | 0,2 timmar  | 0,05 timmar  |
| **10 TB**                   | 19,4 dagar  | 9,7 dagar  | 1,9 dagar  | 0,9 dagar | 0,2 dagar | 0,1 dagar | 0,02 dagar |
| **100 TB**                  | 194,2 dagar | 97,1 dagar | 19,4 dagar | 9,7 dagar | 1,9 dagar | 1 dag    | 0,2 dagar  |
| **1 PB (pb)**                    | 64,7 mån    | 32,4 mån   | 6,5 mån    | 3,2 mån   | 0,6 månad   | 0,3 månad   | 0,06 mån   |
| **10 PB (PB)**                   | 647,3 mån   | 323,6 mån  | 64,7 mån   | 31,6 mån  | 6,5 mån   | 3,2 mån   | 0,6 månad    |

ADF-kopian är skalbar på olika nivåer:

![hur ADF-kopiering skalar](media/copy-activity-performance/adf-copy-scalability.png)

- ADF-kontrollflöde kan starta flera kopieringsaktiviteter parallellt, till exempel med hjälp av [För varje slinga](control-flow-for-each-activity.md).
- En enda kopieringsaktivitet kan dra nytta av skalbara beräkningsresurser: när du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](#data-integration-units) för varje kopieringsaktivitet på ett serverlöst sätt. När du använder självvärderad integrationskörning kan du manuellt skala upp datorn eller skala ut till flera datorer[(upp till 4 noder)](create-self-hosted-integration-runtime.md#high-availability-and-scalability)och en enda kopieringsaktivitet partitionerar filuppsättningen över alla noder.
- En enda kopieringsaktivitet läser från och skriver till datalagret med hjälp av flera trådar [parallellt](#parallel-copy).

## <a name="performance-tuning-steps"></a>Steg för prestandajustering

Vidta de här stegen för att justera prestanda för din Azure Data Factory-tjänst med kopieringsaktiviteten.

1. **Plocka upp en testdatauppsättning och upprätta en baslinje.** Testa pipelinen under utvecklingsfasen med hjälp av kopieringsaktiviteten mot ett representativt dataexempel. Den datauppsättning du väljer ska representera dina typiska datamönster (mappstruktur, filmönster, dataschema och så vidare) och är tillräckligt stor för att utvärdera kopieringsprestanda, till exempel tar det 10 minuter eller längre tid för kopieringsaktivitet att slutföras. Samla in körningsinformation och prestandaegenskaper efter [övervakning av kopieringsaktivitet](copy-activity-monitoring.md).

2. **Hur man maximerar prestanda för en enda kopieringsaktivitet:**

   Till att börja med rekommenderar vi att du först maximerar prestanda med hjälp av en enda kopieringsaktivitet.

   - **Om kopieringsaktiviteten körs på en Azure Integration Runtime:** börja med standardvärden för [DATA Integration Units (DIU)](#data-integration-units) och [parallella](#parallel-copy) kopieringsinställningar. 

   - **Om kopieringsaktiviteten körs på en självvärderad Integration Runtime:** Vi rekommenderar att du använder en dedikerad dator separat från servern som är värd för datalagret för att vara värd för integrationskörning. Börja med standardvärden för [parallellkopieringsinställning](#parallel-copy) och använda en enda nod för den självvärderade IR:t.  

   Utför en prestandatestkörning och ta del av prestanda som uppnåtts samt de faktiska värden som används som DIUs och parallella kopior. Se [kopieringsaktivitetsövervakning](copy-activity-monitoring.md) om hur du samlar in körresultat och prestandainställningar som används och lär dig hur du [felsöker prestanda för kopieringsaktivitet](copy-activity-performance-troubleshooting.md) för att identifiera och lösa flaskhalsen. 

   Iterera för att utföra ytterligare prestandatestkörningar enligt felsöknings- och justeringsvägledningen. När enstaka kopieringsaktivitetskörning inte kan uppnå bättre dataflöde bör du tänka på att maximera det samlade dataflödet genom att köra flera kopior samtidigt som du refererar till steg 3.


3. **Så här maximerar du det sammanlagda dataflödet genom att köra flera kopior samtidigt:**

   Nu när du har maximerat prestanda för en enda kopieringsaktivitet, om du ännu inte har uppnått dataflödets övre gränser för din miljö – nätverk, källdatalager och måldatalager – kan du köra flera kopieringsaktiviteter parallellt med hjälp av ADF-kontrollflödeskonstruktioner som [För varje slinga](control-flow-for-each-activity.md). Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md), Migrera data från Amazon [S3 till ADLS Gen2](solution-template-migration-s3-azure.md)eller [Masskopiering med en](solution-template-bulk-copy-with-control-table.md) kontrolltabelllösningsmallar som allmänt exempel.

5. **Expandera konfigurationen till hela datauppsättningen.** När du är nöjd med körningsresultaten och prestandan kan du expandera definitionen och pipelinen så att den täcker hela datauppsättningen.

## <a name="troubleshoot-copy-activity-performance"></a>Felsöka prestanda för kopieringsaktivitet

Följ [stegen För prestandajustering](#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. Och lär dig hur du felsöker prestandaproblem för varje kopieringsaktivitet i Azure Data Factory från [Felsöka prestanda för kopieringsaktivitet](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Kopiera prestandaoptimeringsfunktioner

Azure Data Factory innehåller följande prestandaoptimeringsfunktioner:

- [Dataintegrationsenheter](#data-integration-units)
- [Självvärd för integreringskörningsskalbarhet](#self-hosted-integration-runtime-scalability)
- [Parallell kopia](#parallel-copy)
- [Stegvis kopia](#staged-copy)

### <a name="data-integration-units"></a>Dataintegrationsenheter

En dataintegrationsenhet är ett mått som representerar kraften (en kombination av CPU, minne och nätverksresursallokering) för en enskild enhet i Azure Data Factory. Dataintegrationsenhet gäller endast för [Azure-integreringskörning](concepts-integration-runtime.md#azure-integration-runtime), men inte [självvärd för integrationskörning](concepts-integration-runtime.md#self-hosted-integration-runtime). [Läs mer](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Självvärd för integreringskörningsskalbarhet

Om du vill öka samtidig arbetsbelastning eller uppnå högre prestanda kan du antingen skala upp eller skala ut den självvärderade integrationskörningen. [Läs mer](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability).

### <a name="parallel-copy"></a>Parallell kopia

Du kan ställa in parallellkopia för att ange den parallellitet som du vill att kopieringsaktiviteten ska använda. Du kan se den här egenskapen som det maximala antalet trådar i kopieringsaktiviteten som läs av källan eller skriver till dina sink-datalager parallellt. [Läs mer](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Stegvis kopia

När du kopierar data från ett källdatalager till ett sink-datalager kan du välja att använda Blob-lagring som ett interimistiskt mellanlagringsarkiv. [Läs mer](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Felsöka prestanda för kopieringsaktivitet](copy-activity-performance-troubleshooting.md)
- [Kopiera prestandaoptimeringsfunktioner för aktivitet](copy-activity-performance-features.md)
- [Använda Azure Data Factory för att migrera data från datasjön eller datalagret till Azure](data-migration-guidance-overview.md)
- [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)
