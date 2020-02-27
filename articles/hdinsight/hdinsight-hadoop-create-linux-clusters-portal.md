---
title: Skapa Apache Hadoop-kluster med hjälp av webbläsare, Azure HDInsight
description: Lär dig hur du skapar Apache Hadoop, Apache HBase, Apache Storm eller Apache Spark kluster på Linux för HDInsight med hjälp av en webbläsare och Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623198"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal är ett webbaserat hanterings verktyg för tjänster och resurser som finns i Microsoft Azure molnet. I den här artikeln får du lära dig hur du skapar Linux-baserade Azure HDInsight-kluster med hjälp av portalen. Ytterligare information finns i [skapa HDInsight-kluster](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure Portal visar de flesta av kluster egenskaperna. Med hjälp av Azure Resource Manager mallar kan du dölja många detaljer. Mer information finns i [skapa Apache Hadoop kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-clusters"></a>Skapa kluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **+ skapa en resurs**på den översta menyn.

    ![Skapa ett nytt kluster i Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Skapa ett nytt kluster i Azure Portal")

1. Välj **Analytics** > **Azure HDInsight** för att gå till sidan **skapa HDInsight-kluster** .

## <a name="basics"></a>Grundläggande inställningar

![Grunderna för HDInsight skapa kluster](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Skapa ett nytt kluster i Azure Portal")

På fliken **grundläggande** anger du följande information:

|Egenskap |Beskrivning |
|---|---|
|Prenumeration|I list rutan väljer du den Azure-prenumeration som används för klustret.|
|Resursgrupp|Välj din befintliga resurs grupp i list rutan eller Välj **Skapa ny**.|
|Klusternamn|Ange ett globalt unikt namn.|
|Region|Välj en region där klustret skapas i list rutan.|
|Kluster typ|Välj **Välj kluster typ** för att öppna en lista. Välj önskad kluster typ i listan. HDInsight-kluster ingår i olika typer. De motsvarar den arbets belastning eller teknik som klustret är anpassat för. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer.|
|Version|Välj en **version**i list rutan. Använd standard versionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).|
|Användarnamn för klusterinloggning|Ange användar namnet, standard är **administratör**.|
|Lösenord för klusterinloggning|Ange lösen ordet.|
|Bekräfta lösen ord för kluster inloggning|Ange lösen ordet igen|
|Secure Shell (SSH)-användarnamn|Ange användar namnet, standard är **sshuser**|
|Använd lösen ord för kluster inloggning för SSH|Om du vill använda samma SSH-lösenord som administratörs lösen ord som du angav tidigare, markerar du kryss rutan **Använd lösen ord för kluster inloggning för SSH** . Om inte, anger du antingen ett **lösen ord** eller en **offentlig nyckel** för att autentisera SSH-användaren. En offentlig nyckel är den metod vi rekommenderar. Välj **Välj** längst ned för att spara konfigurationen av autentiseringsuppgifter.  Mer information finns i [ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Välj **Nästa: lagrings > >** för att gå vidare till nästa flik.

## <a name="storage"></a>Storage

![HDInsight skapa kluster lagring](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Skapa ett nytt kluster i Azure Portal-lagring")

### <a name="primary-storage"></a>Primär lagring

Välj din standard lagrings typ i list rutan för **primär lagrings typ**. De efterföljande fälten som ska slutföras varierar beroende på ditt val. För **Azure Storage**:

1. För **urvals metod**väljer du antingen **Välj från listan**eller **Använd åtkomst nyckel**.
    * I **listan Välj från**väljer du ditt **primära lagrings konto** i list rutan eller väljer **Skapa nytt**.
    * Ange ditt **lagrings konto namn**för **Använd åtkomst nyckel**. Ange sedan **åtkomst nyckeln**.

1. Acceptera standardvärdet för **container**eller ange ett nytt.

### <a name="additional-azure-storage"></a>Ytterligare Azure Storage

Valfritt: Välj **Lägg till Azure Storage** för ytterligare kluster lagring. Det finns inte stöd för att använda ett ytterligare lagrings konto i en annan region än HDInsight-klustret.

### <a name="metastore-settings"></a>Metaarkiv-inställningar

Valfritt: Ange en befintlig SQL Database för att spara Apache Hive, Apache Oozie och/eller Apache Ambari-metadata utanför klustret. Azure SQL Database som används för metaarkiv måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. När du skapar en metaarkiv ska du inte namnge en databas med bindestreck eller bindestreck. Dessa tecken kan orsaka att klustrets skapande process kraschar.

Välj **Nästa: Security + network > >** för att gå vidare till nästa flik.

## <a name="security--networking"></a>Säkerhet + nätverk

![HDInsight skapa kluster säkerhets nätverk](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight skapa kluster säkerhets nätverk")

På fliken **säkerhet + nätverk** anger du följande information:

|Egenskap |Beskrivning |
|---|---|
|Säkerhets paket för företag|Valfritt: Markera kryss rutan om du vill använda **Enterprise Security Package**. Mer information finns i [Konfigurera ett HDInsight-kluster med Enterprise Security Package med Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Valfritt: Välj en TLS-version i list rutan. Mer information finns i [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Virtuellt nätverk|Valfritt: Välj ett befintligt virtuellt nätverk och undernät i den nedrullningsbara listan. Mer information finns i [Planera en virtuell nätverks distribution för Azure HDInsight-kluster](hdinsight-plan-virtual-network-deployment.md). Artikeln innehåller särskilda konfigurations krav för det virtuella nätverket.|
|Disk krypterings inställningar|Valfritt: Markera kryss rutan om du vill använda kryptering. Mer information finns i [kund hanterad nyckel disk kryptering](./disk-encryption.md).|
|Kafka REST-proxy|Den här inställningen är endast tillgänglig för kluster typen Kafka. Mer information finns i [använda en rest-proxy](./kafka/rest-proxy.md).|
|Identitet|Valfritt: Välj en befintlig användardefinierad tjänst identitet från List rutan. Mer information finns i [hanterade identiteter i Azure HDInsight](./hdinsight-managed-identities.md).|

Välj **Nästa: konfiguration + pris > >** för att gå vidare till nästa flik.

## <a name="configuration--pricing"></a>Konfiguration + prissättning

![HDInsight skapa kluster konfiguration](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Fliken konfiguration och prissättning")

Ange följande information på fliken **Configuration + prissättning** :

|Egenskap |Beskrivning |
|---|---|
|+ Lägg till program|Valfritt: Välj de program som du vill använda. Microsoft, oberoende program varu leverantörer (ISV), eller så kan du utveckla dessa program. Mer information finns i [installera program när klustret skapas](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Node-storlek|Valfritt: Välj en nod med olika storlek.|
|Antal noder|Valfritt: Ange antalet noder för den angivna nodtypen. Om du planerar över 32 arbetsnoder väljer du en huvudnods storlek med minst åtta kärnor och 14 GB RAM. Planera noderna antingen vid skapande av kluster eller genom att skala klustret när det har skapats.|
|Aktivera autoskalning|Valfritt: Markera kryss rutan för att aktivera funktionen. Mer information finns i [skala Azure HDInsight-kluster automatiskt](./hdinsight-autoscale-clusters.md).|
|+ Lägg till skript åtgärd|Valfritt: det här alternativet fungerar om du vill använda ett anpassat skript för att anpassa ett kluster, eftersom klustret skapas. Mer information om skript åtgärder finns i [Anpassa Linux-baserade HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md).|

Välj **Granska + skapa > >** för att verifiera kluster konfigurationen och gå sedan till den sista fliken.

## <a name="review--create"></a>Granska + skapa

![HDInsight skapa kluster Sammanfattning](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Ange antal klusternoder")

Granska inställningarna. Välj **Skapa** så att klustret skapas.

Det tar lite tid att skapa klustret, normalt cirka 20 minuter. Övervaka **aviseringar** för att kontrol lera etablerings processen.

## <a name="post-creation"></a>Skapa inlägg

När processen har skapats väljer **du gå till resurs** från meddelandet **distributionen har slutförts** . Kluster fönstret innehåller följande information.

![Översikt över HDI Azure Portal-kluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Kluster egenskaper")

Några av ikonerna i fönstret förklaras på följande sätt:

|Egenskap | Beskrivning |
|---|---|
|Översikt|Innehåller all viktig information om klustret. Exempel är namnet, resurs gruppen som den tillhör, platsen, operativ systemet och URL: en för kluster instrument panelen.|
|Klusterinstrumentpaneler|Dirigerar dig till Ambari-portalen som är kopplad till klustret.|
|SSH + kluster inloggning|Ger information som krävs för att få åtkomst till klustret med hjälp av SSH.|
|Ta bort|Tar bort HDInsight-klustret.|

## <a name="customize-clusters"></a>Anpassa kluster

* [Anpassa HDInsight-kluster med hjälp av bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Anpassa Linux-baserade HDInsight-kluster med hjälp av skript åtgärder](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Tar bort klustret

Se [ta bort ett HDInsight-kluster med hjälp av webbläsaren, PowerShell eller Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

Du har skapat ett HDInsight-kluster. Lär dig nu hur du arbetar med klustret.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop kluster

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Använda MapReduce med HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-kluster

* [Kom igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-program för Apache HBase på HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm kluster

* [Utveckla Java-topologier för Apache Storm i HDInsight](storm/apache-storm-develop-java-topology.md)
* [Använda python-komponenter i Apache Storm på HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuera och övervaka topologier med Apache Storm på HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark kluster

* [Skapa ett fristående program med hjälp av Scala](spark/apache-spark-create-standalone-application.md)
* [Köra jobb via fjärr anslutning på ett Apache Spark kluster med Apache livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](spark/apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](spark/apache-spark-machine-learning-mllib-ipython.md)
