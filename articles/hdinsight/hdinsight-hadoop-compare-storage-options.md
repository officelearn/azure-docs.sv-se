---
title: Jämför lagringsalternativ för användning med Azure HDInsight-kluster
description: Översikt över lagringstyper och hur de fungerar med Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 7f113587dfabd66461a9bcfbde18a0178c6608f0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733553"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Jämför lagringsalternativ för användning med Azure HDInsight-kluster

Azure HDInsight-användare kan välja mellan några olika lagringsalternativ när du skapar HDInsight-kluster:

* Azure Data Lake Storage Gen2
* Azure Storage
* Azure Data Lake Storage Gen1

Den här artikeln innehåller en översikt över dessa olika lagringstyper och deras unika funktioner.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Använd Azure Data Lake Storage Gen2 med Apache Hadoop i Azure HDInsight

Mer information om Azure Data Lake Storage Gen2 finns [introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Azure Data Lake Storage Gen2 tar kärnfunktioner från Azure Data Lake Storage Gen1 som en Hadoop-kompatibel filsystem, Azure Active Directory och POSIX-baserad åtkomst åtkomstkontrolllistor (ACL) och integrerar dem i Azure Blob Storage. Den här kombinationen kan du dra nytta av Azure Data Lake Storage Gen1 prestanda när du även använder lagringsnivåer Blob-lagring och hantering av livscykeln.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Huvudfunktionerna i Azure Data Lake Storage Gen2

* Hadoop-kompatibel åtkomst: Azure Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en Hadoop Distributed File System (HDFS). Azure Blob filsystem (ABFS)-drivrutinen är tillgängliga i alla Apache Hadoop-miljöer, inklusive Azure HDInsight och Azure Databricks för att komma åt data som lagras i Data Lake Storage Gen2.

* En supermängd POSIX-behörigheter: Säkerhetsmodellen för Data Lake Gen2 stöder ACL och POSIX behörigheter tillsammans med vissa extra kornighet som är specifika för Data Lake Storage Gen2. Inställningarna kan konfigureras via Administrationsverktyg eller ramverk som Apache Hive och Apache Spark.

* Kostnadseffektivt: Data Lake Storage Gen2 erbjuder låg kostnad lagringskapacitet och transaktioner. Funktioner, till exempel Azure Blob storage livscykel att minska kostnaderna genom att justera faktureringstaxor när data flyttas via dess livscykel.

* Fungerar med Blob storage-verktyg, ramverk och appar: Data Lake Storage Gen2 fortsätter att fungera med en mängd olika verktyg, ramverk och program som finns i dag för Blob storage.

* Optimerad drivrutinen: ABFS-drivrutinen har optimerats specifikt för analys av stordata. De motsvarande REST-API: erna exponeras via dfs-slutpunkt, dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Nyheter om Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker åtkomst

Azure HDInsight använder hanterade identiteter för att skydda klustret åtkomst till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en automatiskt hanterade autentiseringsuppgifter. De här autentiseringsuppgifterna kan användas för att autentisera till en tjänst som stöder AD-autentisering. Med hjälp av hanterade identiteter måste inte du lagra autentiseringsuppgifter i kod eller konfigurationsfiler.

Mer information finns i [vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Azure Blob-filsystem (ABFS)-drivrutin

Apache Hadoop-program förväntar sig internt att läsa och skriva data från lokalt diskutrymme. En drivrutin för Hadoop-filsystem som ABFS gör det möjligt för Hadoop-program för molnlagring genom emulerar vanliga Hadoop-filsystemsåtgärder. Drivrutinen konverterar de kommandon som togs emot från programmet i åtgärder som förstår den faktiska molnplattformen för lagring.

Drivrutinen för Hadoop-filsystem skulle tidigare konvertera alla filsystemsåtgärder till Azure Storage REST API-anrop på klientsidan och anropa REST-API. Den här klienten konverteringen, men resulterade i flera REST-API-anrop för en enda filsystemsåtgärd som att byta namn på en fil. ABFS har flyttats några av logiken för Hadoop-filsystem från klientsidan från serversidan och Azure Data Lake Storage Gen2 API nu körs parallellt med Blob-API. Den här migreringen förbättrar prestanda eftersom nu vanliga åtgärder för Hadoop-filsystem kan köras med en REST API-anrop.

Mer information finns i [The Azure Blob-filsystem-drivrutinen (ABFS): En dedikerad Azure Storage-drivrutin för Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Azure Data Lake Storage Gen 2 URI-schema

Azure Data Lake Storage Gen2 använder en ny URI-schema för att komma åt filer i Azure storage från HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst (`abfss://` prefix) och okrypterad åtkomst (`abfs://` prefix). Vi rekommenderar att du använder `abfss` möjligt, även om åtkomst till data som finns i samma region i Azure.

`<FILE_SYSTEM_NAME>` Anger sökvägen till filsystemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Den `<PATH>` är det fil eller katalog HDFS-sökvägsnamnet.

Om värden för `<FILE_SYSTEM_NAME>` och `<ACCOUNT_NAME>` inte anges används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel den `hadoop-mapreduce-examples.jar` -fil som medföljer HDInsight-kluster kan du referera till något av följande sökvägar:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Filnamnet är `hadoop-examples.jar` i HDInsight version 2.1 och 1.6 kluster. När du arbetar med filer utanför HDInsight kan de flesta verktyg inte kan identifiera ABFS formatera och förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

Mer information finns i [Använd Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Använda Azure-lagring

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett gränssnitt för Hadoop-distribuerat filsystem (HDFS) kan alla komponenter i HDInsight tillämpas direkt på strukturerade eller ostrukturerade data som har lagrats som blobar.

> [!WARNING]  
> Det finns flera tillgängliga alternativ när du skapar ett Azure Storage-konto. I följande tabell finns information om vilka alternativ som stöds med HDInsight:

| Storage Account-typ | Tjänster som stöds | Stöds prestandanivåer | Stöds åtkomstnivåerna |
|----------------------|--------------------|-----------------------------|------------------------|
| Generell användning V2   | Blob               | Standard                    | Frekvent, lågfrekvent, Arkiv *    |
| General-Purpose V1   | Blob               | Standard                    | Gäller inte                    |
| Blob Storage         | Blob               | Standard                    | Frekvent, lågfrekvent, Arkiv *    |

Vi rekommenderar inte att du använder standardbehållaren för att lagra affärsdata. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Standardbehållaren innehåller program- och loggar. Se till att hämta loggarna innan du tar bort containern.

Med blobbehållare som standardfilsystemet för flera kluster stöds inte.
 
 > [!NOTE]  
 > Arkiv som åtkomstnivå är en offline-nivå som har en flera timme hämtning svarstid och rekommenderas inte för användning med HDInsight. Mer information finns i [arkivåtkomstnivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight
Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight Storage-arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Dessutom ger HDInsight möjlighet att komma åt data som är lagrade i Azure Storage. Syntax:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Här är några saker att tänka på när du använder Azure Storage-konton med HDInsight-kluster.

* **Behållare på lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när du skapar, har du fullständig åtkomst till blobarna i dessa behållare.

* **Offentliga behållare eller offentliga blobar i lagringskonton som inte är anslutna till ett kluster:** Du har läsbehörighet till blobarna i behållarna.
  
  > [!NOTE]  
  > Offentliga containrar låter dig hämta en lista över alla blobar som är tillgängliga i containern samt hämta metadata för containern. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [hantera åtkomst till behållare och blobbar](../storage/blobs/storage-manage-access-to-resources.md).

* **Privata behållare på lagringskonton som inte är anslutna till ett kluster:** Du kan inte komma åt blobarna i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben. Detta beskrivs senare i artikeln.

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i filen core-site.xml. Du kan ändra den här inställningen med hjälp av [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop-strömmande och Apache Pig, kan innehålla en beskrivning av lagringskonton och metadata med dem. (För närvarande fungerar för Pig med lagringskonton, men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. I blobcontainrar förvaras data som nyckel/värde-par och det finns ingen kataloghierarki. Däremot kan snedstreck (/) användas i nyckelnamnet så att det visas som om det vore en fil som lagrats i en katalogstruktur. Nyckeln för en blob kan till exempel vara `input/log1.txt`. Ingen faktisk `input` katalog finns, men på grund av förekomsten av ett snedstreck i nyckelnamnet har utseendet på en sökväg till filen.

### <a id="benefits"></a>Fördelar med Azure Storage
Den medförande prestandakostnaden för att inte samplacera beräkningskluster och lagringsresurser minskar, på grund av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen, där höghastighetsnätverket gör att beräkningsnoderna effektivt kan komma åt data i Azure Storage.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Data återanvändning och delning av:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure storage kan nås via HDFS-API: erna eller via Blob Storage REST-API: er. Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.
* **Dataarkivering:** Lagra data i Azure storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att förlora användardata.
* **Kostnad för datalagring:** Långsiktigt att lagra data i DFS är dyrare än att lagra data i Azure storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure storage. Dessutom, eftersom data inte behöver läsas för generation av varje beräkningskluster sparar du även kostnader för datainläsning.
* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem, bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.
* **GEO-replikering:** Din Azure-lagring kan vara geo-replikerade. Även om detta ger dig geografisk återställning och dataredundans, innebär en växling till den geo-replikerade platsen en avsevärd försämring av prestandan och kan medföra ytterligare kostnader. Vår rekommendation är därför att tänka efter när du väljer geo-replikering och bara göra det om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till exempel `ls`, `copyFromLocal` och `mkdir`) fortfarande fungerar som förväntat. Endast de kommandon som är specifika för den interna HDFS-implementeringen (som kallas DFS), till exempel `fschk` och `dfsadmin`, visa olika beteenden i Azure storage.

## <a name="use-azure-data-lake-storage-gen1"></a>Använda Azure Data Lake Storage Gen1

### <a name="overview"></a>Översikt

Läs mer på Azure Data Lake Storage Gen1 [översikt av Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Med Azure Data Lake kan du samla in data av olika storlekar, former, typer och inmatningshastighet på en enda plats för drifts- och undersökande analyser.

Data Lake Storage Gen1 kan nås från Hadoop (tillgängligt med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST API: er. Den är utformad för att aktivera analyser på lagrade data och är inställd för prestanda för dataanalyser. Och den innehåller alla funktioner i företagsklass direkt ur lådan – säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet – grundläggande för verkliga företagsfall.

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "Lagringsarkitekturen i HDInsight")

Några av de viktigaste funktionerna i Data Lake Storage Gen1 är följande.

#### <a name="built-for-hadoop"></a>Byggt för Hadoop

Data Lake Storage Gen1 är en Apache Hadoop-filsystem som kompatibelt med Hadoop Distributed File System (HDFS) och fungerar med Hadoop-ekosystemet.  Dina befintliga HDInsight-program eller tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 visar också ett WebHDFS-kompatibelt REST-gränssnitt för program

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med hjälp av analytiska Hadoop-ramar, till exempel MapReduce eller Hive. Microsoft Azure HDInsight-kluster kan etableras och konfigurerats för direkt åtkomst till data som lagras i Data Lake Storage Gen1.

#### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och lämpar sig för att lagra en mängd olika data för analys. Det medför inte några gränser för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera mellan kilobyte till petabyte i storlek vilket gör det till ett bra alternativ för lagring av alla typer av data. Data lagras varaktigt genom att göra flera kopior och det finns ingen gräns på vilken tidsperiod som data kan lagras i datasjön.

#### <a name="performance-tuned-for-big-data-analytics"></a>Prestandajusterad för analyser av stordata

Data Lake Storage Gen1 har utformats för att köra storskaliga analytiska system som kräver enormt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil i ett antal enskilda lagringsservrar. Detta förbättrar läsgenomströmning vid läsning av filen parallellt för att utföra dataanalyser.

#### <a name="enterprise-ready-highly-available-and-secure"></a>Klart för företag: Hög tillgänglighet och säkerhet

Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Dina datatillgångar lagras varaktigt genom att göra redundanta kopior som skyddar mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av deras befintliga dataplattform.

Data Lake Storage Gen1 innehåller också säkerhet i företagsklass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

#### <a name="all-data"></a>Alla data

Data Lake Storage Gen1 kan lagra data i ursprungligt format, så som de är, utan några tidigare transformationer. Data Lake Storage Gen1 kräver inte något schema ska definieras innan data läses lämnar det upp till det enskilda analytiska ramverket att tolka data och definiera ett schema vid analysens tidpunkt. Att kunna lagra filer av godtycklig storlek och format gör det möjligt för Data Lake Storage Gen1 hanteringen av strukturerade, halvstrukturerade och Ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du använder lagrade data med hjälp av SDK: er, Azure-portalen och Azure Powershell. Så länge du placerar dina data i lagret med hjälp av dessa gränssnitt och använder lämpliga containrar kan du lagra alla typer av data. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på vilken typ av data som lagras.

### <a name="DataLakeStoreSecurity"></a>Skydda data i Data Lake Storage Gen1
Data Lake Storage Gen1 använder Azure Active Directory för autentisering och åtkomst åtkomstkontrolllistor (ACL) för att hantera åtkomst till dina data.

| Funktion | Beskrivning |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integreras med Azure Active Directory (AAD) för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. Med integrationen drar Data Lake Storage Gen1 fördelar från alla AAD-funktioner, inklusive multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, övervakning av programanvändning, säkerhetsövervakning och avisering, osv. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering i REST-gränssnittet. Se [Data Lake Storage Gen1 autentisering](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomstkontroll genom att stödja behörigheter av POSIX-typ exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Läs mer om hur åtkomstkontrollposter fungerar i kontexten för Data Lake Storage Gen1 [åtkomstkontroll i Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller också kryptering för data som lagras i kontot. Du kan ange krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att ha krypterade data eller välja ingen kryptering. Mer information finns i [kryptering i Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Anvisningar för hur du skapar en krypteringsrelaterad konfiguration finns i [Kom igång med Azure Data Lake Storage Gen1 med Azure portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Om du vill veta mer om hur du skyddar data i Data Lake Storage Gen1? Följ länkarna nedan.

* Anvisningar om hur du skyddar data i Data Lake Storage Gen1 finns i [skydda data i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

### <a name="applications-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1
Data Lake Storage Gen1 är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet. Det är även snyggt integrerat med andra Azure-tjänster.  Följ länkarna nedan för att lära dig mer om hur Data Lake Storage Gen1 kan användas både med öppen källkod-komponenter samt andra Azure-tjänster.

* Se [program och tjänster som är kompatibla med Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) en lista över program med öppen källkod samverkande med Data Lake Storage Gen1.
* Se [integrera med andra Azure-tjänster](../data-lake-store/data-lake-store-integrate-with-other-services.md) att förstå hur Data Lake Storage Gen1 kan användas med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
* Se [scenarier för att använda Data Lake Storage Gen1](../data-lake-store/data-lake-store-data-scenarios.md) att lära dig hur du använder Data Lake Storage Gen1 i scenarier som matar in data, bearbetning av data, hämta data och visualisera data.

### <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Vad är Data Lake Storage Gen1 filsystem (adl: / /)?
Data Lake Storage Gen1 kan nås via det nya filsystemet AzureDataLakeFilesystem (adl: / /), i Hadoop-miljöer (tillgängligt med HDInsight-kluster). Program och tjänster som använder adl:// kan dra nytta av ytterligare prestandaoptimering som inte är tillgänglig i WebHDFS. Därför kan Data Lake Storage Gen1 ger dig möjlighet att utnyttja antingen bästa prestanda med det rekommenderade alternativet för att använda adl: / / eller hantera befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight utnyttjar till fullo AzureDataLakeFilesystem för att ge bästa möjliga prestanda i Data Lake Storage Gen1.

Du kan komma åt dina data i Data Lake Storage Gen1 med `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. Mer information om hur du kommer åt data i Data Lake Storage Gen1 finns i [visa egenskaper för lagrade data](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)