---
title: Migrera data från ett lokalt Hadoop-kluster till Azure Storage
description: Lär dig hur du använder Azure Data Factory för att migrera data från lokalt Hadoop-kluster till Azure Storage.
services: data-factory
ms.author: yexu
author: dearandyxu
ms.reviewer: ''
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 8/30/2019
ms.openlocfilehash: 63b657e77172282225a9bc890b2f185b0f4d42a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417138"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-hadoop-cluster-to-azure-storage"></a>Använda Azure Data Factory för att migrera data från ett lokalt Hadoop-kluster till Azure Storage 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory tillhandahåller en högpresterande, robust och kostnadseffektiv mekanism för att migrera data i stor skala från lokala HDFS till Azure Blob-lagring eller Azure Data Lake Storage Gen2. 

Data Factory erbjuder två grundläggande metoder för att migrera data från lokala HDFS till Azure. Du kan välja metod baserat på ditt scenario. 

- **Data Factory DistCp-läge** (rekommenderas): I Data Factory kan du använda [DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) (distribuerad kopia) för att kopiera filer som de är till Azure Blob-lagring (inklusive [stegvis kopia)](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy)eller Azure Data Lake Store Gen2. Använd Data Factory integrerat med DistCp för att dra nytta av ett befintligt kraftfullt kluster för att uppnå bästa kopieringsflöde. Du får också nytta av flexibel schemaläggning och en enhetlig övervakningsupplevelse från Data Factory. Beroende på din datafabrikskonfiguration skapar kopieringsaktiviteten automatiskt ett DistCp-kommando, skickar data till Hadoop-klustret och övervakar sedan kopieringsstatusen. Vi rekommenderar Data Factory DistCp-läge för att migrera data från ett lokalt Hadoop-kluster till Azure.
- **Data Factory native integration runtime mode**: DistCp är inte ett alternativ i alla scenarier. I en Azure Virtual Networks-miljö stöder verktyget DistCp till exempel inte Azure ExpressRoute privat peering med en virtuell Azure Storage-nätverksslutpunkt. I vissa fall vill du dessutom inte använda ditt befintliga Hadoop-kluster som en motor för att migrera data så att du inte placerar tunga belastningar i klustret, vilket kan påverka prestanda för befintliga ETL-jobb. I stället kan du använda den inbyggda funktionen för datafabrikens integrationskörning som motorn som kopierar data från lokala HDFS till Azure.

Den här artikeln innehåller följande information om båda metoderna:
> [!div class="checklist"]
> * Prestanda 
> * Kopiera återhämtningsförmåga
> * Nätverkssäkerhet
> * Lösningsarkitektur på hög nivå 
> * Bästa praxis för genomförande  

## <a name="performance"></a>Prestanda

I datafabrikens distCp-läge är dataflödet detsamma som om du använder Verktyget DistCp oberoende av detta. Data Factory DistCp-läget maximerar kapaciteten för ditt befintliga Hadoop-kluster. Du kan använda DistCp för stor inter-kluster eller intra-cluster kopiering. 

DistCp använder MapReduce för att påverka distribution, felhantering och återställning och rapportering. Den utökar en lista över filer och kataloger till indata för uppgiftsmappning. Varje uppgift kopierar en filpartition som anges i källlistan. Du kan använda Data Factory integrerat med DistCp för att skapa pipelines för att fullt ut utnyttja nätverksbandbredden, lagrings-IOPS och bandbredden för att maximera datarörflödet för din miljö.  

Data Factory native integration runtime mode tillåter också parallellism på olika nivåer. Du kan använda parallellism för att fullt ut utnyttja nätverksbandbredden, lagrings-IOPS och bandbredden för att maximera datarörflödet:

