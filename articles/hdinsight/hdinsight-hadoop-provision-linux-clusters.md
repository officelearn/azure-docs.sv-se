---
title: Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera
description: Konfigurera Hadoop-, Kafka-, Spark-, HBase-, R-server- eller Storm-kluster för HDInsight från en webbläsare, Azure classic CLI, Azure PowerShell, REST eller SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.date: 02/12/2020
ms.openlocfilehash: 246ec08e9b4edb33fa49318b68cc4364534282b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064651"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Lär dig hur du konfigurerar Apache Hadoop, Apache Spark, Apache Kafka, Interaktiv fråga, Apache HBase, ML Services eller Apache Storm i HDInsight. Lär dig också hur du anpassar kluster och lägger till säkerhet genom att gå med dem till en domän.

Ett Hadoop-kluster består av flera virtuella datorer (noder) som används för distribuerad bearbetning av uppgifter. Azure HDInsight hanterar implementeringsinformation om installation och konfiguration av enskilda noder, så du behöver bara ange allmän konfigurationsinformation.

> [!IMPORTANT]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används. Läs om hur du [tar bort ett kluster.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Metoder för klusterinställningar

I följande tabell visas de olika metoder som du kan använda för att konfigurera ett HDInsight-kluster.

| Kluster som skapats med | Webbläsare | Kommandorad | REST API | SDK |
| --- |:---:|:---:|:---:|:---:|
| [Azure-portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Curl](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Azure Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

I den här artikeln får du hjälp med installationsprogrammet i [Azure-portalen](https://portal.azure.com), där du kan skapa ett HDInsight-kluster.

## <a name="basics"></a>Grundläggande inställningar

![hdinsight skapa alternativ anpassade snabbt](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-basics-blank-fs.png)

### <a name="project-details"></a>Projektinformation

[Azure Resource Manager](../azure-resource-manager/management/overview.md) hjälper dig att arbeta med resurserna i ditt program som en grupp, kallad en [Azure-resursgrupp](../azure-resource-manager/management/overview.md#resource-groups). Du kan distribuera, uppdatera, övervaka eller ta bort alla resurser för ditt program i en enda samordnad åtgärd.

### <a name="cluster-details"></a>Klusterinformation

#### <a name="cluster-name"></a>Klusternamn

HdInsight-klusternamn har följande begränsningar:

* Tillåtna tecken: a-z, 0-9, A-Z
* Max längd: 59
* Reserverade namn: appar
* Klusternamnomfånget är för alla Azure, för alla prenumerationer. Så klusternamnet måste vara unikt över hela världen.
* De första sex tecknen måste vara unika i ett virtuellt nätverk

#### <a name="region"></a>Region

Du behöver inte uttryckligen ange klusterplatsen: Klustret finns på samma plats som standardlagringen. Om du vill ha en lista över regioner som stöds väljer du listrutan **Region** för [HDInsight-priser](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

#### <a name="cluster-type"></a>Klustertyp

Azure HDInsight tillhandahåller för närvarande följande klustertyper, var och en med en uppsättning komponenter för att tillhandahålla vissa funktioner.

> [!IMPORTANT]  
> HDInsight-kluster är tillgängliga i olika typer, var och en för en enda arbetsbelastning eller teknik. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer, till exempel Storm och HBase i ett kluster. Om din lösning kräver tekniker som är spridda över flera HDInsight-klustertyper kan ett [virtuellt Azure-nätverk](https://docs.microsoft.com/azure/virtual-network) ansluta de klustertyper som krävs.

| Klustertyp | Funktioner |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Batchfråga och analys av lagrade data |
| [HBase](hbase/apache-hbase-overview.md) |Bearbetning för stora mängder schemalösa, NoSQL-data |
| [Interaktiv fråga](./interactive-query/apache-interactive-query-get-started.md) |Cachelagring i minnet för interaktiva och snabbare Hive-frågor |
| [Kafka](kafka/apache-kafka-introduction.md) | En distribuerad streamingplattform som kan användas för att skapa strömmande datapipellines och program i realtid |
| [ML Services](r-server/r-server-overview.md) |Olika stordatastatistik, prediktiv modellering och maskininlärningsfunktioner |
| [Gnista](spark/apache-spark-overview.md) |Bearbetning i minnet, interaktiva frågor, bearbetning av mikrobatchström |
| [Storm](storm/apache-storm-overview.md) |Händelsebearbetning i realtid |

#### <a name="version"></a>Version

Välj version av HDInsight för det här klustret. Mer information finns i [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="cluster-credentials"></a>Klusterautentiseringsuppgifter

Med HDInsight-kluster kan du konfigurera två användarkonton när klustret skapas:

* Användarnamn för klusterinloggning: Standardanvändarnamnet är *admin*. Den använder den grundläggande konfigurationen på Azure-portalen. Ibland kallas det "Klusteranvändare" eller "HTTP-användare".
* Användarnamn för Secure Shell (SSH) används för att ansluta till klustret via SSH. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

HTTP-användarnamnet har följande begränsningar:

* Tillåtna specialtecken: `_` och`@`
* Tecken är inte tillåtna:\/#;."', :'!*?$(){}[]<>|&--=+%~-utrymme
* Max längd: 20

SSH-användarnamnet har följande begränsningar:

* Tillåtna specialtecken:`_` och`@`
* Tecken är inte tillåtna:\/#;."', :'!*?$(){}[]<>|&--=+%~-utrymme
* Max längd: 64
* Reserverade namn: hadoop, användare, oozie, hive, mapred, ambari-qa, zookeeper, tez, hdfs, sqoop, garn, hcat, ams, hbase, storm, administratör, admin, användare, user1, test, user2, test1, user3, admin1, 1, 123, a, actuser, adm, admin2, aspnet, backup, console, david, gäst, john, ägare, rot, server, sql, stöd, support_388945a0, sys, test2, test3, user4, user5, spark

## <a name="storage"></a>Lagring

![Inställningar för klusterlagring: HDFS-kompatibla slutpunkter](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-storage.png)

Även om en lokal installation av Hadoop använder DET Hadoop Distributed File System (HDFS) för lagring i klustret, använder du lagringsslutpunkter som är anslutna till klustret i molnet. Med hjälp av molnlagring innebär att du på ett säkert sätt kan ta bort HDInsight-kluster som används för beräkning samtidigt som du behåller dina data.

HDInsight-kluster kan använda följande lagringsalternativ:

* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1
* Azure Storage Allmänt ändamål v2
* Azure Storage Allmänt syfte v1
* Azure Storage Block blob (**endast stöds som sekundär lagring**)

Mer information om lagringsalternativ med HDInsight finns i [Jämför lagringsalternativ för användning med Azure HDInsight-kluster](hdinsight-hadoop-compare-storage-options.md).

> [!WARNING]  
> Det går inte att använda ett ytterligare lagringskonto på en annan plats än HDInsight-klustret.

Under konfigurationen anger du en blob-behållare för ett Azure Storage-konto eller Data Lake Storage för standardslutpunkten för lagring. Standardlagringen innehåller program- och systemloggar. Du kan också ange ytterligare länkade Azure Storage-konton och DataSjölagringskonton som klustret kan komma åt. HDInsight-klustret och de beroende lagringskontona måste finnas på samma Azure-plats.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

### <a name="metastore-settings"></a>Metastore-inställningar

Du kan skapa valfria Hive- eller Apache Oozie-metabutiker. Alla klustertyper stöder dock inte metabutiker och Azure SQL Data Warehouse är inte kompatibelt med metabutiker.

Mer information finns [i Använda externa metadatalager i Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> När du skapar en anpassad metabutik ska du inte använda streck, bindestreck eller blanksteg i databasnamnet. Detta kan leda till att klusterskapandeprocessen misslyckas.

#### <a name="sql-database-for-hive"></a>SQL-databas för Hive

Om du vill behålla Hive-tabellerna när du har tagit bort ett HDInsight-kluster använder du en anpassad metabutik. Du kan sedan ansluta metabutiken till ett annat HDInsight-kluster.

En HDInsight-metabutik som skapas för en HDInsight-klusterversion kan inte delas mellan olika HDInsight-klusterversioner. En lista över HDInsight-versioner finns i [HDInsight-versioner som stöds](hdinsight-component-versioning.md#supported-hdinsight-versions).

#### <a name="sql-database-for-oozie"></a>SQL-databas för Oozie

Om du vill öka prestanda när du använder Oozie använder du en anpassad metabutik. En metabutik kan också ge åtkomst till Oozie-jobbdata när du har tagit bort klustret.

#### <a name="sql-database-for-ambari"></a>SQL-databas för Ambari

Ambari används för att övervaka HDInsight-kluster, göra konfigurationsändringar och lagra klusterhanteringsinformation samt jobbhistorik. Med den anpassade Ambari DB-funktionen kan du distribuera ett nytt kluster och konfigurera Ambari i en extern databas som du hanterar. Mer information finns i [Custom Ambari DB](./hdinsight-custom-ambari-db.md).

> [!IMPORTANT]  
> Du kan inte återanvända en anpassad Oozie-metabutik. Om du vill använda en anpassad Oozie-metabutik måste du tillhandahålla en tom Azure SQL-databas när du skapar HDInsight-klustret.

## <a name="security--networking"></a>Säkerhet + nätverk

![hdinsight skapa alternativ välja enterprise security package](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-security-networking.png)

### <a name="enterprise-security-package"></a>Säkerhetspaket för företag

För klustertyperna Hadoop, Spark, HBase, Kafka och Interactive Query kan du välja att aktivera **Enterprise Security Package**. Det här paketet ger möjlighet att ha en säkrare klusterkonfiguration med hjälp av Apache Ranger och integrera med Azure Active Directory. Mer information finns [i Översikt över företagssäkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).

Med Enterprise-säkerhetspaketet kan du integrera HDInsight med Active Directory och Apache Ranger. Flera användare kan skapas med hjälp av Enterprise-säkerhetspaketet.

Mer information om hur du skapar ett domänanslutet HDInsight-kluster finns i [Skapa domänansluten HDInsight-sandlådamiljö](./domain-joined/apache-domain-joined-configure.md).

### <a name="tls"></a>TLS

Mer information finns i [Transport layer security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)

### <a name="virtual-network"></a>Virtuellt nätverk

Om din lösning kräver tekniker som är spridda över flera HDInsight-klustertyper kan ett [virtuellt Azure-nätverk](https://docs.microsoft.com/azure/virtual-network) ansluta de klustertyper som krävs. Med den här konfigurationen kan kluster och all kod som du distribuerar till dem kommunicera direkt med varandra.

Mer information om hur du använder ett virtuellt Azure-nätverk med HDInsight finns i [Planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

Ett exempel på hur du använder två klustertyper i ett virtuellt Azure-nätverk finns i [Använda Apache Spark Structured Streaming med Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Mer information om hur du använder HDInsight med ett virtuellt nätverk, inklusive specifika konfigurationskrav för det virtuella nätverket, finns i [Planera ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="disk-encryption-setting"></a>Inställning för diskkryptering

Mer information finns i [Kryptering med kundhanterad nyckeldisk](./disk-encryption.md).

### <a name="kafka-rest-proxy"></a>Kafka REST-proxy

Den här inställningen är endast tillgänglig för klustertyp Kafka. Mer information finns i [Använda en REST-proxy](./kafka/rest-proxy.md).

### <a name="identity"></a>Identitet

Mer information finns [i Hanterade identiteter i Azure HDInsight](./hdinsight-managed-identities.md).

## <a name="configuration--pricing"></a>Konfiguration + prissättning

![HDInsight väljer nodstorlek](./media/hdinsight-hadoop-provision-linux-clusters/azure-portal-cluster-configuration.png)

Du debiteras för nodanvändning så länge klustret finns. Faktureringen startar när ett kluster skapas och stoppas när klustret tas bort. Kluster kan inte avallokeras eller spärras.

### <a name="node-configuration"></a>Nodkonfiguration

Varje klustertyp har sitt eget antal noder, terminologi för noder och standardstorlek för virtuella datorer. I följande tabell är antalet noder för varje nodtyp inom parentes.

| Typ | Noder | Diagram |
| --- | --- | --- |
| Hadoop |Huvudnod (2), arbetarnod (1+) |![HDInsight Hadoop klusternoder](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Huvudserver (2), regionserver (1+), huvud-/ZooKeeper-nod (3) |![HdInsight HBase klustertyp setup](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Nimbus-nod (2), övervakare server (1+), ZooKeeper-nod (3) |![HdInsight storm kluster typ setup](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| Spark |Head nod (2), Worker nod (1+), ZooKeeper nod (3) (gratis för A1 ZooKeeper VM storlek) |![HdInsight spark klustertyp setup](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Mer information finns i [Standardnodkonfiguration och storlekar för virtuella datorer för kluster](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) i "Vilka är Hadoop-komponenterna och versionerna i HDInsight?"

Kostnaden för HDInsight-kluster bestäms av antalet noder och storleken på virtuella datorer för noderna.

Olika klustertyper har olika nodtyper, antal noder och nodstorlekar:
* Hadoop-klustertyp standard:
    * Två *huvudnoder*  
    * Fyra *arbetsnoder*
* Standard för stormklustertyp:
    * Två *Nimbus-noder*
    * Tre *ZooKeeper-noder*
    * Fyra *handledarenoder*

Om du bara provar HDInsight rekommenderar vi att du använder en arbetarnod. Mer information om HDInsight-priser finns i [HDInsight-priser](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Gränsen för klusterstorlek varierar mellan Azure-prenumerationer. Kontakta [Azure-faktureringssupport](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) för att öka gränsen.

När du använder Azure-portalen för att konfigurera klustret är nodstorleken tillgänglig via fliken **Konfiguration + prissättning.** I portalen kan du också se kostnaden som är associerad med de olika nodstorlekarna.

### <a name="virtual-machine-sizes"></a>Storlekar för virtuella datorer

När du distribuerar kluster väljer du beräkningsresurser baserat på den lösning du planerar att distribuera. Följande virtuella datorer används för HDInsight-kluster:

* Virtuella datorer i A- och D1-4-serien: [Virtuella storlekar i allmänt syfte](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* D11-14-serien VM: [Minnesoptimerade Linux VM-storlekar](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Information om vilket värde du ska använda för att ange en vm-storlek när du skapar ett kluster med de olika SDK:erna eller när du använder Azure PowerShell finns [i VM-storlekar som ska användas för HDInsight-kluster](../cloud-services/cloud-services-sizes-specs.md#size-tables). I den här länkade artikeln använder du värdet i kolumnen **Storlek** i tabellerna.

> [!IMPORTANT]  
> Om du behöver fler än 32 arbetarnoder i ett kluster måste du välja en huvudnodstorlek med minst 8 kärnor och 14 GB RAM.If you need more than 32 Worker nodes in a cluster, you must select a head node size with least 8 cores and 14 GB of RAM.

Mer information finns i [Storlekar för virtuella datorer](../virtual-machines/windows/sizes.md). Information om prissättning av de olika storlekarna finns i [HDInsight-priser](https://azure.microsoft.com/pricing/details/hdinsight).

### <a name="add-application"></a>Lägga till ett program

Ett HDInsight-program är ett program som användarna kan installera på ett Linux-baserat HDInsight-kluster. Du kan använda program som tillhandahålls av Microsoft, tredje part eller som du utvecklar själv. Mer information finns i [Installera Apache Hadoop-program från tredje part på Azure HDInsight](hdinsight-apps-install-applications.md).

De flesta HDInsight-program installeras på en tom kantnod.  En tom kantnod är en virtuell Linux-dator med samma klientverktyg installerade och konfigurerade som i huvudnoden. Du kan använda kantnoden för åtkomst till klustret, testa klientprogrammen och vara värd för klientprogram. Mer information finns i [Använda tomma kantnoder i HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="script-actions"></a>Skriptåtgärder

Du kan installera ytterligare komponenter eller anpassa klusterkonfigurationen med hjälp av skript när du skapar. Sådana skript anropas via **Skriptåtgärd**, vilket är ett konfigurationsalternativ som kan användas från Azure-portalen, HDInsight Windows PowerShell-cmdlets eller HDInsight .NET SDK. Mer information finns i [Anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

Vissa inbyggda Java-komponenter, som Apache Mahout och Cascading, kan köras i klustret som JAVA Archive-filer (JAR). Dessa JAR-filer kan distribueras till Azure Storage och skickas till HDInsight-kluster med Hadoop-jobbinlämningsmekanismer. Mer information finns i [Skicka Apache Hadoop-jobb programmässigt](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Om du har problem med att distribuera JAR-filer till HDInsight-kluster eller anropa JAR-filer i HDInsight-kluster kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Cascading stöds inte av HDInsight och är inte kvalificerat för Microsoft Support. Listor över komponenter som stöds finns [i Nyheter i klusterversionerna som tillhandahålls av HDInsight](hdinsight-component-versioning.md).

Ibland vill du konfigurera följande konfigurationsfiler under skapandeprocessen:

* klusterIdentity.xml
* core-site.xml
* gateway.xml
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
* garn-site.xml

Mer information finns i [Anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="next-steps"></a>Nästa steg

* [Felsöka klusterskapande fel med Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md)
* [Vad är HDInsight, Apache Hadoop-ekosystemet och Hadoop-kluster?](hadoop/apache-hadoop-introduction.md)
* [Kom igång med Apache Hadoop i HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Arbeta i Apache Hadoop på HDInsight från en Windows-dator](hdinsight-hadoop-windows-tools.md)
