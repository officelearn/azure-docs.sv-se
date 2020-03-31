---
title: Använda Azure Kubernetes-tjänsten med Kafka på HDInsight
description: Lär dig hur du använder Kafka på HDInsight från behållaravbildningar som finns i Azure Kubernetes Service (AKS).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 6abb4f632535f1bda7e9f337f111ba372a624f2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239624"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Använda Azure Kubernetes-tjänsten med Apache Kafka på HDInsight

Lär dig hur du använder Azure Kubernetes Service (AKS) med [Apache Kafka](https://kafka.apache.org/) i HDInsight-klustret. Stegen i det här dokumentet använder ett Node.js-program som finns i AKS för att verifiera anslutningen till Kafka. Det här programmet använder [kafka-nodpaketet](https://www.npmjs.com/package/kafka-node) för att kommunicera med Kafka. Den använder [Socket.io](https://socket.io/) för händelsedrivna meddelanden mellan webbläsarklienten och backend-värden i AKS.

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Azure Kubernetes Service hanterar din värdmiljö för Kubernetes och gör det snabbt och enkelt att distribuera containerprogram. Med hjälp av ett virtuellt Azure-nätverk kan du ansluta de två tjänsterna.

> [!NOTE]  
> Fokus för det här dokumentet ligger på de steg som krävs för att Azure Kubernetes Service ska kunna kommunicera med Kafka på HDInsight. Exemplet i sig är bara en grundläggande Kafka-klient för att visa att konfigurationen fungerar.

## <a name="prerequisites"></a>Krav

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* En Azure-prenumeration

Det här dokumentet förutsätter att du är bekant med att skapa och använda följande Azure-tjänster:

* Kafka på HDInsight
* Azure Kubernetes Service
*  Azure Virtual Networks

Det här dokumentet förutsätter också att du har gått igenom [azure kubernetes-tjänstens självstudiekurs](../../aks/tutorial-kubernetes-prepare-app.md). Den här artikeln skapar en behållartjänst, skapar ett Kubernetes-kluster, `kubectl` ett behållarregister och konfigurerar verktyget.

## <a name="architecture"></a>Arkitektur

### <a name="network-topology"></a>Nätverkstopologi

Både HDInsight och AKS använder ett Virtuellt Azure-nätverk som en behållare för beräkningsresurser. Om du vill aktivera kommunikation mellan HDInsight och AKS måste du aktivera kommunikation mellan deras nätverk. Stegen i det här dokumentet använder Virtual Network Peering till nätverken. Andra anslutningar, till exempel VPN, bör också fungera. Mer information om peering finns i det [virtuella nätverks peering-dokumentet.](../../virtual-network/virtual-network-peering-overview.md)

Följande diagram illustrerar nätverkstopologin som används i det här dokumentet:

![HDInsight i ett virtuellt nätverk, AKS i ett annat, med hjälp av peering](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Namnmatchning är inte aktiverat mellan de peer-märkta nätverken, så IP-adressering används. Som standard är Kafka på HDInsight konfigurerat för att returnera värdnamn i stället för IP-adresser när klienter ansluter. Stegen i det här dokumentet ändrar Kafka så att IP-annonsering används i stället.

## <a name="create-an-azure-kubernetes-service-aks"></a>Skapa en Azure Kubernetes-tjänst (AKS)

Om du inte redan har ett AKS-kluster använder du något av följande dokument för att lära dig hur du skapar ett:

* [Distribuera ett AKS-kluster (Azure Kubernetes Service) – portal](../../aks/kubernetes-walkthrough-portal.md)
* [Distribuera ett AKS-kluster (Azure Kubernetes Service) – CLI](../../aks/kubernetes-walkthrough.md)

> [!IMPORTANT]  
> AKS skapar ett virtuellt nätverk under installationen i **ytterligare** en resursgrupp. Den ytterligare resursgruppen följer namngivningskonventionen **för MC_resourceGroup_AKSclusterName_location**.  
> Det här nätverket är peered till den som skapats för HDInsight i nästa avsnitt.

## <a name="configure-virtual-network-peering"></a>Konfigurera virtuell nätverks peering

### <a name="identify-preliminary-information"></a>Identifiera preliminär information

1. Leta upp den ytterligare **resursgruppen** som innehåller det virtuella nätverket för AKS-klustret från Azure-portalen. [Azure portal](https://portal.azure.com)

2. Välj den __virtuella nätverksresursen__ i resursgruppen. Spara namnet för senare användning.

3. Under **Inställningar**väljer du __Adressutrymme__. Observera adressutrymmet i listan.

### <a name="create-virtual-network"></a>Skapa det virtuella nätverket

1. Om du vill skapa ett virtuellt nätverk för HDInsight navigerar du till __+ Skapa ett__ > __virtuellt__ > __nätverk__för nätverk .

1. Skapa nätverket med följande riktlinjer för vissa egenskaper:

    |Egenskap | Värde |
    |---|---|
    |Adressutrymme|Du måste använda ett adressutrymme som inte överlappar det som används av AKS-klusternätverket.|
    |Location|Använd samma __plats__ för det virtuella nätverk som du använde för AKS-klustret.|

1. Vänta tills det virtuella nätverket har skapats innan du går till nästa steg.

### <a name="configure-peering"></a>Konfigurera peering

1. Om du vill konfigurera peering mellan HDInsight-nätverket och AKS-klusternätverket markerar du det virtuella nätverket och väljer sedan __Peerings__.

1. Välj __+ Lägg till__ och använd följande värden för att fylla i formuläret:

    |Egenskap |Värde |
    |---|---|
    |Namn på peering \<från detta VN-> till fjärrvirviska nätverk|Ange ett unikt namn för den här peering-konfigurationen.|
    |Virtuellt nätverk|välj det virtuella nätverket för **AKS-klustret**.|
    |Namn på peering \<från AKS VN \<> till denna VN->|Ange ett unikt namn.|

    Lämna alla andra fält till standardvärdet och välj sedan __OK__ för att konfigurera peering.

## <a name="create-apache-kafka-cluster-on-hdinsight"></a>Skapa Apache Kafka-kluster på HDInsight

När du skapar Kafka på HDInsight-klustret måste du ansluta till det virtuella nätverk som skapats tidigare för HDInsight. Mer information om hur du skapar ett Kafka-kluster finns i dokumentet [Skapa ett Apache Kafka-kluster.](apache-kafka-get-started.md)

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurera Apache Kafka IP-annonsering

Använd följande steg för att konfigurera Kafka för att annonsera IP-adresser i stället för domännamn:

1. Via en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net`. Ersätt CLUSTERNAME med namnet på Kafka på HDInsight-klustret.

    När du uppmanas till det använder du användarnamnet och lösenordet för HTTPS för klustret. Ambari-webbgränssnittet för klustret visas.

2. Om du vill visa information om Kafka väljer du __Kafka__ i listan till vänster.

    ![Servicelista med Kafka markerad](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Om du vill visa Kafka-konfigurationen väljer du __Configs__ uppifrån mitten.

    ![Konfiguration av Apache Ambari-tjänster](./media/apache-kafka-azure-container-services/select-kafka-config1.png)

4. Om du vill hitta __kafka-env-konfigurationen__ anger du `kafka-env` fältet __Filter__ längst upp till höger.

    ![Kafka konfiguration, för kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Om du vill konfigurera Kafka för att annonsera IP-adresser lägger du till följande text längst ned i __kafka-env-mallfältet:__

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera gränssnittet som Kafka lyssnar på anger du `listeners` i fältet __Filter__ längst upp till höger.

7. Om du vill konfigurera Kafka för att lyssna på alla `PLAINTEXT://0.0.0.0:9092`nätverksgränssnitt ändrar du värdet i __lyssnarfältet__ till .

8. Om du vill spara konfigurationsändringarna använder du knappen __Spara.__ Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    ![Apache Ambari spara konfiguration](./media/apache-kafka-azure-container-services/save-configuration-button.png)

9. Om du vill förhindra fel vid omstart av Kafka använder du knappen __Serviceåtgärder__ och väljer __Aktivera underhållsläge__. Välj OK för att slutföra den här åtgärden.

    ![Serviceåtgärder, med aktiverat underhåll markerat](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka använder du knappen __Starta om__ och väljer Starta om alla __som påverkas__. Bekräfta omstarten och använd sedan __OK-knappen__ när åtgärden är klar.

    ![Knappen Starta om med omstart markerad](./media/apache-kafka-azure-container-services/restart-required-button.png)

11. Om du vill inaktivera underhållsläge använder du knappen __Serviceåtgärder__ och väljer __Stäng av underhållsläge__. Välj **OK** för att slutföra den här åtgärden.

## <a name="test-the-configuration"></a>Testa konfigurationen

Nu kommunicerar Kafka och Azure Kubernetes Service via de peer-virtuella nätverken. Så här testar du den här anslutningen:

1. Skapa ett Kafka-ämne som används av testprogrammet. Information om hur du skapar Kafka-ämnen finns i [dokumentet Skapa ett Apache Kafka-kluster.](apache-kafka-get-started.md)

2. Hämta exempelprogrammet [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test)från .

3. Redigera `index.js` filen och ändra följande rader:

    * `var topic = 'mytopic'`: `mytopic` Ersätt med namnet på kafka-ämnet som används av det här programmet.
    * `var brokerHost = '176.16.0.13:9092`: `176.16.0.13` Ersätt med den interna IP-adressen för en av mäklarvärdarna för ditt kluster.

        Information om hur du hittar den interna IP-adressen för mäklarens värdar (workernodes) i klustret finns i [Apache Ambari REST API-dokumentet.](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) Välj IP-adress för en av de poster `wn`där domännamnet börjar med .

4. Från en kommandorad `src` i katalogen installerar du beroenden och använder Docker för att skapa en avbildning för distribution:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Paket som krävs av det här programmet checkas in `npm` i databasen, så du behöver inte använda verktyget för att installera dem.

5. Logga in på ditt Azure Container Registry (ACR) och hitta loginServer-namnet:

    ```azurecli
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Om du inte känner till ditt Azure-containerregisternamn eller inte är bekant med att använda Azure CLI för att arbeta med Azure Kubernetes-tjänsten läser du [AKS-självstudiekurserna](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tagga `kafka-aks-test` den lokala bilden med inloggningservern för din ACR. Lägg `:v1` också till i slutet för att ange bildversionen:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Skicka avbildningen till registret:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```

    Den här åtgärden tar flera minuter att slutföra.

8. Redigera Kubernetes manifestfil`kafka-aks-test.yaml`( `microsoft` ) och ersätt med ACR loginServer-namnet som hämtats i steg 4.

9. Använd följande kommando för att distribuera programinställningarna från manifestet:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Använd följande kommando för `EXTERNAL-IP` att titta efter programmets:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    När en extern IP-adress har tilldelats använder du __CTRL + C__ för att avsluta klockan

11. Öppna en webbläsare och ange den externa IP-adressen för tjänsten. Du kommer till en sida som liknar följande bild:

    ![Apache Kafka test webbsida bild](./media/apache-kafka-azure-container-services/test-web-page-image1.png)

12. Skriv text i fältet och välj sedan knappen __Skicka.__ Uppgifterna skickas till Kafka. Sedan läser Kafka-konsumenten i programmet meddelandet och lägger till det i avsnittet __Meddelanden från Kafka.__

    > [!WARNING]  
    > Du kan få flera kopior av ett meddelande. Det här problemet uppstår vanligtvis när du uppdaterar webbläsaren efter anslutning eller öppnar flera webbläsaranslutningar till programmet.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)

* [Använd MirrorMaker för att skapa en kopia av Apache Kafka på HDInsight](apache-kafka-mirroring.md)

* [Använd Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Använd Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Ansluta till Apache Kafka via ett virtuellt Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
