---
title: Installationen av kluster för Hadoop, Spark, Kafka, HBase eller R-Server – Azure HDInsight
description: Ställ in Hadoop-, Kafka, Spark, HBase, R Server eller Storm-kluster för HDInsight från en webbläsare, Azure CLI, Azure PowerShell, REST eller SDK.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: f1c42a3e091bc4b3804edfe835e521a99fcee880
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975069"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>Snabbstart: Konfigurera kluster i HDInsight

I den här snabbstarten du lär dig hur du skapar och konfigurerar kluster i HDInsight med Hadoop, Spark, Kafka, Interactive Query, HBase, R Server eller Storm. Lär dig också att anpassa kluster, ansluta dem till en domän och kopplar dem till ett lagringskonto med [förhandsversion av Azure Data Lake Storage Gen2](data-lake-storage-introduction.md) aktiverat.

Ett Hadoop-kluster består av flera virtuella datorer (noder) som används för distribuerad bearbetning av uppgifter. Azure HDInsight hanterar implementeringsdetaljer för installation och konfiguration av enskilda noder, så du behöver bara ange allmänna konfigurationsinformation.

> [!IMPORTANT]
>Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du [ta bort ett kluster.](../../hdinsight/hdinsight-delete-cluster.md)

Ett lagringskonto med Data Lake Storage Gen2 funktioner används som datalager i den här snabbstarten. Med sin tjänst för hierarkiskt namnområde och [Hadoop-drivrutinen](data-lake-storage-abfs-driver.md), Data Lake Storage Gen2 är optimerad för distribuerad bearbetning och analys. Data som lagras i ett lagringskonto som har Data Lake Storage Gen2 aktiverat kvarstår även efter ett HDInsight-klustret tas bort.

## <a name="cluster-setup-methods"></a>Metoder för installation av kluster

I följande tabell visas de olika metoderna som du kan använda för att konfigurera ett HDInsight-kluster.

