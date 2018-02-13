---
title: "Använda Azure Container Service med Kafka på HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Kafka på HDInsight från behållaren avbildningar finns i Azure Container Service (AKS)."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2018
ms.author: larryfr
ms.openlocfilehash: 53342e11476a307bb6af356eb40fe51928041822
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
---
# <a name="use-azure-container-services-with-kafka-on-hdinsight"></a>Använda Azure-Behållartjänster med Kafka på HDInsight

Lär dig hur du använder Azure behållaren tjänster (AKS) med Kafka på HDInsight-kluster. Stegen i det här dokumentet använder ett Node.js-program finns i AKS för att verifiera anslutningarna till Kafka. Det här programmet använder den [kafka nod](https://www.npmjs.com/package/kafka-node) paketet för att kommunicera med Kafka. Den använder [Socket.io](https://socket.io/) för händelsen som drivs meddelandehantering mellan klientens webbläsare och serverdelen finns i AKS.

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Azure Container Service hanterar din värdmiljö Kubernetes och gör det snabbt och enkelt att distribuera av program. Med ett virtuellt Azure-nätverk kan ansluta du de två tjänsterna.

> [!NOTE]
> Det här dokumentet fokuserar på de steg som krävs för att aktivera Azure Behållartjänster kan kommunicera med Kafka på HDInsight. Exemplet själva är bara en grundläggande Kafka klient för att demonstrera att konfigurationen fungerar.

## <a name="prerequisites"></a>Förutsättningar

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* En Azure-prenumeration

Det här dokumentet förutsätts att du är bekant med hjälp av följande Azure-tjänster och skapa:

* Kafka på HDInsight
* Azure Container Service
* Azure Virtual Networks

Det här dokumentet förutsätter också att du har gått igenom de [Azure Behållartjänster kursen](../../aks/tutorial-kubernetes-prepare-app.md). Den här självstudiekursen skapar en behållartjänst, skapas ett Kubernetes kluster, en behållare i registret och konfigurerar den `kubectl` verktyget.

## <a name="architecture"></a>Arkitektur

### <a name="network-topology"></a>Nätverkstopologi

Både HDInsight och AKS använder du ett virtuellt nätverk i Azure som en behållare för beräkningsresurser. Om du vill aktivera kommunikation mellan HDInsight och AKS, måste du aktivera kommunikation mellan sina nätverk. Stegen i det här dokumentet använder virtuella nätverk Peering till nätverk. Andra anslutningar, till exempel VPN, bör också fungera. Mer information om peering finns i [virtuella nätverk peering](../../virtual-network/virtual-network-peering-overview.md) dokumentet.


Följande diagram illustrerar nätverkets topologi som används i det här dokumentet:

![HDInsight i ett virtuellt nätverk och AKS i ett annat nätverk som är anslutna via peering](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Namnmatchning har inte aktiverats mellan peered nätverk, så att IP-adressering används. Som standard konfigureras Kafka på HDInsight för att returnera värdnamn i stället för IP-adresser när klienter ansluter. Stegen i det här dokumentet ändra Kafka om du vill använda IP i stället för annonsering.

## <a name="create-an-azure-container-service-aks"></a>Skapa ett Azure Container Service (AKS)

Om du inte redan har ett AKS kluster använder du något av följande dokument att lära dig hur du skapar en:

* [Distribuera ett Azure Container Service (AKS) kluster - Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Distribuera ett Azure Container Service (AKS) kluster - CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS skapar ett virtuellt nätverk under installationen. Det här nätverket är peerkopplat den du skapade för HDInsight i nästa avsnitt.

## <a name="configure-virtual-network-peering"></a>Konfigurera virtuella nätverk peering

1. Från den [Azure-portalen](https://portal.azure.com)väljer __resursgrupper__, och leta upp resursgruppen som innehåller det virtuella nätverket för AKS klustret. Resursgruppens namn är `MC_<resourcegroup>_<akscluster>_<location>`. Den `resourcegroup` och `akscluster` poster är namnet på resursgruppen som du skapade klustret i och namnet på klustret. Den `location` är den plats som klustret har skapats i.

2. I en resursgrupp väljer du den __för virtuella nätverk__ resurs.

3. Välj __adressutrymmet__. Observera att adressutrymmet som anges.

4. Om du vill skapa ett virtuellt nätverk för HDInsight __+ skapa en resurs__, __nätverk__, och sedan __för virtuella nätverk__.

    > [!IMPORTANT]
    > När du anger värden för det nya virtuella nätverket måste du använda ett adressutrymme som inte överlappar den som används av klusternätverk AKS.

    Använda samma __plats__ för den virtuella nätverk som du använder för AKS-klustret.

    Vänta tills det virtuella nätverket har skapats innan du fortsätter till nästa steg.

5. Välj det virtuella nätverket för att konfigurera peering mellan HDInsight-nätverk och klusternätverk AKS, och välj sedan __Peerkopplingar__. Välj __+ Lägg till__ och använda följande värden för att fylla i formuläret:

    * __Namnet__: Ange ett unikt namn för den här peering konfigurationen.
    * __Virtuellt nätverk__: Använd det här fältet för att välja det virtuella nätverket för den **AKS klustret**.

    Lämnar du alla fält standardvärdet, och välj sedan __OK__ att konfigurera peering.

6. Om du vill konfigurera peering mellan klusternätverk AKS och HDInsight-nätverk, Välj den __AKS kluster virtuellt nätverk__, och välj sedan __Peerkopplingar__. Välj __+ Lägg till__ och använda följande värden för att fylla i formuläret:

    * __Namnet__: Ange ett unikt namn för den här peering konfigurationen.
    * __Virtuellt nätverk__: Använd det här fältet för att välja det virtuella nätverket för den __HDInsight-kluster__.

    Lämnar du alla fält standardvärdet, och välj sedan __OK__ att konfigurera peering.

## <a name="install-kafka-on-hdinsight"></a>Installera Kafka på HDInsight

När du skapar Kafka på HDInsight-kluster, måste du ansluta till det virtuella nätverket som skapade tidigare för HDInsight. Mer information om hur du skapar ett Kafka kluster finns i [skapa ett kluster med Kafka](apache-kafka-get-started.md) dokumentet.

> [!IMPORTANT]
> När du skapar klustret måste du använda den __avancerade inställningar__ att ansluta till virtuella nätverk som du skapade för HDInsight.

## <a name="configure-kafka-ip-advertising"></a>Konfigurera Kafka IP-annonsering

Använd följande steg för att konfigurera Kafka att annonsera IP-adresser i stället för domännamn:

1. Använd en webbläsare och gå till https://CLUSTERNAME.azurehdinsight.net. Ersätt __KLUSTERNAMN__ med namnet på Kafka på HDInsight-kluster.

    När du uppmanas, använda HTTPS-användarnamn och lösenord för klustret. Ambari-Webbgränssnittet för klustret visas.

2. Om du vill visa information om Kafka, Välj __Kafka__ i listan till vänster.

    ![Tjänstlista med Kafka markerat](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Om du vill visa Kafka konfiguration, markera __konfigurationerna__ från översta mitten.

    ![Kafka konfigurationerna för länkar](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Att hitta den __kafka env__ konfiguration, ange `kafka-env` i den __Filter__ i det övre högra hörnet.

    ![Kafka konfiguration för kafka env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Om du vill konfigurera Kafka att annonsera IP-adresser, lägger du till följande texten längst ned i __kafka env mall__ fält:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera det gränssnitt som Kafka lyssnar på, ange `listeners` i den __Filter__ i det övre högra hörnet.

7. Om du vill konfigurera Kafka att lyssna på alla nätverksgränssnitt, ändrar du värdet i den __lyssnare__ till `PLAINTEXT://0.0.0.0:9092`.

8. Om du vill spara konfigurationsändringarna använder den __spara__ knappen. Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    ![Spara konfiguration](./media/apache-kafka-azure-container-services/save-button.png)

9. För att förhindra fel när du startar om Kafka, Använd den __tjänståtgärder__ och välj __aktivera på underhållsläge__. Klicka på OK för att slutföra åtgärden.

    ![Tjänståtgärder med aktivera Underhåll markerat](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka, Använd den __starta om__ och välj __starta om alla berörda__. Bekräfta omstart och sedan använda den __OK__ knappen när åtgärden har slutförts.

    ![Starta om knappen med omstart alla berörda markerad](./media/apache-kafka-azure-container-services/restart-button.png)

11. Inaktivera underhållsläge med de __tjänståtgärder__ och välj __aktivera inaktivera underhållsläge__. Välj **OK** för den här åtgärden.

## <a name="test-the-configuration"></a>Testa konfigurationen

Kafka och Azure Container Service är nu i kommunikation via peered virtuella nätverk. Använd följande steg för att testa den här anslutningen:

1. Skapa ett Kafka ämne som används av testprogrammet. Information om hur du skapar Kafka information finns i [skapa ett kluster med Kafka](apache-kafka-get-started.md) dokumentet.

2. Hämta exempelprogrammet från [https://github.com/Blackmist/Kafka-AKS-Test](https://github.com/Blackmist/Kafka-AKS-Test). 

3. Redigera den `index.js` filen och ändra följande rader:

    * `var topic = 'mytopic'`: Ersätt `mytopic` med namnet på avsnittet Kafka som används av det här programmet.
    * `var brokerHost = '176.16.0.13:9092`: Ersätta `176.16.0.13` med interna IP-adressen för en av värdarna broker för klustret.

        Intern IP-adressen för Service broker (workernodes) för värdar i klustret finns i [Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentet. Välj IP-adressen för en av posterna där domännamnet börjar med `wn`.

4. Från en kommandorad i den `src` directory, installera beroenden och använda Docker för att skapa en avbildning för distribution:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Paket som krävs för det här programmet checkas in i databasen, så du inte behöver använda den `npm` verktyg för att installera dem.

5. Logga in till din Azure Container registret (ACR) och hitta loginServer namn:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Om du inte känner till ditt Azure-behållare registret namn eller är bekant med Azure CLI för att arbeta med Azure Container Service finns i [AKS självstudier](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tagga lokalt `kafka-aks-test` avbildningen med loginServer för din ACR. Lägg även till `:v1` till slutet för att ange Avbildningsversion:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Push-avbildningen i registret:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Den här åtgärden tar flera minuter att slutföra.

8. Redigera Kubernetes manifestfilen (`kafka-aks-test.yaml`) och Ersätt `microsoft` med namnet ACR loginServer hämtade i steg 4.

9. Använd följande kommando för att distribuera programinställningarna från manifestet:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Använd följande kommando för att bevaka den `EXTERNAL-IP` för programmet:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    När en extern IP-adress tilldelas måste använda __CTRL + C__ avsluta klockan

11. Öppna en webbläsare och ange den externa IP-adressen för tjänsten. Du kommer till en sida som liknar följande bild:

    ![Bild av sidan](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Ange text i fältet och välj sedan den __skicka__ knappen. Informationen som skickas till Kafka. Sedan Kafka konsumenten i programmet läser meddelandet och läggs till i __meddelanden från Kafka__ avsnitt.

    > [!WARNING]
    > Du får flera kopior av ett meddelande. Det här problemet vanligtvis händer när du uppdaterar din webbläsare efter anslutning eller öppna flera anslutningar från webbläsaren till programmet.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Kom igång med Kafka på HDInsight](apache-kafka-get-started.md)

* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](apache-kafka-mirroring.md)

* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Använda Apache Spark med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)