---
title: Använd Azure Functions för att skicka data till Kafka på HDInsight | Microsoft Docs
description: Lär dig hur du använder en Azure-funktion för att skriva data till Kafka på HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/08/2018
ms.author: larryfr
ms.openlocfilehash: c657cda7dce0611cb357a0a2063a154f2f6f25f2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Använda Kafka på HDInsight från en funktionsapp i Azure

Lär dig hur du skapar en app i Azure-funktion som skickar data till Kafka på HDInsight.

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) är en serverlösa beräknings-tjänst. Det kan du köra kod på begäran utan att behöva etablera uttryckligen eller hantera infrastrukturen.

[Apache Kafka](https://kafka.apache.org) är en distribuerad direktuppspelningsplattform med öppen källkod som kan användas för att skapa realtidsuppspelade datapipelines och program. Kafka tillhandahåller även funktionen för asynkron meddelandekö som liknar en meddelandekö där du kan publicera och prenumerera på namngivna dataströmmar. Kafka på HDInsight erbjuder en hanterad, maximalt skalbar och högtillgänglig tjänst i Microsoft Azure-molnet.

Kafka på HDInsight tillhandahåller inte en API på internet. Om du vill publicera eller använda data från Kafka, måste du ansluta till klustret Kafka med ett Azure Virtual Network. Azure Functions erbjuder ett enkelt sätt att skapa en offentlig slutpunkt som skickar data till Kafka på HDInsight via en virtuell nätverksgateway.

> [!NOTE]
> Det här dokumentet fokuserar på de steg som krävs för att aktivera Azure Functions att kommunicera med Kafka på HDInsight. Exemplet själva är bara en grundläggande Kafka producent att demonstrera att konfigurationen fungerar.

## <a name="prerequisites"></a>Förutsättningar

* En funktionsapp i Azure. Mer information finns i [skapa din första funktion](../../azure-functions/functions-create-first-azure-function.md) dokumentation.

* Azure-nätverk. Om du vill arbeta med Azure Functions, måste det virtuella nätverket använda en av följande IP-intervall:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Mer information finns i [integrera din app med ett Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md) dokumentet.

* En Kafka på HDInsight-kluster. Information om hur du skapar en Kafka på HDInsight-kluster finns i [skapa ett kluster med Kafka](apache-kafka-get-started.md) dokumentet.

    > [!IMPORTANT]
    > Vid klusterkonfiguration, måste du använda den __avancerade inställningar__ steg för att välja Azure-nätverk och undernät som HDInsight använder. Välj de virtuella nätverk och undernät som skapats i föregående steg.

    Mer information om Kafka och virtuella nätverk finns i [Anslut till Kafka via ett virtuellt nätverk](apache-kafka-connect-vpn-gateway.md) dokumentet.

## <a name="architecture"></a>Arkitektur

Kafka på HDInsight finns i ett Azure Virtual Network. Azure Functions kan kommunicera med det virtuella nätverket med hjälp av en punkt-till-plats-gateway. Nedan visas ett diagram över det här nätverkets topologi:

![Arkitekturen i Azure Functions ansluter till HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Konfigurera Kafka

Informationen i det här avsnittet förbereder Kafka klustret att acceptera data från Azure-funktion. Den omfattar följande konfiguration:

* IP-annonsering
* Samla in Kafka Broker IP-adresser
* Skapa ett Kafka ämne

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurera Kafka för IP-annonsering

Som standard returnerar Zookeeper domännamnet för Kafka mäklare till klienter. Den här konfigurationen fungerar inte utan en DNS-server som klienten (Azure Functions) det går inte att matcha namn för det virtuella nätverket. Använd följande steg för att konfigurera Kafka att annonsera IP-adresser i stället för domännamn för den här konfigurationen:

1. Via en webbläsare går du till https://CLUSTERNAME.azurehdinsight.net. Ersätt __KLUSTERNAMN__ med namnet på Kafka på HDInsight-kluster.

    När du uppmanas, använda HTTPS-användarnamn och lösenord för klustret. Ambari-Webbgränssnittet för klustret visas.

2. Om du vill visa information om Kafka, Välj __Kafka__ i listan till vänster.

    ![Tjänstlista med Kafka markerat](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Om du vill visa Kafka konfiguration, markera __konfigurationerna__ från översta mitten.

    ![Kafka konfigurationerna för länkar](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Att hitta den __kafka env__ konfiguration, ange `kafka-env` i den __Filter__ i det övre högra hörnet.

    ![Kafka konfiguration för kafka env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

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

    ![Spara konfiguration](./media/apache-kafka-azure-functions/save-button.png)

9. För att förhindra fel när du startar om Kafka, Använd den __tjänståtgärder__ och välj __aktivera på underhållsläge__. Klicka på OK för att slutföra åtgärden.

    ![Tjänståtgärder med aktivera Underhåll markerat](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Om du vill starta om Kafka, Använd den __starta om__ och välj __starta om alla berörda__. Bekräfta omstart och sedan använda den __OK__ knappen när åtgärden har slutförts.

    ![Starta om knappen med omstart alla berörda markerad](./media/apache-kafka-azure-functions/restart-button.png)

11. Inaktivera underhållsläge med de __tjänståtgärder__ och välj __aktivera inaktivera underhållsläge__. Välj **OK** för den här åtgärden.

### <a name="get-the-kafka-broker-ip-address"></a>Hämta Service broker Kafka IP-adress

Använd någon av följande metoder för att hämta det fullständigt kvalificerade domännamnet (FQDN) och IP-adresser av noderna i klustret Kafka:

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

Det här kommandot förutsätter att `<resourcegroupname>` är namnet på Azure-resursgrupp som innehåller det virtuella nätverket.

Välj en workernode IP-adress från listan med namn som returneras. Internt fullständigt kvalificerade domännamnet för noden som börjar med bokstäver `wn`. Denna IP-adress används av funktionen för att kommunicera med Kafka.

### <a name="create-a-kafka-topic"></a>Skapa ett Kafka ämne

Kafka lagrar data i __avsnitt__. Innan data skickas till Kafka från en Azure-funktion, måste du skapa funktionen.

Om du vill skapa ett ämne, Följ stegen i den [skapa ett kluster med Kafka](apache-kafka-get-started.md) dokumentet.

## <a name="create-a-function-that-sends-to-kafka"></a>Skapa en funktion som skickar till Kafka

> [!NOTE]
> Steg i det här avsnittet skapar du en JavaScript-funktion som använder den [kafka nod](https://www.npmjs.com/package/kafka-node) paket som ska publicera data till Kafka:

1. Skapa en ny __WebHook + API__ fungera, och välj __JavaScript__ som språk. Mer information om hur du skapar nya funktioner finns i [skapa din första funktion](../../azure-functions/functions-create-first-azure-function.md#create-function) dokumentet.

2. Använd följande kod som en del av funktionen:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Ersätt `'test'` med namnet på avsnittet Kafka som du skapade på ditt HDInsight-kluster.

    Ersätt `10.1.0.7` med IP-adress som hämtats tidigare. Lämna den `:9092` värde. 9092 är den port som Kafka lyssnar på.

    Använd den __spara__ för att spara ändringarna.

    ![Redigeraren med spara knappen](./media/apache-kafka-azure-functions/function-editor.png)

3. Till höger i funktionen editor, Välj __visa filer__. Välj __+ Lägg till__ och lägga till en ny fil med namnet `package.json`. Den här filen används för att ange beroenden på den `kafka-node` paketet.

    ![Lägga till en fil](./media/apache-kafka-azure-functions/add-files.png)

4. Om du vill redigera den nya filen, Välj `package.json` från den __visa filer__ lista. Använd följande text som innehållet i filen:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Använd den __spara__ för att spara ändringarna.

5. Så här installerar du den `kafka-node` paketet använder den _version och package hantering_ avsnitt i den [Utvecklarhandbok för Azure Functions JavaScript](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Du kan få många fel som kafka nod-paketet har installerats. Du kan ignorera dessa fel utan att oroa dig.

## <a name="run-the-function"></a>Kör funktionen

Till höger i funktionen editor, Välj __Test__. Behåll standardinställningarna för testet och välj sedan __kör__. När testet körs, skickar en `name` parameter för funktionen. Den här parametern innehåller ett värde av `Azure`, som infogas i Kafka.

![Testa dialogrutan skärmbild](./media/apache-kafka-azure-functions/function-test-dialog.png)

Om du vill visa information som loggas av funktionen när testet körs, Välj __loggar__ längst ned på sidan. Kör test om du vill generera logginformation.

![Exempel på utdata för funktionen logg](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Kontrollera att data är i Kafka

För att verifiera att data har anlänt i avsnittet Kafka, använder du informationen i den _skapa och använda poster_ avsnitt i den [skapa ett kluster med Kafka](apache-kafka-get-started.md#produce-and-consume-records) dokumentet. Den `kafka-console-consumer` läser data från avsnittet och visar en lista med meddelanden som lagras i avsnittet.

## <a name="next-steps"></a>Nästa steg

Använd följande länkar om du vill veta om hur du använder Apache Kafka på HDInsight:

* [Kom igång med Kafka på HDInsight](apache-kafka-get-started.md)

* [Använd MirrorMaker för att skapa en replik av Kafka på HDInsight](apache-kafka-mirroring.md)

* [Använda Apache Storm med Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Använda Apache Spark med Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Ansluta till Kafka via ett trådlöst Azure-nätverk](apache-kafka-connect-vpn-gateway.md)
