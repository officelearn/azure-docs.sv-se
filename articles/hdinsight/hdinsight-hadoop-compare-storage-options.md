---
title: Jämför lagrings alternativ för användning med Azure HDInsight-kluster
description: Ger en översikt över lagrings typer och hur de fungerar med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: b73810b37020bf01c1088f194bd426e93fd95d2c
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180764"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Jämför lagrings alternativ för användning med Azure HDInsight-kluster

Du kan välja mellan några olika Azure Storage-tjänster när du skapar HDInsight-kluster:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Den här artikeln innehåller en översikt över dessa lagrings typer och deras unika funktioner.

I följande tabell sammanfattas de Azure Storage tjänster som stöds med olika versioner av HDInsight:

| Lagringstjänst | Kontotyp | Namn områdes typ | Tjänster som stöds | Prestanda nivåer som stöds | Åtkomst nivåer som stöds | HDInsight-version | Klustertyp |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Allmänt-syfte v2 | Hierarkisk (FileSystem) | Blob | Standard | Frekvent, låg frekvent, Arkiv | 3.6 + | Alla |
|Azure Storage| Allmänt-syfte v2 | Object | Blob | Standard | Frekvent, låg frekvent, Arkiv | 3.6 + | Alla |
|Azure Storage| Generell användning v1 | Object | Blob | Standard | Gäller inte | Alla | Alla |
|Azure Storage| Blob Storage * * | Object | Blockblob | Standard | Frekvent, låg frekvent, Arkiv | Alla | Alla |
|Azure Data Lake Storage Gen1| Gäller inte | Hierarkisk (FileSystem) | Gäller inte | Saknas | Gäller inte | 3,6 endast | Alla utom HBase |

\* * För HDInsight-kluster kan endast sekundära lagrings konton vara av typen BlobStorage och Page BLOB är inte ett lagrings alternativ som stöds.

Mer information om Azure Storage konto typer finns i [Översikt över Azure Storage-konto](../storage/common/storage-account-overview.md)

Mer information om Azure Storage åtkomst nivåer finns i [Azure Blob Storage: Premium (för hands version), frekvent, låg frekvent lagring och Arkiv lag rings nivåer](../storage/blobs/storage-blob-storage-tiers.md)

Du kan skapa ett kluster med olika kombinationer av tjänster för primär och valfri sekundär lagring. I följande tabell sammanfattas de kluster lagrings konfigurationer som för närvarande stöds i HDInsight:

| HDInsight-version | Primär lagring | Sekundär lagring | Stöds |
|---|---|---|---|
| 3,6 & 4,0 | Generell användning v1, Generell användning v2 | Generell användning v1, Generell användning v2, BlobStorage (block blobbar) | Ja |
| 3,6 & 4,0 | Generell användning v1, Generell användning v2 | Data Lake Storage Gen2 | Nej |
| 3,6 & 4,0 | Generell användning v1, Generell användning v2 | Data Lake Storage Gen1 | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Generell användning v1, Generell användning v2, BlobStorage (block blobbar) | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nej |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Generell användning v1, Generell användning v2, BlobStorage (block blobbar) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nej |
| 4.0 | Data Lake Storage Gen1 | Any | Nej |

\* = Detta kan vara ett eller flera Data Lake Storage Gen2-konton, så länge de är konfigurerat för att använda samma hanterade identitet för kluster åtkomst.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Använda Azure Data Lake Storage Gen2 med Apache Hadoop i Azure HDInsight

Azure Data Lake Storage Gen2 använder kärn funktioner från Azure Data Lake Storage Gen1 och integrerar dem i Azure Blob Storage. Dessa funktioner innehåller ett fil system som är kompatibelt med Hadoop, Azure Active Directory (Azure AD) och POSIX-baserade åtkomst kontrol listor (ACL: er). Med den här kombinationen kan du dra nytta av prestanda för Azure Data Lake Storage Gen1 samtidigt som du använder hantering av nivåer och data livs cykel hantering av Blob Storage.

