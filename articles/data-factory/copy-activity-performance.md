---
title: Guiden Kopiera aktivitets prestanda och skalbarhet i Azure Data Factory | Microsoft Docs
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder kopierings aktiviteten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: jingwang
ms.openlocfilehash: 7b5c0a045fe932db38666559ee415d7b27aa11e4
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614171"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guiden Kopiera aktivitets prestanda och skalbarhet
> [!div class="op_single_selector" title1="Välj den version av Azure Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [Aktuell version](copy-activity-performance.md)

Om du vill utföra en storskalig datamigrering från data Lake eller Enterprise Data Warehouse (ANALYSLÖSNINGAR) till Azure, eller om du vill mata in data i stor skala från olika källor i Azure för stor data analys, är det viktigt att uppnå optimala prestanda och bättre.  Azure Data Factory ger en utförd, elastisk och kostnads effektiv mekanism för att mata in data i stor skala, vilket gör det lättare för data tekniker att skapa högpresterande och skalbara data inmatnings pipeliner.

När du har läst den här artikeln kommer du att kunna besvara följande frågor:

- Vilken nivå av prestanda och skalbarhet kan jag använda för att använda ADF Copy-aktivitet för data migration och scenarier för data inmatning?

- Vilka steg ska jag utföra för att justera prestandan för en ADF Copy-aktivitet?
- Vilka videokanaligt prestanda optimerings rattar kan jag använda för att optimera prestanda för en enskild kopierings aktivitet?
- Vad är andra faktorer utanför ADF att överväga när du optimerar kopierings prestanda?

> [!NOTE]
> Om du inte är bekant med kopierings aktiviteten i allmänhet kan du läsa [översikten kopiera aktivitet](copy-activity-overview.md) innan du läser den här artikeln.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopiera prestanda och skalbarhet som går att nå med ADF

ADF erbjuder en server lös arkitektur som gör det möjligt att använda parallellitet på olika nivåer, vilket gör att utvecklare kan bygga pipeliner för att fullt ut utnyttja din nätverks bandbredd samt lagrings-IOPS och bandbredd för att maximera data flödet för data flödet i din miljö.  Det innebär att du kan beräkna det data flöde som du kan uppnå genom att mäta det minsta data flöde som erbjuds av käll data lagret, mål data lagret och nätverks bandbredden mellan källan och målet.  I tabellen nedan beräknas kopieringens varaktighet baserat på data storleken och bandbredds gränsen för din miljö. 

| Data storlek \ bandbredd | 50 Mbit/s    | 100 Mbit/s  | 200 Mbit/s  | 500 Mbit/s  | 1 Gbit/s   | 10 Gbit/s  |
| --------------------- | ---------- | --------- | --------- | --------- | -------- | -------- |
| 1 GB                  | 2,7 min    | 1,4 min   | 0,7 min   | 0,3 min   | 0,1 min  | 0,0 min  |
| 10 GB                 | 27,3 min   | 13,7 min  | 6,8 min   | 2,7 min   | 1,3 min  | 0,1 min  |
| 100 GB                | 4,6 timmar    | 2,3 timmar   | 1,1 timmar   | 0,5 timmar   | 0,2 timmar  | 0,0 timmar  |
| 1 TB                  | 46,6 timmar   | 23,3 timmar  | 11,7 timmar  | 4,7 timmar   | 2,3 timmar  | 0,2 timmar  |
| 10 TB                 | 19,4 dagar  | 9,7 dagar  | 4,9 dagar  | 1,9 dagar  | 0,9 dagar | 0,1 dagar |
| 100 TB                | 194,2 dagar | 97,1 dagar | 48,5 dagar | 19,4 dagar | 9,5 dagar | 0,9 dagar |
| 1 PB                  | 64,7 mo    | 32,4 Mo   | 16,2 Mo   | 6,5 Mo    | 3,2 Mo   | 0,3 Mo   |
| 10 PB                 | 647,3 Mo   | 323,6 Mo  | 161,8 Mo  | 64,7 mo   | 31,6 Mo  | 3,2 Mo   |

ADF-kopian är skalbar på olika nivåer:

![så här skalar ADF-kopiering](media/copy-activity-performance/adf-copy-scalability.png)

- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser: när du använder Azure Integration Runtime kan du ange [upp till 256 DIUs](#data-integration-units) för varje kopierings aktivitet på ett Server lös sätt. När du använder Integration Runtime med egen värd kan du skala upp datorn manuellt eller skala ut till flera datorer ([upp till 4 noder](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) och en enda kopierings aktivitet partitionerar dess fil uppsättning på alla noder.
- En enskild kopierings aktivitet läser från och skriver till data lagret med hjälp av flera trådar.
- ADF-kontroll flödet kan starta flera kopierings aktiviteter parallellt, till exempel använda [för varje slinga](control-flow-for-each-activity.md).

## <a name="performance-tuning-steps"></a>Prestanda justering steg

Följ dessa steg för att justera prestandan för din Azure Data Factory-tjänst med kopierings aktiviteten.

1. **Upprätta en bas linje.** Under utvecklings fasen testar du din pipeline med hjälp av kopierings aktiviteten mot ett representativt data exempel. Samla in körnings information och prestanda egenskaper efter [Kopiera aktivitets övervakning](copy-activity-overview.md#monitoring).

2. **Maximera prestanda för en enskild kopierings aktivitet**:

   För att börja med rekommenderar vi att du först maximerar prestandan med en enda kopierings aktivitet.

   **Om kopierings aktiviteten körs på en Azure Integration Runtime:**

   Börja med standardvärden för [data integrerings enheter (DIU)](#data-integration-units) och inställningar för [parallell kopiering](#parallel-copy) .  Utför en prestandatest och anteckna de prestanda som uppnås samt de faktiska värdena som används för DIUs och parallella kopior.  Information om hur du samlar in körnings resultat och prestanda inställningar som används hittar du i [Kopiera aktivitets övervakning](copy-activity-overview.md#monitoring) .

   Nu kan du utföra ytterligare prestandatest körningar, varje gång som dubblerar värdet för DIU-inställningen.  Om du tror att prestandan som uppnås med hjälp av Standardinställningen är långt lägre än förväntat kan du öka DIU-inställningen mer drastiskt i den efterföljande test körningen.

   Kopierings aktiviteten bör skalas nästan helt linjärt när du ökar DIU-inställningen.  Om du dubblerar DIU-inställningen som du inte ser data flödet dubbelt, kan det hända att två saker händer:

   - Det angivna kopierings mönstret som du kör drar inte nytta av att lägga till fler DIUs.  Även om du har angett ett större DIU-värde, är den faktiska DIU som används oförändrad, och därför får du samma data flöde som tidigare.  I så fall går du till steg #3
   - Genom att lägga till fler DIUs (mer kraft) och därigenom köra högre frekvens av data extrahering, överföring och inläsning, är antingen käll data lagret, nätverket i mellan eller mål data lagret nått sin Flask hals och eventuellt begränsas.  Om så är fallet kan du prova att kontakta data lager administratören eller nätverks administratören för att höja den övre gränsen, eller också kan du minska inställningen för DIU tills begränsningen stoppas.

   **Om kopierings aktiviteten körs på en egen värd Integration Runtime:**

   Vi rekommenderar att du använder en dedikerad dator separat från den server som är värd för data lagret till värd integrerings körning

   Börja med standardvärden för inställningen för [parallell kopiering](#parallel-copy) och Använd en enda nod för IR med egen värd.  Kör en prestandatest och anteckna de prestanda som uppnås.

   Om du vill uppnå högre data flöde kan du skala upp eller skala ut den egna IR: en:

   - Om processorn och det tillgängliga minnet på IR-noden med egen värd inte används fullt ut, men körningen av samtidiga jobb når gränsen, bör du skala upp genom att öka antalet samtidiga jobb som kan köras på en nod.  Mer information finns [här](create-self-hosted-integration-runtime.md#scale-up) .
   - Om till å andra sidan är processorn hög på den lokala IR-noden och tillgängligt minne är lågt kan du lägga till en ny nod för att skala belastningen över flera noder.  Mer information finns [här](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

   När du skalar upp eller skalar upp kapaciteten för IR med egen värd upprepar du prestanda testet för att se om du får allt bättre data flöde.  Om data flödet slutar att förbättra kan det bero på att käll data lagret, nätverket i mellan eller mål data lagret har nått sin Flask hals och börjar få en begränsad begränsning. Om så är fallet kan du försöka kontakta din administratör för data lager eller nätverks administratören för att höja den övre gränsen, eller så kan du gå tillbaka till din tidigare skalnings inställning för IR med egen värd. 

3. **Maximera sammanställd genom strömning genom att köra flera kopior samtidigt:**

   Nu när du har maximerat prestandan för en enskild kopierings aktivitet och du ännu inte har uppnått de övre gränserna för data flödet – nätverk, käll data lager och mål data lager, kan du köra flera kopierings aktiviteter parallellt med ADF styr flödes konstruktioner, till exempel [för varje slinga](control-flow-for-each-activity.md).

4. **Diagnostisera och optimera prestanda.** Om den prestanda du anser inte uppfyller dina förväntningar kan du identifiera Flask halsar i prestanda. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar.

   I vissa fall när du kör en kopierings aktivitet i Azure Data Factory visas ett meddelande om "prestanda justerings tips" ovanpå [kopierings aktivitets övervakningen](copy-activity-overview.md#monitor-visually), som du ser i följande exempel. Meddelandet anger att du har identifierat Flask halsen som identifierades för den angivna kopierings körningen. Du får också information om vad du kan ändra för att förstärka kopieringen av kopierings data. Tips för prestanda justering ger för närvarande förslag som:

   - Använd PolyBase när du kopierar data till Azure SQL Data Warehouse.
   - Öka Azure Cosmos DB enheter för programbegäran eller Azure SQL Database DTU: er (databas data flödes enheter) när resursen på data lagrings sidan är Flask halsen.
   - Ta bort den onödiga mellanlagrade kopian.

   Reglerna för prestanda justering kommer även att berikas.

   **Exempel: Kopiera till Azure SQL Database med prestanda justerings tips**

   I det här exemplet under en kopierings körning Azure Data Factory meddelanden om att mottagaren Azure SQL Database når hög DTU-användning, vilket gör att Skriv åtgärderna blir långsammare. Förslaget är att öka Azure SQL Database nivån med fler DTU: er. 

   ![Kopiera övervakning med prestanda justerings tips](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Dessutom är följande några vanliga överväganden. En fullständig beskrivning av prestanda diagnosen ligger utanför omfånget för den här artikeln.

   - Prestanda optimerings funktioner:
     - [Parallell kopia](#parallel-copy)
     - [Dataintegrationsenheter](#data-integration-units)
     - [Mellanlagrad kopiering](#staged-copy)
   - [Skalbarhet för integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Expandera konfigurationen till hela data uppsättningen.** När du är nöjd med körnings resultatet och prestandan kan du expandera definitionen och pipelinen för att hantera hela data uppsättningen.

## <a name="copy-performance-optimization-features"></a>Kopiera funktioner för prestanda optimering

Azure Data Factory innehåller följande funktioner för prestanda optimering:

- [Parallell kopia](#parallel-copy)
- [Dataintegrationsenheter](#data-integration-units)
- [Mellanlagrad kopiering](#staged-copy)

### <a name="data-integration-units"></a>Data integrerings enheter

En data integrerings enhet är ett mått som representerar styrkan (en kombination av processor, minne och tilldelning av nätverks resurser) för en enskild enhet i Azure Data Factory. Data integrerings enheten gäller endast för [Azure integration runtime](concepts-integration-runtime.md#azure-integration-runtime), men inte för [integration runtime med egen värd](concepts-integration-runtime.md#self-hosted-integration-runtime).

Den tillåtna DIUs som gör att kopierings aktiviteten körs är mellan 2 och 256. Om inte anges visas i följande tabell de standard-DIUs som används i olika kopia scenarier:

| Kopiera scenario | Standard DIUs bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lager | Mellan 4 och 32 beroende på antalet och storleken på filerna |
| Kopiera data till Azure SQL Database eller Azure Cosmos DB |Mellan 4 och 16 beroende på Azure SQL Database mottagarens eller Cosmos DBs nivå (antal DTU: er/ru: er) |
| Alla andra kopierings scenarier | 4 |

Om du vill åsidosätta denna standardinställning, ange ett värde för den **dataIntegrationUnits** egenskapen på följande sätt. Den *faktiska antalet DIUs* att kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på din datamönster.

Du kan se DIUs som används för varje kopierings körning i kopierings aktivitetens utdata när du övervakar en aktivitets körning. Mer information finns i avsnittet [Kopiera aktivitets övervakning](copy-activity-overview.md#monitoring).

> [!NOTE]
> Inställningen av DIUs som är större än fyra gäller för närvarande endast när du kopierar flera filer från Azure Storage, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, Cloud FTP eller Cloud SFTP till andra moln data lager.

**Exempel**

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
            "dataIntegrationUnits": 32
        }
    }
]
```

#### <a name="data-integration-units-billing-impact"></a>Data Integration enheter fakturering påverkan

Kom ihåg att du debiteras utifrån den totala tiden för kopierings åtgärden. Den totala varaktigheten som du debiteras för data förflyttning är summan av varaktigheten för DIUs. Om ett kopieringsjobb brukade ta en timme med två molnenheter och så tar det nu 15 minuter med åtta molnenheter, förblir övergripande fakturan nästan samma.

### <a name="parallel-copy"></a>Parallell kopia

Du kan använda egenskapen **parallelCopies** för att ange den parallellitet som du vill att kopierings aktiviteten ska använda. Du kan tänka på den här egenskapen som det maximala antalet trådar i kopierings aktiviteten som kan läsa från din källa eller skriva till dina mottagar data lager parallellt.

För varje kopierings aktivitets körning fastställer Azure Data Factory antalet parallella kopior som ska användas för att kopiera data från käll data lagret och till mål data lagret. Standard antalet parallella kopior som används beror på vilken typ av källa och mottagare du använder.

| Kopiera scenario | Parallell kopia Standardantal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade lager |Beror på storleken på filerna och antalet DIUs som används för att kopiera data mellan två moln data lager eller den fysiska konfigurationen för den lokala integration runtime-datorn. |
| Kopiera data från alla käll arkiv till Azure Table Storage |4 |
| Alla andra kopia-scenarier |1 |

> [!TIP]
> När du kopierar data mellan filbaserade butiker ger standard beteendet oftast det bästa data flödet. Standard beteendet bestäms automatiskt baserat på ditt käll fils mönster.

Om du vill styra belastningen på datorer som är värdar för dina data lager, eller om du vill justera kopierings prestanda, kan du åsidosätta standardvärdet och ange ett värde för egenskapen **parallelCopies** . Värdet måste vara ett heltal större än eller lika med 1. Vid körning, för bästa prestanda, använder kopierings aktiviteten ett värde som är mindre än eller lika med det värde som du anger.

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

**Poäng till Anmärkning:**

* När du kopierar data mellan filbaserade lager, bestämmer **parallelCopies** parallellt på filnivå. Delningen i en enskild fil sker under automatiskt och transparent. Den är utformad för att använda den bästa lämpliga segment storleken för en specifik käll data lager typ för att läsa in data parallellt och rätvinkligt till **parallelCopies**. Det faktiska antalet parallella kopior av data movement service använder för att kopieringen under körning är inte fler än antalet filer som du har. Om kopierings beteendet är **mergeFile**kan kopierings aktiviteten inte dra nytta av Parallel på filnivå.
* När du kopierar data från butiker som inte är filbaserade (förutom [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)och [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Connector som källa med data partitionering aktiverat) till butiker som är filbaserade, tjänsten för data förflyttning ignorerar egenskapen **parallelCopies** . Även om parallellitet anges, tillämpas den inte i det här fallet.
* Egenskapen **parallelCopies** är rätvinklig till **dataIntegrationUnits**. Det tidigare räknas över alla enheter för Data-integrering.
* När du anger ett värde för egenskapen **parallelCopies** bör du ta hänsyn till belastnings ökningen på källan och mottagar data lager. Tänk också på belastnings ökningen till den egna värdbaserade integrerings körningen om kopierings aktiviteten har befogenheter av den, till exempel för Hybrid kopiering. Den här belastnings ökningen sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma data lager. Om du ser att antingen data lagret eller den egen värdbaserade integrerings körningen är överbelastad, minskar du **parallelCopies** -värdet för att avlasta belastningen.

### <a name="staged-copy"></a>Mellanlagrad kopiering

När du kopierar data från källans datalager till mottagarens datalager kan du välja att använda Blob storage som en mellanliggande mellanlagringsarkivet. Mellanlagring är särskilt användbart i följande fall:

- **Du vill mata in data från olika data lager i SQL Data Warehouse via PolyBase.** SQL Data Warehouse använder PolyBase som en mekanism för stora dataflöden för att läsa in en stor mängd data till SQL Data Warehouse. Källdata måste finnas i Blob Storage eller Azure Data Lake Store, och det måste uppfylla ytterligare kriterier. När du läser in data från ett datalager än Blob storage eller Azure Data Lake Store kan du aktivera data kopiering via tillfälliga mellanlagringsplatsen Blob-lagring. I så fall utför Azure Data Factory nödvändiga data transformationer för att säkerställa att de uppfyller kraven för PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse effektivt. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Ibland tar det en stund att utföra en hybrid data förflyttning (det vill säga kopiera från ett lokalt data lager till ett moln data lager) över en långsam nätverks anslutning.** Du kan förbättra prestanda genom att använda mellanlagrad kopia för att komprimera data lokalt så att det tar mindre tid att flytta data till lagrings data lagret i molnet. Sedan kan du expandera data i mellanlagrings platsen innan du läser in i mål data lagret.
- **Du vill inte öppna andra portar än port 80 och port 443 i brand väggen på grund av företagets IT-principer.** När du kopierar data från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink måste aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot kan mellanlagrad kopiering dra nytta av den lokala integrerings körningen för att först kopiera data till en mellanlagringsplats för Blob Storage via HTTP eller HTTPS på port 443. Sedan kan den läsa in data i SQL Database eller SQL Data Warehouse från mellanlagring av blob-lagring. I det här flödet behöver du inte aktivera port 1433.

#### <a name="how-staged-copy-works"></a>Hur mellanlagrad kopiering fungerar

När du aktiverar funktionen mellanlagring först data kopieras från källdatalagret till mellanlagring Blob-lagringen (ta med din egen). Därefter kopieras data från datalager för mellanlagring till datalager för mottagare. Azure Data Factory hanterar automatiskt flödet i två steg åt dig. Azure Data Factory rensar också tillfälliga data från mellanlagringen när data förflyttningen är klar.

![Mellanlagrad kopiering](media/copy-activity-performance/staged-copy.png)

När du aktiverar data förflyttning med hjälp av ett mellanlagrings lager, kan du ange om du vill att data ska komprimeras innan du flyttar data från käll data lagret till ett interimistiskt eller mellanlagrat data lager och sedan expanderar innan du flyttar data från ett interimistiskt eller mellanlagrat dat en butik till data lagret för mottagare.

För närvarande kan du inte kopiera data mellan två data lager som är anslutna via en annan egen värd, varken med eller utan mellanlagrad kopia. I sådana fall kan du konfigurera två explicit länknings aktivitet som ska kopieras från källa till mellanlagring och sedan från mellanlagring till mottagare.

#### <a name="configuration"></a>Konfiguration

Konfigurera inställningen **enableStaging** i kopierings aktiviteten och ange om du vill att data ska mellanlagras i Blob Storage innan du läser in dem i ett mål data lager. När du ställer in enableStaging `TRUE`på anger du ytterligare egenskaper som anges i följande tabell. Du måste också skapa en signatur för delad åtkomst för Azure Storage eller lagrings plats för delad åtkomst för mellanlagring om du inte har någon.

| Egenskap | Beskrivning | Standardvärde | Obligatorisk |
| --- | --- | --- | --- |
| enableStaging |Ange om du vill kopiera data via en tiden mellanlagring store. |False |Nej |
| linkedServiceName |Ange namnet på en [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) länkad tjänst som refererar till instansen av lagring som du använder som ett tillfälligt mellanlagringsarkivet. <br/><br/> Du kan inte använda Storage med en signatur för delad åtkomst för att läsa in data i SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Gäller inte |Ja, när **enableStaging** har angetts till TRUE |
| path |Ange sökvägen för Blob-lagring som du vill ska innehålla den mellanlagrade data. Om du inte anger en sökväg skapar tjänsten en behållare för att lagra temporära data. <br/><br/> Ange en sökväg endast om du använder lagring med signatur för delad åtkomst, eller du kräver tillfälliga data finnas i en viss plats. |Gäller inte |Nej |
| enableCompression |Anger om data ska komprimeras innan de kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

>[!NOTE]
> Om du använder mellanlagrad kopiering med komprimering aktive rad stöds inte tjänstens huvud namn eller MSI-autentisering för den länkade Blob-tjänsten.

Här är en exempel definition av en kopierings aktivitet med de egenskaper som beskrivs i föregående tabell:

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

#### <a name="staged-copy-billing-impact"></a>Mellanlagrad kopiering fakturering påverkan

Du debiteras baserat på två steg: kopiera varaktighet och kopierings typ.

* När du använder mellanlagring under en moln kopia, som kopierar data från ett moln data lager till ett annat moln data lager, båda stegen som du har fått av Azure integration runtime debiteras du [summan av kopierings tiden för steg 1 och steg 2] x [Cloud Copy Unit Price].
* När du använder mellanlagring under en hybrid kopiering, som kopierar data från ett lokalt data lager till ett moln data lager, en fas som har en egen värd för integration runtime debiteras du för [hybrid kopieringens varaktighet] x [hybrid kopiera enhets pris] + [moln kopieringens varaktighet] x [Cloud Copy Unit-pris].

## <a name="references"></a>Referenser

Här följer prestanda övervakning och justering av referenser för några av de data lager som stöds:

* Azure Storage, inklusive Blob Storage och table Storage: [Azure Storage skalbarhets mål](../storage/common/storage-scalability-targets.md) och [Azure Storage check lista för prestanda och skalbarhet](../storage/common/storage-performance-checklist.md).
* Azure SQL Database: Du kan [övervaka prestanda](../sql-database/sql-database-single-database-monitor.md) och kontrol lera DTU-procenten (Database Transaction Unit).
* Azure SQL Data Warehouse: Dess funktion mäts i informations lager enheter (DWU: er). Se [hantera beräknings kraft i Azure SQL Data Warehouse (översikt)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Prestanda nivåer i Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Lokala SQL Server: [Övervaka och justera för prestanda](https://msdn.microsoft.com/library/ms189081.aspx).
* Lokal fil Server: [Prestanda justering för fil servrar](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Nästa steg
Se andra artiklar om kopierings aktiviteter:

- [Översikt över Kopieringsaktivitet](copy-activity-overview.md)
- [Kopiera aktivitets schema mappning](copy-activity-schema-and-type-mapping.md)
- [Kopiera aktivitet feltolerans](copy-activity-fault-tolerance.md)
