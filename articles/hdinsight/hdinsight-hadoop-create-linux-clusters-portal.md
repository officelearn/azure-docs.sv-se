---
title: Skapa Apache Hadoop-kluster med hjälp av webbläsaren Azure HDInsight
description: Lär dig att skapa Apache Hadoop-, Apache HBase-, Apache Storm- eller Apache Spark-kluster på HDInsight. Använd webbläsaren och Azure-portalen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: a5f6ac76d509a0a63c2d641f91cd91cdb2e0d19d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313812"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Skapa Linux-baserade kluster i HDInsight med hjälp av Azure-portalen

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure-portalen är ett webbaserat hanteringsverktyg för tjänster och resurser som finns i Microsoft Azure-molnet. I den här artikeln får du lära dig hur du skapar Linux-baserade Azure HDInsight-kluster med hjälp av portalen. Ytterligare information finns i [Skapa HDInsight-kluster](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure-portalen exponerar de flesta klusteregenskaper. Genom att använda Azure Resource Manager-mallar kan du dölja många detaljer. Mer information finns i [Skapa Apache Hadoop-kluster i HDInsight med hjälp av Resource Manager-mallar](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-clusters"></a>Skapa kluster

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj + Skapa **en resurs**på den övre menyn .

    ![Skapa ett nytt kluster i Azure-portalen](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Skapa ett nytt kluster i Azure-portalen")

1. Välj **Analytics** > **Azure HDInsight** för att gå till **sidan Skapa HDInsight-kluster.**

## <a name="basics"></a>Grundläggande inställningar

![HDInsight skapar klustergrunder](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Skapa ett nytt kluster i Azure-portalen")

Ange följande information på fliken **Grunderna:**

|Egenskap |Beskrivning |
|---|---|
|Prenumeration|Välj den Azure-prenumeration som används för klustret i listrutan.|
|Resursgrupp|Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
|Klusternamn|Ange ett globalt unikt namn.|
|Region|Välj en region där klustret skapas i listrutan.|
|Klustertyp|Klicka på **Välj klustertyp** för att öppna en lista. Välj önskad klustertyp i listan. HDInsight-kluster finns i olika typer. De motsvarar den arbetsbelastning eller teknik som klustret är inställt för. Det finns ingen metod som stöds för att skapa ett kluster som kombinerar flera typer.|
|Version|Välj en **version**i listrutan . Använd standardversionen om du inte vet vad du ska välja. Mer information finns i [HDInsight-klusterversioner](hdinsight-component-versioning.md).|
|Användarnamn för klusterinloggning|Ange användarnamn, standard är **admin**.|
|Lösenord för klusterinloggning|Ange lösenordet.|
|Bekräfta lösenord för klusterinloggning|Ange lösenordet igen|
|Secure Shell (SSH)-användarnamn|Ange användarnamn, standard är **sshuser**|
|Använda lösenord för klusterinloggning för SSH|Om du vill ha samma SSH-lösenord som det administratörslösenord som du angav tidigare markerar du kryssrutan **Använd klusterinloggningslösenord för SSH.** Om inte, ange antingen ett **lösenord** eller **offentlig nyckel** för att autentisera SSH-användaren. En offentlig nyckel är det tillvägagångssätt vi rekommenderar. Välj **Välj** längst ned om du vill spara autentiseringskonfigurationen.  Mer information finns i [Anslut till HDInsight (Apache Hadoop) med hjälp av SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Välj **Nästa: Lagring >>** för att gå vidare till nästa flik.

## <a name="storage"></a>Storage

![HDInsight skapar klusterlagring](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Skapa ett nytt kluster i Azure-portalen - lagring")

### <a name="primary-storage"></a>Primär lagring

Välj standardlagringstypen i listrutan för **primär lagringstyp.** De senare fälten som ska fyllas i varierar beroende på ditt val. För **Azure Storage:**

1. För **markeringsmetod**väljer du **antingen Välj från-lista**eller Använd **åtkomstnyckel**.
    * För **Välj från-lista**väljer du sedan ditt **primära lagringskonto** i listrutan eller väljer **Skapa nytt**.
    * Ange ditt **lagringskontonamn**för **Åtkomstnyckeln**för Använd . Ange sedan **åtkomstnyckeln**.

1. För **Behållare**accepterar du standardvärdet eller anger ett nytt.

### <a name="additional-azure-storage"></a>Ytterligare Azure-lagring

Valfritt: Välj **Lägg till Azure Storage** för ytterligare klusterlagring. Det går inte att använda ett ytterligare lagringskonto i en annan region än HDInsight-klustret.

### <a name="metastore-settings"></a>Inställningar för Metastore

Valfritt: Ange en befintlig SQL-databas för att spara Apache Hive-, Apache Oozie- och Apache Ambari-metadata utanför klustret. Azure SQL-databasen som används för metabutiken måste tillåta anslutning till andra Azure-tjänster, inklusive Azure HDInsight. När du skapar en metabutik ska du inte namnge en databas med streck eller bindestreck. Dessa tecken kan orsaka att klusterskapandeprocessen misslyckas.

Välj **Nästa: Säkerhet + nätverk >>** för att gå vidare till nästa flik.

## <a name="security--networking"></a>Säkerhet + nätverk

![HDInsight skapar klustersäkerhetsnätverk](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight skapar klustersäkerhetsnätverk")

Ange följande information på fliken **Säkerhet + nätverk:**

|Egenskap |Beskrivning |
|---|---|
|Säkerhetspaket för företag|Valfritt: Markera kryssrutan om du vill använda **Enterprise Security Package**. Mer information finns i [Konfigurera ett HDInsight-kluster med Enterprise Security Package med hjälp av Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Valfritt: Välj en TLS-version i listrutan. Mer information finns i [Transport layer security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Virtuellt nätverk|Valfritt: Välj ett befintligt virtuellt nätverk och undernät i listrutan. Information finns i [Planera en virtuell nätverksdistribution för Azure HDInsight-kluster](hdinsight-plan-virtual-network-deployment.md). Artikeln innehåller specifika konfigurationskrav för det virtuella nätverket.|
|Inställningar för diskkryptering|Valfritt: Markera kryssrutan om du vill använda kryptering. Mer information finns i [Kryptering med kundhanterad nyckeldisk](./disk-encryption.md).|
|Kafka REST-proxy|Den här inställningen är endast tillgänglig för klustertyp Kafka. Mer information finns i [Använda en REST-proxy](./kafka/rest-proxy.md).|
|Identitet|Valfritt: Välj en befintlig tjänstidentitet som tilldelats av användaren i listrutan. Mer information finns [i Hanterade identiteter i Azure HDInsight](./hdinsight-managed-identities.md).|

Välj **Nästa: Konfiguration + priser >>** för att gå vidare till nästa flik.

## <a name="configuration--pricing"></a>Konfiguration + prissättning

![HDInsight skapar klusterkonfiguration](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Fliken Konfiguration och prissättning")

Ange följande information på fliken **Konfiguration + priser:**

|Egenskap |Beskrivning |
|---|---|
|+ Lägg till program|Valfritt: Välj önskade program. Microsoft, oberoende programvaruleverantörer (ISV) eller så kan du utveckla dessa program. Mer information finns i [Installera program när klustret skapas](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Nodstorlek|Valfritt: Välj en nod av annan storlek.|
|Antal noder|Valfritt: Ange antalet noder för den angivna nodtypen. Om du planerar mer än 32 arbetsnoder väljer du en huvudnodstorlek med minst åtta kärnor och 14 GB RAM.If you plan on more than 32 worker nodes, select a head node size with least eight cores and 14-GB RAM. Planera noderna antingen när klustret skapas eller genom att skala klustret när du har skapat det.|
|Aktivera automatisk skalning|Valfritt: Markera kryssrutan för att aktivera funktionen. Mer information finns i [Skala Azure HDInsight-kluster automatiskt](./hdinsight-autoscale-clusters.md).|
|+ Lägg till skriptåtgärd|Valfritt: Det här alternativet fungerar om du vill använda ett anpassat skript för att anpassa ett kluster, eftersom klustret skapas. Mer information om skriptåtgärder finns i [Anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).|

Välj **Granska + skapa >>** för att validera klusterkonfigurationen och gå vidare till den sista fliken.

## <a name="review--create"></a>Granska + skapa

![HDInsight skapar klustersammanfattning](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Ange antal klusternoder")

Granska inställningarna. Välj **Skapa** så att klustret skapas.

Det tar lite tid att skapa klustret, normalt cirka 20 minuter. Övervaka **meddelanden** för att kontrollera etableringsprocessen.

## <a name="post-creation"></a>Skapa inlägg

När skapandeprocessen har slutförts väljer du **Gå till resurs** från meddelandet Om **distribution.** Klusterfönstret innehåller följande information.

![Översikt över HDI Azure-portalkluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Klusteregenskaper")

Några av ikonerna i fönstret förklaras på följande sätt:

|Egenskap | Beskrivning |
|---|---|
|Översikt|Innehåller all viktig information om klustret. Exempel är namnet, resursgruppen som det tillhör, platsen, operativsystemet och URL:en för klusterinstrumentpanelen.|
|Instrumentpaneler för kluster|Leder dig till Ambari-portalen som är associerad med klustret.|
|SSH + Klusterinloggning|Innehåller information som behövs för att komma åt klustret med hjälp av SSH.|
|Ta bort|Tar bort HDInsight-klustret.|

## <a name="customize-clusters"></a>Anpassa kluster

* [Anpassa HDInsight-kluster med Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Anpassa Linux-baserade HDInsight-kluster med hjälp av skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Ta bort klustret

Se [Ta bort ett HDInsight-kluster med webbläsaren, PowerShell eller Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

Du har skapat ett HDInsight-kluster. Lär dig nu hur du arbetar med klustret.

* [Använda Apache Hive med HDInsight](hadoop/hdinsight-use-hive.md)
* [Komma igång med Apache HBase på HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Utveckla Java-topologier för Apache Storm på HDInsight](storm/apache-storm-develop-java-topology.md)
* [Skapa ett fristående program med Scala](spark/apache-spark-create-standalone-application.md)
