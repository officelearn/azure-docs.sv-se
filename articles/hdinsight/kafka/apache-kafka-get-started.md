---
title: 'Snabbstart: Konfigurera Apache Kafka på HDInsight med Azure-portal'
description: I den här snabbstarten lär du dig hur du skapar ett Apache Kafka-kluster på Azure HDInsight med hjälp av Azure Portal. Du lär dig också om Kafka-ämnen, -prenumeranter och -konsumenter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/24/2020
ms.openlocfilehash: 90f7010970f70379c8adecc4214c44d896a1beaf
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130263"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-azure-portal"></a>Snabbstart: Skapa Apache Kafka-kluster i Azure HDInsight med Azure-portal

[Apache Kafka](./apache-kafka-introduction.md) är en distribuerad strömningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö.

I den här snabbstarten lär du dig hur du skapar ett Apache Kafka-kluster med hjälp av Azure Portal. Du kommer också lära dig hur du kan använda de inkluderade verktygen för att skicka och ta emot meddelanden med Apache Kafka. Detaljerade förklaringar av tillgängliga konfigurationer finns i [Konfigurera kluster i HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Mer information om hur portalen används för att skapa kluster finns [i Skapa kluster i portalen](../hdinsight-hadoop-create-linux-clusters-portal.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Apache Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. I den här snabbstarten har du direkt åtkomst till klustret med SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Apache Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket. Mer information finns i dokumentet [Anslut till Apache Kafka via ett virtuellt nätverk](apache-kafka-connect-vpn-gateway.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Skapa ett Apache Kafka-kluster

Så här skapar du ett Apache Kafka-kluster på HDInsight:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj + Skapa **en resurs**på den övre menyn .

    ![Azure portal skapa resurs HDInsight](./media/apache-kafka-get-started/azure-portal-create-resource.png)

1. Välj **Analytics** > **Azure HDInsight** för att gå till **sidan Skapa HDInsight-kluster.**

1. Ange följande information på fliken **Grunderna:**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Prenumeration    |  Välj den Azure-prenumeration som används för klustret i listrutan. |
    |Resursgrupp     | Skapa en resursgrupp eller välj en befintlig resursgrupp.  En resursgrupp är en container med Azure-komponenter.  I det här fallet innehåller resursgruppen HDInsight-klustret och det beroende Azure Storage-kontot. |
    |Klusternamn   | Ange ett globalt unikt namn. Namnet kan bestå av upp till 59 tecken, inklusive bokstäver, siffror och bindestreck. De första och sista tecknen i namnet får inte vara bindestreck. |
    |Region    | Välj en region där klustret skapas i listrutan.  Välj en region närmare dig för bättre prestanda. |
    |Klustertyp| Välj **Välj klustertyp** om du vill öppna en lista. Välj **Kafka** som klustertyp i listan.|
    |Version|Standardversionen för klustertypen anges. Välj i listrutan om du vill ange en annan version.|
    |Användarnamn och lösenord för klusterinloggning    | Standardinloggningsnamnet är **admin**. Lösenordet måste vara minst 10 tecken långt och innehålla minst en siffra, en versal och en gemen, ett icke-alfanumeriskt tecken (utom tecknen ' " ' . \) Se till att du **inte anger** vanliga lösenord som Pass@word1.|
    |Secure Shell (SSH)-användarnamn | Standardanvändarnamnet är **sshuser**.  Du kan ange ett annat namn som SSH-användarnamn. |
    |Använda lösenord för klusterinloggning för SSH| Markera den här kryssrutan om du vill använda samma lösenord för SSH-användare som det du angav för klusterinloggningsanvändaren.|

   ![Azure portal skapa klustergrunder](./media/apache-kafka-get-started/azure-portal-cluster-basics.png)

    Varje Azure-region (plats) har _feldomäner_. En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.

    För hög datatillgänglighet, välj en plats (region) som innehåller __tre feldomäner__. Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

    Välj fliken **Nästa: Lagring >>** för att gå vidare till lagringsinställningarna.

1. Ange följande värden på fliken **Lagring:**

    |Egenskap  |Beskrivning  |
    |---------|---------|
    |Primär lagringstyp|Använd standardvärdet **Azure Storage**.|
    |Urvalsmetod|Använd standardvärdet **Välj från listan**.|
    |Primärt lagringskonto|Använd listrutan för att välja ett befintligt lagringskonto eller välj **Skapa nytt**. Om du skapar ett nytt konto måste namnet vara mellan 3 och 24 tecken långt och kan innehålla siffror och gemener endast|
    |Container|Använd det automatiskt befolkade värdet.|

    ![HDInsight Linux komma igång ger klusterlagringsvärden](./media/apache-kafka-get-started/azure-portal-cluster-storage.png "Ange lagringsvärden för att skapa ett HDInsight-kluster")

    Välj fliken **Säkerhet + nätverk.**

1. Använd standardinställningarna för säkerhet för den här kursen. Du kan läsa mer om Enterprise Security-paketet i [Konfigurera ett HDInsight-kluster med Enterprise Security-paket med hjälp av Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Om du vill veta hur du använder din egen nyckel för Apache Kafka Diskkryptering, besök [Kundhanterad nyckeldiskkryptering](../disk-encryption.md)

   Om du vill ansluta ditt kluster till ett virtuellt nätverk väljer du ett virtuellt nätverk i listrutan **Virtuellt nätverk**.

   ![Lägga till kluster i ett virtuellt nätverk](./media/apache-kafka-get-started/azure-portal-cluster-security-networking-kafka-vnet.png)

    Välj fliken **Konfiguration + prissättning.**

1. För att garantera tillgängligheten för Apache Kafka på HDInsight måste __antalet noderpost__ för **arbetarnod** anges till 3 eller högre. Standardvärdet är 4.

    **Standarddiskarna per arbetsnodpost** konfigurerar skalbarheten för Apache Kafka på HDInsight. Apache Kafka på HDInsight använder de virtuella datorernas lokala diskar i klustret för att lagra data. Apache Kafka är I/O-tungt, och därför används [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) för att tillhandahålla hög genomströmning och mer lagringsutrymme per nod. Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Vilken typ av disk som används beror på vilken VM-storlek arbetsnoderna (Apache Kafka-broker) använder. Premiumdiskar används automatiskt med virtuella datorer i DS- och GS-serien. Alla andra typer av virtuella dator använder standard.

   ![Ange storleken på Apache Kafka-klustret](./media/apache-kafka-get-started/azure-portal-cluster-configuration-pricing-kafka.png)

    Välj fliken **Granska + skapa.**

1. Granska konfigurationen för klustret. Ändra alla felaktiga inställningar. Slutligen väljer du **Skapa** för att skapa klustret.

    ![sammanfattning av kafka-klusterkonfiguration](./media/apache-kafka-get-started/azure-portal-cluster-review-create-kafka.png)

    Det kan ta upp till 20 minuter att skapa klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ange SSH-användarens lösenord när du uppmanas till detta.

    När du är ansluten visas ett meddelande av följande slag:

    ```output
    Authorized uses only. All activity may be monitored and reported.
    Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    83 packages can be updated.
    37 updates are security updates.


    Welcome to Apache Kafka on HDInsight.

    Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
    ```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Hämta information om värden i Apache Zookeeper och Broker

När du arbetar med Kafka måste du känna till *Apache Zookeeper*- och *Broker*-värdarna. Dessa värdar används med Apache Kafka-API och många av de verktyg som levereras med Kafka.

I det här avsnittet hämtar du värdinformation från om värden från Apache Ambari REST API på klustret.

1. Installera [jq](https://stedolan.github.io/jq/), en kommandorads-JSON-processor. Det här verktyget används för att tolka JSON-dokument och är användbart när du analyserar värdinformationen. Från den öppna SSH-anslutningen anger `jq`du följande kommando för att installera:

    ```bash
    sudo apt -y install jq
    ```

1. Ställ in lösenordsvariabel. Ersätt `PASSWORD` med lösenordet för klusterinloggning och ange sedan kommandot:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahera det korrekt skiftade klusternamnet. Den faktiska höljet för klusternamnet kan vara annorlunda än du förväntar dig, beroende på hur klustret skapades. Det här kommandot hämtar själva höljet och lagrar det sedan i en variabel. Ange följande kommando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Om du gör den här processen utanför klustret finns det en annan procedur för att lagra klusternamnet. Hämta klusternamnet i gemener från Azure-portalen. Ersätt sedan klusternamnet `<clustername>` för i följande kommando `export clusterName='<clustername>'`och kör det: .


1. Om du vill ange en miljövariabel med Zookeeper-värdinformation använder du kommandot nedan. Kommandot hämtar alla Zookeeper värdar och sedan returnerar bara de två första posterna. Det beror på att det är bra att ha viss redundans ifall en värd inte kan nås.

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Det här kommandot kräver Ambari-åtkomst. Om klustret ligger bakom en NSG kör du det här kommandot från en dator som kan komma åt Ambari. 

1. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash
    echo $KAFKAZKHOSTS
    ```

    Det här kommandot returnerar information liknande följande text:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. Använd följande kommando om du vill ange en miljövariabel med värdinformation för Kafka-broker:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > Det här kommandot kräver Ambari-åtkomst. Om klustret ligger bakom en NSG kör du det här kommandot från en dator som kan komma åt Ambari. 

1. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash
    echo $KAFKABROKERS
    ```

    Det här kommandot returnerar information liknande följande text:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Hantera Apache Kafka-ämnen

Kafka lagrar dataströmmar i kategorier som kallas *ämnen*. Du kan hantera ämnena med verktyget `kafka-topics.sh`.

* **Du skapar ett ämne** med följande kommando i SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot ansluter till Zookeeper med hjälp av värdinformationen som lagras i `$KAFKAZKHOSTS`. Det skapar ett Apache Kafka-ämne med namnet **test**.

    * Data som lagras i det här ämnet partitioneras över åtta partitioner.

    * Varje partition replikeras mellan tre arbetarnoder i klustret.

        Om du har skapat klustret i en Azure-region som tillhandahåller tre feldomäner så använd replikeringsfaktorn 3. I annat fall använder du replikeringsfaktorn 4.
        
        I områden med tre feldomäner kan replikeringsfaktorn 3 tillåta att repliker sprids till feldomänerna. I områden med två feldomäner kan replikeringsfaktorn 4 tillåta att repliker sprids jämnt över domänerna.
        
        Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

        Apache Kafka har ingen information om Azure-feldomäner. När du skapar partitionsrepliker för ämnen kanske det inte distribueras repliker korrekt för hög tillgänglighet.

        Garantera hög tillgänglighet med hjälp av [verktyget för partitionsombalansering för Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Du måste köra det här verktyget från en SSH-anslutning till ditt Apache Kafka-klusters huvudnod.

        Om du vill ha bästa möjliga tillgänglighet för dina Apache Kafka-data måste du balansera om ämnets partitionsrepliker när:

        * Du skapar ett nytt ämne eller en ny partition

        * Du skalar upp ett kluster

* **Om du vill lista ämnena** använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot listar de ämnen som är tillgängliga på Apache Kafka-klustret.

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

Kafka lagrar *poster* i ämnen. Poster produceras av *producenter*, och används av *konsumenter*. Producenter och konsumenter kommunicerar med *Kafka-koordinator*tjänsten. Varje arbetsnod i HDInsight-klustret är en Apache Kafka-brokervärd.

Använd följande steg för att lagra poster i det testämne som du skapade tidigare och sedan läsa dem med en konsument:

1. Om du vill skriva poster i ämnet använder du verktyget `kafka-console-producer.sh` från SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Efter det här kommandot kommer du till en tom rad.

2. Skriv in ett textmeddelande på den tomma raden och tryck på Retur. Ange några meddelanden på det här sättet och använd sedan **Ctrl + C** för att komma tillbaka till den vanliga kommandotolken. Varje rad skickas som en separat post till Apache Kafka-ämnet.

3. Om du vill läsa poster i ämnet använder du verktyget `kafka-console-consumer.sh` från SSH-anslutningen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Med det här kommandot hämtar du posterna från ämnet och visar dem. Med hjälp av `--from-beginning` anges att konsumenten ska starta från början av direktuppspelningen så att alla poster hämtas.

    Om du använder en äldre version av Kafka ersätter du `--bootstrap-server $KAFKABROKERS` med `--zookeeper $KAFKAZKHOSTS`.

4. Använd __Ctrl + C__ om du vill stoppa konsumenten.

Du kan också programmässigt skapa producenter och konsumenter. Ett exempel på användning av detta API finns i dokumentet [Apache Kafka-producent- och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Rensa resurser

För att rensa de resurser som har skapats med den här snabbstarten kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

> [!WARNING]  
> Om du tar bort ett Apache Kafka-kluster på HDInsight tas alla data som lagras i Kafka bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda Apache Spark med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)
