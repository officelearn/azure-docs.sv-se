---
title: Kopiera prestandaoptimeringsfunktioner för aktivitet
description: Lär dig mer om de viktigaste funktionerna som hjälper dig att optimera kopieringsaktivitetsprestandan i Azure Data Factory.
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
ms.date: 03/09/2020
ms.openlocfilehash: fd7844340553809e1429097a9dda70f6bdb3e075
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414194"
---
# <a name="copy-activity-performance-optimization-features"></a>Kopiera prestandaoptimeringsfunktioner för aktivitet

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här artikeln beskrivs de prestandaoptimeringsfunktioner för kopieringsaktivitet som du kan utnyttja i Azure Data Factory.

## <a name="data-integration-units"></a>Dataintegrationsenheter

En dataintegrationsenhet är ett mått som representerar kraften (en kombination av CPU, minne och nätverksresursallokering) för en enskild enhet i Azure Data Factory. Dataintegrationsenhet gäller endast för [Azure-integreringskörning](concepts-integration-runtime.md#azure-integration-runtime), men inte [självvärd för integrationskörning](concepts-integration-runtime.md#self-hosted-integration-runtime).

De tillåtna dius för att ge en kopieringsaktivitet körs är **mellan 2 och 256**. Om du inte anges eller om du väljer "Auto" i användargränssnittet, använder Data Factory dynamiskt den optimala DIU-inställningen baserat på käll-sink-par och datamönster. I följande tabell visas diu-intervall som stöds och standardbeteende i olika kopieringsscenarier:

| Kopiera scenario | DIU-intervall som stöds | Standarddiser som bestäms av tjänsten |
|:--- |:--- |---- |
| Mellan filarkiv |- **Kopiera från eller till en fil:** 2-4 <br>- **Kopiera från och till flera filer:** 2-256 beroende på antalet och storleken på filerna <br><br>Om du till exempel kopierar data från en mapp med 4 stora filer och väljer att bevara hierarkin, är den högsta effektiva DIU 16. när du väljer att sammanfoga filen är den högsta effektiva DIU 4. |Mellan 4 och 32 beroende på antalet och storleken på filerna |
| Från filarkiv till icke-filarkiv |- **Kopiera från en enda fil:** 2-4 <br/>- **Kopiera från flera filer:** 2-256 beroende på antalet och storleken på filerna <br/><br/>Om du till exempel kopierar data från en mapp med 4 stora filer är den högsta effektiva DIU 16. |- **Kopiera till Azure SQL Database eller Azure Cosmos DB:** mellan 4 och 16 beroende på sink-nivån (DTUs/RU:er) och källfilmönster<br>- **Kopiera till Azure Synapse Analytics** med PolyBase eller COPY-sats: 2<br>- Annat scenario: 4 |
| Från icke-filarkiv till filarkiv |- **Kopiera från partition-alternativ-aktiverade datalager** (inklusive [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 när du skriver till en mapp, och 2-4 när du skriver till en enda fil. Datapartitionen per källa kan använda upp till fyra dius.<br>- **Andra scenarier:** 2-4 |- **Kopiera från REST eller HTTP:** 1<br/>- **Kopiera från Amazon Redshift** med HJÄLP AV UNLOAD: 2<br>- **Annat scenario**: 4 |
| Mellan icke-filarkiv |- **Kopiera från partition-alternativ-aktiverade datalager** (inklusive [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 när du skriver till en mapp, och 2-4 när du skriver till en enda fil. Datapartitionen per källa kan använda upp till fyra dius.<br/>- **Andra scenarier:** 2-4 |- **Kopiera från REST eller HTTP:** 1<br>- **Annat scenario**: 4 |

Du kan se de dius som används för varje kopieringskörning i övervakningsvyn för kopieringsaktivitet eller aktivitetsutdata. Mer information finns i [Kopiera aktivitetsövervakning](copy-activity-monitoring.md). Om du vill åsidosätta den `dataIntegrationUnits` här standardinställningen anger du ett värde för egenskapen enligt följande. Det *faktiska antalet diu:er* som kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på ditt datamönster.

Du kommer att debiteras **# av begagnade DIUs \* kopiera varaktighet enhetspris \* / DIU-timme**. Se aktuella priser [här](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Lokal valuta och separat diskontering kan gälla per prenumerationstyp.

**Exempel:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Självvärd för integreringskörningsskalbarhet

Om du vill uppnå högre dataflöde kan du antingen skala upp eller skala ut självvärdbaserade IR:

- Om processorn och det tillgängliga minnet på den självvärderade IR-noden inte utnyttjas fullt ut, men körningen av samtidiga jobb når gränsen, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod.  Se [här](create-self-hosted-integration-runtime.md#scale-up) för instruktioner.
- Om processorn å andra sidan är hög på den självvärderade IR-noden eller det tillgängliga minnet är lågt, kan du lägga till en ny nod för att skala ut belastningen över flera noder.  Se [här](create-self-hosted-integration-runtime.md#high-availability-and-scalability) för instruktioner.

I följande scenarier kan körning av en kopia av aktivitet utnyttja flera självvärderade IR-noder:

- Kopiera data från filbaserade butiker, beroende på filernas antal och storlek.
- Kopiera data från partitionsalternativaktiverat datalager (inklusive [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)och SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), beroende på antalet datapartitioner.

## <a name="parallel-copy"></a>Parallell kopia

Du kan ange`parallelCopies` parallellkopia (egenskap) på kopieringsaktivitet för att ange den parallellitet som du vill att kopieringsaktiviteten ska använda. Du kan se den här egenskapen som det maximala antalet trådar i kopieringsaktiviteten som läs av källan eller skriver till dina sink-datalager parallellt.

Den parallella kopian är ortogonal till [dataintegrationsenheter](#data-integration-units) eller [självvärderade IR-noder](#self-hosted-integration-runtime-scalability). Det räknas över alla DIUs eller självvärderade IR-noder.

För varje kopieringsaktivitetskörning tillämpar Azure Data Factory som standard den optimala inställningen för parallellkopia baserat på källmottagarens par och datamönster. 

> [!TIP]
> Standardbeteendet för parallellkopia ger dig vanligtvis det bästa dataflödet, som bestäms automatiskt av ADF baserat på käll-sink-par, datamönster och antal DIUs eller ir:s CPU/minne/nodantal. Se [Felsöka kopieringsaktivitetsprestanda](copy-activity-performance-troubleshooting.md) när parallellkopian ska justeras.

I följande tabell visas parallellkopieringsbeteendet:

| Kopiera scenario | Parallellt kopieringsbeteende |
| --- | --- |
| Mellan filarkiv | `parallelCopies`bestämmer parallellismen **på filnivå**. Segmenteringen i varje fil sker under automatiskt och transparent. Den är utformad för att använda den bästa lämpliga segmentstorleken för en viss datalagertyp för att läsa in data parallellt. <br/><br/>Det faktiska antalet parallella kopior kopieringsaktivitet använder vid körning är inte mer än antalet filer du har. Om kopieringsbeteendet **sammanfogasFile** till filmottagare kan kopieringsaktiviteten inte dra nytta av parallellisering på filnivå. |
| Från filarkiv till icke-filarkiv | - När du kopierar data till Azure SQL Database eller Azure Cosmos DB beror även standardparallkopian på sink-nivån (antal DTUs/RU:er).<br>- När du kopierar data till Azure Table är standard parallellkopia 4. |
| Från icke-filarkiv till filarkiv | - När du kopierar data från partition-alternativ-aktiverade datalager (inklusive [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Tabell](connector-sap-table.md#sap-table-as-source)och SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)standard parallell kopia är 4. Det faktiska antalet parallella kopior kopieringsaktivitet använder vid körning är inte mer än antalet datapartitioner du har. När du använder Självvärdförd integrationskörning och kopiera till Azure Blob/ADLS Gen2, observera att den högsta effektiva parallellkopian är 4 eller 5 per IR-nod.<br>- För andra scenarier börjar inte parallellkopia gälla. Även om parallellism anges, är det inte tillämpas. |
| Mellan icke-filarkiv | - När du kopierar data till Azure SQL Database eller Azure Cosmos DB beror även standardparallkopian på sink-nivån (antal DTUs/RU:er).<br/>- När du kopierar data från partition-alternativ-aktiverade datalager (inklusive [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Tabell](connector-sap-table.md#sap-table-as-source)och SAP [Open Hub),](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)standard parallell kopia är 4.<br>- När du kopierar data till Azure Table är standard parallellkopia 4. |

Om du vill styra belastningen på datorer som är värdar för datalager eller justera `parallelCopies` kopieringsprestanda kan du åsidosätta standardvärdet och ange ett värde för egenskapen. Värdet måste vara ett heltal som är större än eller lika med 1. Vid körning använder kopieringsaktiviteten för bästa prestanda ett värde som är mindre än eller lika med det värde som du anger.

När du anger ett `parallelCopies` värde för egenskapen tar du hänsyn till inläsningsökningen på käll- och sinkdatalager. Tänk också på belastningsökningen till den självvärderade integrationskörningen om kopieringsaktiviteten har befogenhet av den. Den här inläsningsökningen sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du märker att antingen datalagret eller den självvärderade integrationskörningen `parallelCopies` är överväldigad med belastningen minskar du värdet för att avlasta.

**Exempel:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Stegvis kopia

När du kopierar data från ett källdatalager till ett sink-datalager kan du välja att använda Blob-lagring som ett interimistiskt mellanlagringsarkiv. Mellanlagring är särskilt användbart i följande fall:

- **Du vill använda data från olika datalager i SQL Data Warehouse via PolyBase.** SQL Data Warehouse använder PolyBase som en mekanism med högt dataflöde för att läsa in en stor mängd data i SQL Data Warehouse. Källdata måste finnas i Blob-lagring eller Azure Data Lake Store och måste uppfylla ytterligare kriterier. När du läser in data från ett annat datalager än Blob-lagring eller Azure Data Lake Store kan du aktivera datakopiering via tillfällig mellanlagrings-Blob-lagring. I så fall utför Azure Data Factory de dataomvandlingar som krävs för att säkerställa att den uppfyller kraven i PolyBase. Sedan använder PolyBase för att läsa in data i SQL Data Warehouse effektivt. Mer information finns i [Använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Ibland tar det ett tag att utföra en hybriddataförflyttning (det vill veta mer från ett lokalt datalager till ett molndatalager) via en långsam nätverksanslutning.** För att förbättra prestanda kan du använda stegvis kopia för att komprimera data lokalt så att det tar mindre tid att flytta data till mellanlagringsdatalagret i molnet. Sedan kan du expandera data i mellanlagringsarkivet innan du läser in i måldatalagret.
- **Du vill inte öppna andra portar än port 80 och port 443 i brandväggen på grund av företagets IT-principer.** När du till exempel kopierar data från ett lokalt datalager till en Azure SQL Database-diskho eller en Azure SQL Data Warehouse-mottagare måste du aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här fallet kan stegvis kopia dra nytta av den självvärderade integrationskörningen för att först kopiera data till en Blob-lagringsdelsättningsinstans via HTTP eller HTTPS på port 443. Sedan kan det läsa in data i SQL Database eller SQL Data Warehouse från Blob storage staging. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Så här fungerar iscensatt kopia

När du aktiverar mellanlagringsfunktionen kopieras först data från källdatalagret till mellanlagringsbloblagringslagringen (ta med egna). Därefter kopieras data från mellanlagringsdatalagret till sink-datalagret. Azure Data Factory hanterar automatiskt tvåstegsflödet åt dig. Azure Data Factory rensar också tillfälliga data från mellanlagringslagringen när dataförflyttningen är klar.

![Stegvis kopia](media/copy-activity-performance/staged-copy.png)

När du aktiverar dataförflyttning med hjälp av ett mellanlagringslager kan du ange om du vill att data ska komprimeras innan du flyttar data från källdatalagret till ett mellanlagringsdatalager och sedan expanderade innan du flyttar data från ett mellanliggande datalager eller mellanlagringsdatalager till datalagret sink.

För närvarande kan du inte kopiera data mellan två datalager som är anslutna via olika självvärdbaserade IR,varken med eller utan stegvis kopia. För ett sådant scenario kan du konfigurera två explicit kedjade kopieringsaktiviteter för att kopiera från källa till mellanlagring sedan från mellanlagring till diskbänk.

### <a name="configuration"></a>Konfiguration

Konfigurera inställningen **enableStaging** i kopieringsaktiviteten för att ange om du vill att data ska mellanlagras i Blob-lagring innan du läser in dem i ett måldatalager. När du anger **enableStaging** till `TRUE`anger du de ytterligare egenskaper som anges i följande tabell. Du måste också skapa en Azure Storage- eller Storage-signaturlänkad tjänst för mellanlagring om du inte har någon.

| Egenskap | Beskrivning | Standardvärde | Krävs |
| --- | --- | --- | --- |
| enableStaging |Ange om du vill kopiera data via ett interimistiskt mellanlagringsarkiv. |False |Inga |
| linkedServiceName |Ange namnet på en [AzureStorage-länkad](connector-azure-blob-storage.md#linked-service-properties) tjänst, som refererar till den instans av lagring som du använder som ett interimistiskt mellanlagringsarkiv. <br/><br/> Du kan inte använda Lagring med en signatur för delad åtkomst för att läsa in data i SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Ej tillämpligt |Ja, när **enableStaging** är inställt på SANT |
| path |Ange den Blob-lagringssökväg som du vill innehålla mellanlagringsdata. Om du inte anger en sökväg skapar tjänsten en behållare för att lagra tillfälliga data. <br/><br/> Ange bara en sökväg om du använder Lagring med en signatur för delad åtkomst eller om du behöver tillfälliga data för att vara på en viss plats. |Ej tillämpligt |Inga |
| aktiveraKomprimering |Anger om data ska komprimeras innan de kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Inga |

>[!NOTE]
> Om du använder stegvis kopia med komprimering aktiverad stöds inte tjänstens huvudnamn eller MSI-autentisering för mellanlagringsbloblänkad tjänst.

Här är en exempeldefinition av en kopieringsaktivitet med de egenskaper som beskrivs i föregående tabell:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Fakturaeffekt för stegvis kopia

Du debiteras baserat på två steg: kopieringslängd och kopieringstyp.

* När du använder mellanlagring under en molnkopia, som kopierar data från ett molndatalager till ett annat molndatalager, debiteras båda faserna med Azure-integreringskörning, [summan av kopieringstiden för steg 1 och steg 2] x [molnkopieringsenhetspriset].
* När du använder mellanlagring under en hybridkopia, som kopierar data från ett lokalt datalager till ett molndatalager, debiteras ett steg som har befogenhet av en självvärderad integrationskörning, du debiteras för [hybridkopieringsvaraktighet] x [hybridkopieringsenhetspris] + [molnkopieringsvaraktighet] x [molnkopieringsenhetspris].

## <a name="next-steps"></a>Nästa steg
Se de andra artiklarna för kopieringsaktivitet:

- [Kopiera aktivitetsöversikt](copy-activity-overview.md)
- [Kopiera aktivitetsprestanda och skalbarhetsguide](copy-activity-performance.md)
- [Felsöka prestanda för kopieringsaktivitet](copy-activity-performance-troubleshooting.md)
- [Använda Azure Data Factory för att migrera data från datasjön eller datalagret till Azure](data-migration-guidance-overview.md)
- [Migrera data från Amazon S3 till Azure Storage](data-migration-guidance-s3-azure-storage.md)