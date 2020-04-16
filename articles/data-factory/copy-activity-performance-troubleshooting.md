---
title: Felsöka prestanda för kopieringsaktivitet
description: Läs mer om hur du felsöker prestanda för kopieringsaktivitet i Azure Data Factory.
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
ms.openlocfilehash: 6df1903e828c0c4cafa6589d4a85f4016bed893e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414131"
---
# <a name="troubleshoot-copy-activity-performance"></a>Felsöka prestanda för kopieringsaktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs hur du felsöker prestandaproblem för kopieringsaktivitet i Azure Data Factory. 

När du har kört en kopieringsaktivitet kan du samla in resultat- och resultatstatistik i [kopieringsaktivitetsövervakningsvyn.](copy-activity-monitoring.md) Följande är ett exempel.

![Information om övervakningskopieringsaktivitetskörning](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Tips vid prestandajustering

I vissa fall visas **"Prestandajusteringstips"** högst upp när du kör en kopieringsaktivitet i Data Factory. Tipsen talar om för dig flaskhalsen som identifieras av ADF för just den här kopieringskörningen, tillsammans med förslag på hur du kan öka kopieringsdataflödet. Försök att göra recommanded förändring och kör sedan kopian igen.

Som referens ger för närvarande tipsen för prestandajustering förslag på följande fall:

| Kategori              | Tips vid prestandajustering                                      |
| --------------------- | ------------------------------------------------------------ |
| Datalagerspecifik   | Läsa in data i **Azure Synpase Analytics (tidigare SQL DW):** föreslå att du använder PolyBase- eller COPY-uttrycket om det inte används. |
| &nbsp;                | Kopiera data från/till **Azure SQL Database**: när DTU är under hög användning föreslår du uppgradering till högre nivå. |
| &nbsp;                | Kopiera data från/till **Azure Cosmos DB:** när RU är under hög användning föreslår du uppgradering till större RU. |
| &nbsp;                | Intag av data från **Amazon Redshift:** föreslå att du använder AVlastning om den inte används. |
| Begränsning av datalager | Om ett antal läs-/skrivåtgärder begränsas av datalagret under kopieringen föreslår du att du kontrollerar och ökar den tillåtna begäranden för datalagret eller minskar den samtidiga arbetsbelastningen. |
| Körning för integrering  | Om du använder en **självvärderad integrationskörning (IR)** och kopieringsaktivitet väntar länge i kön tills IR har tillgänglig resurs att köra, föreslår du att du skalar ut/upp din IR. |
| &nbsp;                | Om du använder en **Azure Integration Runtime** som finns i en inte optimal region som resulterar i långsam läsning/skrivning föreslår du att du konfigurerar för att använda en IR i en annan region. |
| Feltolerans       | Om du konfigurerar feltolerans och hoppar över inkompatibla rader resulterar i långsam prestanda, föreslår du att käll- och diskindata är kompatibla. |
| Stegvis kopia           | Om den stegvisa kopian är konfigurerad men inte användbar för källfländningsparet föreslår du att den tas bort. |
| Återuppta                | När kopieringsaktiviteten återupptas från den senaste felpunkten men du råkar ändra DIU-inställningen efter den ursprungliga körningen, observera att den nya DIU-inställningen inte börjar gälla. |

## <a name="understand-copy-activity-execution-details"></a>Förstå information om kopieringsaktivitet

Körningsinformationen och varaktigheterna längst ned i övervakningsvyn för kopieringsaktivitet beskriver de nyckelstadier som kopieringsaktiviteten går igenom (se exempel i början av den här artikeln), vilket är särskilt användbart för felsökning av kopieringsprestandan. Flaskhalsen i kopieringskörningen är den med längst varaktighet. Se följande tabell för varje fas definition och lär dig hur [du felsöker kopieringsaktivitet på Azure IR](#troubleshoot-copy-activity-on-azure-ir) och [Felsöka kopieringsaktivitet på självvärdbaserad IR](#troubleshoot-copy-activity-on-self-hosted-ir) med sådan information.

| Fas           | Beskrivning                                                  |
| --------------- | ------------------------------------------------------------ |
| Kö           | Den förflutna tiden tills kopieringsaktiviteten faktiskt startar på integrationskörningen. |
| Förkopiera skript | Den förflutna tiden mellan kopieringsaktivitet som börjar på IR och kopieringsaktivitet som slutförs kör skriptet för förkopiering i sink-datalagret. Använd när du konfigurerar förkopieringsskriptet för databasmottagare, till exempel när du skriver data i Azure SQL Database rensar innan du kopierar nya data. |
| Överföring        | Den förflutna tiden mellan slutet av föregående steg och IR överför alla data från källa till diskho. Understeg under "Transfer" körs parallellt.<br><br>- **Tid till första byte:** Den tid som förflutit mellan slutet av föregående steg och den tidpunkt då IR tar emot den första bytet från källdatalagret. Gäller för icke-filbaserade källor.<br>- **Lista källa:** Den tid som läggs på att räkna upp källfiler eller datapartitioner. Det senare gäller när du konfigurerar partitionsalternativ för databaskällor, t.ex.<br/>-**Läsning från källa:** Den tid som läggs på att hämta data från källdatalagret.<br/>- **Skriva för att sjunka:** Den tid som läggs på att skriva data för att sänka datalagringen. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Felsöka kopieringsaktivitet på Azure IR

Följ [stegen För prestandajustering](copy-activity-performance.md#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. 

När kopieringsaktivitetsprestandan inte uppfyller dina förväntningar, för att felsöka enstaka kopieringsaktivitet som körs på Azure Integration Runtime, om du ser [prestandajusteringstips](#performance-tuning-tips) som visas i kopieringsövervakningsvyn, tillämpa förslaget och försök igen. Annars [förstår du information om kopieringsaktivitet,](#understand-copy-activity-execution-details)kontrollerar vilket stadium som har **längst** varaktighet och använder vägledningen nedan för att öka kopieringsprestanda:

- **"Pre-copy script" upplevt lång varaktighet:** det betyder att pre-copy skript som körs på sink databas tar lång tid att slutföra. Justera den angivna skriptlogiken för för kopia för att förbättra prestanda. Om du behöver ytterligare hjälp med att förbättra skriptet kontaktar du databasteamet.

- **"Transfer - Time to first byte" upplevde lång arbetslängd**: det betyder att källfrågan tar lång tid att returnera data. Kontrollera och optimera frågan eller servern. Om du behöver ytterligare hjälp kontaktar du ditt datalagerteam.

- **"Transfer - Listing source" upplevde lång arbetstid:** det innebär att räkna upp källfiler eller källdatabasdatapartitioner är långsam.

  - När du kopierar data från filbaserad källa, om du använder **jokerteckenfilter** på mappsökväg eller filnamn (`wildcardFolderPath` `wildcardFileName`eller ), eller använder **fil senast ändrat tidsfilter** (`modifiedDatetimeStart` eller`modifiedDatetimeEnd`), notera att ett sådant filter skulle resultera i att kopiera aktivitet som listar alla filer under den angivna mappen på klientsidan och sedan använder filtret. En sådan filuppräkning kan bli flaskhalsen, särskilt när endast små filer uppfyllde filterregeln.

    - Kontrollera om du kan [kopiera filer baserat på sökväg eller namn på datetime-partitionerad fil](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Ett sådant sätt inte medföra börda på notering källa sida.

    - Kontrollera om du kan använda datalagrets inbyggda filter i stället, särskilt "**prefix**" för Amazon S3 och Azure Blob. Prefix filter är ett datalager server-side filter och skulle ha mycket bättre prestanda.

    - Överväg att dela upp enstaka stora datauppsättningar i flera mindre datauppsättningar och låt dessa kopieringsjobb köra samtidigt varje tar itu med en del av data. Du kan göra detta med Lookup/GetMetadata + ForEach + Copy. Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md) eller Migrera data från Amazon [S3 till ADLS](solution-template-migration-s3-azure.md) Gen2-lösningsmallar som allmänt exempel.

  - Kontrollera om ADF rapporterar något begränsningsfel på källan eller om ditt datalager är under högt användningstillstånd. Om så är fallet, antingen minska dina arbetsbelastningar i datalagret, eller försök att kontakta datalageradministratören för att öka begränsningsgränsen eller den tillgängliga resursen.

  - Använd Azure IR i samma eller nära din källdatalagerregion.

- **"Transfer - läsning från källa" upplevde lång arbetstid:** 

  - Anta anslutningsspecifika datainläsningar om det gäller. När du till exempel kopierar data från [Amazon Redshift](connector-amazon-redshift.md)konfigurerar du för att använda Redshift UNLOAD.

  - Kontrollera om ADF rapporterar något begränsningsfel på källan eller om ditt datalager är under hög användning. Om så är fallet, antingen minska dina arbetsbelastningar i datalagret, eller försök att kontakta datalageradministratören för att öka begränsningsgränsen eller den tillgängliga resursen.

  - Kontrollera kopieringskällan och handfatmönstret: 

    - Om kopieringsmönstret stöder större än 4 dataintegrationsenheter (DIUs) - se [det här avsnittet](copy-activity-performance-features.md#data-integration-units) om detaljer, kan du i allmänhet försöka öka dius för att få bättre prestanda. 

    - Annars kan du överväga att dela upp enstaka stora datauppsättningar i flera mindre datauppsättningar och låta dessa kopieringsjobb köra samtidigt varje tar itu med en del av data. Du kan göra detta med Lookup/GetMetadata + ForEach + Copy. Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md), Migrera data från Amazon [S3 till ADLS Gen2](solution-template-migration-s3-azure.md)eller [Masskopiering med en](solution-template-bulk-copy-with-control-table.md) kontrolltabelllösningsmallar som allmänt exempel.

  - Använd Azure IR i samma eller nära din källdatalagerregion.

- **"Transfer - skriva för att sjunka" upplevt lång arbetstid:**

  - Anta anslutningsspecifika datainläsningar om det gäller. När du till exempel kopierar data till [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (tidigare SQL DW) använder du PolyBase eller COPY-sats. 

  - Kontrollera om ADF rapporterar något begränsningsfel på diskbänken eller om datalagret är under hög användning. Om så är fallet, antingen minska dina arbetsbelastningar i datalagret, eller försök att kontakta datalageradministratören för att öka begränsningsgränsen eller den tillgängliga resursen.

  - Kontrollera kopieringskällan och handfatmönstret: 

    - Om kopieringsmönstret stöder större än 4 dataintegrationsenheter (DIUs) - se [det här avsnittet](copy-activity-performance-features.md#data-integration-units) om detaljer, kan du i allmänhet försöka öka dius för att få bättre prestanda. 

    - Annars, gradvis ställa in [parallella kopior](copy-activity-performance-features.md), observera att alltför många parallella kopior kan även skada prestanda.

  - Använd Azure IR i samma eller nära din sink-datalagerregion.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Felsöka kopieringsaktivitet på självvärd ir

Följ [stegen För prestandajustering](copy-activity-performance.md#performance-tuning-steps) för att planera och genomföra prestandatest för ditt scenario. 

När kopieringsprestandan inte uppfyller dina förväntningar, för att felsöka enstaka kopieringsaktivitet som körs på Azure Integration Runtime, om du ser [prestandajusteringstips](#performance-tuning-tips) som visas i kopieringsövervakningsvyn, tillämpa förslaget och försök igen. Annars [förstår du information om kopieringsaktivitet,](#understand-copy-activity-execution-details)kontrollerar vilket stadium som har **längst** varaktighet och använder vägledningen nedan för att öka kopieringsprestanda:

- **"Kö" med lång varaktighet:** det betyder att kopieringsaktiviteten väntar länge i kön tills din självvärderade IR har resurser att köra. Kontrollera IR-kapacitet och användning och [skala upp eller ut](create-self-hosted-integration-runtime.md#high-availability-and-scalability) enligt din arbetsbelastning.

- **"Transfer - Time to first byte" upplevde lång arbetslängd**: det betyder att källfrågan tar lång tid att returnera data. Kontrollera och optimera frågan eller servern. Om du behöver ytterligare hjälp kontaktar du ditt datalagerteam.

- **"Transfer - Listing source" upplevde lång arbetstid:** det innebär att räkna upp källfiler eller källdatabasdatapartitioner är långsam.

  - Kontrollera om den självvärderade IR-datorn har låg latens som ansluter till källdatalagring. Om din källa finns i Azure kan du använda [det här verktyget](http://www.azurespeed.com/Azure/Latency) för att kontrollera svarstiden från den självvärderade IR-datorn till Azure-regionen, desto mindre desto bättre.

  - När du kopierar data från filbaserad källa, om du använder **jokerteckenfilter** på mappsökväg eller filnamn (`wildcardFolderPath` `wildcardFileName`eller ), eller använder **fil senast ändrat tidsfilter** (`modifiedDatetimeStart` eller`modifiedDatetimeEnd`), notera att ett sådant filter skulle resultera i att kopiera aktivitet som listar alla filer under den angivna mappen på klientsidan och sedan använder filtret. En sådan filuppräkning kan bli flaskhalsen, särskilt när endast små filer uppfyllde filterregeln.

    - Kontrollera om du kan [kopiera filer baserat på sökväg eller namn på datetime-partitionerad fil](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md). Ett sådant sätt inte medföra börda på notering källa sida.

    - Kontrollera om du kan använda datalagrets inbyggda filter i stället, särskilt "**prefix**" för Amazon S3 och Azure Blob. Prefix filter är ett datalager server-side filter och skulle ha mycket bättre prestanda.

    - Överväg att dela upp enstaka stora datauppsättningar i flera mindre datauppsättningar och låt dessa kopieringsjobb köra samtidigt varje tar itu med en del av data. Du kan göra detta med Lookup/GetMetadata + ForEach + Copy. Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md) eller Migrera data från Amazon [S3 till ADLS](solution-template-migration-s3-azure.md) Gen2-lösningsmallar som allmänt exempel.

  - Kontrollera om ADF rapporterar något begränsningsfel på källan eller om ditt datalager är under högt användningstillstånd. Om så är fallet, antingen minska dina arbetsbelastningar i datalagret, eller försök att kontakta datalageradministratören för att öka begränsningsgränsen eller den tillgängliga resursen.

- **"Transfer - läsning från källa" upplevde lång arbetstid:** 

  - Kontrollera om den självvärderade IR-datorn har låg latens som ansluter till källdatalagring. Om din källa finns i Azure kan du använda [det här verktyget](http://www.azurespeed.com/Azure/Latency) för att kontrollera svarstiden från den självvärderade IR-datorn till Azure-regionerna, desto mindre desto bättre.

  - Kontrollera om den självvärderade IR-datorn har tillräckligt med inkommande bandbredd för att läsa och överföra data effektivt. Om källdatalagret finns i Azure kan du använda [det här verktyget](https://www.azurespeed.com/Azure/Download) för att kontrollera nedladdningshastigheten.

  - Kontrollera självvärderade IR:s cpu- och minnesanvändningstrend i Azure-portalen -> din > översiktssida för datafabriken. Överväg att [skala upp/ut IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) om CPU-användningen är hög eller tillgängligt minne är lågt.

  - Anta anslutningsspecifika datainläsningar om det gäller. Ett exempel:

    - När du kopierar data från [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)och SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) aktivera datapartitionsalternativ för att kopiera data parallellt.

    - När du kopierar data från [HDFS](connector-hdfs.md)konfigurerar du att använda DistCp.

    - När du kopierar data från [Amazon Redshift](connector-amazon-redshift.md)konfigurerar du för att använda Redshift UNLOAD.

  - Kontrollera om ADF rapporterar något begränsningsfel på källan eller om ditt datalager är under hög användning. Om så är fallet, antingen minska dina arbetsbelastningar i datalagret, eller försök att kontakta datalageradministratören för att öka begränsningsgränsen eller den tillgängliga resursen.

  - Kontrollera kopieringskällan och handfatmönstret: 

    - Om du kopierar data från partitionsalternativaktiverade datalager bör du tänka på att gradvis ställa in [parallellkopiorna](copy-activity-performance-features.md), observera att för många parallella kopior till och med kan skada prestandan.

    - Annars kan du överväga att dela upp enstaka stora datauppsättningar i flera mindre datauppsättningar och låta dessa kopieringsjobb köra samtidigt varje tar itu med en del av data. Du kan göra detta med Lookup/GetMetadata + ForEach + Copy. Se [Kopiera filer från flera behållare](solution-template-copy-files-multiple-containers.md), Migrera data från Amazon [S3 till ADLS Gen2](solution-template-migration-s3-azure.md)eller [Masskopiering med en](solution-template-bulk-copy-with-control-table.md) kontrolltabelllösningsmallar som allmänt exempel.

- **"Transfer - skriva för att sjunka" upplevt lång arbetstid:**

  - Anta anslutningsspecifika datainläsningar om det gäller. När du till exempel kopierar data till [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (tidigare SQL DW) använder du PolyBase eller COPY-sats. 

  - Kontrollera om den självvärderade IR-datorn har låg latens som ansluter till sink-datalagring. Om din diskbänk finns i Azure kan du använda [det här verktyget](http://www.azurespeed.com/Azure/Latency) för att kontrollera svarstiden från den självvärderade IR-datorn till Azure-regionen, desto mindre desto bättre.

  - Kontrollera om den självvärderade IR-datorn har tillräckligt med utgående bandbredd för att överföra och skriva data effektivt. Om ditt sink-datalager finns i Azure kan du använda [det här verktyget](https://www.azurespeed.com/Azure/UploadLargeFile) för att kontrollera överföringshastigheten.

  - Kontrollera om självvärderade IR:s cpu- och minnesanvändningstrend i Azure-portalen -> din > översiktssida för datafabriken. Överväg att [skala upp/ut IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) om CPU-användningen är hög eller tillgängligt minne är lågt.

  - Kontrollera om ADF rapporterar något begränsningsfel på diskbänken eller om datalagret är under hög användning. Om så är fallet, antingen minska dina arbetsbelastningar i datalagret, eller försök att kontakta datalageradministratören för att öka begränsningsgränsen eller den tillgängliga resursen.

  - Överväg att gradvis ställa in [parallella kopior](copy-activity-performance-features.md), observera att alltför många parallella kopior kan även skada prestanda.

## <a name="other-references"></a>Andra referenser

Här är prestandaövervakning och justeringsreferenser för några av de datalager som stöds:

* Azure Blob storage: [Skalbarhet och prestandamål för Blob-lagring](../storage/blobs/scalability-targets.md) och [prestanda- och skalbarhetschecklista för Blob-lagring](../storage/blobs/storage-performance-checklist.md).
* Azure Table storage: [Skalbarhet och prestandamål för tabelllagring](../storage/tables/scalability-targets.md) och [prestanda och skalbarhet checklista för tabelllagring](../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: Du kan [övervaka prestanda](../sql-database/sql-database-single-database-monitor.md) och kontrollera DTU-procenten (Database Transaction Unit).
* Azure SQL Data Warehouse: Dess kapacitet mäts i datalagerenheter (DWUs). Se [Hantera beräkningskraft i Azure SQL Data Warehouse (Översikt)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Prestandanivåer i Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Lokal SQL Server: [Övervaka och finjustera för prestanda](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokal filserver: [Prestandajustering för filservrar](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda och skalbarhetsguide](copy-activity-performance.md)
- [Kopiera prestandaoptimeringsfunktioner för aktivitet](copy-activity-performance-features.md)
- [Använda Azure Data Factory för att migrera data från datasjön eller datalagret till Azure](data-migration-guidance-overview.md)
- [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)
