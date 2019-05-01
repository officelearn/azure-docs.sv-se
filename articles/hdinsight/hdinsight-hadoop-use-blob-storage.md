---
title: Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme – Azure HDInsight
description: Lär dig mer om att fråga efter data från Azure storage och Azure Data Lake Storage för att lagra resultatet av dina analyser.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 24e0b61dfd9950a5c5990f8341e32d048453c5d6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689571"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

Om du vill analysera data i HDInsight-kluster kan du lagra data antingen i [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md), eller en kombination. Dessa lagringsalternativ kan du ta bort HDInsight-kluster som används för beräkning utan att förlora användardata.

Apache Hadoop stöder begreppet standardfilsystem. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapandeprocessen för HDInsight-kluster kan du ange en blobbehållare i Azure Storage som standardfilsystemet eller med HDInsight 3.6, kan du välja antingen Azure Storage eller Azure Data Lake Storage Gen 1 / Azure Data Lake Storage Gen 2 som standardfiler system med några undantag. Support för att använda Data Lake Storage Gen 1 som både standardwebbplatsen och den länkade storage, se [tillgänglighet för HDInsight-kluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. Läs hur Data Lake Storage Gen 1 fungerar med HDInsight-kluster i [använda Azure Data Lake Storage med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-store.md). Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop-kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett gränssnitt för Hadoop-distribuerat filsystem (HDFS) kan alla komponenter i HDInsight tillämpas direkt på strukturerade eller ostrukturerade data som har lagrats som blobar.

> [!WARNING]  
> Storage-kontotyp **BlobStorage** kan bara användas som sekundär lagring för HDInsight-kluster.

| Typ av Storage-konto | Tjänster som stöds | Stöds prestandanivåer | Stöds åtkomstnivåerna |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (generell användning v2)  | Blob     | Standard                    | Frekvent, lågfrekvent, Arkiv\*   |
| Lagring (general-purpose v1)   | Blob     | Standard                    | Gäller inte                    |
| BlobStorage                    | Blob     | Standard                    | Frekvent, lågfrekvent, Arkiv\*   |

Vi rekommenderar att du inte använder standardcontainern för att lagra företagsdata. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Standardbehållaren innehåller program- och loggar. Se till att hämta loggarna innan du tar bort containern.

Det går inte att dela en blob-container som standardfilsystem över flera kluster.

> [!NOTE]  
> Arkiv som åtkomstnivå är en offline-nivå som har en flera timme hämtning svarstid och rekommenderas inte för användning med HDInsight. Mer information finns i [arkivåtkomstnivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Om du väljer att skydda ditt lagringskonto med den **brandväggar och virtuella nätverk** begränsningar för **valda nätverk**, måste du aktivera undantaget **Tillåt att betrodda Microsoft tjänster...**  så att HDInsight kan komma åt ditt storage-konto.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight
Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra strukturerade och ostrukturerade data i Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage-arkitektur")

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

De lagringskonton som definieras under skapandeprocessen och deras nycklar lagras i `%HADOOP_HOME%/conf/core-site.xml` på klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i filen core-site.xml. Du kan ändra den här inställningen med hjälp av [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop-strömmande och Apache Pig, kan innehålla en beskrivning av lagringskonton och metadata med dem. (För närvarande fungerar för Pig med lagringskonton, men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. I blobcontainrar förvaras data som nyckel/värde-par och det finns ingen kataloghierarki. Däremot kan snedstreck (/) användas i nyckelnamnet så att det visas som om det vore en fil som lagrats i en katalogstruktur. Nyckeln för en blob kan till exempel vara *input/log1.txt*. Det finns ingen faktisk katalog för *indata*, men eftersom det finns ett snedstreck i nyckelnamnet ser namnet ut som en filsökväg.

## <a id="benefits"></a>Fördelar med Azure Storage
Den implicerade Prestandakostnaden för att samordna inte beräkningskluster och lagringsresurser som annars skulle hindra sätt på vilket beräkningsklustren skapas nära lagringskontoresurserna i Azure-regionen där höghastighetsnätverket gör det effektivt på Compute-noder för att komma åt data i Azure storage.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Data återanvändning och delning av:** Data i HDFS lagras inuti beräkningsklustren. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure storage kan antingen nås via HDFS-API: erna eller via den [Blob Storage REST API: er](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.

* **Dataarkivering:** Lagra data i Azure storage kan de HDInsight-kluster som används för beräkning tryggt tas bort utan att förlora användardata.

* **Kostnad för datalagring:** Långsiktigt att lagra data i DFS är dyrare än att lagra data i Azure storage eftersom kostnaden för ett beräkningskluster är högre än kostnaden för Azure storage. Eftersom data inte behöver läsas in på nytt för varje generation av beräkningskluster sparar du dessutom kostnader för datainläsning.

* **Elastisk utskalning:** Även om HDFS ger dig ett utskalat filsystem, bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.

* **GEO-replikering:** Din Azure-lagring kan vara geo-replikerade. Även om detta ger dig geografisk återställning och dataredundans, innebär en växling till den geo-replikerade platsen en avsevärd försämring av prestandan och kan medföra ytterligare kostnader. Vår rekommendation är därför att tänka efter när du väljer geo-replikering och bara göra det om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till exempel `ls`, `copyFromLocal` och `mkdir`) fortfarande fungerar som förväntat. Endast de kommandon som är specifika för den interna HDFS-implementeringen (som kallas DFS), till exempel `fschk` och `dfsadmin`, visa olika beteenden i Azure storage.

## <a name="address-files-in-azure-storage"></a>Adressera filer i Azure Storage
Följande URI-schema används för att komma åt filer i Azure Storage från HDInsight:

```config
wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

URI-schemat ger okrypterad åtkomst (med prefixet *wasb:*) och SSL-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

Den `<BlobStorageContainerName>` identifierar namnet på blob-behållare i Azure storage.
Den `<StorageAccountName>` identifierar namnet på Azure Storage-kontot. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Om varken `<BlobStorageContainerName>` eller `<StorageAccountName>` har angetts används standardfilsystemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel kan du referera till den *hadoop-mapreduce-examples.jar*-fil som medföljer HDInsight-kluster på något av följande sätt:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Filnamnet är `hadoop-examples.jar` i HDInsight version 2.1 och 1.6 kluster.

Sökvägen är det fil eller katalog HDFS-sökvägsnamnet. Eftersom behållare i Azure storage är nyckel / värde-lager, finns det inga något faktiskt hierarkiskt filsystem. Ett snedstreck (/) i en blob-nyckel tolkas som en katalogavgränsare. Blob-namnet för *hadoop-mapreduce-examples.jar* är till exempel:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Blob-containrar
Om du vill använda blobar måste du först skapa en [Azure Storage-konto](../storage/common/storage-create-storage-account.md). Som en del av detta anger du en Azure-region där lagringskontot ska skapas. Klustret och lagringskontot måste finnas i samma region. SQL Server-databas för Hive metastore och SQL Server-databasen för Apache Oozie metastore måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en container på ditt Azure Storage-konto. Den här containern kan vara en befintlig blob som skapats utanför HDInsight eller en container som skapats för ett HDInsight-kluster.

Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-container. Detta kan skada jobbets historik. Du rekommenderas att använda en annan container för varje kluster och publicera delade data på ett konto för länkad lagring som anges vid distributionen av alla relevanta kluster snarare än på standardkontot för lagring. Mer information om hur du konfigurerar länkade lagringskonton finns i [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Du kan emellertid återanvända en standardcontainer för lagring när det ursprungliga HDInsight-klustret har tagits bort. När det gäller HBase-kluster kan du faktiskt behålla HBase-tabellschemat och data genom att skapa en ny blobcontainer som standard som använts av ett HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagera med Azure storage

Microsoft tillhandahåller följande verktyg för att arbeta med Azure Storage:

| Verktyg | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Använda ytterligare lagringskonton

När du skapar ett HDInsight-kluster kan du ange ett Azure Storage-konto som du vill koppla det till. Förutom det här lagringskontot kan du lägga till ytterligare lagringskonton från samma Azure-prenumeration eller andra Azure-prenumerationer under skapandeprocessen eller efter att ett kluster har skapats. Mer information om hur du lägger till ytterligare lagringskonton finns i [Skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Du kan inte använda ett annat lagringskonto på en annan plats än HDInsight-klustret.

## <a name="next-steps"></a>Nästa steg

I den här artikeln fick du lära dig hur du använder det HDFS-kompatibla Azure Storage med HDInsight. Du kan skapa skalbara, långsiktiga lösningar för arkivering av insamlade data samt HDInsight för att få tillgång till informationen i lagrade strukturerade och ostrukturerade data.

Mer information finns i:

* [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Kom igång med Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Ladda upp data till HDInsight](hdinsight-upload-data.md)
* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda Apache Pig med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använda Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)