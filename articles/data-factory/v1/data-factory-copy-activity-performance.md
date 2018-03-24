---
title: Kopiera aktivitet prestanda och prestandajustering guiden | Microsoft Docs
description: Läs mer om viktiga faktorer som påverkar prestandan för flytt av data i Azure Data Factory när du använder Kopieringsaktiviteten.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e8cb5df31a87f72c9947f78c25a41b74399b3727
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiera prestandajustering guide och prestanda för aktiviteten
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory, som är allmänt tillgänglig (GA). Om du använder version 2 av Data Factory-tjänsten, som finns i förhandsgranskningen, se [kopiera aktivitet prestanda och prestandajustering guide för Data Factory version 2](../copy-activity-performance.md).

Azure Data Factory-Kopieringsaktiviteten levererar en förstklassig säker, tillförlitlig och högpresterande datainläsning lösning. Det gör att du kan kopiera flera terabyte data varje dag i omfattande olika molnet och lokala datalager. Blixtsnabb snabb prestanda för datainläsning är nyckeln till att se till att du kan fokusera på core ”big data” problemet: skapa lösningar för avancerade analyser och hämtar djupa insikter från alla data.

Azure tillhandahåller en uppsättning företagsklass lösningar för lagring och data warehouse och Kopieringsaktivitet erbjuder en hög optimal upplevelse är enkel att konfigurera och ställa in för datainläsning. Med bara en enda kopia aktivitet, kan du få:

* Läser in data i **Azure SQL Data Warehouse** på **1,2 Gbit/s**. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Läser in data i **Azure Blob storage** på **1.0 Gbit/s**
* Läser in data i **Azure Data Lake Store** på **1.0 Gbit/s**

Den här artikeln beskrivs:

