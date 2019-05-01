---
title: Jämför lagringsalternativ för användning med Azure HDInsight-kluster
description: Översikt över lagringstyper och hur de fungerar med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 2724451d44a793023f7b69196b186f68f6fc6a26
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720480"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Jämför lagringsalternativ för användning med Azure HDInsight-kluster

Du kan välja mellan några olika Azure storage-tjänster när du skapar HDInsight-kluster:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Den här artikeln innehåller en översikt över dessa lagringstyper och deras unika funktioner.

I följande tabell sammanfattas de Azure Storage-tjänster som stöds med olika versioner av HDInsight:

| Lagringstjänst | Kontotyp | Namespace-typ | Tjänster som stöds | Stöds prestandanivåer | Stöds åtkomstnivåerna | HDInsight-version | Klustertyp |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Generell användning V2 | Hierarkiska (filsystem) | Blob | Standard | Frekvent, lågfrekvent, Arkiv | 3.6 + | Alla |
|Azure Storage| Generell användning V2 | Object | Blob | Standard | Frekvent, lågfrekvent, Arkiv | 3.6 + | Alla |
|Azure Storage| General-Purpose V1 | Object | Blob | Standard | Gäller inte | Alla | Alla |
|Azure Storage| Blob Storage** | Object | Blockblob | Standard | Frekvent, lågfrekvent, Arkiv | Alla | Alla |
|Azure Data Lake Storage Gen1| Gäller inte | Hierarkiska (filsystem) | Gäller inte | Saknas | Gäller inte | Endast 3.6 | Alla utom HBase |

** Endast sekundär storage-konton kan vara av typen BlobStorage för HDInsight-kluster.

Mer information om Azure Storage-kontotyper finns [översikt över Azure storage-konto](../storage/common/storage-account-overview.md)

Läs mer på Azure Storage åtkomstnivåerna [Azure Blob storage: Premium (förhandsversion), lagringsnivåerna frekvent, lågfrekvent lagring och Arkivlagring](../storage/blobs/storage-blob-storage-tiers.md)

Du kan skapa ett kluster med olika kombinationer av tjänster för primär och en valfri sekundär lagring. I följande tabell sammanfattas storage klusterkonfigurationer som stöds för närvarande i HDInsight:

| HDInsight-version | Primär lagring | Sekundär lagring | Stöds |
|---|---|---|---|
| 3.6 & 4.0 | Generell användning V1, generell användning V2 | General Purpose V1 , General Purpose V2, BlobStorage(Block Blobs) | Ja |
| 3.6 & 4.0 | Generell användning V1, generell användning V2 | Data Lake Storage Gen2 | Nej |
| 3.6 & 4.0 | Generell användning V1, generell användning V2 | Data Lake Storage Gen1 | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2* | General Purpose V1 , General Purpose V2, BlobStorage(Block Blobs) | Ja |
| 3.6 & 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nej |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | General Purpose V1 , General Purpose V2, BlobStorage(Block Blobs) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nej |
| 4.0 | Data Lake Storage Gen1 | Alla | Nej |

* = Det kan ett eller flera Gen2 för Data Lake Storage-konton, så länge de är alla setup kan använda samma hanterad identitet för åtkomst till klustret.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Använd Azure Data Lake Storage Gen2 med Apache Hadoop i Azure HDInsight

Azure Data Lake Storage Gen2 tar core funktioner från Azure Data Lake Storage Gen1 och integrerar dem i Azure Blob storage. Dessa funktioner innefattar ett filsystem som är kompatibelt med Hadoop, Azure Active Directory (Azure AD) och POSIX-baserade åtkomstkontrollistor (ACL). Den här kombinationen kan du dra nytta av Azure Data Lake Storage Gen1 prestanda när du även använder lagringsnivåer och livscykeln datahantering för Blob storage.

