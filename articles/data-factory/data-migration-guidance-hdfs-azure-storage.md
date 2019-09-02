---
title: Använd Azure Data Factory för att migrera data från ett lokalt Hadoop-kluster till Azure Storage | Microsoft Docs
description: Använd Azure Data Factory för att migrera data från ett lokalt Hadoop-kluster till Azure Storage.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/30/2019
ms.openlocfilehash: 1b26b22fa9cdf9f6671702ceb9640aa39a6ecf17
ms.sourcegitcommit: 8fea78b4521921af36e240c8a92f16159294e10a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70211611"
---
# <a name="use-azure-data-factory-to-migrate-data-from-on-premises-hadoop-cluster-to-azure-storage"></a>Använd Azure Data Factory för att migrera data från ett lokalt Hadoop-kluster till Azure Storage 

Azure Data Factory ger en genomförd, robust och kostnads effektiv mekanism för att migrera data i stor skala från den lokala HDFS till Azure Blob Storage eller Azure Data Lake Storage Gen2. Azure Data Factory innehåller i princip två metoder för att migrera data från en lokal HDFS till Azure. Du kan välja var och en av dem baserat på ditt scenario. 

- ADF DistCp-läge. ADF-stöd med [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) för att kopiera filer som-är i Azure Blob (inklusive mellanlagrad [kopia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)) eller Azure Data Lake Store, och i så fall kan det helt utnyttja klustrets kraft i stället för att köra på den automatiska integrerings körningen i ADF (IR). Det ger bättre kopiering av data genom att särskilt när klustret är mycket kraftfullt. Baserat på din konfiguration i Azure Data Factory konstruerar kopierings aktiviteten automatiskt ett DistCp-kommando, skickar till ditt Hadoop-kluster och övervakar kopierings statusen. Genom att använda ADF integrerat med DistCp kan kunden inte bara utnyttja ditt befintliga kraftfulla kluster för att uppnå bästa möjliga kopiering, men ger även fördelarna med flexibel schemaläggning och enhetlig övervaknings upplevelse från ADF. Som standard är ADF DistCp-läget det rekommenderade sättet att migrera data från ett lokalt Hadoop-kluster till Azure.
- ADF-ursprungligt IR-läge. I vissa fall fungerar DistCp inte för dina fall (till exempel i VNET-miljö stöder DistCp-verktyget inte ExpressRoute Private peering + Azure Storage VNet-slutpunkt). Förutom att du inte vill använda ditt befintliga Hadoop-kluster som motor för att migrera data eftersom den kommer att ta tung belastning i klustret, vilket kan påverka prestanda för befintliga ETL-jobb. I så fall kan du använda inbyggd funktioner i ADF, där ADF (ADF integration Runtime) kan vara motorn för att kopiera data från lokal HDFS till Azure.

Den här artikeln innehåller följande information om båda metoderna för data tekniker och utvecklare:
> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtning
> * Nätverkssäkerhet
> * Hög nivå lösnings arkitektur 
> * Metod tips för implementering  

## <a name="performance"></a>Prestanda

I ADF DistCp läge är genomflödet detsamma som att använda DistCp-verktyget oberoende av varandra, vilket utnyttjar kapaciteten hos ditt befintliga Hadoop-kluster. DistCp (distribuerad kopia) är ett verktyg som används för kopiering av stora kluster mellan grupper. Den använder MapReduce för att påverka distribution, fel hantering och återställning samt rapportering. Den expanderar en lista över filer och kataloger i inmatade mappar, som kopierar en partition av de filer som anges i listan källa. Genom att använda ADF integrerat med DistCp kan du bygga pipelines för att fullt ut utnyttja din nätverks bandbredd samt Storage IOPS och bandbredd för att maximera data flödet för data förflyttning i din miljö.  

I ADF inbyggd IR-läge, tillåter det också parallellitet på olika nivåer, som helt använder din nätverks bandbredd samt lagrings-IOPS och bandbredd för att maximera data flödet genom att skala upp den lokala IR-datorn manuellt eller skala ut till IR med egen värd.

