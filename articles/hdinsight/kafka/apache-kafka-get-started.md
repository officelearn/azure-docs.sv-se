---
title: Konfigurera Apache Kafka på HDInsight med hjälp av Azure portal – Snabbstart
description: I den här snabbstarten lär du dig hur du skapar ett Apache Kafka-kluster på Azure HDInsight med hjälp av Azure Portal. Du lär dig också om Kafka-ämnen, -prenumeranter och -konsumenter.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: mvc
ms.topic: quickstart
ms.date: 03/26/2019
ms.openlocfilehash: 83b6cd910dc644fcbe040854439c2c374649177a
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758463"
---
# <a name="quickstart-create-an-apache-kafka-on-hdinsight-cluster"></a>Snabbstart: Skapa ett Apache Kafka på HDInsight-kluster

Apache Kafka är en distribuerad direktuppspelningsplattform med öppen källkod. Den används ofta som en asynkron meddelandekö eftersom den innehåller funktioner som påminner om en publicera-prenumerera-meddelandekö. 

I den här snabbstarten lär du dig hur du skapar ett [Apache Kafka](https://kafka.apache.org)-kluster med hjälp av Azure Portal. Du kommer också lära dig hur du kan använda de inkluderade verktygen för att skicka och ta emot meddelanden med Apache Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]  
> Apache Kafka-API:et kan endast användas av resurser i samma virtuella nätverk. I den här snabbstarten har du direkt åtkomst till klustret med SSH. Om du vill ansluta andra tjänster, nätverk eller virtuella datorer till Apache Kafka måste du först skapa ett virtuellt nätverk och sedan skapa resurser i nätverket.
>
> Mer information finns i dokumentet [Anslut till Apache Kafka via ett virtuellt nätverk](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* En SSH-klient. Stegen i det här dokumentet använder SSH för att ansluta till klustret.

    Kommandot `ssh` tillhandahålls som standard i Linux-, Unix- och macOS-system. I Windows 10 använder du någon av följande metoder för att installera kommandot `ssh`:

  * Använd [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). Cloud Shell tillhandahåller `ssh`-kommandot och kan konfigureras för att använda antingen Bash eller PowerShell som gränssnittsmiljö.

  * [Installera Windows-undersystemet för Linux](https://docs.microsoft.com/windows/wsl/install-win10). De Linux-distributioner som är tillgängliga via Microsoft Store tillhandahåller `ssh`-kommandot.

    > [!IMPORTANT]  
    > Stegen i det här dokumentet förutsätter att du använder någon av de SSH-klienter som nämns ovan. Om du använder en annan SSH-klient och stöter på problem så sök efter en lösning i SSH-klientens dokumentation.
    >
    > Mer information finns i dokumentet [Använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-an-apache-kafka-cluster"></a>Skapa ett Apache Kafka-kluster

Använd följande steg om du vill skapa ett Apache Kafka i HDInsight-kluster:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **+ Skapa en resurs** från menyn till vänster.

1. Under **Azure Marketplace** väljer du **Analys**.

1. Under **Aktuella** väljer du **HDInsight**.
   
    ![Skapa ett HDInsight-kluster](./media/apache-kafka-get-started/create-hdinsight.png)

2. Från **Grundläggande**, ange eller välj följande information:

    | Inställning | Värde |
    | --- | --- |
    | Klusternamn | Ett unikt namn för HDInsight-klustret. |
    | Prenumeration | Välj din prenumeration. |
    
   Välj __Klustertyp__ för att visa **Klusterkonfiguration**.
   
   ![Grundläggande konfiguration för Apache Kafka-kluster i HDInsight](./media/apache-kafka-get-started/hdinsight-basic-configuration-1.png)

3. I __Klusterkonfiguration__ väljer du följande värden:

    | Inställning | Värde |
    | --- | --- |
    | Typ av kluster | Kafka |
    | Version | Kafka 1.1.0 (HDI 3.6) |

    Använd knappen **Välj** för att spara klustertypsinställningarna och återgå till __Grundläggande__.

    ![Välj klustertyp](./media/apache-kafka-get-started/kafka-cluster-type.png)

4. Från __Grundläggande__, ange eller välj följande information:

    | Inställning | Värde |
    | --- | --- |
    | Användarnamn för klusterinloggning | Inloggningsnamnet vid åtkomst till webbtjänster eller REST API:er som hanteras i klustret. Behåll standardvärdet (admin). |
    | Lösenord för klusterinloggning | Inloggningslösenordet vid åtkomst till webbtjänster eller REST API:er som hanteras i klustret. |
    | Secure Shell (SSH)-användarnamn | Den inloggning som används vid åtkomst till klustret via SSH. Som standard är lösenordet detsamma som lösenordet för klusterinloggning. |
    | Resursgrupp | Resursgruppen som klustret ska skapas i. |
    | Plats | Azure-region som klustret ska skapas i. |

    > [!TIP]  
    > Varje Azure-region (plats) har _feldomäner_. En feldomän är en logisk gruppering av underliggande maskinvara i ett Azure-datacenter. Varje feldomän delar en gemensam strömkälla och nätverksbrytare. De virtuella datorer och hanterade diskar som implementerar noderna i ett HDInsight-kluster är fördelade mellan dessa feldomäner. Den här arkitekturen begränsar de potentiella problemen vid fysiska maskinvarufel.
    >
    > För hög datatillgänglighet, välj en plats (region) som innehåller __tre feldomäner__. Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

   ![Välj en prenumeration](./media/apache-kafka-get-started/hdinsight-basic-configuration-2.png)

    Använd knappen __Nästa__ för att slutföra den grundläggande konfigurationen.

5. Använd standardinställningarna för säkerhet för den här kursen. Du kan läsa mer om Enterprise Security-paketet i [Konfigurera ett HDInsight-kluster med Enterprise Security-paket med hjälp av Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md). Mer information om hur du använder den egen nyckel för Apache Kafka-diskkryptering finns i [Använd en egen nyckel för Apache Kafka på Azure HDInsight](apache-kafka-byok.md)

   Om du vill ansluta ditt kluster till ett virtuellt nätverk väljer du ett virtuellt nätverk i listrutan **Virtuellt nätverk**.

   ![Lägga till kluster i ett virtuellt nätverk](./media/apache-kafka-get-started/kafka-security-config.png)

6. Från **Lagring** ska du välja eller skapa ett lagringskonto. Lämna övriga fält på standardvärden för stegen i det här dokumentet. Spara lagringskonfigurationen genom att klicka på __Nästa__. Mer information om att använda Data Lake Storage Gen2 finns i [Snabbstart: Konfigurera kluster i HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

   ![Ange inställningarna för lagringskontot för HDInsight](./media/apache-kafka-get-started/storage-configuration.png)

7. Från __Program (valfritt)__ väljer du __Nästa__ för att fortsätta med standardinställningarna.

8. Från __Klusterstorlek__ väljer du __Nästa__ för att fortsätta med standardinställningarna.

    > [!IMPORTANT]  
    > För att kunna garantera tillgängligheten för Apache Kafka på HDInsight, måste __antalet arbetsnoder__ anges till 3 eller fler. Standardvärdet är 4.
    
    > [!TIP]  
    > Antalet **diskar per arbetsnod** konfigurerar hur skalbart Apache Kafka i HDInsight är. Apache Kafka på HDInsight använder de virtuella datorernas lokala diskar i klustret för att lagra data. Apache Kafka är I/O-tungt, och därför används [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) för att tillhandahålla hög genomströmning och mer lagringsutrymme per nod. Typen av hanterade diskar kan vara antingen __Standard__ (HDD) eller __Premium__ (SSD). Vilken typ av disk som används beror på vilken VM-storlek arbetsnoderna (Apache Kafka-broker) använder. Premiumdiskar används automatiskt med virtuella datorer i DS- och GS-serien. Alla andra typer av virtuella dator använder standard.

   ![Ange storleken på Apache Kafka-klustret](./media/apache-kafka-get-started/kafka-cluster-size.png)

9. Från __Avancerade inställningar__ väljer du __Nästa__ för att fortsätta med standardinställningarna.

10. Från **Sammanfattning** kan du granska konfigurationen för klustret. Använd länkarna __Redigera__ om du behöver ändra eventuella inställningar som är felaktiga. Till sist skapar du klustret genom att klicka på Skapa.
   
    ![Sammanfattning av klusterkonfiguration](./media/apache-kafka-get-started/kafka-configuration-summary.png)
   
    > [!NOTE]
    > Det kan ta upp till 20 minuter att skapa klustret.

## <a name="connect-to-the-cluster"></a>Anslut till klustret

1. Anslut till Apache Kafka-klustrets primära huvudnod med följande kommando. Ersätt `sshuser` med SSH-användarnamnet. Ersätt `mykafka` med namnet på Apache Kafka-klustret.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. När du ansluter till HDInsight första gången kan SSH-klienten visa en varning om att värdens äkthet inte kan fastställas. Skriv __ja__ när du uppmanas till detta och tryck sedan på __Retur__ så läggs värden till i SSH-klientens lista över betrodda servrar.

3. Ange SSH-användarens lösenord när du uppmanas till detta.

När du är ansluten visas ett meddelande av följande slag:

```text
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
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Hämta information om värden i Apache Zookeeper och Broker

När du arbetar med Kafka måste du känna till *Apache Zookeeper*- och *Broker*-värdarna. Dessa värdar används med Apache Kafka-API och många av de verktyg som levereras med Kafka.

I det här avsnittet hämtar du värdinformation från om värden från Apache Ambari REST API på klustret.

1. Installera verktyget `jq` genom att använda följande kommando från SSH-anslutningen till klustret. Det här verktyget används för att parsa JSON-dokument och är användbart när du ska hämta värdinformation:
   
    ```bash
    sudo apt -y install jq
    ```

2. Använd följande kommando om du vill ange en miljövariabel i klustrets namn:

    > [!Important]
    > Ange klusternamnet med små bokstäver för det här kommandot, även om den har etablerats med versaler i namnet. Versaler namn kan inte användas med Zookeeper och broker information.

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ange Apache Kafka-klustrets namn när du uppmanas till detta.

3. Använd följande kommando om du vill ange en miljövariabel med Zookeeper-värdens information:
    
    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    > [!TIP]
    > Det här kommandot frågar Ambari-tjänsten på klustrets huvudnod direkt. Du kan också komma åt ambari med hjälp av den offentliga adressen för `https://$CLUSTERNAME.azurehdinsight.net:80/`. Vissa nätverkskonfigurationer kan förhindra åtkomst till den offentliga adressen. Till exempel, användning av nätverkssäkerhetsgrupper (NSG) för att begränsa åtkomsten till HDInsight i ett virtuellt nätverk.

    Ange klusterinloggningskontots lösenord när du uppmanas till detta (inte SSH-kontot).

    > [!NOTE]
    > Detta kommando hämtar alla Zookeeper-värdar och returnerar bara de första två posterna. Det beror på att det är bra att ha viss redundans ifall en värd inte kan nås.

4. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Det här kommandot returnerar information liknande följande text:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Använd följande kommando om du vill ange en miljövariabel med värdinformation för Kafka-broker:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Ange klusterinloggningskontots lösenord när du uppmanas till detta (inte SSH-kontot).

6. Använd följande kommando om du vill kontrollera att miljövariabeln är korrekt:

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

    Det här kommandot ansluter till Zookeeper med hjälp av värdinformationen som lagras i `$KAFKAZKHOSTS`. Det skapar ett Apache Kafka-ämne med namnet **test**. 

    * Data som lagras i det här ämnet partitioneras över åtta partitioner.

    * Varje partition replikeras mellan tre arbetarnoder i klustret.

        > [!IMPORTANT]
        > Om du har skapat klustret i en Azure-region som tillhandahåller tre feldomäner så använd replikeringsfaktorn 3. I annat fall använder du replikeringsfaktorn 4.
        
        I områden med tre feldomäner kan replikeringsfaktorn 3 tillåta att repliker sprids till feldomänerna. I områden med två feldomäner kan replikeringsfaktorn 4 tillåta att repliker sprids jämnt över domänerna.
        
        Om du vill ha information om antalet feldomäner i en region läser du dokumentet [Availability of Linux virtual machines](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) (Tillgänglighet för virtuella Linux-datorer).

        > [!IMPORTANT] 
        > Apache Kafka har ingen information om Azure-feldomäner. När du skapar partitionsrepliker för ämnen kanske det inte distribueras repliker korrekt för hög tillgänglighet.

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

    > [!NOTE]
    > Om du använder en äldre version av Kafka ersätter du `--bootstrap-server $KAFKABROKERS` med `--zookeeper $KAFKAZKHOSTS`.

4. Använd __Ctrl + C__ om du vill stoppa konsumenten.

Du kan också programmässigt skapa producenter och konsumenter. Ett exempel på användning av detta API finns i dokumentet [Apache Kafka-producent- och konsument-API med HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Rensa resurser

För att rensa de resurser som har skapats med den här snabbstarten kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

> [!WARNING]
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används.
> 
> Om du tar bort en Apache Kafka i ett HDInsight-kluster tas alla data som lagrats i Kafka bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använda Apache Spark med Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)

