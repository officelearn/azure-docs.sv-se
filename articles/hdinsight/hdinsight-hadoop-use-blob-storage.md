---
title: Fråga efter data i HDFS-kompatibelt Azure-lagringsutrymme – Azure HDInsight
description: Lär dig hur du frågar efter data från Azure Storage och Azure Data Lake Storage att lagra resultatet av din analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/01/2019
ms.openlocfilehash: d934568f09e62ad8c1b472583cbfee79d2c837f6
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936860"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Använda Azure-lagring med Azure HDInsight-kluster

För att analysera data i HDInsight-klustret kan du lagra data antingen i [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage gen 2](../storage/blobs/data-lake-storage-introduction.md)eller en kombination. Med dessa lagrings alternativ kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning utan att förlora användar data.

Apache Hadoop stöder en begreppet standard fil system. Standardfilsystemet kräver att ett standardschema och en utfärdare används. Det kan också användas för att matcha relativa sökvägar. Under skapande processen av HDInsight-kluster kan du ange en BLOB-behållare i Azure Storage som standard fil system, eller med HDInsight 3,6 kan du välja antingen Azure Storage eller Azure Data Lake Storage gen 1/Azure Data Lake Storage gen 2 som standardfiler system med några få undantag. För att kunna använda Data Lake Storage gen 1 som både standard-och länkad lagring, se [tillgänglighet för HDInsight-kluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

I den här artikeln får du lära dig hur Azure Storage fungerar med HDInsight-kluster. Information om hur Data Lake Storage gen 1 fungerar med HDInsight-kluster finns i [använda Azure Data Lake Storage med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-store.md). Mer information om hur du skapar ett HDInsight-kluster finns i [skapa Apache Hadoop kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage är en robust lagringslösning för allmänna ändamål som smidigt kan integreras med HDInsight. HDInsight kan använda en blobcontainer i Azure Storage som standardfilsystem för klustret. Genom ett gränssnitt för Hadoop-distribuerat filsystem (HDFS) kan alla komponenter i HDInsight tillämpas direkt på strukturerade eller ostrukturerade data som har lagrats som blobar.

> [!IMPORTANT]  
> Lagrings konto typen **BlobStorage** kan bara användas som sekundär lagring för HDInsight-kluster.

| Typ av lagringskonto | Tjänster som stöds | Prestanda nivåer som stöds | Åtkomst nivåer som stöds |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (generell användning v2)  | Blob     | Standard                    | Frekvent, låg frekvent, Arkiv\*   |
| Lagring (generell användning v1)   | Blob     | Standard                    | Gäller inte                    |
| BlobStorage                    | Blob     | Standard                    | Frekvent, låg frekvent, Arkiv\*   |

Vi rekommenderar inte att du använder standard-BLOB-behållaren för lagring av affärs data. Ta bort standardcontainern efter varje användning för att minska lagringskostnaden. Standard behållaren innehåller program-och system loggar. Se till att hämta loggarna innan du tar bort containern.

Delning av en BLOB-behållare som standard fil system för flera kluster stöds inte.

> [!NOTE]  
> Arkiv åtkomst nivån är en offline-nivå som har en svars tid på flera timmar och rekommenderas inte för användning med HDInsight. Mer information finns i [Arkiv åtkomst nivå](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Om du väljer att skydda ditt lagrings konto med **brand väggar och begränsningar för virtuella nätverk** på **valda nätverk**måste du aktivera undantaget **Tillåt betrodda Microsoft-tjänster...** så att HDInsight kan komma åt din lagring redovisning.

## <a name="hdinsight-storage-architecture"></a>Lagringsarkitekturen i HDInsight

Följande diagram visar en abstrakt vy av lagringsarkitekturen i HDInsight med Azure Storage:

![Hadoop-kluster använder HDFS API för att komma åt och lagra data i Blob Storage](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "HDInsight Storage arkitektur")

HDInsight ger tillgång till det distribuerade filsystemet som är lokalt anslutet till beräkningsnoderna. Detta filsystem kan nås med hjälp av den fullständigt kvalificerade URI-strängen, till exempel:

    hdfs://<namenodehost>/<path>

Dessutom ger HDInsight möjlighet att komma åt data som är lagrade i Azure Storage. Syntax:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

Här är några saker att tänka på när du använder Azure Storage-konton med HDInsight-kluster.

* **Behållare i de lagrings konton som är anslutna till ett kluster:** Eftersom konto namnet och nyckeln är associerade med klustret när de skapas har du fullständig åtkomst till Blobbarna i dessa behållare.

* **Offentliga behållare eller offentliga blobbar i lagrings konton som inte är anslutna till ett kluster:** Du har skrivskyddad behörighet till Blobbarna i behållarna.
  
> [!NOTE]  
> Offentliga containrar låter dig hämta en lista över alla blobar som är tillgängliga i containern samt hämta metadata för containern. Du kan endast komma åt offentliga blobar om du känner till den exakta webbadressen. Mer information finns i [Hantera åtkomst till behållare och blobbar](../storage/blobs/storage-manage-access-to-resources.md).

* **Privata behållare i lagrings konton som inte är anslutna till ett kluster:** Du har inte åtkomst till Blobbarna i behållarna om du inte definierar lagrings kontot när du skickar WebHCat-jobb. Detta beskrivs senare i artikeln.

De lagrings konton som definieras i skapande processen och deras nycklar lagras `%HADOOP_HOME%/conf/core-site.xml` på klusternoderna. Standardbeteendet för HDInsight är att använda de lagringskonton som definieras i filen core-site.xml. Du kan ändra den här inställningen med [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Flera WebHCat-jobb, inklusive Apache Hive, MapReduce, Apache Hadoop streaming och Apache gris, kan innehålla en beskrivning av lagrings konton och metadata för dem. (För närvarande fungerar för Pig med lagringskonton, men inte för metadata.) Mer information finns i [Använda ett HDInsight-kluster med alternativa lagringskonton och metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobar kan användas för strukturerade och ostrukturerade data. BLOB-behållare lagrar data som nyckel/värde-par, och det finns ingen katalogpartition. Däremot kan snedstreck (/) användas i nyckelnamnet så att det visas som om det vore en fil som lagrats i en katalogstruktur. Nyckeln för en blob kan till exempel vara *input/log1.txt*. Det finns ingen faktisk katalog för *indata*, men eftersom det finns ett snedstreck i nyckelnamnet ser namnet ut som en filsökväg.

## <a id="benefits"></a>Fördelar med Azure Storage

Den underförstådda prestanda kostnaden för att inte placera beräknings kluster och lagrings resurser begränsas av hur beräknings klustren skapas nära lagrings konto resurserna i Azure-regionen, där det snabba nätverket gör det effektivt för Compute-noder för att få åtkomst till data i Azure Storage.

Det finns flera fördelar med att lagra data i Azure Storage i stället för i HDFS:

* **Åter användning och delning av data:** Data i HDFS finns i beräknings klustret. Endast de program som har åtkomst till beräkningsklustren kan använda dessa data med hjälp av HDFS-API:er. Data i Azure Storage kan nås via HDFS-API: erna eller via [Blob Storage REST-API: er](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Därmed kan en större grupp program (inklusive andra HDInsight-kluster) och verktyg användas för att skapa och använda data.

* **Data arkivering:** Genom att lagra data i Azure Storage kan HDInsight-kluster som används för beräkning vara säkert borttagna utan att användar data går förlorade.

* **Kostnad för data lagring:** Att lagra data i DFS för lång sikt är dyrare än att lagra data i Azure Storage eftersom kostnaden för ett beräknings kluster är högre än kostnaden för Azure Storage. Eftersom data inte behöver läsas in på nytt för varje beräknings kluster skapas dessutom även kostnader för inläsning av data.

* **Elastisk skalbarhet:** Även om HDFS ger dig ett skalbart fil system, bestäms skalan av antalet noder som du skapar för klustret. Att ändra skala med HDFS kan vara en mer komplicerad process än att använda de elastiska skalningsfunktionerna som du automatiskt har tillgång till i Azure Storage.

* **Geo-replikering:** Azure-lagringen kan vara geo-replikerad. Även om detta ger dig geografisk återställning och dataredundans, innebär en växling till den geo-replikerade platsen en avsevärd försämring av prestandan och kan medföra ytterligare kostnader. Vår rekommendation är därför att tänka efter när du väljer geo-replikering och bara göra det om värdet i informationen motiverar den extra kostnaden.

Vissa MapReduce-jobb och -paket kan skapa mellanresultat som du inte egentligen vill lagra i Azure Storage. I så fall kan du välja att lagra data i ditt lokala HDFS. Faktum är att HDInsight använder DFS för flera av dessa mellanresultat i Hive-jobb och andra processer.

> [!NOTE]  
> De flesta HDFS-kommandon (till `ls`exempel `copyFromLocal` , `mkdir`och) fungerar fortfarande som förväntat. Endast de kommandon som är specifika för den inbyggda HDFS-implementeringen (som kallas DFS), till exempel `fschk` och `dfsadmin`, visar olika beteenden i Azure Storage.

## <a name="address-files-in-azure-storage"></a>Adressera filer i Azure Storage

Följande URI-schema används för att komma åt filer i Azure Storage från HDInsight:

```config
wasbs://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

URI-schemat ger okrypterad åtkomst (med prefixet *wasb:* ) och SSL-krypterad åtkomst (med *wasbs*). Vi rekommenderar att du använder *wasbs* när det är möjligt, även för åtkomst till data som finns i samma region i Azure.

`<BlobStorageContainerName>` Identifierar namnet på BLOB-behållaren i Azure Storage.
`<StorageAccountName>` Identifierar Azure Storage kontots namn. Ett fullständigt kvalificerat domännamn (FQDN) krävs.

Om varken `<BlobStorageContainerName>` eller `<StorageAccountName>` har angetts används standard fil systemet. För filer i filsystemet kan du använda en relativ sökväg eller en absolut sökväg. Till exempel kan du referera till den *hadoop-mapreduce-examples.jar*-fil som medföljer HDInsight-kluster på något av följande sätt:

```config
wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasbs:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Fil namnet är `hadoop-examples.jar` i HDInsight-versionerna 2,1 och 1,6-kluster.

Sökvägen är Sök vägs namnet för filen eller katalogen HDFS. Eftersom behållare i Azure Storage är nyckel värdes lager finns det inget faktiskt hierarkiskt fil system. Ett snedstreck (/) i en blob-nyckel tolkas som en katalogavgränsare. Blob-namnet för *hadoop-mapreduce-examples.jar* är till exempel:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> När du arbetar med blobar utanför HDInsight kan de flesta verktyg inte identifiera WASB-formatet utan förväntar sig i stället ett grundläggande sökvägsformat som `example/jars/hadoop-mapreduce-examples.jar`.

## <a name="blob-containers"></a>Blob-containrar

Om du vill använda blobbar skapar du först ett [Azure Storage-konto](../storage/common/storage-create-storage-account.md). Som en del av detta anger du en Azure-region där lagringskontot ska skapas. Klustret och lagringskontot måste finnas i samma region. Hive-metaarkiv SQL Server Database och Apache Oozie metaarkiv SQL Server Database måste också finnas i samma region.

Oavsett var den finns tillhör varje blob som du skapar en container på ditt Azure Storage-konto. Den här containern kan vara en befintlig blob som skapats utanför HDInsight eller en container som skapats för ett HDInsight-kluster.

Standardcontainern lagrar klusterspecifik information, till exempel jobbhistorik och loggar. Låt inte flera HDInsight-kluster dela en standardblob-container. Detta kan skada jobbets historik. Vi rekommenderar att du använder en annan behållare för varje kluster och lagrar delade data på ett länkat lagrings konto som anges vid distribution av alla relevanta kluster i stället för standard lagrings kontot. Mer information om hur du konfigurerar länkade lagrings konton finns i [skapa HDInsight-kluster](hdinsight-hadoop-provision-linux-clusters.md). Du kan emellertid återanvända en standardcontainer för lagring när det ursprungliga HDInsight-klustret har tagits bort. För HBase-kluster kan du faktiskt behålla HBase-tabellens schema och data genom att skapa ett nytt HBase-kluster med hjälp av standard-BLOB-behållaren som används av ett HBase-kluster som har tagits bort.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interagera med Azure Storage

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
* [Använda Apache gris med HDInsight](hadoop/hdinsight-use-pig.md)
* [Använd Azure Storage signaturer för delad åtkomst för att begränsa åtkomsten till data med HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](hdinsight-hadoop-use-data-lake-storage-gen2.md)