| Kluster som skapas med | Webbläsare | Kommandorad | REST-API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](../../hdinsight/hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI (version 1.0)](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](../../hdinsight/hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-mallar](../../hdinsight/hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Snabbregistrering: grundläggande konfiguration

Den här artikeln vägleder dig genom installationen i den [Azure-portalen](https://portal.azure.com), där du kan skapa ett HDInsight-kluster med *Snabbregistrering* eller *anpassad*.

![hdinsight skapar alternativ anpassade Snabbregistrering](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

Följ instruktionerna på skärmen för att göra en grundläggande konfiguration. Detaljerade anvisningar nedan för:

* [Resursgruppens namn](#resource-group-name)
* [Klustertyper och konfiguration](#cluster-types) 
* [Klusterinloggning och SSH-användarnamn](#cluster-login-and-ssh-user-name)
* [Plats](#location)

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight 3.3 tillbakadragande](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="resource-group-name"></a>Namn på resursgrupp

[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) hjälper dig att arbeta med resurserna i ditt program som en grupp som kallas en Azure-resursgrupp. Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser för ditt program i en enda samordnad åtgärd.

## <a name="cluster-types"></a> Klustertyper och konfiguration

Azure HDInsight ger för närvarande följande klustertyper, var och en med en uppsättning komponenter att tillhandahålla vissa funktioner.

> [!IMPORTANT]
> HDInsight-kluster finns i olika typer för en enskild arbetsbelastning eller teknik. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster. Om din lösning kräver tekniker som är fördelade på flera HDInsight-klustertyper en [Azure-nätverk](https://docs.microsoft.com/azure/virtual-network) kan ansluta vilka krävs för klustret.

| Klustertyp | Funktioner |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Batchfråga och analys av lagrade data |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |Bearbetar stora mängder schemalös, NoSQL-data |
| [Interaktiv fråga](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |Minnesintern cachelagring för interaktiva och snabba Hive-frågor |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | En distribuerad strömningsplattform som kan användas för att skapa realtidsuppspelade datapipelines och program |
| [R Server](../../hdinsight/r-server/r-server-overview.md) |Olika stordatastatistik, förutsägande modellering och maskininlärningsfunktioner |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |Minnesintern bearbetning, interaktiva frågor, bearbetning av mindre batchar dataströmmen |
| [Storm](../../hdinsight/storm/apache-storm-overview.md) |Händelsebearbetning i realtid |

### <a name="hdinsight-version"></a>HDInsight-version

Välj versionen av HDInsight för det här klustret. Mer information finns i [stöds HDInsight versioner](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

För mer information om hur du skapar domänanslutna HDInsight-kluster, se [skapa domänanslutna HDInsight testmiljö](../../hdinsight/domain-joined/apache-domain-joined-configure.md).

## <a name="cluster-login-and-ssh-user-name"></a>Klusterinloggning och SSH-användarnamn

Du kan konfigurera två användarkonton när klustret skapas med HDInsight-kluster:

* HTTP-användare: Standardanvändarnamnet är *admin*. Den använder den grundläggande konfigurationen på Azure portal. Ibland kallas ”kluster användare”.
* SSH-användare (Linux-kluster): används för att ansluta till klustret via SSH. Mer information finns i [Use SSH with HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

## <a name="location"></a>Plats (regioner) för kluster och lagring

Du behöver inte ange klusterplatsen uttryckligen; klustret är på samma plats som standardlagring. En lista över regioner som stöds, klickar du på den **Region** listrutan på [HDInsight priser](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Storage-slutpunkter för kluster

Även om en lokal installation av Hadoop använder Hadoop Distributed File System (HDFS) för lagring på klustret, i molnet använder storage-slutpunkter som är anslutna till klustret. HDInsight-kluster använder antingen [Data Lake Storage Gen2](data-lake-storage-abfs-driver.md) eller [Blobar i Azure Storage](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). Azure Storage eller Azure Data Lake Storage innebär att du kan ta bort HDInsight-kluster som används för beräkning utan att försämra dina data.

> [!WARNING]
> Med ett annat lagringskonto i en annan plats än HDInsight-kluster stöds inte.

Under konfigurationen för lagringsslutpunkten standard anger du Data Lake Storage. Standardlagring innehåller program- och loggar. Alternativt kan du ange ytterligare länkade lagringskonton som har Data Lake Storage Gen2 aktiverat att klustret kan komma åt. HDInsight-kluster och beroende storage-konton måste vara i samma Azure-plats.

![Inställningar för lagring: HDFS-kompatibla slutpunkter för lagring](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage2.png)

> [!IMPORTANT]
> Se till att **inaktivera Data Lake Store-åtkomst**. Den här inställningen refererar till gammalt *Data Lake Store* funktioner och behöver inaktiveras för *Data Lake Storage* funktioner ska fungera korrekt.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Valfritt metastores

Du kan skapa valfritt metastores för Hive- eller Oozie. Dock inte samtliga klustertyper stöder metastores och Azure SQL Data Warehouse är inte kompatibel med metastores.

Mer information finns i [använda extern metadatalagring i Azure HDInsight](../../hdinsight/hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> När du skapar ett anpassat metaarkiv kan inte använda bindestreck, bindestreck och blanksteg i namnet på databasen. Detta kan orsaka klusterskapningsprocessen misslyckas.

### <a name="use-hiveoozie-metastore"></a>Hive-metaarkiv

Om du vill behålla dina Hive-tabeller när du har tagit bort ett HDInsight-kluster kan du använda ett anpassat metaarkiv. Du kan sedan kopplar metaarkiv till ett annat HDInsight-kluster.

En HDInsight-metastore som har skapats för ett HDInsight-kluster av version kan inte delas mellan olika versioner av HDInsight-kluster. En lista över versioner som HDInsight finns i [stöds HDInsight versioner](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie-metaarkiv

Använd en anpassad metaarkiv för att öka prestandan när du använder Oozie. Ett metaarkiv kan också ge åtkomst till Oozie jobbdata när du har tagit bort ditt kluster.

> [!IMPORTANT]
> Du kan inte återanvända ett anpassat Oozie-metaarkiv. Om du vill använda ett anpassat Oozie-metaarkiv, måste du ange en tom Azure SQL-databas när du skapar HDInsight-kluster.

## <a name="configure-cluster-size"></a>Konfigurera klusterstorlek

Du debiteras för användning av noden för så länge klustret finns. Debiteringen börjar när ett kluster skapas och stoppas när klustret tas bort. Kluster kan inte avallokeras eller läggas i beredskap.

### <a name="number-of-nodes-for-each-cluster-type"></a>Antalet noder för varje typ av kluster

Varje typ av kluster har en egen antal noder, terminologi för noder och standardstorleken för virtuella datorer. I följande tabell är antalet noder för varje nodtyp inom parentes.

| Typ | Noder | Diagram |
| --- | --- | --- |
| Hadoop |Huvudnod (2), datanod (1 +) |![HDInsight Hadoop-klusternoder](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Huvud-server (2), regionsserver (1 +), master-/ ZooKeeper-noder (3) |![HDInsight HBase-klusternoder](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus-nod (2), övervakaren server (1 +), ZooKeeper-nod (3) |![Storm för HDInsight-klusternoder](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| Spark |Huvudnod (2), arbetsnod (1 +), ZooKeeper-nod (3) (kostnadsfritt för A1 ZooKeeper VM-storlek) |![Noder i HDInsight Spark](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

Mer information finns i [standard noden konfiguration och VM-storlekar för kluster](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) i ”vad är Hadoop-komponenter och versionshantering i HDInsight”?

Kostnaden för HDInsight-kluster bestäms av antalet noder och VM-storlekar för noderna.

Olika klustertyper har olika nodtyper, antal noder och storleken på:
* Standardtyp för Hadoop-kluster:
    * Två *huvudnoder*  
    * Fyra *datanoder*
* Standardtyp för storm-kluster:
    * Två *Nimbus-noder*
    * Tre *ZooKeeper-noder*
    * Fyra *överordnade noder*

Om du bara provar HDInsight, rekommenderar vi att du använder en datanod. Läs mer om priserna för HDInsight, [HDInsight priser](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Storleksgräns för klustret varierar mellan olika Azure-prenumerationer. Kontakta [Azure faktureringshjälp](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) att öka gränsen.

När du använder Azure-portalen för att konfigurera klustret, nodstorlek är tillgänglig via den **noden prisnivåer** bladet. Du kan också se den kostnad som hör till olika nodstorlekar i portalen.

![Storleken på HDInsight VM](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

När du distribuerar ett kluster, Välj dataresurser baserade på lösningen som du planerar att distribuera. Följande virtuella datorer används för HDInsight-kluster:

* A och virtuella datorer i D1 – 4 serien: [General-purpose Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14 virtuella datorer i serien: [minnesoptimerade Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Att ta reda på vilket värde som du ska använda för att ange en VM-storlek när du skapar ett kluster med de olika SDK: er eller när du använder Azure PowerShell, se [storlekar som ska användas för HDInsight-kluster](../../cloud-services/cloud-services-sizes-specs.md#size-tables). Från den här länkade artikeln använder du värdet i den **storlek** kolumnen av tabellerna.

> [!IMPORTANT]
> Om du behöver mer än 32 arbetarnoder i ett kluster, måste du välja en head nodstorlek med minst 8 kärnor och 14 GB RAM-minne.

Mer information finns i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md). Information om prissättning i olika storlekar finns i [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="custom-cluster-setup"></a>Anpassad konfiguration

Anpassade kluster installationen bygger på Snabbstartsidan skapa inställningar och lägger till följande alternativ:

- [HDInsight-program](#hdinsight-applications)
- [Klusterstorlek](#cluster-size)
- Avancerade inställningar
  - [Skriptåtgärder](#customize-clusters-using-script-action)
  - [Virtuellt nätverk](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Installera HDInsight-program i kluster

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Du kan använda program förutsatt av Microsoft, tredje part eller som du utvecklar själv. Mer information finns i [installera från tredje part Hadoop-program på Azure HDInsight](../../hdinsight/hdinsight-apps-install-applications.md).

De flesta av HDInsight-program är installerade på en tom edge-nod.  En tom edge-nod är en Linux-dator med samma klientverktyg installeras och konfigureras enligt huvudnoden. Du kan använda gränsnoden för åtkomst till klustret, testa dina klientprogram och som är värd för dina klientprogram. Mer information finns i [använda tomma kantnoder i HDInsight](../../hdinsight/hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Avancerade inställningar: skriptåtgärder

Du kan installera ytterligare komponenter eller anpassa klusterkonfigurationen med hjälp av skript när du skapar. Skripten anropas **skriptåtgärd**, vilket är ett konfigurationsalternativ som kan användas från Azure-portalen, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärd](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md).

Vissa interna Java-komponenter som Mahout och kaskad, kan köras på klustret som Java Arkiv (JAR) filer. De här JAR-filerna kan distribueras till Azure Storage eller Azure Data Lake Storage och skickas till HDInsight-kluster med Hadoop-jobb skickas mekanismer. Mer information finns i [skicka Hadoop-jobb programmässigt](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Om du har problem när du distribuerar JAR-filerna till HDInsight-kluster eller anropa JAR-filerna på HDInsight-kluster, kontakta [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Sammanhängande stöds inte av HDInsight och är inte berättigade till Microsoft Support. För listor över stödda komponenter som finns i [vad är nytt i klusterversionerna från HDInsight](../../hdinsight/hdinsight-component-versioning.md).

Ibland vill du konfigurera följande konfigurationsfilerna under skapandeprocessen:

* clusterIdentity.xml
* Core-site.xml
* gateway.XML
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-plats
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Mer information finns i [anpassa HDInsight-kluster med Bootstrap](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Avancerade inställningar: utöka kluster med ett virtuellt nätverk

Om din lösning kräver tekniker som är fördelade på flera HDInsight-klustertyper en [Azure-nätverk](https://docs.microsoft.com/azure/virtual-network) kan ansluta vilka krävs för klustret. Den här konfigurationen ger klustren, och all kod som du distribuerar till dem, kan kommunicera direkt med varandra.

Mer information om hur du använder Azure-nätverk med HDInsight finns i [utöka HDInsight med Azure-nätverk](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

Ett exempel på hur du använder två klustertyper inom en Azure-nätverk finns i [Använd Spark Structured Streaming med Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md). Läs mer om hur du använder HDInsight med ett virtuellt nätverk, inklusive konfigurationskraven för det virtuella nätverket, [utöka HDInsight-funktioner med hjälp av Azure Virtual Network](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Felsöka åtkomstproblem för kontroll

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../../hdinsight/hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg

- [ABFS Hadoop-filsystem-drivrutin för Azure Data Lake Storage Gen2](data-lake-storage-abfs-driver.md)
- [Självstudie: Extrahera, transformera och läsa in data med Apache Hive i HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
- [Vad är HDInsight, Hadoop-ekosystemet och Hadoop-kluster?](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [Komma igång med Hadoop i HDInsight](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Arbeta i Hadoop på HDInsight från Windows PC](../../hdinsight/hdinsight-hadoop-windows-tools.md)
