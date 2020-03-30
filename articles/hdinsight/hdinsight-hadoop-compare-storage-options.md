---
title: Jämföra lagringsalternativ för användning med Azure HDInsight-kluster
description: Ger en översikt över lagringstyper och hur de fungerar med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095547"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Jämföra lagringsalternativ för användning med Azure HDInsight-kluster

Du kan välja mellan några olika Azure-lagringstjänster när du skapar HDInsight-kluster:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Den här artikeln innehåller en översikt över dessa lagringstyper och deras unika funktioner.

I följande tabell sammanfattas De Azure Storage-tjänster som stöds med olika versioner av HDInsight:

| Lagringstjänst | Kontotyp | Namnområdestyp | Tjänster som stöds | Prestandanivåer som stöds | Åtkomstnivåer som stöds | HDInsight-version | Klustertyp |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Allmänt V2 | Hierarkisk (filsystem) | Blob | Standard | Hot, Cool, Arkiv | 3.6+ | Alla utom Spark 2.1 och 2.2|
|Azure Storage| Allmänt V2 | Objekt | Blob | Standard | Hot, Cool, Arkiv | 3.6+ | Alla |
|Azure Storage| Allmänt V1 | Objekt | Blob | Standard | Ej tillämpligt | Alla | Alla |
|Azure Storage| Blob Lagring** | Objekt | Block Blob | Standard | Hot, Cool, Arkiv | Alla | Alla |
|Azure Data Lake Storage Gen1| Ej tillämpligt | Hierarkisk (filsystem) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | 3.6 Endast | Alla utom HBase |

**För HDInsight-kluster kan endast sekundära lagringskonton vara av typen BlobStorage och Page Blob är inte ett lagringsalternativ som stöds.

Mer information om Azure Storage-kontotyper finns i [Översikt över Azure storage-konto](../storage/common/storage-account-overview.md)

Mer information om Azure Storage-åtkomstnivåer finns i [Azure Blob storage: Premium (förhandsversion), lagringsnivåer för Hot, Cool och Arkiv](../storage/blobs/storage-blob-storage-tiers.md)

Du kan skapa ett kluster med olika kombinationer av tjänster för primär och valfri sekundär lagring. I följande tabell sammanfattas de klusterlagringskonfigurationer som för närvarande stöds i HDInsight:

| HDInsight-version | Primär lagring | Sekundär lagring | Stöds |
|---|---|---|---|
| 3,6 & 4,0 | Allmänt ändamål V1 , Allmänt ändamål V2 | Allmänt ändamål V1 , Allmänt ändamål V2, BlobStorage(Block Blobbar) | Ja |
| 3,6 & 4,0 | Allmänt ändamål V1 , Allmänt ändamål V2 | Data Lake Storage Gen2 | Inga |
| 3,6 & 4,0 | Gen2 för lagring av datasjöar* | Data Lake Storage Gen2 | Ja |
| 3,6 & 4,0 | Gen2 för lagring av datasjöar* | Allmänt ändamål V1 , Allmänt ändamål V2, BlobStorage(Block Blobbar) | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Inga |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Allmänt ändamål V1 , Allmänt ändamål V2, BlobStorage(Block Blobbar) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Inga |
| 4.0 | Data Lake Storage Gen1 | Alla | Inga |
| 4.0 | Allmänt ändamål V1 , Allmänt ändamål V2 | Data Lake Storage Gen1 | Inga |

*=Det kan vara ett eller flera DataSjölagringsgenm2-konton, så länge de alla är inställda på att använda samma hanterade identitet för klusteråtkomst.

> [!Note] 
> Primär lagringsgendlagring i DataSjögend2 stöds inte för Spark 2.1- eller 2.2-kluster. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Använda Azure Data Lake Storage Gen2 med Apache Hadoop i Azure HDInsight

Azure Data Lake Storage Gen2 tar kärnfunktioner från Azure Data Lake Storage Gen1 och integrerar dem i Azure Blob-lagring. Dessa funktioner inkluderar ett filsystem som är kompatibelt med Hadoop, Azure Active Directory (Azure AD) och POSIX-baserade åtkomstkontrollistor (ACL). Med den här kombinationen kan du dra nytta av prestandan hos Azure Data Lake Storage Gen1 samtidigt som du använder nivåindelning och datalivscykelhantering av Blob-lagring.

