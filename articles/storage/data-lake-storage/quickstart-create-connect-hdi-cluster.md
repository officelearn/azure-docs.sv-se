---
title: Installationsprogrammet för Hadoop, Spark, Kafka, HBase eller R - Server i Azure HDInsight-kluster
description: Konfigurera Hadoop, Kafka, Spark, HBase, R Server eller Storm-kluster för HDInsight från en webbläsare, Azure CLI, Azure PowerShell, REST eller SDK.
keywords: konfiguration av hadoop, kafka kluster installationsprogrammet, spark-kluster installationsprogrammet vad är i hadoop-kluster
services: hdinsight
documentationcenter: ''
author: jamesbak
manager: jahogg
tags: azure-portal
ms.component: data-lake-storage-gen2
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 6707610fc3410eab6eb2985c7d8a0ef821354689
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062087"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>Snabbstart: Skapa kluster i HDInsight

I den här snabbstarten du lär dig hur du skapar och konfigurerar kluster i HDInsight med Hadoop, Spark, Kafka, interaktiva frågan, HBase, R Server eller Storm. Du också lära dig att anpassa kluster, Anslut dem till en domän och koppla till [Azure Data Lake lagring Gen2 Preview](introduction.md).

Ett Hadoop-kluster består av flera virtuella datorer (noder) som används för distribuerad bearbetning av uppgifter. Azure HDInsight hanterar implementeringsinformation om installation och konfiguration av enskilda noder, så du behöver bara ange allmänna konfigurationsinformation.

> [!IMPORTANT]
>Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du [tar bort ett kluster.](../../hdinsight/hdinsight-delete-cluster.md)

Lagring av data Lake används som datalager i denna Snabbstart. Med dess hierarkiska Namespace-tjänsten och [Hadoop-drivrutinen](abfs-driver.md), lagring av Data Lake är optimerad för distribuerad bearbetning och analys. Data som lagras i lagring av Data Lake kvarstår även efter ett HDInsight-kluster har tagits bort.

## <a name="cluster-setup-methods"></a>Installationsmetoder för kluster

I följande tabell visas de olika metoder som du kan använda för att ställa in ett HDInsight-kluster.

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