- En enda kopieringsaktivitet kan dra nytta av skalbara beräkningsresurser. Med en självvärderad integrationskörning kan du manuellt skala upp datorn eller skala ut till flera datorer[(upp till fyra noder).](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability) En enda kopieringsaktivitet partitionerar sin filuppsättning över alla noder. 
- En enda kopieringsaktivitet läser från och skriver till datalagret med hjälp av flera trådar. 
- Data Factory kontrollflöde kan starta flera kopieringsaktiviteter parallellt. Du kan till exempel använda en [För varje slinga](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

Mer information finns i [resultatguiden för kopieringsaktivitet](https://docs.microsoft.com/azure/data-factory/copy-activity-performance).

## <a name="resilience"></a>Motståndskraft

I Data Factory DistCp-läge kan du använda olika DistCp-kommandoradsparametrar (till exempel `-i`ignorera fel eller `-update`skriva data när källfilen och målfilen skiljer sig åt i storlek) för olika nivåer av återhämtning.

I läget För inbyggd integrering av datafabrik har Data Factory en inbyggd mekanism för återförsök i ett enda kopieringsläge. Den kan hantera en viss nivå av tillfälliga fel i datalagret eller i det underliggande nätverket. 

När du gör binär kopiering från lokala HDFS till Blob-lagring och från lokala HDFS till Data Lake Store Gen2 utför Data Factory automatiskt kontrollpunkter i stor utsträckning. Om en kopieringsaktivitet körs misslyckas eller time out, på ett efterföljande nytt försök (se till att antalet försök på nytt är > 1), återupptas kopian från den senaste felpunkten i stället för att starta från början.

## <a name="network-security"></a>Nätverkssäkerhet 

Som standard överför Data Factory data från lokala HDFS till Blob-lagring eller Azure Data Lake Storage Gen2 med hjälp av en krypterad anslutning via HTTPS-protokoll. HTTPS tillhandahåller datakryptering under överföring och förhindrar avlyssning och man-in-the-middle-attacker. 

Alternativt, om du inte vill att data ska överföras via det offentliga internet, för högre säkerhet, kan du överföra data via en privat peering-länk via ExpressRoute. 

## <a name="solution-architecture"></a>Lösningsarkitektur

Den här bilden visar migrerande data över det offentliga internet:

![Diagram som visar lösningsarkitekturen för att migrera data över ett offentligt nätverk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-public-network.png)

- I den här arkitekturen överförs data säkert med hjälp av HTTPS via det offentliga internet. 
- Vi rekommenderar att du använder datafabrikens distCp-läge i en offentlig nätverksmiljö. Du kan dra nytta av ett kraftfullt befintligt kluster för att uppnå bästa kopieringsdataflöde. Du får också nytta av flexibel schemaläggning och enhetlig övervakningserfarenhet från Data Factory.
- För den här arkitekturen måste du installera datafabrikens självvärderade integrationskörningstid på en Windows-dator bakom en företagsbrandvägg för att skicka kommandot DistCp till Ditt Hadoop-kluster och övervaka kopieringsstatusen. Eftersom maskinen inte är motorn som kommer att flytta data (endast för kontrolländamål), påverkar maskinens kapacitet inte dataförflyttningens dataflöde.
- Befintliga parametrar från kommandot DistCp stöds.

Den här bilden visar migrerande data via en privat länk: 

![Diagram som visar lösningsarkitekturen för att migrera data över ett privat nätverk](media/data-migration-guidance-hdfs-to-azure-storage/solution-architecture-private-network.png)

- I den här arkitekturen migreras data via en privat peering-länk via Azure ExpressRoute. Data går aldrig över det offentliga internet.
- DistCp-verktyget stöder inte ExpressRoute-privat peering med en Azure Storage-slutpunkt för virtuellt nätverk. Vi rekommenderar att du använder Data Factorys inbyggda kapacitet via integrationskörningen för att migrera data.
- För den här arkitekturen måste du installera datafabrikens självvärderade integrationskörning på en Virtuell Windows-dator i det virtuella Azure-nätverket. Du kan skala upp den virtuella datorn manuellt eller skala ut till flera virtuella datorer för att helt utnyttja nätverks- och lagrings-IOPS eller bandbredden.
- Den rekommenderade konfigurationen som ska börjas med för varje Virtuell Azure -dator (med datafabrikens självvärderade integreringskörning installerad) är Standard_D32s_v3 med ett 32 vCPU och 128 GB minne. Du kan övervaka cpu- och minnesanvändningen för den virtuella datorn under datamigrering för att se om du behöver skala upp den virtuella datorn för bättre prestanda eller för att skala ned den virtuella datorn för att minska kostnaderna.
- Du kan också skala ut genom att associera upp till fyra VM-noder med en enda självvärderad integrationskörning. Ett enda kopieringsjobb som körs mot en självvärderad integrationskörning partitionerar automatiskt filuppsättningen och använder alla VM-noder för att kopiera filerna parallellt. För hög tillgänglighet rekommenderar vi att du börjar med två VM-noder för att undvika ett scenario med en punkt i felet under datamigrering.
- När du använder den här arkitekturen är inledande migrering av ögonblicksbilddata och deltadatamigrering tillgängliga för dig.

## <a name="implementation-best-practices"></a>Bästa praxis för genomförande

Vi rekommenderar att du följer dessa metodtips när du implementerar datamigrering.

### <a name="authentication-and-credential-management"></a>Autentisering och hantering av autentiseringsuppgifter 

- Om du vill autentisera till HDFS kan du använda [antingen Windows (Kerberos) eller Anonymous](https://docs.microsoft.com/azure/data-factory/connector-hdfs#linked-service-properties). 
- Flera autentiseringstyper stöds för anslutning till Azure Blob-lagring.  Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity). Hanterad identitet som bygger på en automatiskt hanterad Data Factory-identitet i Azure Active Directory (Azure AD) kan du konfigurera pipelines utan att ange autentiseringsuppgifter i den länkade tjänstdefinitionen. Du kan också autentisera till Blob-lagring med hjälp av ett [tjänsthuvudnamn,](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication)en [signatur för delad åtkomst](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication)eller en [lagringskontonyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- Flera autentiseringstyper stöds också för anslutning till Data Lake Storage Gen2.  Vi rekommenderar starkt att du använder [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), men du kan också använda ett [tjänsthuvudnamn](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) eller en [lagringskontonyckel](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 
- När du inte använder hanterade identiteter för Azure-resurser rekommenderar vi starkt [att du lagrar autentiseringsuppgifterna i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) för att göra det enklare att centralt hantera och rotera nycklar utan att ändra Data Factory-länkade tjänster. Detta är också en [bästa praxis för CI / CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Inledande migrering av ögonblicksbilddata 

I datafabrikens distCp-läge kan du skapa en kopieringsaktivitet för att skicka kommandot DistCp och använda olika parametrar för att styra det ursprungliga datamigreringsbeteendet. 

I datafabrikens inbyggda integrationskörningsläge rekommenderar vi datapartition, särskilt när du migrerar mer än 10 TB data. Om du vill partitionera data använder du mappnamnen på HDFS. Sedan kan varje datafabrikskopieringsjobb kopiera en mapppartition i taget. Du kan köra flera datafabrikskopieringsjobb samtidigt för bättre dataflöde.

Om något av kopieringsjobben misslyckas på grund av tillfälliga problem i nätverks- eller datalagringsarkivet kan du köra det misslyckade kopieringsjobbet igen för att läsa in den specifika partitionen från HDFS. Andra kopieringsjobb som läser in andra partitioner påverkas inte.

### <a name="delta-data-migration"></a>Migrering av Delta-data 

I datafabrikens distCp-läge kan du använda kommandoradsparametern `-update`DistCp , skriva data när källfilen och målfilen skiljer sig åt i storlek, för deltadatamigrering.

I inbyggt integrationsläge för Data Factory är det mest högpresterande sättet att identifiera nya eller ändrade filer från HDFS genom att använda en tidspartitionerad namngivningskonvention. När dina data i HDFS har tidspartitionerats med tidssegmentinformation i filen eller mappnamnet (till exempel */yyyy/mm/dd/file.csv)* kan pipelinen enkelt identifiera vilka filer och mappar som ska kopieras stegvis.

Alternativt, om dina data i HDFS inte är tidsbe partitionerade, kan Data Factory identifiera nya eller ändrade filer med hjälp av deras **LastModifiedDate-värde.** Data Factory söker igenom alla filer från HDFS och kopierar endast nya och uppdaterade filer som har en senast ändrad tidsstämpel som är större än ett angivet värde. 

Om du har ett stort antal filer i HDFS kan den första filskanningen ta lång tid, oavsett hur många filer som matchar filtervillkoret. I det här fallet rekommenderar vi att du först partitionerar data med samma partition som du använde för den första ögonblicksbildmigrering. Sedan kan filsökning ske parallellt.

### <a name="estimate-price"></a>Uppskatta pris 

Tänk på följande pipeline för att migrera data från HDFS till Azure Blob-lagring: 

![Diagram som visar prispipelinen](media/data-migration-guidance-hdfs-to-azure-storage/pricing-pipeline.png)

Låt oss anta följande information: 

- Total datavolym är 1 PB.
- Du migrerar data med hjälp av det inbyggda integrationskörningsläget för inbyggd integrering i Data Factory.
- 1 PB är uppdelad i 1000 partitioner och varje kopia flyttar en partition.
- Varje kopieringsaktivitet är konfigurerad med en självvärd integreringskörning som är associerad med fyra datorer och som uppnår 500 MBps-dataflöde.
- ForEach samtidighet är inställd på **4** och aggregerat dataflöde är 2 GBps.
- Totalt tar det 146 timmar att slutföra migreringen.

Här är det uppskattade priset baserat på våra antaganden: 

![Tabell som visar prisberäkningar](media/data-migration-guidance-hdfs-to-azure-storage/pricing-table.png)

> [!NOTE]
> Detta är ett hypotetiskt prisexempel. Din faktiska prissättning beror på det faktiska dataflödet i din miljö.
> Priset för en Virtuell Azure Windows -dator (med självvärd för integrationskörning installerad) ingår inte.

### <a name="additional-references"></a>Ytterligare referenser

- [HDFS-kontakt](https://docs.microsoft.com/azure/data-factory/connector-hdfs)
- [Azure Blob-lagringsappen](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Azure Data Lake Storage Gen2-anslutning](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Kopiera inställningsguide för aktivitetsprestanda](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Skapa och konfigurera lokalt installerad integrationskörning](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Självvärd för integrationskörning med hög tillgänglighet och skalbarhet](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Säkerhetsöverväganden vid dataflytt](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Spara autentiseringsuppgifter i Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Kopiera en fil stegvis baserat på ett tidspartierat filnamn](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Kopiera nya och ändrade filer baserat på LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Prissida för Data Factory](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Nästa steg

- [Kopiera filer från flera behållare med hjälp av Azure Data Factory](solution-template-copy-files-multiple-containers.md)