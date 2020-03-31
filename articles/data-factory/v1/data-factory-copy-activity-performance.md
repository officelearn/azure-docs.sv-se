---
title: Guide till prestandajustering för kopieringsaktiviteter
description: Lär dig mer om viktiga faktorer som påverkar prestanda för data förflyttning i Azure Data Factory när du använder Kopiera aktivitet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c4ca328aa0ddc61d86a435b93fe775f294287b98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527392"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guide till prestandajustering för kopieringsaktiviteter

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](data-factory-copy-activity-performance.md)
> * [Version 2 (aktuell version)](../copy-activity-performance.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av datafabrikstjänsten läser du [Kopiera aktivitetsprestanda och justeringsguide för Data Factory](../copy-activity-performance.md).

Azure Data Factory Copy Activity levererar en förstklassig säker, tillförlitlig och högpresterande datainläsningslösning. Det gör att du kan kopiera tiotals terabyte data varje dag över en mängd olika moln och lokala datalager. Blixtsnabb datainläsningsprestanda är nyckeln till att du kan fokusera på det centrala problemet med "big data": att skapa avancerade analyslösningar och få djupa insikter från alla dessa data.

Azure tillhandahåller en uppsättning datalagrings- och informationslagerlösningar i företagsklass och copy activity erbjuder en mycket optimerad datainläsning som är enkel att konfigurera och konfigurera. Med bara en enda kopieringsaktivitet kan du uppnå:

* Läser in data i **Azure SQL Data Warehouse** på **1,2 GBps**. En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
* Läsa in data i **Azure Blob-lagring** med **1,0 GBps**
* Läsa in data i **Azure Data Lake Store** med **1,0 GBps**

I den här artikeln beskrivs:

* [Referensnummer](#performance-reference) för prestanda för käll- och sinkdatalager som stöds för att hjälpa dig att planera projektet.
* Funktioner som kan öka kopieringsdataflödet i olika scenarier, inklusive [molndataförflyttningsenheter,](#cloud-data-movement-units) [parallellkopia](#parallel-copy)och [stegvis kopia;](#staged-copy)
* [Vägledning för prestandajustering](#performance-tuning-steps) om hur du justerar prestanda och de viktigaste faktorer som kan påverka kopieringsprestanda.

> [!NOTE]
> Om du inte är bekant med Kopiera aktivitet i allmänhet läser du [Flytta data med hjälp av Kopiera aktivitet](data-factory-data-movement-activities.md) innan du läser den här artikeln.
>

## <a name="performance-reference"></a>Referens för prestanda

Som referens visas kopieringsdataflödet i MBps för de givna käll- och diskbänksparen baserat på interna tester. Som jämförelse visas också hur olika inställningar för [molndatarörelserheter](#cloud-data-movement-units) eller [datahanteringsgatewayskalbarhet](data-factory-data-management-gateway-high-availability-scalability.md) (flera gatewaynoder) kan hjälpa till att kopiera prestanda.

![Matris för prestanda](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>I Azure Data Factory version 1 är de minimala molndataförflyttningsenheterna för moln-till-moln-kopiering två. Om inget anges, se standarddataförflyttningsenheter som används i [molndataförflyttningsenheter](#cloud-data-movement-units).

**Punkter att notera:**
* Dataflöde beräknas med hjälp av följande formel: [storleken på data som läss från källan]/[Kopiera aktivitetskörningsvaraktighet].
* Prestandareferensnumren i tabellen mättes med hjälp av [TPC-H-datauppsättning](http://www.tpc.org/tpch/) i en enda kopieringsaktivitetskörning.
* I Azure-datalager finns källan och diskhon i samma Azure-region.
* För hybridkopiering mellan lokala datalager och molndatalager kördes varje gatewaynod på en dator som var separat från det lokala datalagret med nedanspecifikationen. När en enskild aktivitet kördes på gatewayen förbrukade kopieringen endast en liten del av testdatorns processor-, minnes- eller nätverksbandbredd. Läs mer [om datahanteringsgateway](#considerations-for-data-management-gateway).
    <table>
    <tr>
        <td>Processor</td>
        <td>32 kärnor 2,20 GHz Intel Xeon E5-2660 v2</td>
    </tr>
    <tr>
        <td>Minne</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Nätverk</td>
        <td>Internet-gränssnitt: 10 Gbit/s; intranätgränssnitt: 40 Gbit/s</td>
    </tr>
    </table>


> [!TIP]
> Du kan uppnå högre dataflöde genom att utnyttja fler dataförflyttningsenheter (DMUs) än standardmaxydsenheter, vilket är 32 för en moln-till-moln-kopieringsaktivitetskörning. Med 100 DMUs kan du till exempel kopiera data från Azure Blob till Azure Data Lake Store med **1.0GBps**. Mer information om den här funktionen och scenariot som stöds finns i avsnittet [Molndatarörelserenheter.](#cloud-data-movement-units) Kontakta [Azure-supporten](https://azure.microsoft.com/support/) för att begära fler DD:er.

## <a name="parallel-copy"></a>Parallell kopia
Du kan läsa data från källan eller skriva data till målet **parallellt i en kopieringsaktivitetskörning**. Den här funktionen förbättrar dataflödet för en kopiering och minskar den tid det tar att flytta data.

Den här inställningen skiljer sig från egenskapen **samtidighet** i aktivitetsdefinitionen. Egenskapen **samtidighet** bestämmer antalet **samtidiga kopieringsaktivitet** som körs för att bearbeta data från olika aktivitetsfönster (01:00 till 02:00, 02:00 till 03:00, 03:00 till 04:00 och så vidare). Den här funktionen är användbar när du utför en historisk belastning. Funktionen för parallellkopiering gäller för en **enskild aktivitetskörning**.

Låt oss titta på ett exempelscenario. I följande exempel måste flera segment från det förflutna bearbetas. Data Factory kör en instans av kopieringsaktivitet (en aktivitetskörning) för varje segment:

* Datasegmentet från det första aktivitetsfönstret (01.00 till 02.00) ==> Aktivitet körs 1
* Datasegmentet från det andra aktivitetsfönstret (02.00 till 03.00) ==> Aktivitet körs 2
* Datasegmentet från det andra aktivitetsfönstret (03.00 till 04.00) ==> Aktivitet körs 3

Och så vidare.

I det här exemplet när **samtidighetsvärdet** är inställt på 2, **kör Aktivitet 1** och Aktivitet **2** kopiera data från två **aktivitetsfönster samtidigt** för att förbättra dataförflyttningsprestanda. Men om flera filer är associerade med Aktivitet kör 1 kopierar dataflyttningstjänsten filer från källan till mål en-filen i taget.

### <a name="cloud-data-movement-units"></a>Molndatarörelseenheter
En **molndataförflyttningsenhet (DMU)** är ett mått som representerar kraften (en kombination av CPU, minne och nätverksresursallokering) för en enskild enhet i Data Factory. DMU gäller för moln-till-moln-kopieringsåtgärder, men inte i en hybridkopia.

**De minimala molndatarörelseenheterna för att ge kopiera aktivitetskörning är två.** Om det inte anges visas i följande tabell de standard-DKU:er som används i olika kopieringsscenarier:

| Kopiera scenario | Standard-DD:er som bestäms av tjänsten |
|:--- |:--- |
| Kopiera data mellan filbaserade butiker | Mellan 4 och 16 beroende på antalet och storleken på filerna. |
| Alla andra kopieringsscenarier | 4 |

Om du vill åsidosätta den här standardinställningen anger du ett värde för egenskapen **cloudDataMovementUnits** enligt följande. De **tillåtna värdena** för egenskapen **cloudDataMovementUnits** är 2, 4, 8, 16, 32. Det **faktiska antalet DU:er i molnet** som kopieringen använder vid körning är lika med eller mindre än det konfigurerade värdet, beroende på ditt datamönster. Information om vilken prestandavinstnivå du kan få när du konfigurerar fler enheter för en viss kopiaskälla och diskho finns i [prestandareferensen](#performance-reference).

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
> Om du behöver fler DD:er i molnet för ett högre dataflöde kontaktar du [Azure-supporten](https://azure.microsoft.com/support/). Inställningen på 8 och högre fungerar för närvarande endast när du **kopierar flera filer från Blob storage/Data Lake Store/Amazon S3/cloud FTP/cloud SFTP till Blob storage/Data Lake Store/Azure SQL Database**.
>

### <a name="parallelcopies"></a>parallelCopies (parallelCopies)
Du kan använda egenskapen **parallelCopies** för att ange den parallellitet som du vill att kopiera aktivitet ska använda. Du kan se den här egenskapen som det maximala antalet trådar i Kopiera aktivitet som kan läsa från källan eller skriva till dina sink-datalager parallellt.

För varje kopieringsaktivitetskörning bestämmer Data Factory antalet parallella kopior som ska användas för att kopiera data från källdatalagret och till måldatalagret. Standardantalet parallellkopior som används beror på vilken typ av källa och diskho som du använder.

| Källa och diskbänk | Standardantal för parallell kopia bestäms av tjänsten |
| --- | --- |
| Kopiera data mellan filbaserade butiker (Blob-lagring; Data Lake Store; Amazon S3; Ett lokalt filsystem. en lokal HDFS) |Mellan 1 och 32. Beror på storleken på filerna och antalet molndataförflyttningsenheter (DMUs) som används för att kopiera data mellan två molndatalager, eller den fysiska konfigurationen av gateway-datorn som används för en hybridkopia (för att kopiera data till eller från ett lokalt datalager). |
| Kopiera data från **alla källdatalager till Azure Table-lagring** |4 |
| Alla andra käll- och diskbänkspar |1 |

Vanligtvis bör standardbeteendet ge dig det bästa dataflödet. Om du vill styra belastningen på datorer som är värdar för dina datalager, eller för att justera kopieringsprestanda, kan du välja att åsidosätta standardvärdet och ange ett värde för egenskapen **parallelCopies.** Värdet måste vara mellan 1 och 32 (båda inklusive). Vid körning använder copy activity ett värde som är mindre än eller lika med det värde som du anger vid bästa prestanda.

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
Punkter att notera:

* När du kopierar data mellan filbaserade butiker bestämmer **parallelCopies** parallellismen på filnivå. Den chunking inom en enda fil skulle ske under automatiskt och transparent, och det är utformat för att använda den bästa lämpliga segmentstorlek för en viss källa datalager typ för att ladda data parallellt och ortogonal till parallelCopies. Det faktiska antalet parallellkopior som dataflyttningstjänsten använder för kopieringen vid körningen är inte mer än det antal filer du har. Om kopieringsbeteendet är **mergeFile**kan copy activity inte dra nytta av parallellisering på filnivå.
* När du anger ett värde för egenskapen **parallelCopies** bör du tänka på beläggningsökningen på käll- och sinkdatalager och till gatewayen om det är en hybridkopia. Detta händer särskilt när du har flera aktiviteter eller samtidiga körningar av samma aktiviteter som körs mot samma datalager. Om du märker att antingen datalagret eller Gateway är överväldigad med belastningen, minska **värdet parallelCopies** för att avlasta.
* När du kopierar data från butiker som inte är filbaserade i arkiv som är filbaserade, ignorerar dataförflyttningstjänsten egenskapen **parallelCopies.** Även om parallellism anges, är det inte tillämpas i detta fall.

> [!NOTE]
> Du måste använda Data Management Gateway version 1.11 eller senare för att använda **funktionen parallelCopies** när du gör en hybridkopia.
>
>

Om du vill använda dessa två egenskaper bättre och förbättra dataflödet finns i exempelanvändningsfallen. Du behöver inte konfigurera **parallelCopies** för att dra nytta av standardbeteendet. Om du konfigurerar och **parallelCopies** är för liten, kanske flera DD:er i molnet inte utnyttjas fullt ut.

### <a name="billing-impact"></a>Faktureringspåverkan
Det är **viktigt** att komma ihåg att du debiteras baserat på den totala tiden för kopieringen. Om ett kopieringsjobb brukade ta en timme med en molnenhet och nu tar det 15 minuter med fyra molnenheter, förblir den totala fakturan nästan densamma. Du kan till exempel använda fyra molnenheter. Den första molnenheten spenderar 10 minuter, den andra, 10 minuter, den tredje, 5 minuter och den fjärde, 5 minuter, allt i en kopiera aktivitet köra. Du debiteras för den totala kopieringstiden (dataflyttning), vilket är 10 + 10 + 5 + 5 = 30 minuter. Att använda **parallelCopies** påverkar inte faktureringen.

## <a name="staged-copy"></a>Stegvis kopia
När du kopierar data från ett källdatalager till ett sink-datalager kan du välja att använda Blob-lagring som ett interimistiskt mellanlagringsarkiv. Mellanlagring är särskilt användbart i följande fall:

1. **Du vill använda data från olika datalager i SQL Data Warehouse via PolyBase**. SQL Data Warehouse använder PolyBase som en mekanism med högt dataflöde för att läsa in en stor mängd data i SQL Data Warehouse. Källdata måste dock finnas i Blob-lagring och måste uppfylla ytterligare kriterier. När du läser in data från ett annat datalager än Blob-lagring kan du aktivera datakopiering via tillfällig mellanlagrings-Blob-lagring. I så fall utför Data Factory de dataomvandlingar som krävs för att säkerställa att den uppfyller kraven i PolyBase. Sedan använder polybase för att läsa in data i SQL Data Warehouse. Mer information finns i [Använda PolyBase för att läsa in data i Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).
2. **Ibland tar det ett tag att utföra en hybriddataförflyttning (det vill veta mer om att kopiera mellan ett lokalt datalager och ett molndatalager) via en långsam nätverksanslutning**. För att förbättra prestanda kan du komprimera data lokalt så att det tar mindre tid att flytta data till mellanlagringsdatalagret i molnet. Sedan kan du expandera data i mellanlagringsarkivet innan du läser in dem i måldatalagret.
3. **Du vill inte öppna andra portar än port 80 och port 443 i brandväggen på grund av företagets IT-principer.** När du till exempel kopierar data från ett lokalt datalager till en Azure SQL Database-diskho eller en Azure SQL Data Warehouse-mottagare måste du aktivera utgående TCP-kommunikation på port 1433 för både Windows-brandväggen och företagets brandvägg. I det här fallet dra nytta av gatewayen för att först kopiera data till en Blob lagring mellanlagringsinstans över HTTP eller HTTPS på port 443. Läs sedan in data i SQL Database eller SQL Data Warehouse från Blob storage-mellanlagring. I det här flödet behöver du inte aktivera port 1433.

### <a name="how-staged-copy-works"></a>Så här fungerar iscensatt kopia
När du aktiverar mellanlagringsfunktionen kopieras först data från källdatalagret till mellanlagringsdatalagret (ta med egna). Därefter kopieras data från mellanlagringsdatalagret till sink-datalagret. Data Factory hanterar automatiskt tvåstegsflödet åt dig. Data Factory rensar också tillfälliga data från mellanlagringslagringen när dataförflyttningen är klar.

I scenariot för molnkopiering (både käll- och sinkdatalager finns i molnet) används inte gatewayen. Tjänsten Data Factory utför kopieringsåtgärderna.

![Stegvis kopia: Molnscenario](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

I hybridkopieringsscenariot (källan är lokal och sink finns i molnet) flyttar gatewayen data från källdataarkivet till ett mellanlagringsdatalager. Data Factory-tjänsten flyttar data från mellanlagringsdatalagret till sink-datalagret. Kopiering av data från ett molndatalager till ett lokalt datalager via mellanlagring stöds också med det omvända flödet.

![Stegvis kopia: Hybridscenario](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

När du aktiverar dataförflyttning med hjälp av ett mellanlagringslager kan du ange om du vill att data ska komprimeras innan du flyttar data från källdatalagret till ett mellanlagringsdatalager och sedan expanderade innan du flyttar data från en mellanliggande data eller mellanlagringsdata lagra till diskbänksdatalagret.

För närvarande kan du inte kopiera data mellan två lokala datalager med hjälp av ett mellanlagringsarkiv. Vi förväntar oss att detta alternativ kommer att vara tillgängligt inom kort.

### <a name="configuration"></a>Konfiguration
Konfigurera inställningen **enableStaging** i Kopiera aktivitet för att ange om du vill att data ska mellanlagras i Blob-lagring innan du läser in dem i ett måldatalager. När du anger **enableStaging** till TRUE anger du de ytterligare egenskaper som visas i nästa tabell. Om du inte har någon måste du också skapa en signaturlänkad Azure Storage- eller Storage-tjänst för delad åtkomst för mellanlagring.

| Egenskap | Beskrivning | Standardvärde | Krävs |
| --- | --- | --- | --- |
| **enableStaging** |Ange om du vill kopiera data via ett interimistiskt mellanlagringsarkiv. |False |Inga |
| **linkedServiceName** |Ange namnet på en [AzureStorage-](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [AzureStorageSas-länkad](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) tjänst, som refererar till den instans av lagring som du använder som ett interimistiskt mellanlagringsarkiv. <br/><br/> Du kan inte använda Lagring med en signatur för delad åtkomst för att läsa in data i SQL Data Warehouse via PolyBase. Du kan använda den i alla andra scenarier. |Ej tillämpligt |Ja, när **enableStaging** är inställt på SANT |
| **Sökvägen** |Ange den Blob-lagringssökväg som du vill innehålla mellanlagringsdata. Om du inte anger en sökväg skapar tjänsten en behållare för att lagra tillfälliga data. <br/><br/> Ange bara en sökväg om du använder Lagring med en signatur för delad åtkomst eller om du behöver tillfälliga data för att vara på en viss plats. |Ej tillämpligt |Inga |
| **aktiveraKomprimering** |Anger om data ska komprimeras innan de kopieras till målet. Den här inställningen minskar mängden data som överförs. |False |Inga |

Här är en exempeldefinition av kopieringsaktivitet med de egenskaper som beskrivs i föregående tabell:

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

### <a name="billing-impact"></a>Faktureringspåverkan
Du debiteras baserat på två steg: kopieringslängd och kopieringstyp.

* När du använder mellanlagring under en molnkopia (kopiera data från ett molndatalager till ett annat molndatalager) debiteras du [summan av kopieringstiden för steg 1 och steg 2] x [molnkopieringsenhetspris].
* När du använder mellanlagring under en hybridkopia (kopiera data från ett lokalt datalager till ett molndatalager) debiteras du för [hybridkopieringsvaraktighet] x [hybridkopieringsenhetspris] + [molnkopieringsvaraktighet] x [molnkopieringsenhetspris].

## <a name="performance-tuning-steps"></a>Steg för prestandajustering
Vi föreslår att du vidtar följande åtgärder för att justera prestanda för din Data Factory-tjänst med kopieringsaktivitet:

1. **Upprätta en baslinje**. Under utvecklingsfasen testar du pipelinen med hjälp av Kopiera aktivitet mot ett representativt dataexempel. Du kan använda [datafabrikssliceringsmodellen](data-factory-scheduling-and-execution.md) för att begränsa mängden data som du arbetar med.

   Samla in körningstid och prestandaegenskaper med hjälp av **övervaknings- och hanteringsappen**. Välj **Övervaka & Hantera** på startsidan för Data Factory. Välj **utdatauppsättningen**i trädvyn . Välj kör Kopiera aktivitet i listan **Aktivitetsfönster.** **I Aktivitetsfönster** visas varaktigheten för kopiera aktivitet och storleken på de data som kopieras. Dataflödet visas i **Aktivitetsfönsterutforskaren**. Mer information om appen finns i [Övervaka och hantera Azure Data Factory-pipelines med hjälp av övervaknings- och hanteringsappen](data-factory-monitor-manage-app.md).

   ![Aktivitetskörningsinformation](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Senare i artikeln kan du jämföra prestanda och konfiguration av ditt scenario med Kopiera aktivitets [prestandareferens](#performance-reference) från våra tester.
2. **Diagnostisera och optimera prestanda**. Om prestandan du observerar inte uppfyller dina förväntningar måste du identifiera flaskhalsar i prestanda. Optimera sedan prestanda för att ta bort eller minska effekten av flaskhalsar. En fullständig beskrivning av prestandadiagnos är utanför ramen för den här artikeln, men här är några vanliga överväganden:

   * Prestandafunktioner:
     * [Parallell kopia](#parallel-copy)
     * [Molndatarörelseenheter](#cloud-data-movement-units)
     * [Stegvis kopia](#staged-copy)
     * [Skalbarhet för datahanteringsgateway](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gateway för datahantering](#considerations-for-data-management-gateway)
   * [Källkod](#considerations-for-the-source)
   * [Kanalmottagare](#considerations-for-the-sink)
   * [Serialisering och deserialisering](#considerations-for-serialization-and-deserialization)
   * [Komprimering](#considerations-for-compression)
   * [Kolumnmappning](#considerations-for-column-mapping)
   * [Andra överväganden](#other-considerations)
3. **Expandera konfigurationen till hela datauppsättningen**. När du är nöjd med körningsresultaten och prestandan kan du expandera den aktiva perioden för definition och pipeline så att den täcker hela datauppsättningen.

## <a name="considerations-for-data-management-gateway"></a>Överväganden för datahanteringsgateway
**Gateway setup**: Vi rekommenderar att du använder en dedikerad dator för att vara värd för Data Management Gateway. Se [Överväganden för att använda Data Management Gateway](data-factory-data-management-gateway.md#considerations-for-using-gateway).

**Gateway övervakning och skala upp / ut:** En enda logisk gateway med en eller flera gateway noder kan tjäna flera Kopiera aktivitet körs samtidigt. Du kan visa ögonblicksbilder i nära realtid av resursutnyttjande (CPU, minne, nätverk(in/ut), etc.) på en gateway-dator samt antalet samtidiga jobb som körs jämfört med gränsen i Azure-portalen, se [Övervaka gatewayen i portalen](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Om du har stort behov av hybriddataförflyttning antingen med ett stort antal samtidiga kopieringsaktiviteter körs eller med stor mängd data att kopiera, överväga att [skala upp eller skala ut gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) för att bättre utnyttja din resurs eller att etablera mer resurs för att ge kopiera.

## <a name="considerations-for-the-source"></a>Överväganden för källan
### <a name="general"></a>Allmänt
Se till att det underliggande datalagret inte är överbelastat av andra arbetsbelastningar som körs på eller mot det.

För Microsoft-datalager läser du [övervaknings- och justeringsämnen](#performance-reference) som är specifika för datalager och hjälper dig att förstå prestandaegenskaper för datalager, minimera svarstider och maximera dataflödet.

Om du kopierar data från Blob-lagring till SQL Data Warehouse kan du överväga att använda **PolyBase** för att öka prestanda. Mer information finns i Använda PolyBase för [att läsa in data i Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserade datalager
*(Inkluderar Blob storage, Data Lake Store, Amazon S3, lokala filsystem och lokala HDFS)*

* **Genomsnittlig filstorlek och filantal:** Kopiera aktivitet överför data en fil i taget. Med samma mängd data som ska flyttas, är det totala dataflödet lägre om data består av många små filer snarare än några stora filer på grund av bootstrap fasen för varje fil. Kombinera därför små filer till större filer om möjligt för att få högre dataflöde.
* **Filformat och komprimering**: Mer sätt att förbättra prestanda finns i [överväganden för serialisering och deserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimeringsavsnitt.](#considerations-for-compression)
* Det **lokala filsystemscenariot,** där **datahanteringsgateway** krävs, finns i avsnittet [Överväganden för datahanteringsgateway.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relationsdatalager
*(Inkluderar SQL-databas; SQL-datalager; Amazon Redshift; SQL Server-databaser; och Oracle, MySQL, DB2, Teradata, Sybase och PostgreSQL databaser, etc.)*

* **Datamönster**: Tabellschemat påverkar kopieringsflödet. En stor radstorlek ger dig bättre prestanda än liten radstorlek, för att kopiera samma mängd data. Anledningen är att databasen mer effektivt kan hämta färre batchar med data som innehåller färre rader.
* **Fråga eller lagrad procedur**: Optimera logiken i frågan eller lagrad procedur som du anger i källan Kopiera aktivitet för att hämta data mer effektivt.
* Lokala **relationsdatabaser**, till exempel SQL Server och Oracle, som kräver användning av **Data Management Gateway**, finns i avsnittet Överväganden för datahanteringsgateway.

## <a name="considerations-for-the-sink"></a>Överväganden för diskbänken
### <a name="general"></a>Allmänt
Se till att det underliggande datalagret inte är överbelastat av andra arbetsbelastningar som körs på eller mot det.

Information om Microsoft-datalager finns i [övervaknings- och justeringsämnen](#performance-reference) som är specifika för datalager. De här avsnitten kan hjälpa dig att förstå prestandaegenskaper för datalager och hur du minimerar svarstider och maximerar dataflödet.

Om du kopierar data från **Blob-lagring** till **SQL Data Warehouse**kan du använda **PolyBase** för att öka prestanda. Mer information finns i Använda PolyBase för [att läsa in data i Azure SQL Data Warehouse.](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) En genomgång med ett användningsfall finns [i Ladda 1 TB till Azure SQL Data Warehouse under 15 minuter med Azure Data Factory](data-factory-load-sql-data-warehouse.md).

### <a name="file-based-data-stores"></a>Filbaserade datalager
*(Inkluderar Blob storage, Data Lake Store, Amazon S3, lokala filsystem och lokala HDFS)*

* **Kopieringsbeteende**: Om du kopierar data från ett annat filbaserat datalager har kopieringsaktivitet tre alternativ via egenskapen **copyBehavior.** Hierarkin bevaras, hierarkin förenklas eller sammanfogas filer. Antingen bevara eller platta hierarki har liten eller ingen prestanda overhead, men sammanslagning av filer orsakar prestanda omkostnader att öka.
* **Filformat och komprimering**: Se [överväganden för serialisering och avserialisering](#considerations-for-serialization-and-deserialization) och [överväganden för komprimeringsavsnitt](#considerations-for-compression) för fler sätt att förbättra prestanda.
* **Blob storage**: För närvarande stöder Blob-lagring endast blockblobar för optimerad dataöverföring och dataflöde.
* För **lokala filsystemscenarier** som kräver användning av **Data Management Gateway**finns i avsnittet Överväganden för [datahanteringsgateway.](#considerations-for-data-management-gateway)

### <a name="relational-data-stores"></a>Relationsdatalager
*(Inkluderar SQL Database, SQL Data Warehouse, SQL Server-databaser och Oracle-databaser)*

* **Kopieringsbeteende**: Beroende på vilka egenskaper du har angett för **sqlSink**skriver Kopiera aktivitet data till måldatabasen på olika sätt.
  * Som standard använder dataförflyttningstjänsten API:et för masskopiering för att infoga data i tilläggsläge, vilket ger bästa prestanda.
  * Om du konfigurerar en lagrad procedur i diskhon använder databasen data en rad i taget i stället för som en massinläsning. Prestanda sjunker avsevärt. Om din datauppsättning är stor kan du överväga att byta till att använda egenskapen **sqlWriterCleanupScript.**
  * Om du konfigurerar **sqlWriterCleanupScript-egenskapen** för varje kopieringsaktivitetskörning utlöser tjänsten skriptet och sedan använder du API:et masskopia för att infoga data. Om du till exempel vill skriva över hela tabellen med de senaste data kan du ange ett skript för att först ta bort alla poster innan de nya data fylls i massinläsning från källan.
* **Datamönster och batchstorlek:**
  * Tabellschemat påverkar kopieringsdataflödet. Om du vill kopiera samma mängd data ger en stor radstorlek dig bättre prestanda än en liten radstorlek eftersom databasen mer effektivt kan utföra färre databats innehåller.
  * Kopiera aktivitet infogar data i en serie batchar. Du kan ange antalet rader i en batch med hjälp av egenskapen **writeBatchSize.** Om dina data har små rader kan du ställa in egenskapen **writeBatchSize** med ett högre värde för att dra nytta av lägre batchomkostnader och högre dataflöde. Om radstorleken på dina data är stor, var försiktig när du ökar **writeBatchSize**. Ett högt värde kan leda till ett kopieringsfel som orsakas av överbelastning av databasen.
* Lokala **relationsdatabaser** som SQL Server och Oracle, som kräver användning av **Data Management Gateway,** finns i avsnittet Överväganden för [datahanteringsgateway.](#considerations-for-data-management-gateway)

### <a name="nosql-stores"></a>NoSQL butiker
*(Inkluderar tabelllagring och Azure Cosmos DB )*

* För **tabelllagring:**
  * **Partition**: Skriva data till interfolierade partitioner försämrar dramatiskt prestanda. Sortera källdata efter partitionsnyckel så att data infogas effektivt i den ena partitionen efter den andra, eller justera logiken för att skriva data till en enda partition.
* För **Azure Cosmos DB:**
  * **Batchstorlek**: Egenskapen **writeBatchSize** anger antalet parallella begäranden till Azure Cosmos DB-tjänsten för att skapa dokument. Du kan förvänta dig bättre prestanda när du ökar **writeBatchSize** eftersom fler parallella begäranden skickas till Azure Cosmos DB. Titta dock efter begränsning när du skriver till Azure Cosmos DB (felmeddelandet är "Begäranden är stor"). Olika faktorer kan orsaka begränsning, inklusive dokumentstorlek, antalet termer i dokumenten och målsamlingens indexeringsprincip. Om du vill uppnå högre kopieringsdataflöde bör du överväga att använda en bättre samling, till exempel S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Överväganden för serialisering och avserialisering
Serialisering och deserialisering kan inträffa när indatauppsättningen eller utdatauppsättningen är en fil. Se [Fil- och komprimeringsformat](data-factory-supported-file-and-compression-formats.md) som stöds med information om filformat som stöds av Kopiera aktivitet.

**Kopiera beteende:**

* Kopiera filer mellan filbaserade datalager:
  * När både in- och utdatauppsättningar har samma eller inga filformatsinställningar kör dataförflyttningstjänsten en binär kopia utan serialisering eller deserialisering. Du ser ett högre dataflöde jämfört med scenariot, där inställningarna för käll- och sink-filformat skiljer sig från varandra.
  * När både in- och utdatauppsättningar är i textformat och endast kodningstypen är olika, kodar dataflyttningstjänsten bara konverteringen av data. Det gör inte någon serialisering och deserialization, vilket orsakar vissa prestanda overhead jämfört med en binär kopia.
  * När både in- och utdatauppsättningar har olika filformat eller olika konfigurationer, till exempel avgränsare, deserialiserar dataförflyttningstjänsten källdata för att strömma, transformera och sedan serialisera dem till det utdataformat som du angav. Den här åtgärden resulterar i en mycket mer betydande prestandaomkostnader jämfört med andra scenarier.
* När du kopierar filer till/från ett datalager som inte är filbaserat (till exempel från ett filbaserat arkiv till ett relationsarkiv) krävs serialiserings- eller avserialiseringssteget. Det här steget resulterar i betydande prestandakostnader.

**Filformat**: Det filformat du väljer kan påverka kopieringsprestanda. Avro är till exempel ett kompakt binärt format som lagrar metadata med data. Den har brett stöd i Hadoop-ekosystemet för bearbetning och frågor. Avro är dock dyrare för serialisering och deserialisering, vilket resulterar i lägre kopieringsdataflöde jämfört med textformat. Gör ditt val av filformat i hela bearbetningsflödet holistiskt. Börja med vilket formulär data lagras i, källdatalager eller som ska extraheras från externa system. Det bästa formatet för lagring, analytisk bearbetning och frågor. och i vilket format data ska exporteras till datamars för rapportering och visualiseringsverktyg. Ibland kan ett filformat som är suboptimalt för läs- och skrivprestanda vara ett bra val när du tänker på den övergripande analysprocessen.

## <a name="considerations-for-compression"></a>Överväganden för komprimering
När indatauppsättningen är en fil kan du ange att kopiera aktivitet ska utföra komprimering eller dekompression när den skriver data till målet. När du väljer komprimering gör du en kompromiss mellan indata/utdata (I/O) och CPU. Komprimera datakostnaderna extra i beräkningsresurser. Men i gengäld minskar det nätverk I / O och lagring. Beroende på dina data kan du se en ökning av det totala kopieringsflödet.

**Codec**: Kopieringsaktivitet stöder gzip-, bzip2- och Deflate-komprimeringstyper. Azure HDInsight kan använda alla tre typerna för bearbetning. Varje komprimeringscodec har fördelar. Bzip2 har till exempel det lägsta kopieringsdataflödet, men du får bäst Hive-frågeprestanda med bzip2 eftersom du kan dela upp det för bearbetning. Gzip är det mest balanserade alternativet, och det används oftast. Välj den codec som bäst passar ditt end-to-end-scenario.

**Nivå:** Du kan välja mellan två alternativ för varje komprimeringskodifierare: snabbast komprimerad och optimalt komprimerad. Det snabbaste komprimerade alternativet komprimerar data så snabbt som möjligt, även om den resulterande filen inte är optimalt komprimerad. Det optimalt komprimerade alternativet lägger mer tid på komprimering och ger en minimal mängd data. Du kan testa båda alternativen för att se vilka som ger bättre prestanda i ditt fall.

**En faktor**: Om du vill kopiera en stor mängd data mellan ett lokalt arkiv och molnet kan du överväga att använda interims blob-lagring med komprimering. Att använda interim lagring är användbart när bandbredden i ditt företagsnätverk och dina Azure-tjänster är den begränsande faktorn, och du vill att indatauppsättningen och utdatauppsättningen både ska vara i okomprimerad form. Mer specifikt kan du dela upp en enda kopieringsaktivitet i två kopieringsaktiviteter. Den första kopieringsaktiviteten kopierar från källan till en mellanliggande blob eller mellanlagringsblob i komprimerad form. Den andra kopieringsaktiviteten kopierar komprimerade data från mellanlagring och expanderar sedan medan den skriver till diskhon.

## <a name="considerations-for-column-mapping"></a>Överväganden för kolumnmappning
Du kan ange egenskapen **columnMappings** i Kopiera aktivitet så att alla eller en delmängd av indatakolumnerna mappas till utdatakolumnerna. När dataförflyttningstjänsten läser data från källan måste den utföra kolumnmappning på data innan den skriver data till diskhon. Den här extra bearbetningen minskar kopieringsdataflödet.

Om ditt källdatalager är frågebart, till exempel om det är ett relationsarkiv som SQL Database eller SQL Server, eller om det är ett NoSQL-arkiv **query** som Tabelllagring eller Azure Cosmos DB, kan du överväga att skicka kolumnfiltreringen och ändra ordning på logiken till frågeegenskapen i stället för att använda kolumnmappning. På så sätt sker projektionen medan dataförflyttningstjänsten läser data från källdatalagret, där den är mycket effektivare.

## <a name="other-considerations"></a>Andra överväganden
Om storleken på de data som du vill kopiera är stor kan du justera affärslogiken för att ytterligare partitionera data med hjälp av skivningsmekanismen i Data Factory. Schemalägg sedan kopiera aktivitet så att den körs oftare för att minska datastorleken för varje kopieringsaktivitetskörning.

Var försiktig med antalet datauppsättningar och kopieringsaktiviteter som kräver att Data Factory ansluts till samma datalager samtidigt. Många samtidiga kopieringsjobb kan begränsa ett datalager och leda till försämrade prestanda, kopiera interna försök till jobb och i vissa fall körningsfel.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Exempelscenario: Kopiera från en lokal SQL Server till Blob-lagring
**Scenario**: En pipeline är byggd för att kopiera data från en lokal SQL Server till Blob-lagring i CSV-format. För att göra kopieringsjobbet snabbare bör CSV-filerna komprimeras till bzip2-format.

**Test och analys**: Genomströmningen av kopieringsaktivitet är mindre än 2 MBps, vilket är mycket långsammare än prestandariktmärket.

**Prestandaanalys och justering:** Om du vill felsöka prestandaproblemet ska vi titta på hur data bearbetas och flyttas.

1. **Läsdata:** Gateway öppnar en anslutning till SQL Server och skickar frågan. SQL Server svarar genom att skicka dataströmmen till Gateway via intranätet.
2. **Serialisera och komprimera data:** Gateway serialiserar dataströmmen till CSV-format och komprimerar data till en bzip2-ström.
3. **Skrivdata:** Gateway laddar upp bzip2-strömmen till Blob-lagring via Internet.

Som du kan se bearbetas data och flyttas på ett sekventiellt direktuppspel: SQL Server > LAN > Gateway > WAN-> Blob-lagring. **Den övergripande prestandan är gated av minsta genomströmning över rörledningen**.

![Dataflöde](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

En eller flera av följande faktorer kan orsaka flaskhalsen i prestanda:

* **Källa:** SQL Server själv har lågt dataflöde på grund av tunga belastningar.
* **Gateway för datahantering:**
  * **LAN:** Gateway ligger långt från SQL Server-datorn och har en anslutning med låg bandbredd.
  * **Gateway**: Gateway har nått sina belastningsbegränsningar för att utföra följande åtgärder:
    * **Serialisering:** Serialisering av dataströmmen till CSV-format har långsamt dataflöde.
    * **Komprimering:** Du valde en långsam komprimeringscodec (till exempel bzip2, som är 2,8 MBps med Core i7).
  * **WAN**: Bandbredden mellan företagsnätverket och dina Azure-tjänster är låg (till exempel T1 = 1 544 kbit/s. T2 = 6 312 kbit/s).
* **Sink:** Blob lagring har lågt dataflöde. (Det här scenariot är osannolikt eftersom dess serviceavtal garanterar minst 60 MBps.)

I det här fallet kan bzip2-datakomprimering sakta ner hela pipelinen. Om du byter till en gzip-komprimeringscodec kan det underlätta flaskhalsen.

## <a name="sample-scenarios-use-parallel-copy"></a>Exempelscenarier: Använd parallellkopia
**Scenario I:** Kopiera 1 000 1 MB-filer från det lokala filsystemet till Blob-lagring.

**Analys- och prestandajustering:** Om du till exempel har installerat gateway på en maskin med fyra kärnor använder Data Factory 16 parallella kopior för att flytta filer från filsystemet till Blob-lagring samtidigt. Den här parallella körningen bör resultera i högt dataflöde. Du kan också uttryckligen ange antalet parallella kopior. När du kopierar många små filer hjälper parallellkopior dramatiskt genomströmning genom att använda resurser mer effektivt.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: Kopiera 20 blobbar på 500 MB vardera från Blob-lagring till DataSjölagringsanalys och justera sedan prestanda.

**Analys- och prestandajustering:** I det här fallet kopierar Data Factory data från Blob-lagring till DataSjölagret med hjälp av enkopiering **(parallelCopies** set to 1) och dataförflyttningsenheter med ett moln. Dataflödet du observerar kommer att vara nära det som beskrivs i [prestandareferensavsnittet](#performance-reference).

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: Enskilda filstorlek är större än dussintals MBs och den totala volymen är stor.

**Analys och prestandasvarvning:** Ökad **parallelCopies** resulterar inte i bättre kopieringsprestanda på grund av resursbegränsningarna för en DMU med ett moln. I stället bör du ange fler DU:er i molnet för att få mer resurser för att utföra dataförflyttningen. Ange inte ett värde för egenskapen **parallelCopies.** Data Factory hanterar parallellismen åt dig. I det här fallet, om du ställer in **cloudDataMovementUnits** till 4, inträffar ett dataflöde på ungefär fyra gånger.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referens
Här är prestandaövervakning och justeringsreferenser för några av de datalager som stöds:

* Azure Blob storage: [Skalbarhet och prestandamål för Blob-lagring](../../storage/blobs/scalability-targets.md) och [prestanda- och skalbarhetschecklista för Blob-lagring](../../storage/blobs/storage-performance-checklist.md).
* Azure Table storage: [Skalbarhet och prestandamål för tabelllagring](../../storage/tables/scalability-targets.md) och [prestanda och skalbarhet checklista för tabelllagring](../../storage/tables/storage-performance-checklist.md).
* Azure SQL Database: Du kan [övervaka prestanda](../../sql-database/sql-database-single-database-monitor.md) och kontrollera dataenheten (DTU)
* Azure SQL Data Warehouse: Dess kapacitet mäts i datalagerenheter (DWUs); se [Hantera beräkningskraft i Azure SQL Data Warehouse (Översikt)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [Prestandanivåer i Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* Lokal SQL Server: [Övervaka och finjustera prestanda](https://msdn.microsoft.com/library/ms189081.aspx)
* Lokal filserver: [Prestandajustering för filservrar](https://msdn.microsoft.com/library/dn567661.aspx)