Mer information om Azure Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Grundläggande funktioner för Azure Data Lake Storage Gen2

* **Åtkomst som är kompatibel med Hadoop:** I Azure Data Lake Storage Gen2 kan du hantera och komma åt data på samma sätt som med ett HADoop Distributed File System (HDFS). Azure Blob File System (ABFS) är tillgänglig i alla Apache Hadoop-miljöer, inklusive Azure HDInsight och Azure Databricks. Använd ABFS för att komma åt data som lagras i Data Lake Storage Gen2.

* **En superuppsättning av POSIX-behörigheter:** Säkerhetsmodellen för Data Lake Gen2 stöder behörigheter för ACL och POSIX tillsammans med några extra granularitet som är specifika för Data Lake Storage Gen2. Inställningar kan konfigureras via administrationsverktyg eller ramverk som Apache Hive och Apache Spark.

* **Kostnadseffektivitet:** Data Lake Storage Gen2 erbjuder billig lagringskapacitet och transaktioner. Funktioner som Azure Blob storage life cycle hjälper till att sänka kostnaderna genom att justera faktureringsfrekvensen när data rör sig genom dess livscykel.

* **Kompatibilitet med Blob-lagringsverktyg, ramverk och appar:** Data Lake Storage Gen2 fortsätter att arbeta med ett brett utbud av verktyg, ramverk och program för Blob-lagring.

* **Optimerad drivrutin:** ABFS-drivrutinen är optimerad speciellt för stordataanalys. Motsvarande REST-API:er visas via dfs-slutpunkten (Distributed File System) dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Nyheter för Azure Data Lake Storage Gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker filåtkomst

Azure HDInsight använder hanterade identiteter för att skydda klusteråtkomst till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en uppsättning automatiskt hanterade autentiseringsuppgifter. Dessa autentiseringsuppgifter kan användas för att autentisera till alla tjänster som stöder Active Directory-autentisering. Om du använder hanterade identiteter krävs inte att du lagrar autentiseringsuppgifter i kod- eller konfigurationsfiler.

Mer information finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Azure Blob-filsystemdrivrutin

Apache Hadoop-program förväntar sig inbyggt att läsa och skriva data från lokal disklagring. En Hadoop filsystem drivrutin som ABFS gör hadoop applikationer att arbeta med molnlagring genom att efterlikna regelbundna Hadoop filsystem verksamhet. Drivrutinen konverterar de kommandon som tas emot från programmet till åtgärder som den faktiska molnlagringsplattformen förstår.

Tidigare konverterade Hadoop-filsystemdrivrutinen alla filsystemåtgärder till AZURE Storage REST API-anrop på klientsidan och anropade sedan REST API.Previously, the Hadoop file system driver converted all file system operations to Azure Storage REST API calls on the client side and then invoked the REST API. Den här konverteringen på klientsidan resulterade dock i flera REST API-anrop för en enda filsystemåtgärd som att byta namn på en fil. ABFS har flyttat en del av Hadoop filsystem logik från klientsidan till serversidan. Azure Data Lake Storage Gen2 API körs nu parallellt med Blob API. Den här migreringen förbättrar prestanda eftersom vanliga Hadoop-filsystemåtgärder nu kan utföras med ett REST API-anrop.