* [Prestanda referensnummer](#performance-reference) stöd för källa och mottagare datalager för att planera projektet.
* Funktioner som kan öka kopiera genomflöde i olika scenarier, inklusive [molnet data movement enheter](#cloud-data-movement-units), [parallell kopiera](#parallel-copy), och [mellanlagrad kopiera](#staged-copy);
* [Riktlinjer för prestandajustering](#performance-tuning-steps) på hur du ställer in prestanda- och de viktigaste faktorerna som kan påverka prestanda för kopia.

> [!NOTE]
> Om du inte är bekant med Kopieringsaktiviteten i allmänhet kan se [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestandareferens för

Som en referens, visar tabellen nedan kopiera genomflöde i Mbit/s för de angivna källa och mottagare par baserat på interna tester. För jämförelse, visas också hur olika inställningar för [molnet data movement enheter](#cloud-data-movement-units) eller [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) (flera gateway-noder) hjälper dig att kopiera prestanda.

![Matris för prestanda](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>Azure Data Factory version 1 är minimal molnet dataflytt enheterna för molnet till molnkopieringen två. Om inget anges finns data movement standardenheter som används i [molnet data movement enheter](#cloud-data-movement-units).

**Pekar på Observera:**
* Genomströmning beräknas med hjälp av följande formel: [storleken på data läsas från källan] / [kopia aktivitet kör duration].
* Referensnummer prestanda i tabellen har mäts med [TPC-H](http://www.tpc.org/tpch/) datauppsättningen i en enda kopia aktivitet körs.
* I Azure datalager är källa och mottagare i samma Azure-region.
* För hybridkopiering mellan lokala och moln datalager, varje gateway-noden körs på en dator som är separat från lokala datalager med nedan specifikation. När en enskild aktivitet kördes på gateway förbrukat kopieringen bara en liten del av testdatorn CPU, minne eller bandbredd i nätverket. Mer information från [för Data Management Gateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>Processor</td>
        <td>32 kärnor 2.20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Minne</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Nätverk</td>
        <td>Internet-gränssnittet: 10 Gbit/s; intranätgränssnittet: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan få bättre genomströmning genom att utnyttja mer data movement enheter (DMUs) än standardvärdet maximala DMUs som är 32 för en moln-to-cloud kopieringsaktiviteten kör. Till exempel med 100 DMUs du kan åstadkomma kopiering av data från Azure Blob till Azure Data Lake Store på **1.0GBps**. Finns det [molnet data movement enheter](#cloud-data-movement-units) finns mer information om den här funktionen och scenariot som stöds. Kontakta [Azure-supporten](https://azure.microsoft.com/support/) att begära mer DMUs.

## <a name="parallel-copy"></a>Parallell kopia
Du kan läsa data från eller skriva data till destinationen **parallellt i en Kopieringsaktivitet kör**. Den här funktionen förbättrar genomflödet av en kopieringsåtgärd och minskar den tid det tar för att flytta data.

Denna inställning skiljer sig från den **samtidighet** egenskap i aktivitetsdefinitionen. Den **samtidighet** egenskapen bestämmer hur många **samtidiga Kopieringsaktiviteten kör** för bearbetning av data från olika aktivitet windows (AM 1 till 2 AM, 2 AM 3 AM, 3 AM till 4 AM och så vidare). Den här funktionen är användbart när du utför en historisk belastning. Parallell kopiera funktionen gäller för en **enkel aktivitet kör**.

Nu ska vi titta på ett exempelscenario. I följande exempel behöver flera segment från tidigare bearbetas. Data Factory kör en instans av Kopieringsaktiviteten (en aktivitet kör) för varje segment:

* Datasektorn från den första aktivitetsfönstret (1 AM till 02: 00) == > aktivitet köras 1
* Datasektorn från andra aktivitetsfönstret (02: 00 till 3 AM) == > aktivitet köras 2
* Datasektorn från andra aktivitetsfönstret (3 AM till 04: 00) == > aktivitet köras 3

Och så vidare.

I det här exemplet när den **samtidighet** har värdet 2, **aktivitet köras 1** och **aktivitet köras 2** kopiera data från två aktivitet windows **samtidigt** att förbättra prestandan för flytt av data. Men om flera filer som är associerade med aktiviteten kör 1, kopierar av data movement service filer från källan till filen en i taget.

### <a name="cloud-data-movement-units"></a>Molnet data movement enheter
En **moln data movement enhet (dmu här)** är ett mått som representerar en enhet i Data Factory styrka (en kombination av CPU, minne och nätverksresursallokering). Dmu här gäller för moln-to-cloud kopieringsåtgärder, men inte i en hybrid-kopia.

**Minimal molnet dataflytt enheter för att möta Kopieringsaktiviteten kör är två.** Om den inte anges visas i följande tabell standard-DMUs som används i olika kopiera scenarier:

| Kopiera scenario | Standard DMUs bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lagrar | Mellan 2 och 16 beroende på antalet och storleken på filerna. |
| Alla andra copy-scenarier | 2 |

Om du vill åsidosätta denna standardinställning måste du ange ett värde för den **cloudDataMovementUnits** egenskapen på följande sätt. Den **tillåtna värden** för den **cloudDataMovementUnits** egenskapen är 2, 4, 8, 16, 32. Den **faktiska antalet molnet DMUs** att kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på din datamönster. Information om andelen prestandafördelar som du kan få när du konfigurerar flera enheter för en specifik kopieringskälla och mottagare finns i [Prestandareferens](#performance-reference).

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

> [!NOTE]
> Om du behöver mer molnet DMUs för en högre genomströmning Kontakta [Azure-supporten](https://azure.microsoft.com/support/). Inställning av 8 och senare fungerar aktuellt endast när du **kopiera flera filer från Blob storage/Datasjölager/Amazon S3 eller ett moln FTP-eller ett moln SFTP till Blob storage/Datasjölager/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Du kan använda den **parallelCopies** egenskap som anger parallellitet som du vill kopiera aktiviteter ska användas. Du kan se den här egenskapen som det maximala antalet trådar i Kopieringsaktiviteten som kan läsa från käll- eller skriva till din sink datalager parallellt.

För varje kopia aktivitet som kör avgör Data Factory hur många parallella kopior som ska använda för att kopiera data från källan data lagras och till mål-data lagras. Standardvärdet för antalet parallella kopior som ska användas beror på vilken typ av källa och mottagare som du använder.  

| Källa och mottagare | Standardvärdet för parallell Kopiera antal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade butiker (Blob-lagring. Data Lake Store; Amazon S3; ett lokalt filsystem; ett lokalt HDFS) |Mellan 1 och 32. Beror på storleken på filerna och antalet molnet data movement enheter (DMUs) används för att kopiera data mellan två molnet datalager eller den fysiska konfigurationen av Gateway-datorn som används för en hybridkopiering (för att kopiera data till och från ett lokalt datalager). |
| Kopiera data från **alla källdata lagring till Azure Table storage** |4 |
| Alla andra källa och mottagare par |1 |

Vanligtvis standardbeteendet bör du få bästa genomflöde. Kontrollera belastningen på datorer som är värdar för dina data lagras eller om du vill justera prestandan, kopia, kan du åsidosätta standardvärdet och ange ett värde för den **parallelCopies** egenskapen. Värdet måste vara mellan 1 och 32 (båda inkluderande). Vid körning använder för bästa prestanda Kopieringsaktiviteten ett värde som är mindre än eller lika med värdet som du anger.

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Pekar på Observera:

* När du kopierar data mellan filbaserade lagrar den **parallelCopies** fastställa parallellitet på filnivå. Dela upp i en fil som skulle hända under automatiskt och transparent och den har utformats för att använda bästa lämplig segmentstorleken för en viss källa datalagertyp att läsa in data i parallella och ortogonal mot parallelCopies. Det faktiska antalet parallella kopior av data movement service används för kopieringen vid körning är inte fler än antalet filer som du har. Om beteendet kopia är **mergeFile**, Kopieringsaktivitet kan inte utnyttja filnivå parallellitet.
* När du anger ett värde för den **parallelCopies** egenskap, Överväg att belastningen ökar på din käll- och mottagarnoderna datalager och till gateway om det är en kopia av hybrid. Detta händer särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du märker att dataarkivet eller Gateway blir överbelastad till följd med belastningen minskar den **parallelCopies** värde att avlasta belastningen.
* När du kopierar data från butiker som inte är filbaserade till butiker som är filbaserade av data movement service ignorerar den **parallelCopies** egenskapen. Även om parallellitet anges tillämpas den inte i det här fallet.

> [!NOTE]
> Du måste använda Data Management Gateway 1.11 eller senare för att använda den **parallelCopies** funktion när du gör en kopia av hybrid.
>
>

Lättare att använda de här två egenskaperna och förbättra din flytt datagenomströmning finns i [exempel på användningsområden](#case-study-use-parallel-copy). Du behöver inte konfigurera **parallelCopies** dra nytta av standardbeteendet. Om du konfigurerar och **parallelCopies** är för liten flera molnet DMUs inte kan användas fullt ut.  

### <a name="billing-impact"></a>Fakturering påverkan
Den har **viktiga** komma ihåg att debiteras baserat på den totala tiden av kopieringen. Om en kopieringsjobbet används för att ta en timme till ett moln enhet och nu det tar 15 minuter med fyra enheter i molnet, förblir nästan samma övergripande faktura. Exempelvis kan du använda fyra enheter i molnet. Den första moln enheten använder 10 minuter kan det andra 10 minuter, det tredje 5 minuter, och det fjärde 5 minuter köras i en Kopieringsaktivitet. Du debiteras för tid Totalt antal copy (dataflyttning), vilket är 10 + 10 + 5 + 5 = 30 minuter. Med hjälp av **parallelCopies** påverkar inte fakturering.

## <a name="staged-copy"></a>Stegvis kopia
När du kopierar data från ett dataarkiv som källa till datalagret en mottagare kan du använda Blob storage som ett tillfälligt fristående Arkiv. Mellanlagring är särskilt användbart i följande fall:

1. **Du vill mata in data från olika datalager i SQL Data Warehouse via PolyBase**. SQL Data Warehouse använder PolyBase som en hög genomströmning mekanism för att läsa in stora mängder data till SQL Data Warehouse. Dock källdata måste vara i Blob storage och det måste uppfylla ytterligare villkor. När du läser in data från ett annat dataarkiv än Blob storage kan du Aktivera kopiering via mellanlagring Blob mellanlagring data. I så fall utför Data Factory omvandlingarna data som krävs för att säkerställa att den uppfyller kraven för PolyBase. Sedan använder PolyBase för att läsa in data till SQL Data Warehouse. Mer information finns i [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Ibland tar en stund att utföra en hybrid dataflyttning (det vill säga för att kopiera mellan en lokala data store och en molndata lagrar) över en långsam nätverksanslutning**. Du kan komprimera data lokal så att det tar mindre tid att flytta data till den tillfälliga databasen i molnet för att förbättra prestanda. Du kan sedan expandera data i tillfälliga store innan du läser in i datalagret mål.
3. **Du inte vill öppna andra portar än port 80 och port 443 i brandväggen, på grund av företagets IT-principer**. När du kopierar data från ett lokalt datalager till en Azure SQL Database-sink eller en Azure SQL Data Warehouse sink måste du aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot utnyttja gateway till den första kopieringsdata till en fristående instans för Blob-lagring via HTTP eller HTTPS på port 443. Sedan läsa in data till SQL Database eller SQL Data Warehouse från Blob storage Förproduktion. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Hur mellanlagrade Kopiera verk
När du aktiverar funktionen fristående först data kopieras från datalagret källan till den tillfälliga databasen (ta med din egen). Därefter kopieras data från fristående datalagret till datalagret sink. Data Factory hanteras automatiskt i två steg flödet för dig. Data Factory rensar även temporära data från den tillfälliga lagringen när dataflytten har slutförts.

Gateway används inte i molnet kopiera scenario (både källa och mottagare data lagras i molnet). Data Factory-tjänsten utför åtgärderna kopia.

![Mellanlagrad kopia: scenariot](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

I ett hybridscenario kopia (källan är lokala och mottagare är i molnet), gatewayen flyttar data från datalagret källan till en fristående dataarkiv. Data Factory-tjänsten flyttar data från dataarkivet mellanlagrade till datalagret mottagare. Kopiera data från ett dataarkiv som moln till ett lokalt datalager via mellanlagring även stöds med omvänd flödet.

![Mellanlagrad kopia: hybridscenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

När du aktiverar dataflyttning med hjälp av ett fristående Arkiv, kan du ange om du vill att data ska komprimeras innan du flyttar data från datalagret källan till ett tillfälligt eller fristående datalager och sedan expandera innan du flyttar data från en interimistisk eller mellanlagring datalagret till datalagret sink.

För närvarande kan du kopiera data mellan två lokala datalager med hjälp av ett fristående Arkiv. Vi räknar med det här alternativet ska vara tillgänglig snart.

### <a name="configuration"></a>Konfiguration
Konfigurera den **enableStaging** inställning i en Kopieringsaktivitet för att ange om du vill att data ska samlas i Blob storage innan du läser in den i ett dataarkiv som mål. När du anger **enableStaging** att värdet är TRUE, ange ytterligare egenskaper som anges i nästa tabell. Om du inte har något du behöver skapa ett Azure Storage eller lagring delade åtkomst signatur-länkad tjänst för Förproduktion.

| Egenskap | Beskrivning | Standardvärde | Krävs |
| --- | --- | --- | --- |
| **enableStaging** |Ange om du vill kopiera data via en interimistisk mellanlagring store. |False |Nej |
| **linkedServiceName** |Ange namnet på en [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) länkade tjänst som refererar till instansen av lagring som du använder som ett tillfälligt fristående Arkiv. <br/><br/> Du kan inte använda lagring med en signatur för delad åtkomst för att läsa in data till SQL Data Warehouse via PolyBase. Du kan använda den i andra scenarier. |Gäller inte |Ja, när **enableStaging** har angetts till TRUE |
| **Sökväg** |Ange sökvägen för Blob-lagring som du vill ska innehålla mellanlagrade data. Om du inte anger en sökväg, skapar en behållare för att lagra temporära data i tjänsten. <br/><br/> Ange en sökväg om du använder lagring med en signatur för delad åtkomst eller tillfälliga data i en specifik plats behöver. |Gäller inte |Nej |
| **enableCompression** |Anger om data ska komprimeras innan den kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

Här är en exempel-definition av Kopieringsaktiviteten med de egenskaper som beskrivs i tabellen ovan:

```json
"activities":[  
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Fakturering påverkan
Du debiteras baserat på två steg: kopiera varaktighet och typen.

* När du använder debiteras mellanlagring under en molnkopieringen (kopiera data från ett dataarkiv som moln till ett annat dataarkiv i molnet), du [summan av kopiera varaktighet för steg 1 och 2] x [kopia-enhetspriset för molnet].
* När du använder mellanlagring under en hybridkopiering (kopiera data från ett lokalt datalager i ett moln datalager), debiteras du för [hybrid kopiera duration] x [kopia-enhetspriset för hybrid] + [kopia varaktighet för moln] x [kopia-enhetspriset för molnet].

## <a name="performance-tuning-steps"></a>Prestanda prestandajustering steg
Vi rekommenderar att du gör följande för att anpassa prestandan för din Data Factory-tjänsten med Kopieringsaktiviteten:

1. **Upprätta en baslinje för**. Testa din pipeline under utvecklingsfasen med hjälp av Kopieringsaktiviteten mot ett representativt exempel. Du kan använda Data Factory [segmentering modellen](data-factory-scheduling-and-execution.md) att begränsa mängden data som du arbetar med.

   Samla in körningstiden och prestandaegenskaper med hjälp av den **övervakning och hantering av App**. Välj **övervaka och hantera** på startsidan Data Factory. I trädvyn, väljer du den **utdatauppsättningen**. I den **aktivitet Windows** Välj Kopieringsaktiviteten körs. **Aktiviteten Windows** visas Kopieringsaktiviteten varaktighet och storleken på data som kopieras. Dataflödet visas i **aktivitet fönstret Explorer**. Mer information om appen finns [övervaka och hantera Azure Data Factory pipelines med hjälp av övervakning och hantering av App](data-factory-monitor-manage-app.md).

   ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Senare i artikeln du kan jämföra prestanda och konfigurationen av din scenariot för att kopiera aktivitetens [Prestandareferens](#performance-reference) från våra tester.
2. **Diagnostisera och optimera prestanda**. Om du se prestanda inte uppfyller dina förväntningar, måste du identifiera flaskhalsar. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar. En fullständig beskrivning av Prestandadiagnos är utanför omfattningen för den här artikeln, men här är några vanliga överväganden:

   * Avancerade funktioner:
     * [Parallell kopia](#parallel-copy)
     * [Molnet data movement enheter](#cloud-data-movement-units)
     * [Stegvis kopia](#staged-copy)
     * [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gateway för datahantering](#considerations-for-data-management-gateway)
   * [Source](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappningen](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)
3. **Expandera konfigurationen till hela datauppsättningen**. När du är nöjd med resultaten av körningen och prestanda kan du expandera definitionen och pipelinens aktiva period som täcker hela datauppsättningen.

## <a name="considerations-for-data-management-gateway"></a>Överväganden för Data Management Gateway
**Installationsprogram för gateway**: Vi rekommenderar att du använder en särskild dator till värd Data Management Gateway. Se [överväganden för att använda Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).  

**Gateway-övervakning och upp/skalbar**: en enskild logisk gateway med en eller flera gateway-noder kan hantera flera Kopieringsaktiviteten körs samtidigt samtidigt. Du kan visa nära realtid ögonblicksbild av resursutnyttjande (CPU, minne, network(in/out) osv) på en gateway-dator samt antalet samtidiga jobb som körs mot gräns i Azure-portalen finns [övervakaren gateway i portalen](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Om du har tunga behov på hybrid dataflyttning med stort antal samtidiga kopiera aktiviteten körs eller med stora mängder data att kopiera anser [skala upp eller ut gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) för att bättre utnyttja din resurs eller för att etablera flera resurs för att ge kopia. 

## <a name="considerations-for-the-source"></a>Överväganden för källa
### <a name="general"></a>Allmänt
Var noga med att underliggande datalagret inte är överbelastad till följd av andra arbetsbelastningar som körs på eller i förhållande till den.

Microsoft datalager finns [övervaka och justera avsnitt](#performance-reference) som är specifika för datalager och hjälper dig att förstå data lagra prestandaegenskaper, minimera svarstider och maximera genomströmningen.

Om du kopierar data från Blob storage till SQL Data Warehouse, kan du använda **PolyBase** ökar prestandan. Se [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) mer information. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserade datakällor
*(Inklusive Blob-lagring, Data Lake Store, Amazon S3, lokalt filsystem och lokala HDFS)*

* **Genomsnittlig storlek och antalet filer**: Kopieringsaktiviteten överför en fil i taget. Med samma mängd data som ska flyttas, är det totala genomflödet lägre om data som består av flera små filer i stället för ett par stora filer på grund av fasen bootstrap för varje fil. Därför om möjligt, kombinera små filer till större filer för att få bättre genomströmning.
* **Format och komprimering**: fler sätt att förbättra prestanda finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt.
* För den **lokalt filsystem** scenario där **Data Management Gateway** är krävs, finns det [överväganden för Data Management Gateway](#considerations-for-data-management-gateway) avsnitt.

### <a name="relational-data-stores"></a>Lagrar relationsdata
*(Omfattar SQL-databasen. SQL Data Warehouse; Amazon Redshift; SQL Server-databaser. och Oracle, MySQL, DB2, Teradata, Sybase och PostgreSQL databaser osv.)*

* **Datamönster**: tabellens schema påverkar kopiera genomflöde. En stor Radstorleken ger en bättre prestanda än små Radstorleken kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar av data som innehåller färre rader.
* **Fråga eller lagrad procedur**: Optimera logiken för frågan eller lagrad procedur som du anger i Kopieringsaktiviteten källan för att hämta data mer effektivt.
* För **lokala relationsdatabaser**, till exempel SQL Server och Oracle, vilket kräver att **Data Management Gateway**, finns det [överväganden för Data Management Gateway](#considerations-on-data-management-gateway) avsnitt.

## <a name="considerations-for-the-sink"></a>Överväganden för sink
### <a name="general"></a>Allmänt
Var noga med att underliggande datalagret inte är överbelastad till följd av andra arbetsbelastningar som körs på eller i förhållande till den.

Microsoft datalager finns [övervaka och justera avsnitt](#performance-reference) som är specifika för datalager. Dessa avsnitt kan hjälpa dig att förstå dataegenskaper store prestanda och hur du minimera svarstider och maximera genomströmningen.

Om du kopierar data från **Blob storage** till **SQL Data Warehouse**, Överväg att använda **PolyBase** ökar prestandan. Se [Använd PolyBase för att läsa in data till Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) mer information. En genomgång med ett användningsfall finns [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserade datakällor
*(Inklusive Blob-lagring, Data Lake Store, Amazon S3, lokalt filsystem och lokala HDFS)*

* **Kopiera beteende**: Om du kopierar data från ett annat filbaserat dataarkiv Kopieringsaktiviteten har tre alternativ via den **copyBehavior** egenskapen. Bevarar hierarki, förenklas hierarkin eller sammanfogar filer. Bevara eller förenkla hierarkin har liten eller ingen prestanda försämras, men Sammanfoga filer gör att prestanda försämras att öka.
* **Format och komprimering**: finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt om fler sätt att förbättra prestanda.
* **BLOB storage**: för närvarande Blob storage stöder endast blockblobbar för optimerad dataöverföring och genomflöde.
* För **lokalt filsystem** scenarier som kräver användning av **Data Management Gateway**, finns det [överväganden för Data Management Gateway](#considerations-for-data-management-gateway) avsnitt.

### <a name="relational-data-stores"></a>Lagrar relationsdata
*(Omfattar SQL-databas, SQL Data Warehouse, SQL Server-databaser och Oracle-databaser)*

* **Kopiera beteende**: beroende på vilka egenskaper som har angetts för **sqlSink**, Kopieringsaktiviteten skriver data till måldatabasen på olika sätt.
  * Som standard bifoga data movement service använder API: et Bulk Copy infoga data i läge, som ger bästa prestanda.
  * Om du konfigurerar en lagrad procedur i sink gäller en datarad samtidigt i stället för som en massinläsning i databasen. Datorns prestanda sjunker. Om din datauppsättning är stort, i förekommande fall kan du byta till det **sqlWriterCleanupScript** egenskapen.
  * Om du konfigurerar den **sqlWriterCleanupScript** egenskap för varje kopia aktiviteten kör tjänsten utlöser skriptet och sedan du använder Bulk Copy API för att infoga data. Du kan till exempel ange ett skript för att först ta bort alla poster innan massinläsning nya data från källan om du vill skriva över hela tabellen med den senaste informationen.
* **Mönstret och batch datastorleken**:
  * Tabellens schema påverkar kopiera genomflöde. Om du vill kopiera samma mängd data får stora Radstorleken du bättre prestanda än en liten Radstorleken eftersom databasen mer effektivt kan genomföra färre batchar av data.
  * Kopieringsaktiviteten infogar data i en serie med batchar. Du kan ange antalet rader i en batch med hjälp av den **writeBatchSize** egenskapen. Om dina data har liten rader, kan du ange den **writeBatchSize** egenskap med ett högre värde för att dra nytta av lägre batch kostnader och högre genomströmning. Om Radstorleken på dina data är stor, var försiktig när du ökar **writeBatchSize**. Ett högt värde kan leda till en kopia misslyckades på grund av överbelastning i databasen.
* För **lokala relationsdatabaser** som SQL Server och Oracle, vilket kräver att **Data Management Gateway**, finns det [överväganden för Data Management Gateway](#considerations-for-data-management-gateway) avsnitt.

### <a name="nosql-stores"></a>NoSQL-lagringsplatser
*(Omfattar tabellagring och Azure Cosmos DB)*

* För **tabell lagring**:
  * **Partitionen**: skrivning av data till överlagrad partitioner avsevärt försämrar prestanda. Sortera källdata av partitionsnyckel så att data infogas effektivt i en partition efter en annan eller justera logik för att skriva data till en enda partition.
* För **Cosmos Azure DB**:
  * **Batchstorlek**: den **writeBatchSize** egenskapen anger hur många parallella begäranden till Azure DB som Cosmos-tjänsten för att skapa dokument. Du kan förvänta dig bättre prestanda om du ökar **writeBatchSize** eftersom flera parallella begäranden skickas till Azure Cosmos DB. Dock bevaka begränsning vid skrivning till Azure Cosmos DB (felmeddelandet är ”förfrågningar är stor”). Olika faktorer kan orsaka begränsning, inklusive storlek, antal villkoren i dokument och vilken målsamling indexprincip. Överväg att använda en bättre samling, till exempel S3 för att uppnå högre kopiera genomflöde.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering
Serialisering och deserialisering kan inträffa när din inkommande datauppsättning eller datamängd för utdata är en fil. Se [stöds format och komprimering](data-factory-supported-file-and-compression-formats.md) med information om filformat som stöds av Kopieringsaktiviteten.

**Kopiera beteende**:

* Kopierar filer mellan filbaserade datalager:
  * Om inkommande och utgående datauppsättningar båda har samma eller inga inställningar för format, av data movement service kör en binär kopia utan serialisering eller deserialisering. Du ser ett högre genomflöde jämfört med ett scenario där inställningarna källa och mottagare i formatet skiljer sig från varandra.
  * När indata och utdata datauppsättningar båda finns i textformat och endast kodningen typen är olika, av data movement service har endast kodning konvertering. Den gör inte alla serialisering och deserialisering, vilket gör att prestanda försämras jämfört med en binär kopia.
  * Om inkommande och utgående datauppsättningar båda har olika filformat eller olika konfigurationer som avgränsare, av data movement service deserializes källdata för att strömma-, omvandlings- och serialisera det till utdataformat som du angett. Den här åtgärden resulterar i en mycket större prestanda försämras jämfört med andra scenarier.
* När du kopierar filer till eller från ett dataarkiv som inte är filbaserade (till exempel från ett filbaserat store till relationella store) krävs steget serialisering eller deserialisering. Det här steget resulterar i betydande prestanda försämras.

**Filformatet**: filformat som du väljer kan påverka prestanda för kopia. Avro är till exempel ett compact binärformat som lagrar metadata med data. Det har brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dock dyrare för serialisering och deserialisering, vilket resulterar i lägre kopiera genomflöde jämfört med textformat. Kontrollera ditt val av filformatet under bearbetning flödet helhetsmässigt. Starta med vilken form data lagras i källan datalager eller ska extraheras från externa system. Det bästa formatet för lagring, analytisk bearbetning och frågar; och i vilket format data ska exporteras till dataarkiv för rapportering och visualisering verktyg. Ibland ett format som är något sämre för Läs- och skrivprestanda kan vara bra när du funderar på övergripande analytiska processen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering
När datauppsättningen inkommande eller utgående är en fil kan ange du Kopieringsaktiviteten att utföra komprimering eller dekomprimering som skriver data till målet. När du väljer komprimering kan du göra en kompromiss mellan indata/utdata (I/O) och processor. Komprimera data kostnaderna extra beräkningsresurser. Men i RETUR minskas från nätverket och lagring. Beroende på dina data, kan du se en höjning av den totala kopiera genomflödet.

**Codec**: Kopieringsaktiviteten stöder gzip, bzip2 och Deflate komprimeringstyper. Azure HDInsight kan använda alla tre typer för bearbetning. Varje komprimerings-codec har fördelar. Till exempel bzip2 har lägsta kopiera genomströmningen, men du får bästa frågeprestanda Hive med bzip2 eftersom du kan dela för bearbetning. Gzip är det mest belastningsutjämnade alternativet och det används mest ofta. Välj den codec som passar bäst för ditt scenario för slutpunkt till slutpunkt.

**Nivå**: du kan välja mellan två alternativ för varje komprimerings-codec: snabbaste komprimerade och optimalt komprimerade. Snabbaste komprimerade alternativet komprimeras data så snabbt som möjligt, även om den resulterande filen inte komprimeras optimalt. Alternativet optimalt komprimerade ägnar mer tid åt komprimering och ger en minimal mängd data. Du kan testa båda alternativen för att se vilket ger bättre prestanda i ditt fall.

**En faktor**: Om du vill kopiera en stor mängd data mellan en lokal lagring och molnet, Överväg att använda tillfälliga blob-lagring med komprimering. Det är praktiskt att använda mellanlagring när bandbredden för företagets nätverk och Azure-tjänster är begränsande faktor och datamängden som indata och utdata datauppsättning båda ska vara okomprimerad. Mer specifikt kan du dela upp en enda kopia aktivitet i två kopiera aktiviteter. Den första kopieringsaktiviteten kopieras från källan till en tillfällig eller fristående blob i komprimerat format. Andra kopieringsaktiviteten kopierar komprimerade data från mellanlagring och expanderar sedan medan den skriver till sink.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappningen
Du kan ange den **columnMappings** egenskap i en Kopieringsaktivitet att mappa alla eller en delmängd av indatakolumnerna till utdatakolumner. När av data movement service läser data från källan, måste den utföra kolumnmappningen på data innan den skriver data till sink. Den här extra bearbetning minskar kopiera genomflöde.

Om källa datalager frågbar kan till exempel om det är en relationell butik som SQL Database eller SQL Server, eller om det är en NoSQL-butiken som Table storage eller Azure Cosmos DB du sänder kolumnen filtrering och omordning logik för att den **frågan** egenskapen istället för att använda kolumnmappningen. Det här sättet projektionen inträffar när av data movement service läser data från datalagret källa om det är mycket effektivare.

## <a name="other-considerations"></a>Andra överväganden
Om storleken på data som du vill kopiera är stor, kan du justera affärslogik att partitionera data med hjälp av mekanismen för slicing i Data Factory. Schemalägg sedan Kopieringsaktiviteten ska köras oftare för att minska storleken på data för varje Kopieringsaktiviteten kör.

Var försiktig om antalet datauppsättningar och kopiera aktiviteter som kräver Data Factory till koppling till samma datalager på samma gång. Många samtidiga kopiera jobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera jobbet interna försök, och i vissa fall, fel vid körning.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempelscenario: kopiera från en lokal SQL Server till Blob storage
**Scenariot**: en pipeline är utformat för att kopiera data från en lokal SQL Server till Blob storage i CSV-format. Om du vill göra kopieringsjobbet snabbare ska CSV-filer komprimeras till bzip2 format.

**Test- och**: genomflödet av Kopieringsaktiviteten är mindre än 2 Mbit/s som är mycket långsammare än benchmark prestanda.

**Prestandaanalys och justera**: Om du vill felsöka prestandaproblemet, ska vi titta på hur data behandlas och flyttas.

1. **Läsa data**: Gateway öppnar en anslutning till SQL Server och skickar frågan. SQL Server svarar genom att skicka dataströmmen till gatewayen via intranätet.
2. **Serialisera och komprimera data**: Gateway Serialiserar dataströmmen till CSV-format och data till en dataström med bzip2 komprimeras.
3. **Skriva data**: Gateway överför bzip2 dataströmmen till Blob storage via Internet.

Som du kan se data som behandlas och flyttas i strömmande ordning: SQL Server > LAN > Gateway > WAN > Blob storage. **Prestandan gated av lägsta dataflöde över pipeline**.

![Dataflöde](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Flaskhals kan leda till en eller flera av följande faktorer:

* **Källan**: SQL Server har låg genomströmning på grund av tunga laster.
* **Data Management Gateway**:
  * **LAN**: Gateway finns är långt från SQL Server-datorn och har en långsam anslutning.
  * **Gateway**: Gateway har nått sin belastningen begränsningar om du vill utföra följande åtgärder:
    * **Serialisering**: serialiseringen dataströmmen till CSV-format har långsam genomflöde.
    * **Komprimering**: du har valt en långsam komprimerings-codec (till exempel bzip2, vilket är 2,8 Mbit/s med Core i7).
  * **WAN**: bandbredden mellan företagsnätverket och Azure-tjänster är låg (till exempel T1 = 1,544 kbit/s; T2 = 6,312 kbit/s).
* **Sink**: Blob storage har låg genomströmning. (Det här scenariot är inte troligt eftersom dess SLA garanterar minst 60 Mbit/s).

I det här fallet kan bzip2 datakomprimering långsammare hela pipeline. Växla till en gzip komprimerings-codec kan underlättar den här begränsningen.

## <a name="sample-scenarios-use-parallel-copy"></a>Exempelscenarier: Använd parallella kopia
**Scenariot I:** kopiera 1 000 1 MB filer från lokalerna filsystemet till Blob storage.

**Analys- och prestandajustering**: T.ex, om du har installerat gateway på en dator i fyra kärnor Data Factory använder 16 parallella kopior flytta filer från filsystemet till Blob storage samtidigt. Den här parallellkörning resulterar i högt dataflöde. Du kan också explicit ange antalet parallella kopior. När du kopierar många små filer, hjälpa parallella kopior kraftigt genomströmning effektivare användning av resurser.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenariot II**: kopiera 20 blobbar 500 MB från Blob storage till Data Lake Store Analytics och justera prestanda.

**Analys- och prestandajustering**: I det här scenariot Data Factory kopierar data från Blob storage till Data Lake Store med hjälp av en kopia (**parallelCopies** värdet 1) och enkel molndata flytt enheter. Genomströmning du se nära som beskrivs i den [prestanda referensavsnitt](#performance-reference).   

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenariot III**: enskilda filstorlek är större än dussintals MB och total volym är stor.

**Analys- och aktivera prestanda**: öka **parallelCopies** inte resulterar i bättre copy prestanda på grund av resursbegränsningar av ett enda moln dmu här. I stället bör du ange mer molnet DMUs få mer resurser för att utföra dataflytten. Ange inte ett värde för den **parallelCopies** egenskapen. Data Factory hanterar parallellitet för dig. I det här fallet, om du ställer in **cloudDataMovementUnits** 4, en genomströmning på ungefär fyra gånger inträffar.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referens
Här följer prestandaövervakning och justera referenser för några av datalager stöds:

* Azure-lagring (inklusive Blob storage och Table storage): [skalbarhetsmål för Azure Storage](../../storage/common/storage-scalability-targets.md) och [Azure Storage checklistan för prestanda och skalbarhet](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Du kan [övervaka prestanda](../../sql-database/sql-database-single-database-monitor.md) och markera databasen transaktion enhet (DTU) procent
* Azure SQL Data Warehouse: Sin förmåga mäts i informationslagerenheter (dwu: er); Se [hantera beräkningskraft i Azure SQL Data Warehouse (översikt)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [prestandanivåer i Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Lokal SQL Server: [övervakaren och finjustera för prestanda](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokala filserver: [prestandajustering för filservrar](https://msdn.microsoft.com/library/dn567661.aspx)
