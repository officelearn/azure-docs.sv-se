---
title: Kluster konfiguration för Apache Hadoop, Apache Spark, Apache Kafka, Apache HBase eller R Server – Azure HDInsight
description: Konfigurera Hadoop-, Kafka-, Spark-, HBase-, R Server-eller Storm-kluster för HDInsight från en webbläsare, den klassiska Azure CLI, Azure PowerShell, REST eller SDK.
keywords: konfiguration av Hadoop-kluster, Kafka kluster konfiguration, Spark-kluster installation, vad är kluster i Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: c47cc49a8e81b44a4529f8f82954797e2648c938
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69983096"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Lär dig hur du konfigurerar och konfigurerar kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka, interaktiv fråga, Apache HBase, ML-tjänster eller Apache Storm. Lär dig också hur du anpassar kluster och lägger till säkerhet genom att ansluta dem till en domän.

Ett Hadoop-kluster består av flera virtuella datorer (noder) som används för distribuerad bearbetning av aktiviteter. Azure HDInsight hanterar implementerings information om installation och konfiguration av enskilda noder, så du behöver bara ange allmän konfigurations information.

> [!IMPORTANT]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Lär dig hur du [tar bort ett kluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Kluster installations metoder
I följande tabell visas de olika metoder som du kan använda för att skapa ett HDInsight-kluster.

| Kluster som skapats med | Webbläsare | Kommandorad | REST-API | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Azure Resource Manager mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Snabb registrering: Grundläggande kluster konfiguration
Den här artikeln vägleder dig genom installationen i [Azure Portal](https://portal.azure.com), där du kan skapa ett HDInsight-kluster med hjälp av *snabb registrering* eller *anpassad*. 

![HDInsight Create Options Custom Quick Create](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Följ anvisningarna på skärmen för att göra en grundläggande kluster konfiguration. Mer information finns nedan:

* [Resurs grupps namn](#resource-group-name)
* [Kluster typer och konfiguration](#cluster-types) 
* [Klusternamn](#cluster-name)
* [Kluster inloggning och SSH-användarnamn](#cluster-login-and-ssh-username)
* [Location](#location)

## <a name="resource-group-name"></a>Resursgruppsnamn

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) hjälper dig att arbeta med resurserna i ditt program som en grupp, som kallas för en Azure-resurs grupp. Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser för ditt program i en enda samordnad åtgärd.

## <a name="cluster-types"></a>Kluster typer och konfiguration
Azure HDInsight tillhandahåller för närvarande följande kluster typer, var och en med en uppsättning komponenter för att tillhandahålla vissa funktioner.

> [!IMPORTANT]  
> HDInsight-kluster är tillgängliga i olika typer, var och en för en enskild arbets belastning eller teknik. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel storm och HBase på ett kluster. Om lösningen kräver tekniker som sprids över flera HDInsight-kluster typer kan ett [virtuellt Azure-nätverk](https://docs.microsoft.com/azure/virtual-network) ansluta de kluster typer som krävs. 

| Klustertyp | Funktioner |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batch-fråga och analys av lagrade data |
| [HBase](hbase/apache-hbase-overview.md) |Bearbetning av stora mängder schema lösta, NoSQL data |
| [Interaktiv fråga](./interactive-query/apache-interactive-query-get-started.md) |Minnes intern cachelagring för interaktiva och snabbare Hive-frågor |
| [Kafka](kafka/apache-kafka-introduction.md) | En distribuerad strömmande plattform som kan användas för att bygga real tids strömnings data pipelines och program |
| [ML-tjänster](r-server/r-server-overview.md) |Olika stor data statistik, förutsägande modellering och maskin inlärnings funktioner |
| [Spark](spark/apache-spark-overview.md) |Minnes intern bearbetning, interaktiva frågor, bearbetning av mikrobatch-dataström |
| [Stort](storm/apache-storm-overview.md) |Händelsebearbetning i realtid |


### <a name="hdinsight-version"></a>HDInsight-version
Välj HDInsight-versionen för det här klustret. Mer information finns i [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="cluster-name"></a>Klusternamn

HDInsight-kluster namn har följande begränsningar:
- Tillåtna tecken: a-z, 0-9, A-Z 
- Max längd: 59
- Reserverade namn: appar
- Måste vara unik
- De första 6 tecknen måste vara unika inom ett VNET

## <a name="cluster-login-and-ssh-username"></a>Kluster inloggning och SSH-användarnamn
Med HDInsight-kluster kan du konfigurera två användar konton när klustret skapas:

* HTTP-användare: Standardanvändarnamnet är *admin*. Den grundläggande konfigurationen används på Azure Portal. Ibland kallas det "kluster användare".
* SSH-användare: Används för att ansluta till klustret via SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

HTTP-användarnamnet har följande begränsningar:
- Tillåtna specialtecken: _ och @ 
- Tecken som inte tillåts: #;. "'\/,: '! *? $ ({}) [] < > | &--= +% ~ ^ Space
- Max längd: 20

SSH-användarnamnet har följande begränsningar:
- Tillåtna specialtecken: _ och @ 
- Tecken som inte tillåts: #;. "'\/,: '! *? $ ({}) [] < > | &--= +% ~ ^ Space
- Max längd: 64
- Reserverade namn: Hadoop, användare, Oozie, Hive, mapred, Ambari-frågor, Zookeeper, tez, HDFS, Sqoop, garn, hcat, AMS, HBase, Storm, administratör, admin, användare, Användare1, test, användare2, TEST1, user3, admin1, 1, 123, a, actuser, ADM, admin2, ASPNET, säkerhets kopiering, konsol, David, gäst, John, ägare, rot, Server, SQL, support, support_388945a0, sys, TEST2, test3, user4, user5, Spark

Med säkerhets paketet för företag kan du integrera HDInsight med Active Directory och Apache Ranger. Flera användare kan skapas med hjälp av Enterprise Security-paketet.

## <a name="location"></a>Plats (regioner) för kluster och lagring

Du behöver inte ange kluster platsen uttryckligen: Klustret är på samma plats som standard lagringen. Om du vill ha en lista över regioner som stöds klickar du på list rutan **region** i [priser för HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Lagrings slut punkter för kluster

Även om en lokal installation av Hadoop använder Hadoop Distributed File System (HDFS) för lagring i klustret, i molnet använder du lagrings slut punkter som är anslutna till klustret. HDInsight-kluster använder antingen [Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md) eller [blobbar i Azure Storage](hdinsight-hadoop-use-blob-storage.md). Med hjälp av Azure Storage eller Data Lake Storage kan du på ett säkert sätt ta bort HDInsight-kluster som används för beräkning samtidigt som dina data behålls. 

> [!WARNING]  
> Det finns inte stöd för att använda ett ytterligare lagrings konto på en annan plats från HDInsight-klustret.

Under konfigurationen för standard lagrings slut punkten anger du en BLOB-behållare för ett Azure Storage konto eller Data Lake Storage. Standard lagringen innehåller program-och system loggar. Alternativt kan du ange ytterligare länkade Azure Storage konton och Data Lake Storage konton som klustret har åtkomst till. HDInsight-klustret och beroende lagrings kontona måste finnas på samma Azure-plats.

![Kluster lagrings inställningar: HDFS-kompatibla slut punkter för lagring](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Valfri metastores
Du kan skapa valfria Hive-eller Apache Oozie-metastores. Alla kluster typer stöder dock inte metastores, och Azure SQL Data Warehouse är inte kompatibel med metastores. 

Mer information finns i [använda externa metadata butiker i Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> När du skapar en anpassad metaarkiv ska du inte använda bindestreck, bindestreck eller blank steg i databas namnet. Detta kan orsaka att klustrets skapande process kraschar.

### <a name="use-hiveoozie-metastore"></a>Hive-metaarkiv

Använd en anpassad metaarkiv om du vill behålla Hive-tabellerna när du har tagit bort ett HDInsight-kluster. Du kan sedan ansluta metaarkiv till ett annat HDInsight-kluster.

An-HDInsight metaarkiv som skapas för en HDInsight-kluster version kan inte delas mellan olika HDInsight-kluster versioner. En lista över HDInsight-versioner finns i [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Oozie metaarkiv

Använd en anpassad metaarkiv för att öka prestandan när du använder Oozie. En metaarkiv kan också ge åtkomst till Oozie-jobb data när du har tagit bort klustret. 

> [!IMPORTANT]  
> Du kan inte återanvända en anpassad Oozie-metaarkiv. Om du vill använda en anpassad Oozie-metaarkiv måste du ange en tom Azure SQL Database när du skapar HDInsight-klustret.


## <a name="custom-cluster-setup"></a>Anpassad kluster konfiguration
Anpassad kluster installation bygger på snabb starts inställningarna och lägger till följande alternativ:
- [Säkerhets paket för företag](#enterprise-security-package)
- [HDInsight-program](#install-hdinsight-applications-on-clusters)
- [Kluster storlek](#configure-cluster-size)
- [Skript åtgärder](#advanced-settings-script-actions)
- [Virtuellt nätverk](#advanced-settings-extend-clusters-with-a-virtual-network)
 
## <a name="enterprise-security-package"></a>Säkerhets paket för företag

För Hadoop-, Spark-, HBase-, Kafka-och interaktiv fråga-kluster typer kan du välja att aktivera **Enterprise Security Package**. Det här paketet ger möjlighet att ha en säkrare kluster konfiguration genom att använda Apache Ranger och integrera med Azure Active Directory. Mer information finns i [Översikt över företags säkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

![HDInsight-alternativ för skapande väljer Enterprise Security-paket](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

Mer information om hur du skapar domänanslutna HDInsight-kluster finns i avsnittet [skapa domänansluten HDInsight-miljö](./domain-joined/apache-domain-joined-configure.md). 

## <a name="install-hdinsight-applications-on-clusters"></a>Installera HDInsight-program i kluster

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Du kan använda program från Microsoft, tredje part eller som du utvecklar själv. Mer information finns i [installera Apache Hadoop program från tredje part i Azure HDInsight](hdinsight-apps-install-applications.md).

De flesta av HDInsight-programmen installeras på en tom Edge-nod.  En tom Edge-nod är en virtuell Linux-dator med samma klient verktyg installerade och konfigurerade som i head-noden. Du kan använda Edge-noden för att komma åt klustret, testa dina klient program och vara värd för dina klient program. Mer information finns i [använda tomma Edge-noder i HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Konfigurera kluster storlek

Du debiteras för användning av Node så länge klustret finns. Faktureringen startar när ett kluster skapas och stoppas när klustret tas bort. Kluster kan inte avallokeras eller spärras.

### <a name="number-of-nodes-for-each-cluster-type"></a>Antal noder för varje kluster typ
Varje kluster typ har sitt eget antal noder, terminologi för noder och standard storlek för virtuell dator. I följande tabell är antalet noder för varje nodtyp inom parentes.

| type | Noder | Diagram |
| --- | --- | --- |
| Hadoop |Head-nod (2), arbetsnoden (1 +) |![HDInsight Hadoop-klusternoder](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Huvud server (2), region Server (1 +), Master/ZooKeeper-nod (3) |![HDInsight HBase-klusternoder](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus Node (2), övervaknings Server (1 +), ZooKeeper Node (3) |![HDInsight Storm-klusternoder](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Head Node (2), arbetsnoden (1 +), ZooKeeper Node (3) (kostnads fri för a1 ZooKeeper VM-storlek) |![HDInsight Spark-klusternoder](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Mer information finns i [standard konfiguration av noder och virtuella dator storlekar för kluster](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) i "Vad är Hadoop-komponenter och-versioner i HDInsight?"

Kostnaden för HDInsight-kluster bestäms av antalet noder och storlekarna för virtuella datorer för noderna. 

Olika typer av kluster har olika nodtyper, antal noder och Node-storlekar:
* Standardvärde för Hadoop-kluster typ: 
    * Två *Head-noder*  
    * Fyra *arbetsnoder*
* Standard Storm kluster typ: 
    * Två *Nimbus-noder*
    * Tre *ZooKeeper-noder*
    * Fyra *överordnade noder* 

Om du bara försöker använda HDInsight rekommenderar vi att du använder en arbetsnod. Mer information om priser för HDInsight finns i avsnittet om [priser för HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Kluster storleks gränsen varierar mellan Azure-prenumerationer. Kontakta [Azures fakturerings support](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) för att öka gränsen.

När du använder Azure Portal för att konfigurera klustret är Node-storleken tillgängligt via bladet **Node pris nivåer** . I portalen kan du också se kostnaden som är associerad med de olika nodernas storlekar. 

![VM-storlekar för HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer 
När du distribuerar kluster väljer du beräknings resurser baserat på den lösning som du planerar att distribuera. Följande virtuella datorer används för HDInsight-kluster:
* Virtuella datorer i och D1-4-serien: [Allmänna Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11 – 14-seriens virtuella datorer: [Minnesoptimerade virtuella Linux-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

För att ta reda på vilket värde du ska använda för att ange en VM-storlek när du skapar ett kluster med hjälp av de olika SDK: erna eller när du använder Azure PowerShell, se [storleken på virtuella datorer som används för HDInsight-kluster](../cloud-services/cloud-services-sizes-specs.md#size-tables). I den här länkade artikeln använder du värdet i kolumnen **storlek** i tabellerna.

> [!IMPORTANT]  
> Om du behöver fler än 32 arbetsnoder i ett kluster måste du välja en huvudnods storlek med minst 8 kärnor och 14 GB RAM.

Mer information finns i [storlekar för virtuella datorer](../virtual-machines/windows/sizes.md). Information om priser för de olika storlekarna finns i avsnittet om [priser för HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="advanced-settings-script-actions"></a>Avancerade inställningar: Skriptåtgärder

Du kan installera ytterligare komponenter eller anpassa kluster konfigurationen genom att använda skript när du skapar. Sådana skript anropas via **skript åtgärd**, vilket är ett konfigurations alternativ som kan användas från Azure Portal, HDInsight Windows PowerShell-cmdletar eller HDInsight .NET SDK. Mer information finns i [Anpassa HDInsight-kluster med skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md).

Vissa inbyggda Java-komponenter, t. ex. apache Mahout och överlappande, kan köras på klustret som filer för Java-arkiv (JAR). Dessa JAR-filer kan distribueras till Azure Storage och skickas till HDInsight-kluster med insändnings metoder för Hadoop-jobb. Mer information finns i [skicka Apache Hadoop jobb program mässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du har problem med att distribuera jar-filer till HDInsight-kluster eller anropa jar-filer på HDInsight-kluster.
>
> Överlappande stöds inte av HDInsight och är inte giltigt för Microsoft Support. Listor över komponenter som stöds finns i [Nyheter i de kluster versioner som tillhandahålls av HDInsight](hdinsight-component-versioning.md).

Ibland vill du konfigurera följande konfigurationsfiler under skapande processen:

* clusterIdentity.xml
* Site. XML
* Gateway. XML
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred – plats
* oozie-site.xml
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* yarn-site. XML

Mer information finns i [Anpassa HDInsight-kluster med hjälp av bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Avancerade inställningar: Utöka kluster med ett virtuellt nätverk
Om lösningen kräver tekniker som sprids över flera HDInsight-kluster typer kan ett [virtuellt Azure-nätverk](https://docs.microsoft.com/azure/virtual-network) ansluta de kluster typer som krävs. Den här konfigurationen tillåter kluster och all kod som du distribuerar till dem för att kommunicera direkt med varandra.

Mer information om hur du använder ett virtuellt Azure-nätverk med HDInsight finns i [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

Ett exempel på hur du använder två kluster typer i ett virtuellt Azure-nätverk finns i [använda Apache Spark strukturerad strömning med Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Mer information om hur du använder HDInsight med ett virtuellt nätverk, inklusive särskilda konfigurations krav för det virtuella nätverket, finns i [planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).


## <a name="next-steps"></a>Nästa steg

- [Felsöka kluster skapande fel med Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
- [Vad är HDInsight, Apache Hadoop eko system och Hadoop-kluster?](hadoop/apache-hadoop-introduction.md)
- [Kom igång med att använda Apache Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)