Mer information finns i [Azure Blob Filesystem driver (ABFS): En dedikerad Azure Storage-drivrutin för Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema för Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 använder ett nytt URI-schema för att komma åt filer i Azure Storage från HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst.

`<FILE_SYSTEM_NAME>`identifierar sökvägen till filsystemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identifierar Azure Storage-kontonamnet. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

`<PATH>`är fil- eller katalogens HDFS-sökvägsnamn.

Om värden `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` för och inte anges används standardfilsystemet. För filerna i standardfilsystemet använder du en relativ sökväg eller en absolut sökväg. `hadoop-mapreduce-examples.jar` Filen som medföljer HDInsight-kluster kan till exempel refereras till med hjälp av någon av följande sökvägar:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Filnamnet finns `hadoop-examples.jar` i HDInsight version 2.1- och 1.6-kluster. När du arbetar med filer utanför HDInsight, kommer du att upptäcka att de flesta verktyg inte känner igen `example/jars/hadoop-mapreduce-examples.jar`ABFS-format utan i stället förväntar sig en grundläggande väg format, till exempel .

Mer information finns [i Använda Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en robust lagringslösning för allmänt syfte som integreras sömlöst med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett HDFS-gränssnitt kan den fullständiga uppsättningen komponenter i HDInsight fungera direkt på strukturerade eller ostrukturerade data som lagras som blobbar.

Vi rekommenderar att du använder separata lagringsbehållare för standardklusterlagring och dina affärsdata för att isolera HDInsight-loggarna och temporära filer från dina egna affärsdata. Vi rekommenderar också att du tar bort standardblobbehållaren, som innehåller program- och systemloggar, efter varje användning för att minska lagringskostnaden. Se till att hämta loggarna innan du tar bort containern.

Om du väljer att skydda ditt **lagringskonto med begränsningarna brandväggar och virtuella nätverk** i valda **nätverk**måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...** så att HDInsight kan komma åt ditt lagringskonto.

### <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram ger en abstrakt vy över HDInsight-arkitekturen i Azure Storage:

![HDInsight-lagringsarkitektur](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight-lagringsarkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Via HDInsight kan du även komma åt data i Azure Storage. Syntaxen ser ut så här:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Tänk på följande principer när du använder ett Azure Storage-konto med HDInsight-kluster:

* **Containrar på de lagringskonton som är anslutna till ett kluster:** Eftersom kontonamnet och nyckeln associeras med klustret när det skapas har du full tillgång till blobarna i dessa containrar.

* **Offentliga behållare eller offentliga blobbar i lagringskonton som *inte* är anslutna till ett kluster:** Du har skrivskyddad behörighet till blobbar i behållarna.
  
  > [!NOTE]  
  > Med offentliga behållare kan du hämta en lista över alla blobbar som är tillgängliga i behållaren och hämta behållarmetadata. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [Hantera anonym läsåtkomst till behållare och blobbar](../storage/blobs/storage-manage-access-to-resources.md).

* **Privata behållare i lagringskonton som *inte* är anslutna till ett kluster:** Du kan inte komma åt blobbar i behållarna om du inte definierar lagringskontot när du skickar WebHCat-jobben. 

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Som standard använder HDInsight de lagringskonton som definierats i filen core-site.xml. Du kan ändra den här inställningen med apache [Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop streaming och Apache Pig, kan bära en beskrivning av lagringskonton och metadata med dem. (Detta gäller för närvarande för Gris med lagringskonton men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metabutiker](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. Blob-behållare lagrar data som nyckel-/värdepar och har ingen kataloghierarki. Men nyckelnamnet kan innehålla ett snedstrecktecken ( / ) för att få det att se ut som om en fil lagras i en katalogstruktur. En blob-tangent kan till `input/log1.txt`exempel vara . Det `input` finns ingen faktisk katalog, men på grund av snedstreckstecknet i nyckelnamnet ser nyckeln ut som en sökväg.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Fördelar med Azure Storage
Beräkningskluster och lagringsresurser som inte är samlokaliseras har implicita prestandakostnader. Dessa kostnader minskas med hur beräkningskluster skapas nära lagringskontoresurserna i Azure-regionen. I den här regionen kan beräkningsnoderna effektivt komma åt data via höghastighetsnätverket i Azure Storage.

När du lagrar data i Azure Storage i stället för HDFS får du flera fördelar:

* **Återanvändning och delning av data:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage, däremot, kan nås via antingen HDFS API:er eller Blob storage REST API:er. På grund av det här arrangemanget kan en större uppsättning program (inklusive andra HDInsight-kluster) och verktyg användas för att producera och använda data.

* **Data arkivering:** När data lagras i Azure Storage kan HDInsight-kluster som används för beräkning tas bort på ett säkert sätt utan att förlora användardata.

* **Kostnad för datalagring:** Att lagra data i DFS på lång sikt är dyrare än att lagra data i Azure Storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure Storage. Eftersom data inte behöver laddas om för varje generering av beräkningskluster sparar du också kostnader för datainläsning.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem bestäms skalan av antalet noder som du skapar för klustret. Att ändra skalan kan vara mer komplicerat än att förlita sig på de elastiska skalningsfunktionerna som du får automatiskt i Azure Storage.

* **Geo-replikering:** Din Azure Storage kan geo-replikeras. Även om geo-replikering ger dig geografisk återställning och dataredundans, påverkar en redundans till den georepnadda platsen allvarligt din prestanda och det kan medföra extra kostnader. Så välj geo-replikering försiktigt och endast om värdet av data motiverar den extra kostnaden.

Vissa MapReduce-jobb och paket kan skapa mellanliggande resultat som du inte vill lagra i Azure Storage. I så fall kan du välja att lagra data i den lokala HDFS. HDInsight använder DFS för flera av dessa mellanliggande resultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon `ls`(till exempel , `copyFromLocal`och `mkdir`) fungerar som förväntat i Azure Storage. Endast de kommandon som är specifika för den inbyggda HDFS-implementeringen `fschk` (som kallas DFS), till exempel och `dfsadmin`, visar olika beteenden i Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Översikt över Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 är en företagsomfattande hyperskalalagringsdatabas för stordata analytiska arbetsbelastningar. Med Hjälp av Azure Data Lake kan du samla in data av valfri storlek, typ och inmatningshastighet på ett ställe för drift- och undersökningsanalys.

Access Data Lake Storage Gen1 från Hadoop (tillgängligt med ett HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API:er. Data Lake Storage Gen1 är utformad för att möjliggöra analys av lagrade data och är inställd för prestanda i dataanalysscenarier. Out of the box, den innehåller de funktioner som är viktiga för verkliga företag användningsfall. Dessa funktioner omfattar säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet.

Mer information om Azure Data Lake Storage Gen1 finns i den detaljerade [översikten över Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

De viktigaste funktionerna i Data Lake Storage Gen1 inkluderar följande.

### <a name="compatibility-with-hadoop"></a>Kompatibilitet med Hadoop

Data Lake Storage Gen1 är ett Apache Hadoop-filsystem som är kompatibelt med HDFS och fungerar med Hadoop-ekosystemet.  Dina befintliga HDInsight-program eller -tjänster som använder WebHDFS API kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 exponerar också ett WebHDFS-kompatibelt REST-gränssnitt för program.

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med Hadoop analytiska ramverk som MapReduce eller Hive. Azure HDInsight-kluster kan etableras och konfigureras för att direkt komma åt data som lagras i Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och är lämplig för lagring av en mängd olika data för analys. Det innebär inga begränsningar för kontostorlekar, filstorlekar eller mängden data som kan lagras i en datasjö. Enskilda filer kan variera i storlek från kilobyte till petabyte, vilket gör Data Lake Storage Gen1 ett utmärkt val att lagra alla typer av data. Data lagras varaktigt genom att göra flera kopior, och det finns inga gränser för hur länge data kan lagras i datasjön.

### <a name="performance-tuning-for-big-data-analytics"></a>Prestandajustering för stordataanalys

Data Lake Storage Gen1 är byggd för att köra storskaliga analytiska system som kräver massivt dataflöde för att fråga och analysera stora mängder data. Datasjön sprider delar av en fil över flera enskilda lagringsservrar. När du analyserar data förbättrar den här inställningen läsdataflödet när filen läss parallellt.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Beredskap för företag: Mycket tillgänglig och säker

Data Lake Storage Gen1 ger branschstandard tillgänglighet och tillförlitlighet. Datatillgångar lagras på ett sätt: överflödiga kopior skyddar mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av sin befintliga dataplattform.

Data Lake Storage Gen1 ger också säkerhet i företagsklass för lagrade data. Mer information finns [i Skydda data i Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexibla datastrukturer

Data Lake Storage Gen1 kan lagra alla data i sitt ursprungliga format, som det är, utan att kräva tidigare omvandlingar. Data Lake Storage Gen1 kräver inte att ett schema definieras innan data läses in. Det enskilda analytiska ramverket tolkar data och definierar ett schema vid tidpunkten för analysen. Eftersom den kan lagra filer med godtyckliga storlekar och format kan Data Lake Storage Gen1 hantera strukturerade, halvstrukturerade och ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i huvudsak mappar och filer. Du arbetar med lagrade data med hjälp av SDK:er, Azure-portalen och Azure PowerShell. Så länge du lägger dina data i butiken med hjälp av dessa gränssnitt och lämpliga behållare, kan du lagra alla typer av data. Data Lake Storage Gen1 utför ingen särskild hantering av data baserat på vilken typ av data som lagras.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Datasäkerhet i Gen1 för lagring av datasjö
Data Lake Storage Gen1 använder Azure Active Directory för autentisering och använder åtkomstkontrollistor (ACL: er) för att hantera åtkomst till dina data.

| **Funktion** | **Beskrivning** |
| --- | --- |
| Autentisering |DataSjölagringsgenm1 integreras med Azure Active Directory (Azure AD) för identitets- och åtkomsthantering för alla data som lagras i Data Lake Storage Gen1. På grund av integrationen drar Data Lake Storage Gen1 nytta av alla Azure AD-funktioner. Dessa funktioner inkluderar multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomstkontroll, programanvändningsövervakning, säkerhetsövervakning och aviseringar och så vidare. Data Lake Storage Gen1 stöder OAuth 2.0-protokollet för autentisering inom REST-gränssnittet. Se [Autentisering i Azure Data Lake Storage Gen1 med Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |DataSjölagringsgenm1 ger åtkomstkontroll genom att stödja behörigheter i POSIX-format som exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Mer information om hur ACL fungerar i samband med Data Lake Storage Gen1 finns [i Åtkomstkontroll i Gen1](../data-lake-store/data-lake-store-access-control.md)för lagring av datasjö . |
| Kryptering |Data Lake Storage Gen1 tillhandahåller också kryptering för data som lagras i kontot. Du anger krypteringsinställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att få dina data krypterade eller välja ingen kryptering. Mer information finns [i Kryptering i DataSjölagring Gen1](../data-lake-store/data-lake-store-encryption.md). Instruktioner om hur du tillhandahåller en krypteringsrelaterad konfiguration finns i [Komma igång med Azure Data Lake Storage Gen1 med Azure-portalen](../data-lake-store/data-lake-store-get-started-portal.md). |

Mer information om hur du skyddar data i Gen1 för lagring av datasjö, finns [i Skydda data som lagras i Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1
Data Lake Storage Gen1 är kompatibel med de flesta komponenter med öppen källkod i Hadoop-ekosystemet. Det är även snyggt integrerat med andra Azure-tjänster.  Följ länkarna nedan om du vill veta mer om hur Data Lake Storage Gen1 kan användas både med komponenter med öppen källkod och andra Azure-tjänster.

* Se [Stordataprogram med öppen källkod som fungerar med Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) för en lista över program med öppen källkod som samverkar med Data Lake Storage Gen1.
* Se [Integrera Azure Data Lake Storage Gen1 med andra Azure-tjänster](../data-lake-store/data-lake-store-integrate-with-other-services.md) för att förstå hur du använder Data Lake Storage Gen1 med andra Azure-tjänster för att aktivera ett bredare spektrum av scenarier.
* Se [Använda Azure Data Lake Storage Gen1 för stordatakrav för](../data-lake-store/data-lake-store-data-scenarios.md) att lära dig hur du använder Data Lake Storage Gen1 i scenarier som att använda data, bearbeta data, hämta data och visualisera data.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 filsystem (adl://)
I Hadoop-miljöer (tillgängligt med ett HDInsight-kluster) kan du komma åt Data Lake Storage Gen1 via det nya filsystemet AzureDataLakeFilesystem (adl://). Prestanda för program och tjänster som använder adl:// kan optimeras på ett sätt som för närvarande inte är tillgängligt i WebHDFS. När du använder Data Lake Storage Gen1 får du därför flexibiliteten att antingen utnyttja bästa prestanda genom att använda den rekommenderade adl:// eller underhålla befintlig kod genom att fortsätta använda WebHDFS API direkt. Azure HDInsight drar full nytta av AzureDataLakeFilesystem för att ge bästa prestanda på Data Lake Storage Gen1.

Få tillgång till dina data i DataSjölagring Gen1 med hjälp av följande:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Mer information om hur du kommer åt data i DataSjölagring Gen1 finns i [Åtgärder som är tillgängliga för lagrade data](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