![hdinsight skapa alternativ anpassade Snabbregistrering](media/quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

Följ anvisningarna på skärmen för att göra en grundläggande konfiguration. Information ges nedan för:

* [Resursgruppens namn](#resource-group-name)
* [Klustertyper och konfiguration](#cluster-types) 
* [Klustrets inloggningsnamn och SSH-användarnamn](#cluster-login-and-ssh-username)
* [Plats](#location)

> [!IMPORTANT]
> Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight 3.3 pensionering](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="resource-group-name"></a>Resursgruppsnamn

[Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) hjälper dig att arbeta med resurserna i ditt program som en grupp som kallas en Azure-resursgrupp. Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser i programmet i en enda, samordnad åtgärd.

## <a name="cluster-types"></a> Klustertyper och konfiguration
Azure HDInsight innehåller för närvarande följande typer av klustret, var och en med en uppsättning komponenter att tillhandahålla vissa funktioner.

> [!IMPORTANT]
> HDInsight-kluster finns i olika typer för en enda arbetsbelastning eller teknik. Det finns ingen stöds metod för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase på ett kluster. Om din lösning kräver tekniker som är fördelade på flera typer för HDInsight-kluster, en [virtuella Azure-nätverket](https://docs.microsoft.com/azure/virtual-network) kan ansluta vilka krävs för klustret. 
>
>

| Klustertyp | Funktioner |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Batch-fråga och analys av lagrade data |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |För stora mängder schemalös, NoSQL-data bearbetades. |
| [Interaktiva fråga](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |Cachelagra i minnet för interaktiva och snabbare Hive-frågor |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | En distribuerad strömmande plattform som kan användas för att skapa realtid strömmande data pipelines och program |
| [R Server](../../hdinsight/r-server/r-server-overview.md) |Olika stordata statistik, förutsägelsemodellering och maskininlärning funktioner |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |Minnesintern bearbetning, interaktiva frågor dataströmmen micro batch-bearbetning |
| [Storm](../../hdinsight/storm/apache-storm-overview.md) |Händelsebearbetning i realtid |

### <a name="hdinsight-version"></a>HDInsight-version

Välj versionen av HDInsight för det här klustret. Mer information finns i [stöds HDInsight-versioner](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="enterprise-security-package"></a>Enterprise-säkerhetspaketet

Hadoop, Spark och interaktiva frågor klustertyper du vill aktivera den **Enterprise säkerhetspaketet**. Det här paketet ger möjlighet att ha en säkrare konfiguration genom att använda Apache Ranger och integrera med Azure Active Directory. Mer information finns i [Enterprise säkerhetspaketet i Azure HDInsight](../../hdinsight/domain-joined/apache-domain-joined-introduction.md).

![hdinsight skapa alternativ väljer enterprise säkerhetspaketet](./media/quickstart-create-connect-hdi-cluster/hdinsight-creation-enterprise-security-package.png)

Mer information om hur du skapar domänanslutna HDInsight-kluster, se [skapa domänanslutna HDInsight begränsat läge](../../hdinsight/domain-joined/apache-domain-joined-configure.md).

## <a name="cluster-login-and-ssh-user-name"></a>Klustrets inloggningsnamn och SSH-användarnamn

Du kan konfigurera två användarkonton när klustret skapas med HDInsight-kluster:

* HTTP-användare: Standardanvändarnamnet är *admin*. Den grundläggande konfigurationen används på Azure-portalen. Den kallas ibland ”Cluster användare”.
* SSH-användare (Linux-kluster): används för att ansluta till klustret via SSH. Mer information finns i [Use SSH with HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

Säkerhetspaketet Enterprise kan du integrera HDInsight med Active Directory och Apache Ranger. Flera användare kan skapas med hjälp av säkerhetspaketet Enterprise.

## <a name="location"></a>Plats (regioner) för kluster och lagring

Du behöver inte ange klusterplatsen explicit; klustret är på samma plats som standardlagring. En lista över regioner som stöds, klickar du på den **Region** listrutan på [HDInsight priser](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Storage-slutpunkter för kluster

Även om en lokal installation av Hadoop använder Hadoop Distributed File System (HDFS) för lagring på klustret, i molnet använder du lagring slutpunkter som är anslutna till klustret. HDInsight-kluster använder antingen [Data Lake lagring Gen2](abfs-driver.md) eller [Blobbar i Azure Storage](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). Azure Storage eller lagring av Data Lake innebär kan du ta bort HDInsight-kluster som används för beräkning samtidigt behålla dina data.

> [!WARNING]
> Med ett konto för ytterligare lagringsutrymme på en annan plats i HDInsight-kluster stöds inte.

Under konfigurationen för standardslutpunkten lagring anger du lagring av Data Lake. Standard-lagringen innehåller program- och loggar. Alternativt kan ange du ytterligare länkade Azure Data Lake Storage-konton som har åtkomst till klustret. HDInsight-klustret och beroende storage-konton måste vara i samma Azure-plats.

![Inställningar för lagring: HDFS-kompatibla lagring slutpunkter](media/quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage-TEMP.png)

> [!IMPORTANT]
> Se till att **inaktivera Data Lake Store-åtkomst**. Inställningarna som refererar till gammalt *Datasjölager* funktioner och måste inaktiveras för *lagring av Data Lake* funktioner ska fungera korrekt.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Valfritt metastores
Du kan skapa valfria Hive- eller Oozie metastores. Men inte alla klustertyper stöder metastores och Azure SQL Data Warehouse är inte kompatibelt med metastores. 

Mer information finns i [använda externa metadata lagras i Azure HDInsight](../../hdinsight/hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> När du skapar en anpassad metastore, Använd inte bindestreck, bindestreck och blanksteg i databasens namn. Detta kan orsaka klusterskapandeprocessen misslyckas.

### <a name="use-hiveoozie-metastore"></a>Hive metastore

Om du vill behålla dina Hive-tabeller när du har tagit bort ett HDInsight-kluster kan du använda en anpassad metastore. Du kan sedan bifoga metastore till en annan HDInsight-kluster.

Ett HDInsight-metastore som har skapats för ett HDInsight-kluster av version kan inte delas mellan olika versioner av HDInsight-kluster. En lista över HDInsight-versioner, se [stöds HDInsight-versioner](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie metastore

Använda en anpassad metastore för att öka prestandan när du använder Oozie. En metastore kan också ge åtkomst till Oozie jobbdata när du tar bort klustret. 

> [!IMPORTANT]
> Du kan inte återanvända en anpassad Oozie metastore. Om du vill använda en anpassad Oozie metastore måste du ange en tom Azure SQL Database när du skapar HDInsight-kluster.

## <a name="configure-cluster-size"></a>Konfigurera klustrets storlek

Du debiteras för användning av noden för länge klustret finns. Faktureringen påbörjas när ett kluster skapas och stoppas när klustret har tagits bort. Kluster kan inte frigöra allokerade eller spärra.


### <a name="number-of-nodes-for-each-cluster-type"></a>Antalet noder för varje typ av kluster
Varje typ av kluster har sin egen antal noder, terminologi för noderna och standard VM-storlek. I följande tabell är antalet noder för varje nodtyp inom parentes.

| Typ | Noder | Diagram |
| --- | --- | --- |
| Hadoop |Huvudnod (2), datanoden (1 +) |![HDInsight Hadoop klusternoder](media/quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |HEAD-server (2), region-server (1 +), master/ZooKeeper-noden (3) |![HDInsight HBase klusternoder](media/quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus-noden (2), handledarens server (1 +), ZooKeeper-noden (3) |![Storm på HDInsight-klusternoder](media/quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| Spark |Huvudnod (2), arbetsnoden (1 +), ZooKeeper-noden (3) (kostnadsfritt för A1 ZooKeeper VM-storlek) |![HDInsight Spark klusternoder](media/quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

Mer information finns i [standard nod konfiguration och virtuella storlekar för kluster](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) i ”vad är Hadoop-komponenter och versioner i HDInsight”?

Kostnaden för HDInsight-kluster bestäms av antalet noder och storlekar för virtuella datorer för noderna. 

Olika klustertyper har olika nodtyper, antal noder och noden storlekar:
* Standardtyp för Hadoop-kluster: 
    * Två *huvudnoder*  
    * Fyra *datanoder*
* Standardtyp för storm-kluster: 
    * Två *Nimbus-noder*
    * Tre *ZooKeeper-noder*
    * Fyra *handledarens noder* 

Om du försöker bara i HDInsight, rekommenderar vi att du använder en datanod. Läs mer om prissättningen för HDInsight [HDInsight priser](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Storleksgräns för klustret varierar mellan olika Azure-prenumerationer. Kontakta [Azure faktureringssupporten](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) att höja gränsen.
>

När du använder Azure-portalen för att konfigurera klustret, nodstorlek är tillgänglig via den **nods prisnivå** bladet. Du kan också se kostnaden som är associerad med en annan nod storlekar i portalen. 

![HDInsight VM nod storlekar](media/quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer 
När du distribuerar kluster, Välj beräkningsresurser baserat på om du planerar att distribuera lösningen. Följande virtuella datorer används för HDInsight-kluster:
* A och D1 4 serien VMs: [General-purpose Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11 14 serien VM: [minnesoptimerade Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Ta reda på vilket värde som du ska använda för att ange en VM-storlek när du skapar ett kluster med en annan SDK eller när du använder Azure PowerShell, se [VM-storlekar för HDInsight-kluster](../../cloud-services/cloud-services-sizes-specs.md#size-tables). Använd värdet i från den här länkade artikeln i **storlek** kolumn i tabellerna.

> [!IMPORTANT]
> Om du behöver mer än 32 worker-noder i ett kluster, måste du välja en huvudnod storlek med minst 8 kärnor och 14 GB RAM-minne.
>
>

Mer information finns i [storlekar för virtuella datorer](../../virtual-machines/windows/sizes.md). Information om priser med olika storlekar, se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Anpassad konfiguration
Anpassade kluster installationen bygger på Snabbstartsidan skapa inställningar och lägger till följande alternativ:
- [HDInsight-program](#hdinsight-applications)
- [Klusterstorleken](#cluster-size)
- Avancerade inställningar
  - [Skriptåtgärder](#customize-clusters-using-script-action)
  - [Virtuellt nätverk](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Installera HDInsight-program i kluster

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Du kan använda program, under förutsättning av Microsoft, tredje part eller som du utvecklar själv. Mer information finns i [installera Hadoop-program från tredje part på Azure HDInsight](../../hdinsight/hdinsight-apps-install-applications.md).

De flesta av HDInsight-program är installerade på en tom kantnod.  En tom kantnod är en virtuell Linux-dator med samma klientverktygen installeras och konfigureras som huvudnoden. Du kan använda kantnoden för åtkomst till klustret, testa ditt klientprogram och värd för ditt program. Mer information finns i [använda tom edge-noder i HDInsight](../../hdinsight/hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Avancerade inställningar: Script åtgärder

Du kan installera ytterligare komponenter eller anpassa klustrets konfiguration med hjälp av skript när du skapar. Dessa skript anropas **skriptåtgärd**, vilket är ett konfigurationsalternativ som kan användas från Azure-portalen, HDInsight Windows PowerShell-cmdlets eller HDInsight .NET SDK. Mer information finns i [anpassa HDInsight-kluster med skriptåtgärder](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md).

Vissa inbyggda Java-komponenter som Mahout och kaskad, kan köras i klustret som Java Arkiv (JAR) filer. Dessa JAR-filer kan distribueras till Azure Storage eller Azure Data Lake Storage och skickas till HDInsight-kluster med Hadoop-jobbet skicka mekanismer. Mer information finns i [skicka Hadoop-jobb via programmering](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Om du har problem med distribution av JAR-filer till HDInsight-kluster eller anropa JAR-filer på HDInsight-kluster, kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/).
>
> Sammanhängande stöds inte av HDInsight och är inte tillgänglig för Microsofts Support. Listor med stödda komponenter finns [vad är nytt i klusterversioner som tillhandahålls av HDInsight](../../hdinsight/hdinsight-component-versioning.md).
>
>

Ibland kan vill du konfigurera följande konfigurationsfilerna under skapandeprocessen:

* clusterIdentity.xml
* Core-site.xml
* gateway.XML
* hbase env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-plats
* oozie-site.xml
* oozie env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Mer information finns i [anpassa HDInsight-kluster med Bootstrap](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Avancerade inställningar: utöka kluster med ett virtuellt nätverk
Om din lösning kräver tekniker som är fördelade på flera typer för HDInsight-kluster, en [virtuella Azure-nätverket](../../hdinsight/https://docs.microsoft.com/azure/virtual-network) kan ansluta vilka krävs för klustret. Den här konfigurationen kan kluster och all kod som du distribuerar till dem att kommunicera direkt med varandra.

Mer information om hur du använder Azure-nätverk med HDInsight finns [utöka HDInsight med virtuella Azure-nätverk](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

Ett exempel på hur du använder två typer av klustret i Azure-nätverk finns [Använd Spark strukturerade strömning med Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md). Mer information om hur du använder HDInsight med ett virtuellt nätverk, inklusive specifika konfigurationskrav för det virtuella nätverket finns [utöka HDInsight funktioner med hjälp av Azure Virtual Network](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Felsökning av problem med åtkomstkontroll

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](../../hdinsight/hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Nästa steg

- [ABFS Hadoop-filsystem-drivrutin för Azure Data Lake lagring Gen2](abfs-driver.md)
- [Självstudier: Extrahera, transformera och läsa in data på Azure HDInsight med Apache Hive](tutorial-extract-transform-load-hive.md)
- [Vad är HDInsight Hadoop-ekosystemet och Hadoop-kluster?](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [Komma igång med Hadoop i HDInsight](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Fungera med Hadoop i HDInsight från en Windows-dator](../../hdinsight/hdinsight-hadoop-windows-tools.md)
