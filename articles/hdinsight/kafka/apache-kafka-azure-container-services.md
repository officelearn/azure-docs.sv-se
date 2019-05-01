---
title: Använda Azure Kubernetes-tjänst med Kafka på HDInsight
description: Lär dig hur du använder Kafka på HDInsight från behållaravbildningar som finns i Azure Kubernetes Service (AKS).
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 35ef708cdcedc2d7bafedb8bf3686e4b468177df
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723679"
---
# <a name="use-azure-kubernetes-service-with-apache-kafka-on-hdinsight"></a>Använda Azure Kubernetes-tjänst med Apache Kafka på HDInsight

Lär dig hur du använder Azure Kubernetes Service (AKS) med [Apache Kafka](https://kafka.apache.org/) på HDInsight-kluster. Stegen i det här dokumentet använder ett Node.js-program i AKS för att verifiera anslutning med Kafka. Det här programmet använder den [kafka-noden](https://www.npmjs.com/package/kafka-node) paketet för att kommunicera med Kafka. Den använder [Socket.io](https://socket.io/) för händelsedriven meddelandetjänst mellan webbläsarklienten och serverdelen i AKS.

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Azure Kubernetes Service hanterar din värdbaserade Kubernetes-miljö och gör det snabbt och enkelt att distribuera program i behållare. Med Azure Virtual Network kan ansluta du de två tjänsterna.

> [!NOTE]  
> Fokus i det här dokumentet är på de steg som krävs för att aktivera Azure Kubernetes Service för att kommunicera med Kafka på HDInsight. I exempel själva är bara en grundläggande Kafka klient till visar att konfigurationen fungerar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* En Azure-prenumeration

Det här dokumentet förutsätter att du är bekant med att skapa och använda följande Azure-tjänster:

* Kafka på HDInsight
* Azure Kubernetes Service
* Azure Virtual Networks

Det här dokumentet förutsätter också att du har gått igenom de [självstudie för Azure Kubernetes Service](../../aks/tutorial-kubernetes-prepare-app.md). Den här självstudien skapar du en behållartjänst, skapar ett Kubernetes-kluster, ett behållarregister och konfigurerar den `kubectl` verktyget.

## <a name="architecture"></a>Arkitektur

### <a name="network-topology"></a>Nätverkstopologi

Både HDInsight och AKS använder du Azure Virtual Network som en behållare för beräkningsresurser. Om du vill aktivera kommunikation mellan HDInsight och AKS, måste du aktivera kommunikation mellan sina nätverk. Stegen i det här dokumentet använder Peering för virtuellt nätverk till nätverk. Andra anslutningar, till exempel VPN, bör också fungera. Mer information om peering finns i den [peerkoppling av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md) dokumentet.


Följande diagram illustrerar nätverkets topologi som används i det här dokumentet:

![HDInsight i ett virtuellt nätverk och AKS i ett annat nätverk som är anslutna via peering](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]  
> Namnmatchning är inte aktiverat mellan peer-kopplade nätverk, så att IP-adresser används. Kafka på HDInsight har konfigurerats för att returnera värdnamn i stället för IP-adresser när klienter ansluter som standard. Stegen i det här dokumentet ändrar Kafka för att använda IP annonserar i stället.

## <a name="create-an-azure-kubernetes-service-aks"></a>Skapa en Azure Kubernetes Service (AKS)

Om du inte redan har ett AKS-kluster, kan du använda en av följande dokument att lära dig hur du skapar en:

* [Distribuera ett Azure Kubernetes Service (AKS)-kluster – Portal](../../aks/kubernetes-walkthrough-portal.md)
* [Distribuera ett kluster för Azure Kubernetes Service (AKS) – CLI](../../aks/kubernetes-walkthrough.md)

> [!NOTE]  
> AKS skapar ett virtuellt nätverk under installationen. Det här nätverket är peer-kopplat till den skapade för HDInsight i nästa avsnitt.

## <a name="configure-virtual-network-peering"></a>Konfigurera virtuell nätverkspeering

1. Från den [Azure-portalen](https://portal.azure.com)väljer __resursgrupper__, och leta reda på resursgruppen som innehåller det virtuella nätverket för AKS-klustret. Resursgruppens namn är `MC_<resourcegroup>_<akscluster>_<location>`. Den `resourcegroup` och `akscluster` poster är namnet på resursgruppen som du skapade i klustret och namnet på klustret. Den `location` är den plats som klustret har skapats i.

2. I resursgruppen och väljer den __virtuellt nätverk__ resurs.

3. Välj __adressutrymme__. Observera adressutrymmet i listan.

4. Skapa ett virtuellt nätverk för HDInsight, välja __+ skapa en resurs__, __nätverk__, och sedan __virtuellt nätverk__.

    > [!IMPORTANT]  
    > När du anger värdena för det nya virtuella nätverket, måste du använda ett adressutrymme som inte överlappar det som används av nätverket för AKS-klustret.

    Använd samma __plats__ för den virtuella nätverk som du använde för AKS-klustret.

    Vänta tills det virtuella nätverket har skapats innan du fortsätter till nästa steg.

5. Välj det virtuella nätverket för att konfigurera peer-kopplingen mellan det HDInsight-nätverket och nätverket för AKS-klustret, och välj sedan __Peerings__. Välj __+ Lägg till__ och Använd följande värden för att fylla i formuläret:

   * __Namn__: Ange ett unikt namn för den här peering-konfigurationen.
   * __Virtuellt nätverk__: Använd det här fältet för att välja det virtuella nätverket för den **AKS-kluster**.

     Lämnar du alla andra fält på standardvärdet och välj sedan __OK__ att konfigurera peer-kopplingen.

6. Om du vill konfigurera peering mellan ett AKS-klusternätverk och HDInsight-nätverk, Välj den __AKS kluster virtuellt nätverk__, och välj sedan __Peerings__. Välj __+ Lägg till__ och Använd följande värden för att fylla i formuläret:

   * __Namn__: Ange ett unikt namn för den här peering-konfigurationen.
   * __Virtuellt nätverk__: Använd det här fältet för att välja det virtuella nätverket för den __HDInsight-kluster__.

     Lämnar du alla andra fält på standardvärdet och välj sedan __OK__ att konfigurera peer-kopplingen.

## <a name="install-apache-kafka-on-hdinsight"></a>Installera Apache Kafka på HDInsight

När du skapar Kafka på HDInsight-kluster kan ansluta du det virtuella nätverket som skapats tidigare för HDInsight. Mer information om hur du skapar ett Kafka-kluster finns i den [skapar ett Apache Kafka-kluster](apache-kafka-get-started.md) dokumentet.

> [!IMPORTANT]  
> När du skapar klustret, måste du använda den __avancerade inställningar__ att ansluta till det virtuella nätverket som du skapade för HDInsight.

## <a name="configure-apache-kafka-ip-advertising"></a>Konfigurera Apache Kafka IP-annonsering

Använd följande steg för att konfigurera Kafka att annonsera IP-adresser i stället för domännamn:

1. Via en webbläsare går du till https://CLUSTERNAME.azurehdinsight.net. Ersätt __CLUSTERNAME__ med namnet på Kafka på HDInsight-klustret.

    När du uppmanas, använda HTTPS-användarnamn och lösenord för klustret. Ambari-Webbgränssnittet för klustret visas.

2. Om du vill visa information om Kafka, Välj __Kafka__ i listan till vänster.

    ![Tjänstlista med Kafka markerat](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Om du vill visa Kafka konfiguration, Välj __Peeringkonfigurationer__ från överst i mitten.

    ![Peeringkonfigurationer länkar för Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Att hitta den __kafka-env__ konfiguration, ange `kafka-env` i den __Filter__ i det övre högra hörnet.

    ![Kafka-konfigurationen för kafka-env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Om du vill konfigurera Kafka att annonsera IP-adresser, lägger du till följande text till slutet av den __kafka-env-template__ fält:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Om du vill konfigurera det gränssnitt som Kafka lyssnar på, ange `listeners` i den __Filter__ i det övre högra hörnet.

7. Om du vill konfigurera Kafka för att lyssna på alla nätverksgränssnitt, ändra värdet i den __lyssnare__ fältet `PLAINTEXT://0.0.0.0:9092`.

8. Om du vill spara konfigurationsändringarna, använda den __spara__ knappen. Ange ett textmeddelande som beskriver ändringarna. Välj __OK__ när ändringarna har sparats.

    ![Spara konfiguration](./media/apache-kafka-azure-container-services/save-button.png)

9. För att förhindra fel när du startar om Kafka, använda den __tjänståtgärder__ och välj __aktivera på underhållsläge__. Välj OK om du vill slutföra den här åtgärden.

    ![Tjänståtgärder, med aktivera Underhåll markerat](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka, använda den __starta om__ och välj __starta om alla påverkade__. Bekräfta omstarten, och sedan använda den __OK__ knappen när åtgärden har slutförts.

    ![Starta om knappen om alla påverkade markerad](./media/apache-kafka-azure-container-services/restart-button.png)

11. Om du vill inaktivera underhållsläge, använda den __tjänståtgärder__ och välj __stänga av underhållsläge__. Välj **OK** att slutföra åtgärden.

## <a name="test-the-configuration"></a>Testa konfigurationen

Kafka och Azure Kubernetes Service är nu i kommunikation via peerkopplade virtuella nätverk. Om du vill testa den här anslutningen, använder du följande steg:

1. Skapa ett Kafka-ämne som används av testprogrammet. Information om hur du skapar ämnen Kafka finns i den [skapar ett Apache Kafka-kluster](apache-kafka-get-started.md) dokumentet.

2. Ladda ned exempelprogrammet från [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Redigera den `index.js` filen och ändra följande rader:

    * `var topic = 'mytopic'`: Ersätt `mytopic` med namnet på Kafka-ämne som används av det här programmet.
    * `var brokerHost = '176.16.0.13:9092`: Ersätt `176.16.0.13` med interna IP-adressen för en av värdarna broker för klustret.

        Den interna IP-adressen för den asynkrona meddelandekön värdar (workernodes) i klustret finns i den [Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentet. Välj IP-adressen för en av posterna där domännamnet börjar med `wn`.

4. Från kommandoraden i den `src` directory, installerar beroenden och använda Docker för att skapa en avbildning för distribution:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]  
    > Paket som krävs för det här programmet är markerade i databasen, så du inte behöver använda den `npm` verktyget ska installeras.

5. Logga in till din Azure Container Registry (ACR) och hitta namnet på inloggningsservern:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]  
    > Om du inte vet Azure Container Registry-namn eller är bekant med hjälp av Azure CLI för att arbeta med Azure Kubernetes Service finns i den [AKS självstudier](../../aks/tutorial-kubernetes-prepare-app.md).

6. Tagga lokalt `kafka-aks-test` avbildningen med namnet på inloggningsservern för din ACR. Lägg även till `:v1` till slutet för att indikera versionsnumret för avbildningen:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Överför avbildningen till registret:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Den här åtgärden tar flera minuter att slutföra.

8. Redigera Kubernetes-manifestfil (`kafka-aks-test.yaml`) och Ersätt `microsoft` med namnet på ACR-inloggningsservern som du hämtade i steg 4.

9. Använd följande kommando för att distribuera programinställningarna från manifestet:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Använd följande kommando för att se upp för den `EXTERNAL-IP` av programmet:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    När en extern IP-adress tilldelas använda __CTRL + C__ avsluta klockan

11. Öppna en webbläsare och ange den externa IP-adressen för tjänsten. Du kommer till en sida som liknar följande bild:

    ![Bild av sidan](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Ange text i fältet och välj sedan den __skicka__ knappen. Informationen som skickas till Kafka. Kafka-konsument i programmet läser meddelandet och lägger till den till den __meddelanden från Kafka__ avsnittet.

    > [!WARNING]  
    > Du får flera kopior av ett meddelande. Det här problemet vanligtvis sker när du uppdaterar din webbläsare när du har anslutit eller öppna flera webbläsare anslutningar till programmet.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Kom igång med Apache Kafka på HDInsight](apache-kafka-get-started.md)

* [Använd MirrorMaker för att skapa en replik av Apache Kafka på HDInsight](apache-kafka-mirroring.md)

* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Anslut till Apache Kafka via ett Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
