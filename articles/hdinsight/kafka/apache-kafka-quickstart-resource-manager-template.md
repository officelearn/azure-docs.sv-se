---
title: 'Snabbstart: Apache Kafka med Hjälp av Azure Resource Manager - HDInsight'
description: I den här snabbstarten får du lära dig hur du skapar ett Apache Kafka-kluster på Azure HDInsight med Azure Resource Manager-mallen. Du lär dig också om Kafka-ämnen, -prenumeranter och -konsumenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: f5f92044a0274b809388eeb164be9f1587013e0b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064622"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Snabbstart: Skapa Apache Kafka-kluster i Azure HDInsight med hjälp av Resource Manager-mallen

I den här snabbstarten använder du en Azure Resource Manager-mall för att skapa ett [Apache Kafka-kluster](./apache-kafka-introduction.md) i Azure HDInsight. Kafka är en distribuerad direktuppspelningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. I den här snabbstarten har du direkt åtkomst till klustret med SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket. Mer information finns i dokumentet [Anslut till Apache Kafka via ett virtuellt nätverk](apache-kafka-connect-vpn-gateway.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-apache-kafka-cluster"></a>Skapa ett Apache Kafka-kluster

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-kafka).

:::code language="json" source="~/quickstart-templates/101-hdinsight-kafka/azuredeploy.json" range="1-150":::

Två Azure-resurser definieras i mallen:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): skapa ett Azure Storage-konto.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): skapa ett HDInsight-kluster.

### <a name="deploy-the-template"></a>Distribuera mallen

1. Välj knappen **Distribuera till Azure** nedan om du vill logga in på Azure och öppna Resource Manager-mallen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Ange eller välj följande värden:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|Välj den Azure-prenumeration som används för klustret i listrutan.|
    |Resursgrupp|Välj din befintliga resursgrupp i listrutan eller välj **Skapa ny**.|
    |Location|Värdet fylls i automatiskt med den plats som används för resursgruppen.|
    |Klusternamn|Ange ett globalt unikt namn. För den här mallen använder du bara gemener och siffror.|
    |Användarnamn för klusterinloggning|Ange användarnamn, standard är **admin**.|
    |Lösenord för klusterinloggning|Ange ett lösenord. Lösenordet måste vara minst 10 tecken långt och innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (utom tecknen ' " ' ). |
    |Ssh-användarnamn|Ange användarnamn, standard är **sshuser**|
    |Ssh lösenord|Ange lösenordet.|

    ![En skärmbild av mallegenskaperna](./media/apache-kafka-quickstart-resource-manager-template/resource-manager-template-kafka.png)

1. Läs igenom **villkoren**. Välj sedan **jag godkänner de villkor som anges ovan,** sedan **Köp**. Du får ett meddelande om att distributionen pågår. Det tar cirka 20 minuter att skapa ett kluster.

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

När klustret har skapats får du ett **meddelande om distribution** med länken Gå till **resurs.** På sidan Resursgrupp visas en lista över ditt nya HDInsight-kluster och standardlagringsutrymmet som är associerat till klustret. Varje kluster har ett [Azure Storage-konto](../hdinsight-hadoop-use-blob-storage.md) eller ett [Azure Data Lake Storage-kontoberoende.](../hdinsight-hadoop-use-data-lake-store.md) Det kallas standardlagringskontot. HDInsight-klustret och dess standardlagringskonto måste samlokaliseras i samma Azure-region. Om du tar bort kluster tas inte lagringskontot bort.

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a>Hämta information om värden i Apache Zookeeper och Broker

När du arbetar med Kafka måste du känna till *Apache Zookeeper*- och *Broker*-värdarna. Dessa värdar används med Kafka-API och många av de verktyg som levereras med Kafka.

I det här avsnittet hämtas information om värden från klustrets Ambari REST API.

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Från SSH-anslutningen använder du följande `jq` kommando för att installera verktyget. Det här verktyget används för att parsa JSON-dokument och är användbart när du ska hämta värdinformation:

    ```bash
    sudo apt -y install jq
    ```

1. Använd följande kommando om du vill ange en miljövariabel i klustrets namn:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ange Kafka-klustrets namn när du uppmanas till detta.

1. Om du vill ange en miljövariabel med Zookeeper-värdinformation använder du kommandot nedan. Kommandot hämtar alla Zookeeper värdar och sedan returnerar bara de två första posterna. Det beror på att det är bra att ha viss redundans ifall en värd inte kan nås.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Ange klusterinloggningskontots lösenord när du uppmanas till detta (inte SSH-kontot).

1. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Det här kommandot returnerar information liknande följande text:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Använd följande kommando om du vill ange en miljövariabel med Kafka-värdens information om asynkron meddelandekö:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Ange klusterinloggningskontots lösenord när du uppmanas till detta (inte SSH-kontot).

1. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Det här kommandot returnerar information liknande följande text:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Hantera Apache Kafka-ämnen

Kafka lagrar dataströmmar i kategorier som kallas *ämnen*. Du kan hantera ämnena med verktyget `kafka-topics.sh`.

* **Du skapar ett ämne** med följande kommando i SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot ansluter till Zookeeper med hjälp av värdinformationen som lagras i `$KAFKAZKHOSTS`. Det skapar sedan ett Kafka-ämne med namnet **test**.

    * Data som lagras i det här ämnet partitioneras över åtta partitioner.

    * Varje partition replikeras mellan tre arbetarnoder i klustret.

        Om du har skapat klustret i en Azure-region som tillhandahåller tre feldomäner så använd replikeringsfaktorn 3. I annat fall använder du replikeringsfaktorn 4.
        
        I områden med tre feldomäner kan replikeringsfaktorn 3 tillåta att repliker sprids till feldomänerna. I områden med två feldomäner kan replikeringsfaktorn 4 tillåta att repliker sprids jämnt över domänerna.
        
        Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

        Kafka är inte medveten om Azure-feldomäner. När du skapar partitionsrepliker för ämnen kanske det inte distribueras repliker korrekt för hög tillgänglighet.

        Garantera hög tillgänglighet med hjälp av [verktyget för partitionsombalansering för Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Du måste köra det här verktyget från en SSH-anslutning till ditt Kafka-klusters huvudnod.

        Om du vill ha bästa möjliga tillgänglighet för dina Kafka-data måste du balansera om ämnets partitionsrepliker när:

        * Du skapar ett nytt ämne eller en ny partition

        * Du skalar upp ett kluster

* **Om du vill lista ämnena** använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot listar de ämnen som är tillgängliga på Kafka-klustret.

* **Om du vill ta bort ett ämne** använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot tar bort ämnet med namnet `topicname`.

    > [!WARNING]  
    > Om du tar bort ämnet `test`, som du skapade tidigare, måste du återskapa det. Det används under steg senare i det här dokumentet.

Om du vill ha mer information om vilka kommandon som är tillgängliga med verktyget `kafka-topics.sh` använder du följande kommando:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Skapa och använda poster

Kafka lagrar *poster* i ämnen. Poster produceras av *producenter*, och används av *konsumenter*. Producenter och konsumenter kommunicerar med *Kafka-koordinator*tjänsten. Varje arbetsnod i HDInsight-klustret är en Kafka-koordinatorvärd.

Använd följande steg för att lagra poster i det testämne som du skapade tidigare och sedan läsa dem med en konsument:

1. Om du vill skriva poster i ämnet använder du verktyget `kafka-console-producer.sh` från SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Efter det här kommandot kommer du till en tom rad.

1. Skriv in ett textmeddelande på den tomma raden och tryck på Retur. Ange några meddelanden på det här sättet och använd sedan **Ctrl + C** för att komma tillbaka till den vanliga kommandotolken. Varje rad skickas som en separat post till Kafka-ämnet.

1. Om du vill läsa poster i ämnet använder du verktyget `kafka-console-consumer.sh` från SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Med det här kommandot hämtar du posterna från ämnet och visar dem. Med hjälp av `--from-beginning` anges att konsumenten ska starta från början av direktuppspelningen så att alla poster hämtas.

    Om du använder en äldre version av `--bootstrap-server $KAFKABROKERS` Kafka ersätter du med `--zookeeper $KAFKAZKHOSTS`.

1. Använd __Ctrl + C__ om du vill stoppa konsumenten.

Du kan också programmässigt skapa producenter och konsumenter. Ett exempel på användning av detta API finns i dokumentet [Apache Kafka-producent- och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kanske du vill ta bort klustret. Med HDInsight lagras dina data i Azure Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är många gånger högre än avgifterna för lagring, är det ekonomiskt meningsfullt att ta bort kluster när de inte används.

Navigera till klustret på Azure-portalen och välj **Ta bort**.

![Resurshanterarens mall HBase](./media/apache-kafka-quickstart-resource-manager-template/azure-portal-delete-kafka.png)

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. Genom att ta bort resursgruppen tar du bort både HDInsight-klustret och standardlagringskontot.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du skapar ett Apache Kafka-kluster i HDInsight med hjälp av en Resource Manager-mall. I nästa artikel får du lära dig hur du skapar ett program som använder Apache Kafka Streams API och kör det med Kafka på HDInsight.

> [!div class="nextstepaction"]
> [Använda Apache Kafka streams API i Azure HDInsight](./apache-kafka-streams-api.md)