Mer information om Azure Data Lake Storage Gen2 finns i [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Core-funktioner i Azure Data Lake Storage Gen2

* **Åtkomst som är kompatibel med Hadoop:** I Azure Data Lake Storage Gen2 kan du hantera och komma åt data precis som med en Hadoop Distributed File System (HDFS). ABFS-drivrutinen (Azure Blob File System) är tillgänglig i alla Apache Hadoop miljöer, inklusive Azure HDInsight och Azure Databricks. Använd ABFS för att komma åt data som lagras i Data Lake Storage Gen2.

* **En supermängd av POSIX-behörigheter:** Säkerhets modellen för Data Lake Gen2 stöder ACL-och POSIX-behörigheter tillsammans med viss extra detaljerad information för att Data Lake Storage Gen2. Inställningar kan konfigureras via administrations verktyg eller ramverk som Apache Hive och Apache Spark.

* **Kostnads effektivitet:** Data Lake Storage Gen2 erbjuder lagrings kapacitet och transaktioner med låg kostnad. Funktioner som Azure Blob Storage livs cykel hjälper till att minska kostnaderna genom att justera fakturerings taxan när data flyttas genom livs cykeln.

* **Kompatibilitet med Blob Storage-verktyg, ramverk och appar:** Data Lake Storage Gen2 fortsätter att arbeta med en bred uppsättning verktyg, ramverk och program för Blob Storage.

* **Optimerad driv rutin:** ABFS-drivrutinen optimeras specifikt för stor data analys. Motsvarande REST-API: er sker via DFS-slutpunkten (Distributed File System), dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Vad är nytt för Azure Data Lake Storage gen 2

#### <a name="managed-identities-for-secure-file-access"></a>Hanterade identiteter för säker fil åtkomst

Azure HDInsight använder hanterade identiteter för att skydda kluster åtkomsten till filer i Azure Data Lake Storage Gen2. Hanterade identiteter är en funktion i Azure Active Directory som tillhandahåller Azure-tjänster med en uppsättning automatiskt hanterade autentiseringsuppgifter. De här autentiseringsuppgifterna kan användas för att autentisera till en tjänst som stöder Active Directory autentisering. Om du använder hanterade identiteter behöver du inte lagra autentiseringsuppgifter i kod-eller konfigurationsfiler.

Mer information finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Azure Blob File System-drivrutin

Apache Hadoop program förväntar sig att läsa och skriva data från lokal disk lagring. En Hadoop-fil system driv rutin som ABFS gör att Hadoop-program fungerar med moln lagring genom att emulera vanliga Hadoop-filsystem-åtgärder. Driv rutinen konverterar de kommandon som tas emot från programmet till åtgärder som den faktiska moln lagrings plattformen förstår.

Tidigare har Hadoop-filsystemets driv rutin konverterat alla fil system åtgärder för att Azure Storage REST API anrop på klient sidan och sedan anropade REST API. Den här konverteringen på klient sidan ledde dock till flera REST API anrop för en enda fil system åtgärd som att byta namn på en fil. ABFS har flyttat en del av Hadoop-filsystemets logik från klient sidan till Server sidan. Azure Data Lake Storage Gen2 API körs nu parallellt med BLOB-API: et. Den här migreringen förbättrar prestandan eftersom du kan utföra vanliga Hadoop-filsystem åtgärder med ett REST API-anrop.

Mer information finns i [Azure Blob filesystem-driv rutinen (ABFS): En dedikerad Azure Storage driv rutin för](../storage/blobs/data-lake-storage-abfs-driver.md)Hadoop.

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-schema för Azure Data Lake Storage gen 2 

Azure Data Lake Storage Gen2 använder ett nytt URI-schema för att komma åt filer i Azure Storage från HDInsight:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

URI-schemat ger SSL-krypterad åtkomst.

`<FILE_SYSTEM_NAME>`identifierar sökvägen till fil systemet Data Lake Storage Gen2.

`<ACCOUNT_NAME>`identifierar Azure Storage kontots namn. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

`<PATH>`är sökvägen till filen eller katalogen HDFS.

Om värden för `<FILE_SYSTEM_NAME>` och `<ACCOUNT_NAME>` inte anges används standard fil systemet. För filerna i standard fil systemet använder du en relativ sökväg eller en absolut sökväg. Till exempel `hadoop-mapreduce-examples.jar` kan filen som medföljer HDInsight-kluster refereras till genom att använda någon av följande sökvägar:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> Fil namnet är `hadoop-examples.jar` i HDInsight-versionerna 2,1 och 1,6-kluster. När du arbetar med filer utanför HDInsight kan du se att de flesta verktyg inte känner igen ABFS-formatet utan förväntar sig ett grundläggande Sök vägs format, `example/jars/hadoop-mapreduce-examples.jar`till exempel.

Mer information finns i [använd Azure Data Lake Storage Gen2-URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure Storage är en robust lagrings lösning för allmänna ändamål som integreras sömlöst med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Med ett HDFS-gränssnitt kan den fullständiga uppsättningen komponenter i HDInsight köras direkt på strukturerade eller ostrukturerade data som lagras som blobbar.

Vi rekommenderar att du använder separata lagrings behållare för standard kluster lagringen och dina affärs data för att isolera HDInsight-loggar och temporära filer från dina egna affärs data. Vi rekommenderar också att du tar bort standard-BLOB-behållaren, som innehåller program-och system loggar, efter varje användning för att minska lagrings kostnaderna. Se till att hämta loggarna innan du tar bort containern.

Om du väljer att skydda ditt lagrings konto med **brand väggar och begränsningar för virtuella nätverk** på **valda nätverk**måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...** så att HDInsight kan komma åt din lagring redovisning.

### <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram innehåller en översikt över HDInsight-arkitekturen för Azure Storage:

![HDInsight Storage arkitektur](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight Storage arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Via HDInsight kan du också komma åt data i Azure Storage. Syntaxen är följande:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Tänk på följande principer när du använder ett Azure Storage-konto med HDInsight-kluster:

* **Behållare i de lagrings konton som är anslutna till ett kluster:** Eftersom konto namnet och nyckeln är associerade med klustret när de skapas har du fullständig åtkomst till Blobbarna i dessa behållare.

* **Offentliga behållare eller offentliga blobbar i lagrings konton som *inte* är anslutna till ett kluster:** Du har skrivskyddad behörighet till Blobbarna i behållarna.
  
  > [!NOTE]  
  > Med offentliga behållare kan du hämta en lista över alla blobar som är tillgängliga i behållaren och hämta metadata för behållare. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [Hantera anonym läsbehörighet till containrar och blobbar](../storage/blobs/storage-manage-access-to-resources.md).

* **Privata behållare i lagrings konton som *inte* är anslutna till ett kluster:** Du har inte åtkomst till Blobbarna i behållarna om du inte definierar lagrings kontot när du skickar WebHCat-jobben. 

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i %HADOOP_HOME%/conf/core-site.xml i klusternoderna. Som standard använder HDInsight de lagrings konton som definierats i filen site. xml. Du kan ändra den här inställningen med hjälp av [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop streaming och Apache gris, kan innehålla en beskrivning av lagrings konton och metadata för dem. (Det här gäller för närvarande för gris med lagrings konton, men inte för metadata.) Mer information finns i [använda ett HDInsight-kluster med alternativa lagrings konton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. BLOB-behållare lagrar data som nyckel/värde-par och har ingen katalogpartition. Nyckel namnet kan dock innehålla ett snedstreck (/) som gör att det visas som om en fil lagras i en katalog struktur. En blobs nyckel kan till exempel vara `input/log1.txt`. Det finns `input` ingen faktisk katalog, men på grund av snedstrecket i nyckel namnet ser nyckeln ut som en fil Sök väg.

### <a id="benefits"></a>Fördelar med Azure Storage
Beräknings kluster och lagrings resurser som inte samplaceras har underförstådda prestanda kostnader. Dessa kostnader begränsas av hur beräknings klustren skapas nära lagrings konto resurserna i Azure-regionen. I den här regionen kan Compute-noderna effektivt komma åt data över det snabba nätverket i Azure Storage.

När du lagrar data i Azure Storage i stället för HDFS får du flera fördelar:

* **Åter användning och delning av data:** Data i HDFS finns i beräknings klustret. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage, med kontrast, kan nås via antingen HDFS-API: erna eller REST-API: er för Blob Storage. På grund av den här ordningen kan en större uppsättning program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.

* **Data arkivering:** När data lagras i Azure Storage kan HDInsight-kluster som används för beräkning vara säkert borttagna utan att förlora användar data.

* **Kostnad för data lagring:** Att lagra data i DFS för lång sikt är dyrare än att lagra data i Azure Storage eftersom kostnaden för ett beräknings kluster är högre än kostnaden för Azure Storage. Eftersom data inte behöver läsas in på nytt för varje beräknings kluster skapas även data inläsnings kostnader.

* **Elastisk skalbarhet:** Även om HDFS ger dig ett skalbart fil system, bestäms skalan av antalet noder som du skapar för klustret. Det kan vara mer komplicerat att ändra skalan än att förlita dig på de elastiska skalnings funktionerna som du får automatiskt i Azure Storage.

* **Geo-replikering:** Din Azure Storage kan vara geo-replikerad. Även om geo-replikering ger dig geografisk återställning och dataredundans, påverkar en redundansväxling till den geo-replikerade platsen prestanda och ytterligare kostnader kan tillkomma. Välj därför försiktigt geo-replikering och endast om värdet för data motiverar den extra kostnaden.

Vissa MapReduce-jobb och-paket kan skapa mellanliggande resultat som du inte vill lagra i Azure Storage. I så fall kan du välja att lagra data i den lokala HDFS. HDInsight använder DFS för flera av dessa mellanliggande resultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till `ls`exempel `copyFromLocal`,, `mkdir`och) fungerar som förväntat i Azure Storage. Endast de kommandon som är specifika för den inbyggda HDFS-implementeringen (som kallas DFS), till exempel `fschk` och `dfsadmin`, visar olika beteenden i Azure Storage.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Översikt över Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 är en företagsomfattande storskalig lagrings plats för stor data analys arbets belastningar. Med hjälp av Azure Data Lake kan du samla in data om storlek, typ och inmatnings hastighet på en plats för operativa och undersökande analyser.

Åtkomst Data Lake Storage Gen1 från Hadoop (tillgängligt med ett HDInsight-kluster) med hjälp av WebHDFS-kompatibla REST-API: er. Data Lake Storage Gen1 har utformats för att möjliggöra analyser av lagrade data och är anpassat för prestanda i scenarier med data analyser. I rutan finns de funktioner som är viktiga för verkliga företags användnings fall. Dessa funktioner omfattar säkerhet, hanterbarhet, skalbarhet, tillförlitlighet och tillgänglighet.

Mer information om Azure Data Lake Storage Gen1 finns i den detaljerade [översikten över Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md).

Viktiga funktioner i Data Lake Storage Gen1 inkluderar följande.

### <a name="compatibility-with-hadoop"></a>Kompatibilitet med Hadoop

Data Lake Storage Gen1 är ett Apache Hadoop fil system som är kompatibelt med HDFS och fungerar med Hadoop-eko systemet.  Dina befintliga HDInsight-program eller-tjänster som använder WebHDFS-API: n kan enkelt integreras med Data Lake Storage Gen1. Data Lake Storage Gen1 också att exponera ett WebHDFS REST-gränssnitt för program.

Data som lagras i Data Lake Storage Gen1 kan enkelt analyseras med hjälp av Hadoop-analytiska ramverk som MapReduce eller Hive. Azure HDInsight-kluster kan tillhandahållas och konfigureras för direkt åtkomst till data som lagras i Data Lake Storage Gen1.

### <a name="unlimited-storage-petabyte-files"></a>Obegränsad lagring, petabytefiler

Data Lake Storage Gen1 ger obegränsad lagring och är lämplig för att lagra en mängd data för analys. Det innebär inte några begränsningar för konto storlekar, fil storlekar eller mängden data som kan lagras i ett data Lake. Enskilda filer kan variera mellan kilobyte och petabyte, vilket gör Data Lake Storage Gen1 ett bra val för att lagra vilken typ av data som helst. Data lagras varaktigt genom att göra flera kopior, och det finns ingen gräns för hur länge data kan lagras i data Lake.

### <a name="performance-tuning-for-big-data-analytics"></a>Prestanda justering för Big data Analytics

Data Lake Storage Gen1 är byggd för att köra storskaliga analys system som kräver enorma data flöden för att fråga och analysera stora mängder data. Data Lake sprider delar av en fil över flera enskilda lagrings servrar. När du analyserar data förbättrar den här installationen det lästa data flödet när filen läses parallellt.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Beredskap för företag: Hög tillgänglighet och säker

Data Lake Storage Gen1 ger bransch standard tillgänglighet och tillförlitlighet. Data till gångar lagras varaktigt: redundanta kopior skyddar mot oväntade fel. Företag kan använda Data Lake Storage Gen1 i sina lösningar som en viktig del av sin befintliga data plattform.

Data Lake Storage Gen1 ger också säkerhet i företags klass för lagrade data. Mer information finns i [skydda data i Azure Data Lake Storage gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexibla data strukturer

Data Lake Storage Gen1 kan lagra data i sitt ursprungliga format, precis som det är, utan föregående omvandling. Data Lake Storage Gen1 kräver inte att ett schema definieras innan data läses in. Det enskilda analys ramverket tolkar data och definierar ett schema vid tidpunkten för analysen. Eftersom den kan lagra filer av godtycklig storlek och format kan Data Lake Storage Gen1 hantera strukturerade, semistrukturerade och ostrukturerade data.

Data Lake Storage Gen1 behållare för data är i stort sett mappar och filer. Du arbetar med lagrade data med hjälp av SDK: er, Azure Portal och Azure PowerShell. Så länge du lagrar dina data i butiken genom att använda dessa gränssnitt och lämpliga behållare, kan du lagra alla typer av data. Data Lake Storage Gen1 utför inte någon särskild hantering av data baserat på den typ av data som lagras.

## <a name="DataLakeStoreSecurity"></a>Data säkerhet i Data Lake Storage Gen1
Data Lake Storage Gen1 använder Azure Active Directory för autentisering och använder åtkomst kontrol listor (ACL: er) för att hantera åtkomst till dina data.

| **Funktion** | **Beskrivning** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 integreras med Azure Active Directory (Azure AD) för identitets-och åtkomst hantering för alla data som lagras i Data Lake Storage Gen1. På grund av integreringen Data Lake Storage Gen1 fördelarna med alla Azure AD-funktioner. Dessa funktioner omfattar multifaktorautentisering, villkorlig åtkomst, rollbaserad åtkomst kontroll, övervakning av program användning, säkerhetsövervakning och avisering och så vidare. Data Lake Storage Gen1 stöder OAuth 2,0-protokollet för autentisering i REST-gränssnittet. Se [autentisering i Azure Data Lake Storage gen1 med Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Åtkomstkontroll |Data Lake Storage Gen1 ger åtkomst kontroll genom att stödja POSIX-format-behörigheter som exponeras av WebHDFS-protokollet. Du kan aktivera ACL:er i rotmappen, i undermappar och i enskilda filer. Mer information om hur ACL: er fungerar i samband med Data Lake Storage Gen1 finns i [åtkomst kontroll i data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md). |
| Kryptering |Data Lake Storage Gen1 innehåller även kryptering för data som lagras i kontot. Du anger krypterings inställningarna när du skapar ett Data Lake Storage Gen1-konto. Du kan välja att kryptera dina data eller välja ingen kryptering. Mer information finns i [kryptering i data Lake Storage gen1](../data-lake-store/data-lake-store-encryption.md). Instruktioner för hur du tillhandahåller en krypterings-relaterad konfiguration finns i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Mer information om hur du skyddar data i Data Lake Storage Gen1 finns i [skydda data som lagras i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Program som är kompatibla med Data Lake Storage Gen1
Data Lake Storage Gen1 är kompatibelt med de flesta komponenter med öppen källkod i Hadoop-eko systemet. Det är även snyggt integrerat med andra Azure-tjänster.  Följ länkarna nedan om du vill veta mer om hur Data Lake Storage Gen1 kan användas både med komponenter med öppen källkod och andra Azure-tjänster.

* Se [Big data program med öppen källkod som fungerar med Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) för en lista med program med öppen källkod som samverkar med data Lake Storage gen1.
* Se [integrera Azure Data Lake Storage gen1 med andra Azure-tjänster](../data-lake-store/data-lake-store-integrate-with-other-services.md) för att förstå hur du använder data Lake Storage gen1 med andra Azure-tjänster för att möjliggöra ett bredare utbud av scenarier.
* Se [använda Azure Data Lake Storage gen1 för Big data-krav](../data-lake-store/data-lake-store-data-scenarios.md) för att lära dig hur du använder data Lake Storage gen1 i scenarier som att mata in data, bearbeta data, hämta data och visualisera data.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 fil system (adl://)
I Hadoop-miljöer (tillgängliga med ett HDInsight-kluster) kan du komma åt Data Lake Storage Gen1 via det nya fil systemet, AzureDataLakeFilesystem (adl://). Prestanda för program och tjänster som använder adl://kan optimeras på sätt som inte är tillgängliga i WebHDFS. Det innebär att när du använder Data Lake Storage Gen1 får du flexibiliteten att antingen använda den bästa prestandan genom att använda den rekommenderade adl://eller underhålla befintlig kod genom att fortsätta att använda WebHDFS-API: et direkt. Azure HDInsight drar full nytta av AzureDataLakeFilesystem för att ge bästa möjliga prestanda på Data Lake Storage Gen1.

Få åtkomst till dina data i Data Lake Storage Gen1 med hjälp av följande:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Mer information om hur du kommer åt data i Data Lake Storage Gen1 finns i [åtgärder som är tillgängliga på lagrade data](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