- En enda kopierings aktivitet kan dra nytta av skalbara beräknings resurser. Med integration runtime med egen värd kan du manuellt skala upp datorn eller skala ut till flera datorer ([upp till 4 noder](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) och en enda kopierings aktivitet partitionerar dess fil uppsättning på alla noder. 
- En enskild kopierings aktivitet läser från och skriver till data lagret med hjälp av flera trådar. 
- ADF-kontroll flödet kan starta flera kopierings aktiviteter parallellt, till exempel använda [för varje slinga](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Du kan få mer information från [prestanda guiden för kopierings aktivitet](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)

## <a name="resilience"></a>Elasticitet

I ADF DistCp-läge kan du använda olika DistCp-kommando rads parametrar (t. ex.-i, ignorera felen;-uppdatera, skriva data när käll filen och målfilen skiljer sig i storlek) för att uppnå olika återhämtnings nivåer.

I ADF inbyggd IR-läge i en enskild kopierings aktivitet har ADF inbyggd återförsöks funktion så att den kan hantera en viss nivå av tillfälliga fel i data lager eller i det underliggande nätverket. När du gör en binär kopiering från en lokal HDFS till blob och från den lokala HDFS till ADLS Gen2, utför ADF automatiskt kontroll punkter i stor utsträckning. Om körningen av kopierings aktiviteten har misslyckats eller nått tids gränsen för ett senare försök (se till att antalet nya försök > 1) återupptas, fortsätter kopian från den senaste fel punkten i stället för att börja från början.

## <a name="network-security"></a>Nätverkssäkerhet 

Som standard överför ADF data från den lokala HDFS till Azure Blob Storage eller Azure Data Lake Storage Gen2 med hjälp av krypterad anslutning över HTTPS-protokoll.  HTTPS tillhandahåller data kryptering vid överföring och förhindrar avlyssning och man-in-the-middle-attacker. 

Alternativt, om du inte vill att data ska överföras över offentliga Internet, kan du uppnå högre säkerhet genom att överföra data via en privat peering-länk via Azure Express Route. Se lösnings arkitekturen nedan om hur detta kan uppnås.

## <a name="solution-architecture"></a>Lösningsarkitektur

Migrera data över offentliga Internet:

![lösning – arkitektur – offentligt – nätverk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- I den här arkitekturen överförs data säkert med HTTPS över offentligt Internet. 
- ADF DistCp-läge rekommenderas för användning i offentliga nätverks miljöer. Genom att göra det kan du inte bara utnyttja ditt befintliga kraftfulla kluster för att uppnå bästa möjliga kopiering, men du får också fördelen med flexibel schemaläggning och enhetlig övervaknings upplevelse från ADF.
- Du måste installera ADF-integration runtime med egen värd på en Windows-dator bakom företags brand väggen för att skicka DistCp-kommandot till ditt Hadoop-kluster och övervaka kopierings statusen. Eftersom den här datorn inte kommer att vara motorn för att flytta data (endast för kontroll syfte) påverkar datorns kapacitet inte data flödet.
- De befintliga parametrarna från DistCp-kommandot stöds.


Migrera data över privat länk: 

![lösning – arkitektur – privat nätverk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- I den här arkitekturen görs datamigrering via en privat peering-länk via Azure Express Route, så att data aldrig passerar över offentliga Internet.
- DistCp-verktyget stöder inte Express Route privat peering + Azure Storage VNet-slutpunkt, så du uppmanas att använda inbyggd kapacitet i ADF via integration runtime för att migrera data.
- Du måste installera ADF-integration runtime med egen värd på en virtuell Windows-dator i ditt virtuella Azure-nätverk för att uppnå den här arkitekturen. Du kan skala upp den virtuella datorn manuellt eller skala ut till flera virtuella datorer för att helt utnyttja nätverket och lagrings-IOPS/bandbredd.
- Den rekommenderade konfigurationen som ska börja med för varje virtuell Azure-dator (med automatisk värd integrerings körning i real tid) är Standard_D32s_v3 med 32-vCPU och 128 GB-minne. Du kan fortsätta att övervaka CPU-och minnes användning för den virtuella datorn under datamigreringen för att se om du behöver skala upp den virtuella datorn ytterligare för bättre prestanda eller skala ned den virtuella datorn för att spara pengar.
- Du kan också skala ut genom att associera upp till 4 VM-noder med en enda egen IR. Ett enda kopierings jobb som körs mot en egen värd-IR partitionerar automatiskt fil uppsättningen och utnyttjar alla VM-noder för att kopiera filerna parallellt. För hög tillgänglighet rekommenderar vi att du börjar med två noder för virtuella datorer för att undvika en enskild felpunkt under datamigreringen.
- Migreringen av både inledande ögonblicks data och delta data kan uppnås med den här arkitekturen.


## <a name="implementation-best-practices"></a>Metod tips för implementering 

### <a name="authentication-and-credential-management"></a>Autentisering och hantering av autentiseringsuppgifter 

- Om du vill autentisera till HDFS kan du [antingen använda Windows (Kerberos) eller anonym](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Flera typer av autentisering stöds för att ansluta till Azure Blob Storage.  Du rekommenderas att använda [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity) : byggt ovanpå en automatiskt hanterad ADF-identifiering i Azure AD, där du kan konfigurera pipelines utan att ange autentiseringsuppgifter i den länkade tjänst definitionen.  Alternativt kan du autentisera till Azure Blob Storage med [tjänstens huvud namn](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), [signatur för delad åtkomst](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)eller [lagrings konto nyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Flera autentiseringstyper stöds också för att ansluta till Azure Data Lake Storage Gen2.  Användning av [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity) är starkt rekommenderat, även om [tjänstens huvud namn](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) eller [lagrings konto nyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication) också kan användas. 
- Om du inte använder hanterade identiteter för Azure-resurser rekommenderar vi att du [sparar autentiseringsuppgifterna i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) för att göra det enklare att hantera och rotera nycklar centralt utan att ändra ADF-länkade tjänster.  Detta är också en av de [rekommenderade metoderna för CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Första migrering av ögonblicks bild data 

I ADF DistCp-läge kan du skapa en kopierings aktivitet för att skicka DistCp-kommandot med olika parametrar för att styra det inledande datamigrerings beteendet. 

I ADF inbyggd IR-läge rekommenderas datapartitionen särskilt när du migrerar mer än 10 TB data. För att partitionera data, använda mappnamnen på HDFS och sedan kan varje jobb i ADF-kopiering kopiera en partition i taget. Du kan köra flera jobb för ADF-kopiering samtidigt för bättre data flöde.
Om något av kopierings jobben Miss lyckas på grund av ett tillfälligt problem med nätverket eller data lagret kan du köra om det misslyckade kopierings jobbet för att läsa in den aktuella partitionen igen från HDFS. Alla andra kopierings jobb som läser in andra partitioner påverkas inte.

### <a name="delta-data-migration"></a>Migrering av delta data 

I ADF DistCp-läge kan du utnyttja DistCp kommando rads parametrar "-Update, skriva data när käll filen och målfilen skiljer sig i storlek" för att uppnå migreringen av delta data.

I ADF inbyggd IR-läge är det mest presterande sättet att identifiera nya eller ändrade filer från HDFS genom att använda tidspartitionerad namngivnings konvention – när dina data i HDFS har tidsenhet partitioner ATS med Time-slice-information i fil-eller mappnamnet (till exempel/YYYY/MM/DD/ File. csv) kan pipelinen enkelt identifiera vilka filer/mappar som ska kopieras stegvis.
Alternativt, om dina data i HDFS inte är tidspartitionerade, kan ADF identifiera nya eller ändrade filer med deras LastModifiedDate. Hur det fungerar är att ADF genomsöker alla filer från HDFS och bara kopierar den nya och uppdaterade filen vars senast ändrade tidstämpel är större än ett visst värde. Tänk på att om du har ett stort antal filer i HDFS kan den första fil genomsökningen ta lång tid, oavsett hur många filer som matchar filter villkoret. I det här fallet rekommenderar vi att du först partitionerar data, använder samma partition för första migreringen av ögonblicks bilder, så att fil genomsökningen kan ske parallellt.

### <a name="estimating-price"></a>Uppskattar pris 

Tänk på följande pipeline som skapats för att migrera data från HDFS till Azure Blob Storage: 

![priser – pipeline](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Låt oss anta följande: 

- Total data volym är 1 PB
- Migrera data med andra lösnings arkitektur (ADF Native IR-läge)
- 1 PB är uppdelad i 1000 partitioner och varje kopia flyttas en partition
- Varje kopierings aktivitet konfigureras med en egen värd-IR som är associerad med 4 datorer och ger 500 Mbit/s-genomflöde.
- Förväntad concurrency är 4 och sammanställt data flöde är 2 Gbit/s
- Totalt tar det 146 timmar att slutföra migreringen.


Här är det uppskattade priset baserat på ovanstående antaganden: 

![priser – tabell](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Detta är ett exempel på en hypotetisk prissättning.  Din faktiska prissättning beror på det faktiska data flödet i din miljö.
> Priset för virtuell Azure Windows-dator (med egen värd för integration Runtime installerat) ingår inte.

### <a name="additional-references"></a>Ytterligare referenser 
- [HDFS-koppling](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob Storage-anslutningsapp](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2 koppling](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Prestanda justerings guide för kopierings aktivitet](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Skapa och konfigurera egen värd Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Egen värd för integration runtime-tillgänglighet och skalbarhet](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Säkerhets överväganden vid data förflyttning](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Lagra autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopierings fil, stegvis baserat på tidspartitionerat fil namn](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiera nya och ändrade filer baserat på LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Sidan med priser för ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)