Mer information om Azure Data Lake Storage Gen2 finns [introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Huvudfunktionerna i Azure Data Lake Storage Gen2

* **Åtkomst som är kompatibel med Hadoop:** Du kan hantera och komma åt data precis som med en Hadoop Distributed File System (HDFS) i Azure Data Lake Storage Gen2. Azure Blob File System (ABFS)-drivrutinen är tillgängliga i alla Apache Hadoop-miljöer, inklusive Azure HDInsight och Azure Databricks. Använd ABFS för att komma åt data som lagras i Data Lake Storage Gen2.

* **En supermängd POSIX-behörigheter:** Säkerhetsmodellen för Data Lake Gen2 stöder ACL och POSIX behörigheter tillsammans med vissa extra kornighet som är specifika för Data Lake Storage Gen2. Inställningarna kan konfigureras via Administrationsverktyg och ramverk som Apache Hive och Apache Spark.

* **Kostnadseffektivitet:** Data Lake Storage Gen2 erbjuder låg kostnad lagringskapacitet och transaktioner. Funktioner, till exempel livscykeln för Azure Blob storage att minska kostnaderna genom att justera faktureringstaxor när data flyttas under dess livscykel.

* **Kompatibilitet med Blob storage-verktyg, ramverk och appar:** Data Lake Storage Gen2 fortsätter att fungera med en mängd olika verktyg, ramverk och program för Blob storage.

* **Optimerad drivrutinen:** ABFS-drivrutinen har optimerats specifikt för analys av stordata. De motsvarande REST-API: erna exponeras via slutpunkt för distribuerat filsystem (DFS) system, dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Vad är nytt för Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker åtkomst

Azure HDInsight använder hanterade identiteter för att skydda klustret åtkomst till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en automatiskt hanterade autentiseringsuppgifter. De här autentiseringsuppgifterna kan användas för att autentisera till en tjänst som stöder Active Directory-autentisering. Med hjälp av hanterade identiteter måste inte du lagra autentiseringsuppgifter i kod eller konfigurationsfiler.

Mer information finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Azure Blob-Filssystemet-drivrutin

Apache Hadoop-program förväntar sig internt att läsa och skriva data från lokalt diskutrymme. En Hadoop drivrutin som ABFS gör det möjligt för Hadoop-program för molnlagring genom emulerar vanliga Hadoop-filsystemsåtgärder. Drivrutinen konverterar de kommandon som togs emot från programmet i åtgärder som förstår den faktiska molnplattformen för lagring.

Tidigare Hadoop-filsystemsdrivrutin konverteras alla filsystemsåtgärder till Azure Storage REST API-anrop på klientsidan och anropa REST-API. Den här klienten konverteringen, men resulterade i flera REST API-anrop för en enskild fil systemfunktioner som namnbyte av en fil. ABFS har flyttats några av Hadoop-filen system logiken från klienten till servern. Azure Data Lake Storage Gen2 API nu körs parallellt med Blob-API. Den här migreringen förbättrar prestanda eftersom nu vanliga Hadoop-filsystemsåtgärder kan köras med en REST API-anrop.

Mer information finns i [The Azure Blob-filsystem-drivrutinen (ABFS): En dedikerad Azure Storage-drivrutin för Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema för Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 använder en ny URI-schema för att komma åt filer i Azure Storage från HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst (`abfss://` prefix) och okrypterad åtkomst (`abfs://` prefix). Använd `abfss` om möjligt även vid åtkomst till data som finns i samma region i Azure.

`<FILE_SYSTEM_NAME>` Anger sökvägen till filsystemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>` identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

`<PATH>` är det fil eller katalog HDFS-sökvägsnamnet.

Om värden för `<FILE_SYSTEM_NAME>` och `<ACCOUNT_NAME>` inte anges används standardfilsystemet. Använda en relativ sökväg eller en absolut sökväg för filer i filsystemet. Till exempel den `hadoop-mapreduce-examples.jar` -fil som medföljer HDInsight-kluster kan du referera till något av följande sökvägar:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Filnamnet är `hadoop-examples.jar` i HDInsight version 2.1 och 1.6 kluster. När du arbetar med filer utanför HDInsight, hittar du att de flesta verktyg inte känner igen ABFS formatera men förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

Mer information finns i [Använd Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en robust allmänna lagringslösning som smidigt kan integreras med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett HDFS-gränssnitt kan en fullständig uppsättning komponenter i HDInsight tillämpas direkt på strukturerade eller Ostrukturerade data som lagrats som blobar.

Vi rekommenderar att du använder separata behållare för dina standardklusterlagringen och dina affärsdata för att isolera HDInsight-loggar och temporära filer från din egen affärsdata. Vi rekommenderar också tar bort standardbehållaren, som innehåller program- och systemloggar, efter varje användning för att minska kostnaden för lagring. Se till att hämta loggarna innan du tar bort containern.

Om du väljer att skydda ditt lagringskonto med den **brandväggar och virtuella nätverk** begränsningar för **valda nätverk**, måste du aktivera undantaget **Tillåt att betrodda Microsoft tjänster...**  så att HDInsight kan komma åt ditt storage-konto.

### <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram visar en abstrakt vy av HDInsight-arkitektur i Azure Storage:

![Diagram som visar hur Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och Ostrukturerade data i Blob storage](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "Lagringsarkitekturen i HDInsight")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Du kan också komma åt data i Azure Storage med HDInsight. Syntaxen är följande:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Överväg följande principer när du använder ett Azure Storage-konto med HDInsight-kluster:

* **Behållare på lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när du skapar, har du fullständig åtkomst till blobarna i dessa behållare.

* **Offentliga behållare eller offentliga blobar i lagringskonton som är *inte* ansluten till ett kluster:** Du har läsbehörighet till blobarna i behållarna.
  
  > [!NOTE]  
  > Offentliga behållare låter dig att hämta en lista över alla blobbar som är tillgängliga i behållaren och för att hämta metadata för behållaren. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../storage/blobs/storage-manage-access-to-resources.md).

* **Privata behållare på lagringskonton som är *inte* ansluten till ett kluster:** Du kan inte komma åt blobarna i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben. 

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Som standard använder HDInsight storage-konton som definieras i filen core-site.xml. Du kan ändra den här inställningen med hjälp av [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop-strömmande och Apache Pig, kan innehålla en beskrivning av lagringskonton och metadata med dem. (Detta gäller för närvarande för Pig med lagringskonton, men inte för metadata.) Mer information finns i [med ett HDInsight-kluster med alternativa lagringskonton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. BLOB-behållare förvaras data som nyckel/värde-par och har ingen Kataloghierarki. Nyckelnamnet kan däremot innehålla ett snedstreck (/) så att det visas som om en fil som lagrats i en katalogstruktur. Till exempel nyckeln för en blob kan vara `input/log1.txt`. Ingen faktisk `input` katalog finns, men på grund av ett snedstreck i nyckelnamnet nyckeln som ser ut som en sökväg till filen.

### <a id="benefits"></a>Fördelar med Azure Storage
Beräkningskluster och lagringsresurser som inte är samordnade ha krävt prestanda kostnader. Dessa kostnader begränsas av sättet på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-region. I den här regionen beräkningsnoderna effektivt åtkomst till data via höghastighetsnätverk i Azure Storage.

När du lagrar data i Azure Storage i stället för HDFS kan få du många fördelar:

* **Data återanvändning och delning av:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage, kan däremot nås via HDFS-API: er eller Blob storage REST API: er. På grund av detta till användas en större grupp program (inklusive andra HDInsight-kluster) och verktyg för att skapa och använda data.

* **Dataarkivering:** När data lagras i Azure Storage, kan de HDInsight-kluster som används för beräkning, raderas på ett säkert sätt utan att förlora användardata.

* **Kostnad för datalagring:** Långsiktigt att lagra data i DFS är dyrare än att lagra data i Azure Storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure Storage. Eftersom data inte har läsas in på nytt för varje kluster compute-generering, du som sparar också kostnader för inläsning av data.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem, bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala kan vara mer komplicerat än att använda de elastiska skalningsfunktionerna som du får automatiskt i Azure Storage.

* **GEO-replikering:** Azure Storage kan vara geo-replikerade. Även om geo-replikering ger dig geografisk återställning och dataredundans, en växling till den geo-replikerade platsen påverkar kraftigt prestanda och det kan innebära ytterligare kostnader. Så du väljer geo-replikering försiktighet och bara om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till exempel `ls`, `copyFromLocal`, och `mkdir`) fungerar som förväntat i Azure Storage. Endast de kommandon som är specifika för den interna HDFS-implementeringen (som kallas DFS), till exempel `fschk` och `dfsadmin`, visa olika beteenden i Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Översikt över Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagringsplats för analytiska arbetsbelastningar för stordata. Med Azure Data Lake kan du samla in data av alla storlekar, typer och inmatning inmatningshastighet på en plats för driftsanalyser och undersökande analyser.

Åtkomst till Data Lake Storage Gen1 från Hadoop (tillgängligt med HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST API: er. Data Lake Storage Gen1 har utformats för att aktivera analyser på lagrade data och är inställd för prestanda i scenarier för dataanalys. Direkt innehåller den de funktioner som är viktiga för verkliga företagsfall. Funktionerna omfattar säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet.

Mer information om Azure Data Lake Storage Gen1 hittar du i [översikt av Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

De viktigaste funktionerna i Data Lake Storage Gen1 inkluderar följande.

### <a name="compatibility-with-hadoop"></a>Kompatibilitet med Hadoop

Data Lake Storage Gen1 är en Apache Hadoop-filsystem som är kompatibelt med HDFS och fungerar med Hadoop-ekosystemet.  Dina befintliga HDInsight-program eller tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 visar också ett WebHDFS-kompatibelt REST-gränssnitt för program.

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med hjälp av analytiska Hadoop-ramar, till exempel MapReduce eller Hive. Azure HDInsight-kluster kan etableras och konfigurerats för direkt åtkomst till data som lagras i Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och lämpar sig för att lagra en mängd olika data för analys. Den har inte gränser för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera i storlek från kilobyte till petabyte, vilket gör Data Lake Storage Gen1 ett bra alternativ för att lagra alla typer av data. Data lagras varaktigt genom att göra flera kopior och det finns inga begränsningar för hur länge data kan lagras i datasjön.

### <a name="performance-tuning-for-big-data-analytics"></a>Prestandajustering för big data analytics

Data Lake Storage Gen1 är utformat för att köra storskaliga analytiska system som kräver enormt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil över flera enskilda lagringsservrar. När du analyserar data förbättrar läsgenomströmning i den här installationen när filen läses parallellt.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Beredskap för företag: Hög tillgänglighet och säkerhet

Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Datatillgångar lagras varaktigt: redundanta kopior skydda mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av deras befintliga dataplattform.

Data Lake Storage Gen1 innehåller också säkerhet i företagsklass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexibla datastrukturer

Data Lake Storage Gen1 kan lagra data i ursprungligt format, så som de är, utan att tidigare transformationer. Data Lake Storage Gen1 kräver inte något schema ska definieras innan data läses. Det enskilda analytiska ramverket tolkas data och definierar ett schema vid analysens tidpunkt. Eftersom den kan lagra filer av godtycklig storlek och format, kan Data Lake Storage Gen1 hantera strukturerade, semistrukturerade och Ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du använder lagrade data med hjälp av SDK: er, Azure-portalen och Azure PowerShell. Så länge du placerar dina data i arkivet med hjälp av dessa gränssnitt och lämpliga behållare kan du lagra alla typer av data. Data Lake Storage Gen1 utföra inte någon särskild hantering av data baserat på vilken typ av data som lagras.

## <a name="DataLakeStoreSecurity"></a>Datasäkerhet i Data Lake Storage Gen1
Data Lake Storage Gen1 använder Azure Active Directory för autentisering och använder åtkomst åtkomstkontrolllistor (ACL) för att hantera åtkomst till dina data.

| **Funktion** | **Beskrivning** |
| --- | --- |
| Autentisering |Data Lake Storage Gen1 integreras med Azure Active Directory (Azure AD) för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. Tack vare integreringen, Data Lake Storage Gen1 dra nytta av alla Azure AD-funktioner. Dessa funktioner inkluderar multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, övervakning av programanvändning, säkerhetsövervakning och avisering och så vidare. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering i REST-gränssnittet. Se [autentisering i Azure Data Lake Storage Gen1 med hjälp av Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomstkontroll genom att stödja behörigheter av POSIX-typ som exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Läs mer om hur åtkomstkontrollposter fungerar i kontexten för Data Lake Storage Gen1 [åtkomstkontroll i Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller också kryptering för data som lagras i kontot. Du kan ange krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att ha krypterade data eller välja Ingen kryptering. Mer information finns i [kryptering i Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Anvisningar om hur du skapar en krypteringsrelaterad konfiguration finns i [Kom igång med Azure Data Lake Storage Gen1 med Azure portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Mer information om hur du skyddar data i Data Lake Storage Gen1 finns [skydda data som lagrats i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1
Data Lake Storage Gen1 är kompatibelt med de flesta öppna källkomponenter i Hadoop-ekosystemet. Det är även snyggt integrerat med andra Azure-tjänster.  Följ länkarna nedan för att lära dig mer om hur Data Lake Storage Gen1 kan användas både med öppen källkod-komponenter samt andra Azure-tjänster.

* Se [öppen källkod stordata-program som fungerar med Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) en lista över program med öppen källkod som samverkar med Data Lake Storage Gen1.
* Se [integrering av Azure Data Lake Storage Gen1 med andra Azure-tjänster](../data-lake-store/data-lake-store-integrate-with-other-services.md) att lära dig hur du använder Data Lake Storage Gen1 med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
* Se [med hjälp av Azure Data Lake Storage Gen1 för stordatakrav](../data-lake-store/data-lake-store-data-scenarios.md) att lära dig hur du använder Data Lake Storage Gen1 i scenarier som matar in data, bearbetning av data, hämta data och visualisera data.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 filsystem (adl: / /)
I Hadoop-miljöer (tillgängligt med HDInsight-kluster), kan du komma åt Data Lake Storage Gen1 via det nya filsystemet AzureDataLakeFilesystem (adl: / /). Prestanda för program och tjänster som använder adl: / / kan optimeras på ett sätt som inte är tillgänglig i WebHDFS. Därför när du använder Data Lake Storage Gen1, du får flexibilitet att antingen tillg bästa prestanda med hjälp av den rekommenderade adl: / / eller hantera befintlig kod genom att fortsätta att använda WebHDFS API direkt. Azure HDInsight utnyttjar fullständig AzureDataLakeFilesystem för bästa prestanda på Data Lake Storage Gen1.

Åtkomst till dina data i Data Lake Storage Gen1 med följande:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Mer information om hur du kommer åt data i Data Lake Storage Gen1 finns i [åtgärder som är tillgängliga för lagrade data](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
