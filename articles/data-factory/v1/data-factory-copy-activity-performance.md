---
title: Kopiera aktivitet prestanda- och Justeringsguiden | Microsoft Docs
description: Läs mer om viktiga faktorer som påverkar prestandan för dataförflyttning i Azure Data Factory när du använder Kopieringsaktivitet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ec8c58e4ced0d8df958e242b9c1671aeed8c2ee6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60488258"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Kopiera aktivitet prestanda- och justeringsguide

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-copy-activity-performance.md)
> * [Version 2 (aktuell version)](../copy-activity-performance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factory-tjänsten finns i [kopiera aktivitet prestanda- och Justeringsguiden för Data Factory](../copy-activity-performance.md).

Azure Data Factory-Kopieringsaktiviteten levererar en förstklassig säker, tillförlitlig och höga prestanda för inläsning av data lösning. Det gör att du kan kopiera tiotals terabyte data varje dag på en rad olika molnet och lokala datalager. – Blixtsnabb prestanda för datainläsning är nyckeln till att se till att du kan fokusera på problemet core ”stordata”: bygga avancerade Analyslösningar och få djupa insikter från alla data.

Azure innehåller en uppsättning av företagsklass lösningar för lagring och data warehouse och Kopieringsaktivitet erbjuder en optimerad upplevelse som är lätt att göra inställningar för inläsning av data. Med bara en enda Kopieringsaktivitet, kan du åstadkomma:

* Läser in data till **Azure SQL Data Warehouse** på **1,2 Gbit/s**. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Läser in data till **Azure Blob storage** på **1,0 GB/s**
* Läser in data till **Azure Data Lake Store** på **1,0 GB/s**

Den här artikeln beskrivs:

* [Referensnummer för prestanda](#performance-reference) för källa och mottagare datalager när du planerar ditt projekt; som stöds
* Funktioner som kan öka dataflödet kopia i olika scenarier, inklusive [enheter för dataflytt i molnet](#cloud-data-movement-units), [parallell kopia](#parallel-copy), och [mellanlagrad kopiering](#staged-copy);
* [Prestandajusteringsvägledning](#performance-tuning-steps) på hur du ställer in prestanda- och de viktigaste faktorerna som kan påverka kopieringen bättre prestanda.

> [!NOTE]
> Om du inte är bekant med en Kopieringsaktivitet i allmänhet, se [flytta data med hjälp av Kopieringsaktiviteten](data-factory-data-movement-activities.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Prestandareferens för

Tabellen nedan visar som referens, du kopiera dataflöde i Mbit/s för de angivna par för källa och mottagare baserat på interna tester. Jämförelse, visas också hur olika inställningar för [enheter för dataflytt i molnet](#cloud-data-movement-units) eller [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) (flera gateway-noder) kan hjälpa på kopieringen bättre prestanda.

![Matris för prestanda](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>De minimala enheterna för molndataflytt för moln-till-moln kopian i Azure Data Factory version 1 är två. Om inget anges finns i standard dataflyttenheter som används i [enheter för dataflytt i molnet](#cloud-data-movement-units).

**Saker att Observera:**
* Dataflödet beräknas med hjälp av följande formel: [storleken på data läses från källan] / [körningen av Kopieringsaktiviteten varaktighet].
* Referensnummer för prestanda i tabellen har mätas med [TPC-H](http://www.tpc.org/tpch/) uppsättning data i en enda kopieringsaktivitetskörning.
* I Azure datalager finns källa och mottagare i samma Azure-region.
* För hybridkopiering mellan lokala och molnbaserade datalager måste varje gateway-noden körs på en dator som var avskild från lokala datalager med nedan specifikationen. När en enda aktivitet kördes på gatewayen, förbrukas kopieringen endast en liten del av testdatorn CPU, minne eller bandbredd i nätverket. Läs mer i [överväganden för Data Management Gateway](#considerations-for-data-management-gateway).
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
        <td>Internet-gränssnittet: 10 Gbit/s; intranät-gränssnitt: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan uppnå högre dataflöde med hjälp av flera enheter för dataflytt (DMUs) än standardvärdet maximala DMUs, vilket är 32 för en moln-till-moln kopieringsaktivitetskörning. Till exempel med 100 DMUs du kan uppnå kopiering av data från Azure Blob till Azure Data Lake Store på **1.0GBps**. Se den [enheter för dataflytt i molnet](#cloud-data-movement-units) finns mer information om den här funktionen och scenario som stöds. Kontakta [Azure-supporten](https://azure.microsoft.com/support/) att begära mer DMUs.

## <a name="parallel-copy"></a>Parallell kopia
Du kan läsa data från eller skriva data till målet **parallellt inom en Kopieringsaktivitetskörning**. Den här funktionen förbättrar dataflödet för en kopieringsåtgärd och minskar den tid det tar för att flytta data.

Den här inställningen skiljer sig från den **samtidighet** -egenskapen i aktivitetsdefinitionen. Den **samtidighet** egenskapen anger hur många **samtidiga Kopieringsaktivitet körs** att bearbeta data från olika aktivitetsfönster (01: 00 till 14: 00 2 AM till 3 AM, 3 AM till 4 AM och så vidare). Den här funktionen är användbar när du utför en historisk belastning. Funktionen för parallell kopia gäller för en **enkel aktivitetskörning**.

Låt oss titta på ett exempelscenario. I följande exempel behöver flera segment från tidigare som ska bearbetas. Data Factory kör en instans av Kopieringsaktivitet (en aktivitet kör) för varje sektor:

* Datasektorn från den första aktivitetsfönstret (01: 00 till 2 AM) == > aktivitet köras 1
* Datasektorn från andra aktivitetsfönstret (02: 00 till 3: 00) == > aktivitet köras 2
* Datasektorn från andra aktivitetsfönstret (3: 00 till 4 AM) == > aktivitet köras 3

Och så vidare.

I det här exemplet, när den **samtidighet** värdet anges till 2, **aktivitet köras 1** och **aktivitet köras 2** kopiera data från två aktivitetsfönster **samtidigt** att förbättra prestandan för dataförflyttning. Men om flera filer är associerade med aktiviteten kör 1 kopierar av data movement service filer från källan till filen en i taget.

### <a name="cloud-data-movement-units"></a>Enheter för molndataflytt
En **cloud data movement enhet (dmu här)** är ett mått som representerar (en kombination av processor, minne och nätverksresursallokering) en enhet i Data Factory. Dmu här gäller för moln-till-moln kopieringsåtgärder, men inte i en hybrid-kopia.

**De minimala enheterna för molndataflytt möjligheter för körningen av Kopieringsaktiviteten är två.** Om inte anges visas i följande tabell de standard-DMUs som används i olika kopia scenarier:

| Kopiera scenario | Standard DMUs bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade lager | Mellan 4 och 16 beroende på antalet och storleken på filerna. |
| Alla andra kopia-scenarier | 4 |

Om du vill åsidosätta denna standardinställning, ange ett värde för den **cloudDataMovementUnits** egenskapen på följande sätt. Den **tillåtna värden** för den **cloudDataMovementUnits** egenskapen är 2, 4, 8, 16, 32. Den **faktiska antalet molntjänster DMUs** att kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på din datamönster. Information om nivå av prestanda som du kan få när du konfigurerar fler enheter för en specifik kopieringskälla och mottagare finns i den [Prestandareferens](#performance-reference).

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
> Om du behöver mer molnet DMUs för ett högre dataflöde kan kontakta [Azure-supporten](https://azure.microsoft.com/support/). Inställningen för den 8 och senare och fungerar för närvarande endast när du **kopiera flera filer från Blob storage/Data Lake Store/Amazon S3/SFTP för FTP/i molnet i molnet till Blob storage/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies
Du kan använda den **parallelCopies** egenskap som anger parallellitet som du vill Kopieringsaktivitet att använda. Du kan tänka på den här egenskapen som det maximala antalet trådar i Kopieringsaktiviteten som kan läsa från källan eller skriva till dina datalager för mottagare parallellt.

För varje körningen av Kopieringsaktiviteten, avgör hur många parallella kopior som ska använda för att kopiera data från källan datalagring och att sidan måldatalager lagra i Data Factory. Standardvärdet för antal parallella kopior som används beror på vilken typ av källa och mottagare som du använder.

| Källa och mottagare | Parallell kopia Standardantal bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade lager (Blob-lagring. Data Lake Store; Amazon S3; ett lokalt filsystem; ett lokalt HDFS) |Mellan 1 och 32. Beror på storleken på filerna och antalet enheter för molndataflytt (DMUs) används för att kopiera data mellan två molndatalager eller en fysisk konfiguration av Gateway-datorn som används för en hybrid-kopia (för att kopiera data till och från ett lokalt datalager). |
| Kopiera data från **alla källans datalager till Azure Table storage** |4 |
| Alla andra par för källa och mottagare |1 |

Vanligtvis är standardbeteendet bör du få det bästa dataflödet. Men att styra belastningen på datorer som är värdar för dina data lagras eller om du vill justera prestanda för kopia som du kan välja att åsidosätta standardvärdet och ange ett värde för den **parallelCopies** egenskapen. Värdet måste vara mellan 1 och 32 (båda inkluderande). Vid körning använder för bästa prestanda Kopieringsaktiviteten ett värde som är mindre än eller lika med värdet som du anger.

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
Saker att Observera:

* När du kopierar data mellan filbaserade den **parallelCopies** fastställa parallellitet på filnivå. Dela upp inom en enda fil skulle inträffa under automatiskt och transparent och den har utformats för att använda den bästa lämpliga segmentstorleken för en viss källa datalagertyp att läsa in data i parallella program och rätvinkliga till parallelCopies. Det faktiska antalet parallella kopior av data movement service använder för att kopieringen under körning är inte fler än antalet filer som du har. Om kopieringsbeteendet är **mergeFile**, Kopieringsaktivitet inte kan utnyttja parallellitet på filnivå.
* När du anger ett värde för den **parallelCopies** egenskapen, Överväg att belastningen ökar på dina käll- och mottagar-datalager och till gateway om det är en hybrid-kopia. Detta sker särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du märker att antingen datalager eller gatewayen blir överbelastad till följd med belastning kan minska den **parallelCopies** värde att avlasta belastningen.
* När du kopierar data från butiker som inte är filbaserad till datalager som är filbaserade, av data movement service ignorerar den **parallelCopies** egenskapen. Även om parallellitet anges, tillämpas den inte i det här fallet.

> [!NOTE]
> Du måste använda Data Management Gateway 1.11 eller senare för att använda den **parallelCopies** funktion när du gör en kopia av hybrid.
>
>

Att använda dessa två egenskaper för bättre och förbättra dina dataflyttflödet, se exemplet användningsfall. Du behöver inte konfigurera **parallelCopies** att dra nytta av standardbeteendet. Om du konfigurerar och **parallelCopies** är för liten flera moln DMUs inte kan användas fullt ut.

### <a name="billing-impact"></a>Fakturering påverkan
Den har **viktiga** att komma ihåg att du debiteras utifrån den totala tiden för kopieringsåtgärden. Om ett kopieringsjobb brukade ta en timme med en cloud enhet och så tar det nu 15 minuter med fyra molnenheter, förblir övergripande fakturan nästan samma. Exempelvis kan använda du fyra molnenheter för. Den första molnenheten tar 10 minuter, den andra mallen, 10: e minut, det tredje 5 minuter, och det fjärde 5 minuter, alla i en Kopieringsaktivitet körs. Du debiteras för totalt antal kopia (dataflytt)-tiden, vilket är 10 + 10 + 5 + 5 = 30 minuter. Med hjälp av **parallelCopies** påverkar inte fakturering.

## <a name="staged-copy"></a>Mellanlagrad kopiering
När du kopierar data från källans datalager till mottagarens datalager kan du välja att använda Blob storage som en mellanliggande mellanlagringsarkivet. Mellanlagring är särskilt användbart i följande fall:

1. **Du vill mata in data från olika datalager i SQL Data Warehouse via PolyBase**. SQL Data Warehouse använder PolyBase som en mekanism för stora dataflöden för att läsa in en stor mängd data till SQL Data Warehouse. Dock källdata måste vara i Blob storage och den uppfylla ytterligare kriterier. När du läser in data från ett datalager än Blob-lagring, aktiverar du data kopiering via tillfälliga mellanlagringsplatsen Blob-lagring. I så fall kan utför Data Factory omvandlingarna data som krävs för att säkerställa att den uppfyller kraven för PolyBase. Sedan används PolyBase för att läsa in data i SQL Data Warehouse. Mer information finns i [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Ibland kan det ta en stund att utföra en hybriddataförflyttning (det vill säga för att kopiera mellan en lokal data store och en molndata lagrar) över en långsam nätverksanslutning**. Du kan komprimera data lagras lokalt så att det tar mindre tid att flytta data till det tillfälliga datalagret i molnet för att förbättra prestanda. Sedan kan du expandera data i mellanlagringsarkivet innan du läser in dem till måldatalagret.
3. **Du inte vill öppna portar än port 80 och port 443 i brandväggen, på grund av företagets IT-principer**. När du kopierar data från ett lokalt datalager till en Azure SQL Database-mottagare eller en Azure SQL Data Warehouse sink måste aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här scenariot kan utnyttja gatewayen till den första kopieringsdata till en Blob storage mellanlagrings-instans via HTTP eller HTTPS på port 443. Läs sedan in data i SQL Database eller SQL Data Warehouse från mellanlagring för Blob storage. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Hur mellanlagrad kopiering fungerar
När du aktiverar funktionen mellanlagring först data kopieras från källdatalagret till datalager för mellanlagring (ta med din egen). Därefter kopieras data från datalager för mellanlagring till datalager för mottagare. Data Factory hanterar automatiskt två steg flödet för dig. Data Factory rensar också tillfälliga data från den tillfälliga lagringen efter att dataöverföringen har slutförts.

Gateway används inte i molnet kopia scenario (både källa och kanalmottagare data butiker finns i molnet). Data Factory-tjänsten utför kopieringsåtgärder.

![Mellanlagrad kopiering: Scenariot med molnet](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

I ett hybridscenario kopia (källa är på plats och mottagare är i molnet), gatewayen flyttar data från källans datalager till ett datalager för mellanlagring. Data Factory-tjänsten flyttar data från datalager för mellanlagring till de mottagande datalagren. Kopiera data från ett molndatalager till ett lokalt datalager via mellanlagring även stöds med omvänd flödet.

![Mellanlagrad kopiering: Scenario med hybridanvändning](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

När du aktiverar dataförflyttning med hjälp av en mellanlagringsarkivet kan ange du om du vill att data ska komprimeras innan du flyttar data från källans datalager till ett mellanliggande eller tillfälliga datalager och sedan expandera innan du flyttar data från en tiden eller organiserar data lagra till de mottagande datalagren.

För närvarande kan kopiera du inte data mellan två lokala datalager med hjälp av en mellanlagringsarkivet. Vi räknar med det här alternativet ska vara tillgänglig snart.

### <a name="configuration"></a>Konfiguration
Konfigurera den **enableStaging** inställningen i Kopieringsaktiviteten till att ange om du vill att data ska mellanlagras i Blob storage innan du läser in dem till ett måldatalager. När du ställer in **enableStaging** att värdet är TRUE, ange ytterligare egenskaper som anges i nästa tabell. Om du inte har någon kan du även behöva skapa ett Azure Storage eller Storage shared access signature-länkad tjänst för mellanlagring.

| Egenskap  | Beskrivning | Standardvärde | Krävs |
| --- | --- | --- | --- |
| **enableStaging** |Ange om du vill kopiera data via en tiden mellanlagring store. |False |Nej |
| **linkedServiceName** |Ange namnet på en [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) länkad tjänst som refererar till instansen av lagring som du använder som ett tillfälligt mellanlagringsarkivet. <br/><br/> Du kan inte använda Storage med signatur för delad åtkomst för att läsa in data till SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Gäller inte |Ja, när **enableStaging** har angetts till TRUE |
| **Sökväg** |Ange sökvägen för Blob-lagring som du vill ska innehålla den mellanlagrade data. Om du inte anger en sökväg, skapar en behållare för att lagra tillfälliga data i tjänsten. <br/><br/> Ange en sökväg endast om du använder lagring med signatur för delad åtkomst, eller du kräver tillfälliga data finnas i en viss plats. |Gäller inte |Nej |
| **enableCompression** |Anger om data ska komprimeras innan den kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Nej |

Här är en exempeldefinition av Kopieringsaktiviteten med egenskaper som beskrivs i tabellen ovan:

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

* När du använder debiteras mellanlagring under en molnkopieringen (kopiera data från ett molndatalager till ett annat molndatalager), du [summan av Kopieringstid för steg 1 och 2] x [cloud kopia Enhetspris].
* När du använder mellanlagring vid en hybridkopiering (kopiera data från ett lokalt datalager till ett molndatalager) debiteras du för [hybrid Kopieringstid] x [Enhetspris hybrid kopia] + [cloud Kopieringstid] x [cloud kopia Enhetspris].

## <a name="performance-tuning-steps"></a>Prestanda justering steg
Vi rekommenderar att du gör följande för att finjustera prestanda för Data Factory-tjänsten med en Kopieringsaktivitet:

1. **Upprätta en baslinje för**. Testa din pipeline med hjälp av Kopieringsaktivitet mot ett representativt datasampel under utvecklingsfasen för. Du kan använda Data Factory [uppdelning modellen](data-factory-scheduling-and-execution.md) att begränsa mängden data som du arbetar med.

   Samla in körningstiden och prestandaegenskaper med hjälp av den **övervaknings- och Hanteringsappen**. Välj **övervaka och hantera** på startsidan Datafabrik. I trädvyn, väljer du den **utdatauppsättning**. I den **aktivitet Windows** väljer Kopieringsaktiviteten. **Aktivitet Windows** visar varaktigheten för Kopieringsaktiviteten och mängden data som kopieras. Dataflödet visas i **aktivitet fönstret Explorer**. Läs mer om appen i [övervaka och hantera Azure Data Factory-pipelines med övervaknings- och Hanteringsappen](data-factory-monitor-manage-app.md).

   ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Senare i artikeln kan du jämföra prestanda och konfiguration av ditt scenario för att Kopieringsaktivitet [Prestandareferens](#performance-reference) från våra tester.
2. **Diagnostisera och optimera prestanda**. Om du se prestanda inte uppfyller dina förväntningar kan behöva du identifiera flaskhalsar i prestanda. Sedan kan optimera prestanda för att ta bort eller minska effekten av flaskhalsar. En fullständig beskrivning av Prestandadiagnostik ligger utanför omfånget för den här artikeln, men här följer några vanliga överväganden:

   * Prestandafunktioner:
     * [Parallell kopia](#parallel-copy)
     * [Enheter för molndataflytt](#cloud-data-movement-units)
     * [Mellanlagrad kopiering](#staged-copy)
     * [Data Management Gateway skalbarhet](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gateway för datahantering](#considerations-for-data-management-gateway)
   * [Källa](#considerations-for-the-source)
   * [mottagare](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappningen](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)
3. **Expandera konfigurationen till hela din datauppsättning**. När du är nöjd med resultat från instruktionskörningar och prestanda kan expandera du definitions- och pipelinens aktiva period för att täcka hela din datauppsättning.

## <a name="considerations-for-data-management-gateway"></a>Överväganden för Data Management Gateway
**Installationsprogram för gateway**: Vi rekommenderar att du använder en dedikerad dator till värd Data Management Gateway. Se [att tänka på när Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gateway-övervakning och skala in/ut**: En enskild logisk gateway med en eller flera gateway-noder kan hantera flera Kopieringsaktivitet körs på samma gång samtidigt. Du kan visa nästan i realtid ögonblicksbild av Resursanvändning (processor, minne, network(in/out) osv) på en gateway-dator samt hur många samtidiga jobb som körs jämfört med gränsen i Azure-portalen finns i [övervakaren gateway i portalen](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Om du har behov av tunga på hybriddataförflyttning med stort antal samtidiga kopia aktivitetskörningar eller med stora mängder data som ska kopieras, Överväg att [skala upp eller skala ut gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) för att bättre utnyttja din resurs eller för att etablera Mer resursen möjligheter för kopia.

## <a name="considerations-for-the-source"></a>Överväganden för källan
### <a name="general"></a>Allmänt
Var noga med att det underliggande datalagringen inte bli överhopade av andra arbetsbelastningar som körs på eller mot den.

Microsoft-datalager, se [övervakning och justering ämnen](#performance-reference) som är specifika för datalager och hjälper dig att se data lagra prestandaegenskaper, minimera svarstider och maximera genomströmningen.

Om du kopierar data från Blob storage till SQL Data Warehouse kan du överväga att använda **PolyBase** att öka prestanda. Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) mer information. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserat datalager
*(Inklusive Blob-lagring, Data Lake Store, Amazon S3, lokala filsystem och lokala HDFS)*

* **Genomsnittlig storlek och antalet filer**: Kopieringsaktivitet överför en fil i taget. Med samma mängden data som ska flyttas, är det totala arbetsflödet lägre om data består av många små filer i stället för ett par stora filer på grund av fasen bootstrap för varje fil. Om det är möjligt, kombinera därför små filer i större filer för att få högre dataflöde.
* **Format och komprimering**: Fler sätt att förbättra prestanda finns i den [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt.
* För den **lokala filsystem** scenario där **Datahanteringsgateway** är krävs, finns i den [överväganden för Data Management Gateway](#considerations-for-data-management-gateway) avsnittet.

### <a name="relational-data-stores"></a>Relationsdata
*(Inklusive SQL-databas. SQL Data Warehouse; Amazon Redshift; SQL Server-databaser. och Oracle, MySQL, DB2, Teradata, Sybase och PostgreSQL-databaser osv.)*

* **Datamönster**: Din tabellschemat påverkar kopia dataflöde. En stor Radstorleken ger dig en bättre prestanda än små Radstorleken att kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: Optimera logiken för frågan eller lagrad procedur som du anger i Kopieringsaktiviteten källan för att hämta data mer effektivt.
* För **lokala relationsdatabaser**, till exempel SQL Server- och Oracle, som kräver användning av **Data Management Gateway**, finns i överväganden för Data Management Gateway-avsnittet.

## <a name="considerations-for-the-sink"></a>Överväganden för mottagaren
### <a name="general"></a>Allmänt
Var noga med att det underliggande datalagringen inte bli överhopade av andra arbetsbelastningar som körs på eller mot den.

Microsoft-datalager, finns i [övervakning och justering ämnen](#performance-reference) som är specifika för datalager. Dessa avsnitt kan hjälpa dig att förstå data store prestandaegenskaper och hur du minimera svarstider och maximera genomströmningen.

Om du kopierar data från **Blob-lagring** till **SQL Data Warehouse**, Överväg att använda **PolyBase** att öka prestanda. Se [använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) mer information. En genomgång med ett användningsfall finns i [läsa in 1 TB i Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserat datalager
*(Inklusive Blob-lagring, Data Lake Store, Amazon S3, lokala filsystem och lokala HDFS)*

* **Kopiera beteende**: Om du kopierar data från ett annat filbaserat datalager Kopieringsaktiviteten har tre alternativ via den **copyBehavior** egenskapen. Den bevarar hierarki, plattar ut hierarki eller sammanfogar filer. Antingen behålla eller förenkla hierarkin har lite eller ingen prestanda försämras, men Sammanfoga filer gör att prestanda försämras att öka.
* **Format och komprimering**: Se den [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimering](#considerations-for-compression) avsnitt fler sätt att förbättra prestanda.
* **BLOB-lagring**: Blob storage stöder för närvarande endast blockblobar för optimerad dataöverföring och dataflöde.
* För **lokala filsystem** scenarier som kräver användning av **Datahanteringsgateway**, finns i den [överväganden för Data Management Gateway](#considerations-for-data-management-gateway) avsnittet.

### <a name="relational-data-stores"></a>Relationsdata
*(Inklusive SQL-databas, SQL Data Warehouse, SQL Server-databaser och Oracle-databaser)*

* **Kopiera beteende**: Beroende på vilka egenskaper som du har angett för **sqlSink**, Kopieringsaktivitet skriver data till måldatabasen på olika sätt.
  * Som standard läggs data movement service använder API: et Bulk Copy att infoga data i läge, som ger bästa prestanda.
  * Om du konfigurerar en lagrad procedur i sink gäller databasen en datarad samtidigt i stället för som en massinläsning. Prestanda försämras betydligt. Om datauppsättningen är stor, när så är tillämpligt, Överväg att byta till den **sqlWriterCleanupScript** egenskapen.
  * Om du konfigurerar den **sqlWriterCleanupScript** egenskapen för varje Kopieringsaktiviteten kör tjänsten utlöser skriptet och sedan använder du Bulk Copy API och infoga data. Om du vill skriva över hela tabellen med den senaste informationen, kan du till exempel ange ett skript för att först ta bort alla poster innan massinläsning nya data från källan.
* **Datastorlek för mönstret och batch**:
  * Din tabellschemat påverkar kopia dataflöde. Om du vill kopiera samma mängden data som får en stor Radstorleken du bättre prestanda än en liten Radstorleken eftersom databasen mer effektivt kan genomföra färre batchar av data.
  * Kopieringsaktivitet infogar data i en serie med batchar. Du kan ange antalet rader i en batch med hjälp av den **writeBatchSize** egenskapen. Om dina data har liten rader, kan du ange den **writeBatchSize** egenskap med ett högre värde kan dra nytta av lägre omkostnader för batch och högre dataflöde. Om Radstorleken på dina data är stor, var försiktig när du ökar **writeBatchSize**. Ett högt värde kan leda till en kopieringen misslyckades på grund av överbelastning i databasen.
* För **lokala relationsdatabaser** som SQL Server- och Oracle, som kräver användning av **Datahanteringsgateway**, finns i den [överväganden för Data Management Gateway](#considerations-for-data-management-gateway)avsnittet.

### <a name="nosql-stores"></a>NoSQL-Arkiv
*(Inklusive tabellagring och Azure Cosmos DB)*

* För **tabellagring**:
  * **Partition**: Skriva data till överlagrad partitioner avsevärt försämrar prestanda. Sortera dina källdata efter partitionsnyckel så att data infogas effektivt i en partition efter en annan eller ändra logik för att skriva data till en enda partition.
* För **Azure Cosmos DB**:
  * **Batchstorlek**: Den **writeBatchSize** egenskapen anger hur många parallella förfrågningar till Azure Cosmos DB-tjänsten för att skapa dokument. Du kan förvänta dig bättre prestanda om du ökar **writeBatchSize** eftersom fler parallella begäranden skickas till Azure Cosmos DB. Se dock för begränsning av när du skriver till Azure Cosmos DB (felmeddelandet är ”förfrågan är stor”). Olika faktorer kan orsaka att begränsningar, inklusive dokumentstorlek, antalet villkoren i dokumenten och vilken målsamling indexeringsprincip. Överväg att använda en bättre samling, till exempel S3 för att uppnå högre kopia dataflöde.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och deserialisering
Serialisering och deserialisering kan inträffa när ditt inkommande datauppsättning eller en datauppsättning för utdata är en fil. Se [stöds format och komprimering](data-factory-supported-file-and-compression-formats.md) med information om filformat som stöds av Kopieringsaktivitet.

**Kopiera beteende**:

* Kopiera filer mellan filbaserat datalager:
  * När in- och utdata datauppsättningar båda har samma eller inga filformatinställningar, av data movement service körs en binär kopia utan serialisering eller avserialisering. Du kan se ett högre genomflöde jämfört med scenario, där filformatinställningar källa och mottagare skiljer sig från varandra.
  * När indata och utdata datauppsättningar båda är i textformat och endast kodningen typen är olika, av data movement service kräver endast kodning konvertering. Gör den alla serialisering och deserialisering, vilket gör att prestanda försämras jämfört med en binär kopia.
  * När in- och utdata datauppsättningar båda har olika filformat eller olika konfigurationer som avgränsare, av data movement service deserializes källdata för att strömma, transformera och serialisera det till utdataformat som du angett. Den här åtgärden resulterar i en mycket mer betydande overhead jämfört med andra scenarier.
* När du kopierar filer till/från ett datalager som inte är filbaserade (till exempel från store filbaserade som du kan använda för att en relationslagringsplats) krävs serialisering eller avserialisering steg. Det här steget leder till betydande prestanda försämras.

**Filformatet**: Filformat som du väljer kan påverka kopieringen bättre prestanda. Avro är till exempel ett kompakt binärformat som lagrar metadata med data. Det har ett brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dock dyrare för serialisering och deserialisering, vilket resulterar i lägre kopia genomflöde jämfört med textformat. Se ditt val av filformatet i hela flödet bearbetning holistiskt. Börja med vilken form data lagras i källdatalagren eller som ska extraheras från externa system. Det bästa formatet för lagring, analytisk behandling och fråga; och i vilket format data ska exporteras till dataarkiv för verktyg för rapportering och visualisering. Ibland ett format som är optimal för Läs- och skrivprestanda kan vara ett bra val när du funderar på övergripande analysprocessen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering
När datauppsättningen inkommande eller utgående är en fil kan ange du Kopieringsaktivitet för att utföra komprimering eller dekomprimering eftersom den skriver data till målet. När du väljer komprimering kan du göra en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera data kostnaderna extra beräkningsresurser. Men i utbyte kan det minskar nätverkets i/o och lagring. Beroende på dina data, kan du se en högre övergripande kopia dataflöde.

**Codec**: Kopieringsaktivitet har stöd för gzip, bzip2 och Deflate-komprimeringstyper. Azure HDInsight kan använda alla tre typer för bearbetning. Varje komprimerings-codec har fördelar. Till exempel bzip2 har den lägsta kopia dataflöden, men du får bästa Hive frågeprestanda med bzip2 eftersom du kan dela upp den för bearbetning. Gzip är det mest balanserade alternativet, och det används oftast. Välj codec som bäst passar ditt scenario för slutpunkt till slutpunkt.

**Nivå**: Du kan välja mellan två alternativ för varje komprimerings-codec: snabbaste komprimerade och optimalt komprimerad. Det snabbaste komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad. Alternativet optimalt komprimerade tillbringar mer tid på komprimering och återger en minimal mängd data. Du kan testa båda alternativen för att se vilket ger bättre prestanda i ditt fall.

**Ersättning**: Överväg att använda tillfälliga blob-lagring med komprimering för att kopiera en stor mängd data mellan en lokal databas och molnet. Det är praktiskt att använda mellanlagring när bandbredden för företagets nätverk och dina Azure-tjänster är den begränsande faktorn och du vill att den inkommande datauppsättningen och datauppsättningen för utdata både i okomprimerade form. Mer specifikt kan du dela upp en enda Kopieringsaktivitet i två kopieringsaktiviteter. Första kopieringsaktiviteten kopierar från källan till en tillfällig eller mellanlagring blob i komprimerad form. Andra kopieringsaktiviteten kopierar komprimerade data från mellanlagring och sedan expanderar medan den skriver till mottagaren.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappningen
Du kan ange den **columnMappings** egenskapen i Kopieringsaktiviteten till kartan alla eller en delmängd av kolumnerna indata till utdatakolumner. När av data movement service läser data från källan, behöver så utföra kolumnmappning på data innan den skriver data till mottagaren. Den här extra bearbetningen minskar kopia dataflöde.

Om ditt källdatalager är frågningsbar, till exempel om det är en relationslagringsplats som SQL Database eller SQL Server, eller om det är ett NoSQL-Arkiv som Table storage eller Azure Cosmos DB kan du push-överföra den kolumn som filtrering och sortering logik för att den **fråga** egenskapen istället för att använda kolumnmappning. På så sätt kan projektionen inträffar medan av data movement service läser data från källans datalager, där det är mycket mer effektivt.

## <a name="other-considerations"></a>Annat att tänka på
Om storleken på data som du vill kopiera är stor, kan du justera affärslogiken för att partitionera data med hjälp av mekanismen för slicing i Data Factory. Schemalägg sedan Kopieringsaktivitet körs oftare för att minska storleken på data för varje körningen av Kopieringsaktiviteten.

Var försiktig antalet datauppsättningar och kopieringsaktiviteter som kräver Data Factory till koppling till samma datalager samtidigt. Många samtidiga kopia jobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera jobbet interna återförsök, och i vissa fall, fel vid körning.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempelscenario: Kopiera från en lokal SQL Server till Blob storage
**Scenario**: En pipeline är utformat för att kopiera data från en lokal SQL Server till Blob storage i CSV-format. Om du vill göra kopieringsjobbet snabbare ska CSV-filer komprimeras bzip2-format.

**Testning och analys**: Dataflödet för Kopieringsaktivitet är mindre än 2 Mbit/s, vilket är mycket långsammare än benchmark för prestanda.

**Prestandaanalys och justera**: Om du vill felsöka prestandaproblem, nu ska vi titta på hur data bearbetas och flyttas.

1. **Läsa data**: Gatewayen öppnar en anslutning till SQL Server och skickar frågan. SQL-servern svarar genom att skicka dataströmmen till gatewayen via intranätet.
2. **Serialisera och komprimera data**: Gatewayen Serialiserar dataströmmen till CSV-format och komprimerar data till en bzip2-dataström.
3. **Skriva data**: Gateway överför bzip2 dataströmmen till Blob storage via Internet.

Som du ser data håller på att behandlas och flyttas i strömmande ordning: SQL Server > LAN > Gateway > WAN > Blob-lagring. **Den övergripande prestandan är begränsad av minsta dataflödet i pipelinen**.

![Dataflöde](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka flaskhals för prestanda:

* **Källa**: SQL-servern har låg genomströmning på grund av tunga belastningar.
* **Data Management Gateway**:
  * **LAN**: Gatewayen är placerad är långt från SQL Server-datorn och har en långsam anslutning.
  * **Gateway**: Gateway har nått sin belastningen begränsningar om du vill utföra följande åtgärder:
    * **Serialisering**: Serialisering av data i dataströmmen till CSV-format har långsam dataflöde.
    * **Komprimering**: Du har valt en långsam komprimerings-codec (till exempel bzip2, vilket är 2,8 Mbit/s med Core i7).
  * **WAN**: Bandbredden mellan företagsnätverket och dina Azure-tjänster är låg (till exempel T1 = 1,544 kbit/s; T2 = 6,312 kbit/s).
* **Mottagare**: BLOB-lagring har lågt dataflöde. (Det här scenariot är inte troligt eftersom dess serviceavtalsgarantier minst 60 Mbit/s.)

I det här fallet kan bzip2 datakomprimering långsammare hela pipelinen. Växla till en gzip komprimerings-codec kan underlätta den här begränsningen.

## <a name="sample-scenarios-use-parallel-copy"></a>Exempelscenarier: Använda parallella kopia
**Scenario I:** Kopiera 1 000 1 MB-filer från det lokala filsystemet till Blob storage.

**Analys och prestandajustering**: Exempel: Om du har installerat gatewayen på en dator med fyra kärnor, Data Factory använder 16 parallella kopior flytta filer från filsystemet till blobblagringen samtidigt. Den här parallell körning resulterar i högt dataflöde. Du kan också uttryckligen ange antalet parallella kopior. När du kopierar många små filer att parallella kopior dramatiskt dataflöde med hjälp av resurser effektivare.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: Kopiera 20 blobar på 500 MB från Blob storage till Data Lake Store Analytics och sedan justera prestanda.

**Analys och prestandajustering**: I det här scenariot Data Factory kopierar data från Blob storage till Data Lake Store med hjälp av enstaka kopia (**parallelCopies** inställt på 1) och dataflyttenheter som enda moln. Dataflödet som du ser nära som beskrivs i den [prestanda referensavsnittet](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: Enskild fil är större än dussintals MB och total volym är stor.

**Analys och aktivera prestanda**: Öka **parallelCopies** resulterar inte i ger kopieringen bättre prestanda på grund av resursbegränsningar av ett enda moln dmu här. I stället bör du ange mer molnet DMUs att få mer resurser för att utföra dataförflyttning. Ange ett värde för den **parallelCopies** egenskapen. Data Factory hanterar parallellitet för dig. I det här fallet, om du ställer in **cloudDataMovementUnits** till 4, ett dataflöde på ungefär fyra gånger inträffar.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referens
Här följer prestandaövervakning och justering referenser för några av datalager som stöds:

* Azure Storage (inklusive Blob-lagring och tabellagring): [Azure Storage-skalbarhetsmål](../../storage/common/storage-scalability-targets.md) och [checklista för prestanda och skalbarhet i Azure Storage](../../storage/common/storage-performance-checklist.md)
* Azure SQL Database: Du kan [övervaka prestanda](../../sql-database/sql-database-single-database-monitor.md) och kontrollera database transaction unit (DTU) procent
* Azure SQL Data Warehouse: Dess funktion mäts i informationslagerenheter (dwu: er); Se [hantera beräkningskraft i Azure SQL Data Warehouse (översikt)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Prestandanivåer i Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* En lokal SQLServer: [Övervaka och finjustera prestanda](https://msdn.microsoft.com/library/ms189081.aspx)
* En lokal filserver: [Prestandajustering för filservrar](https://msdn.microsoft.com/library/dn567661.aspx)
