---
title: Apache Kafka TLS-kryptering & autentisering - Azure HDInsight
description: Ställ in TLS-kryptering för kommunikation mellan Kafka-klienter och Kafka-mäklare samt mellan Kafka-mäklare. Konfigurera SSL-autentisering av klienter.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: b0154401a9233a6ea85a8e8c06ee14fcc918b2b6
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657085"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurera TLS-kryptering och autentisering för Apache Kafka i Azure HDInsight

Den här artikeln visar hur du ställer in TLS-kryptering (Transport Layer Security), som tidigare kallades SSL-kryptering (Secure Sockets Layer), mellan Apache Kafka-klienter och Apache Kafka-mäklare. Den visar också hur du ställer in autentisering av klienter (kallas ibland tvåvägs TLS).

> [!Important]
> Det finns två klienter som du kan använda för Kafka-program: en Java-klient och en konsolklient. Endast Java-klienten `ProducerConsumer.java` kan använda TLS för både produktion och konsumtion. Konsolproducentklienten `console-producer.sh` fungerar inte med TLS.

> [!Note] 
> HDInsight Kafka konsol producent med version 1.1 stöder inte SSL.
## <a name="apache-kafka-broker-setup"></a>Apache Kafka mäklare setup

Kafka TLS-mäklarinställningen använder fyra virtuella hdinsight-kluster-datorer på följande sätt:

* headnode 0 - Certifikatutfärdaren (CA)
* arbetarnod 0, 1 och 2 - mäklare

> [!Note] 
> Den här guiden använder självsignerade certifikat, men den säkraste lösningen är att använda certifikat som utfärdats av betrodda certifikatutfärdare.

Sammanfattningen av mäklaren inställningsprocessen är följande:

1. Följande steg upprepas på var och en av de tre arbetsnoderna:

    1. Generera ett certifikat.
    1. Skapa en begäran om certifikatsignering.
    1. Skicka begäran om certifikatsignering till certifikatutfärdaren.
    1. Logga in på certifikatutfärdaren och signera begäran.
    1. SCP det signerade certifikatet tillbaka till arbetarnoden.
    1. SCP det offentliga certifikatet för certifikatutfärdaren till arbetsnoden.

1. När du har alla certifikat lägger du in certifikaten i cert-arkivet.
1. Gå till Ambari och ändra konfigurationerna.

Använd följande detaljerade instruktioner för att slutföra mäklarinställningen:

> [!Important]
> I följande kodavsnitt är wnX en förkortning för en av de tre arbetsnoderna `wn1` `wn2` och bör ersättas med `wn0`, eller i förekommande fall. `WorkerNode0_Name`och `HeadNode0_Name` bör ersättas med namnen på respektive maskiner.

1. Utför den första installationen på huvudnod 0, som för HDInsight fyller rollen för certifikatutfärdaren (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utför samma första inställning på var och en av mäklare (arbetarnoderna 0, 1 och 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Kör följande steg på var och en av arbetsnoderna med kodavsnittet nedan.
    1. Skapa en keystore och fyll i den med ett nytt privat certifikat.
    1. Skapa en begäran om certifikatsignering.
    1. SCP certifikatsigneringsbegäran till certifikatutfärdaren (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. På ca-datorn kör följande kommando för att skapa ca-cert- och ca-key-filer:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Ändra till certifikatutfärdarens dator och signera alla mottagna certifikatsigneringsbegäranden:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Skicka tillbaka de signerade certifikaten till arbetsnoderna från certifikatutfärdaren (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Skicka certifikatet för certifikatutfärdaren till varje arbetsnod.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Lägg till certifikatutfärdarens offentliga certifikat i truststore och keystore på varje arbetsnod. Lägg sedan till arbetarnodens egna signerade certifikat i keystore

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Uppdatera Kafka-konfigurationen för att använda TLS- och omstartsmäklare

Du har nu ställt in varje Kafka-mäklare med en keystore och truststore och importerat rätt certifikat. Ändra sedan relaterade Kafka-konfigurationsegenskaper med Ambari och starta sedan om Kafka-mäklarna.

Gör så här för att slutföra konfigurationsändringen:

1. Logga in på Azure-portalen och välj ditt Azure HDInsight Apache Kafka-kluster.
1. Gå till Ambari UI genom att klicka på **Ambari hem** under **Cluster instrumentpaneler**.
1. Under **Kafka Broker** ställa **lyssnare** egendom till`PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Under **Avancerad kafka-mäklare** som **security.inter.broker.protocol** egendom till`SSL`

    ![Redigera Kafka ssl-konfigurationsegenskaper i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Under **Anpassad kafka-mäklare** som **ssl.client.auth** egenskap till `required`. Det här steget krävs bara om du konfigurerar autentisering och kryptering.

    ![Redigera kafka ssl-konfigurationsegenskaper i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Lägg till nya konfigurationsegenskaper i filen server.properties.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Gå till Ambari konfigurationsgränssnittet och kontrollera att de nya egenskaperna visas under Avancerad kafka-env och egenskapen kafka-env.Go to Ambari configuration UI and verify that the new properties show up under **Advanced kafka-env** and the **kafka-env template** property.

    För HDI version 3.6:

    ![Redigera kafka-env mall egendom i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    För HDI version 4.0:

     ![Redigera kafka-env mall egendom i Ambari fyra](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Starta om alla Kafka mäklare.
1. Starta administratörskunden med producent- och konsumentalternativ för att kontrollera att både producenter och konsumenter arbetar med port 9093.

## <a name="client-setup-without-authentication"></a>Klientinställningar (utan autentisering)

Om du inte behöver autentisering är sammanfattningen av stegen för att konfigurera endast TLS-kryptering:

1. Logga in på certifikatutfärdaren (aktiv huvudnod).
1. Kopiera certifikatcertifikatet till klientdatorn från certifikatutfärdaren (wn0).
1. Logga in på klientdatorn (hn1) och navigera till `~/ssl` mappen.
1. Importera certifikatcertifikatet till truststore.
1. Importera certifikatcertifikatet till keystore.

De här stegen beskrivs i följande kodavsnitt.

1. Logga in på ca-noden.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Kopiera ca-cert till klientdatorn

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Logga in på klientmaskinen (standby-huvudnod).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Importera CERTIFIKATUTFÄRDARCERTIFIKATET till truststore.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Importera certifikatcertifikatet till keystore.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Skapa filen `client-ssl-auth.properties`. Den bör ha följande rader:

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Klientinställningar (med autentisering)

> [!Note]
> Följande steg krävs bara om du ställer in både TLS-kryptering **och** autentisering. Om du bara konfigurerar kryptering kan du se [Klientinställningar utan autentisering](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

I följande fyra steg sammanfattas de uppgifter som krävs för att slutföra klientkonfigurationen:

1. Logga in på klientmaskinen (standby-huvudnod).
1. Skapa en java keystore och få ett signerat certifikat för mäklaren. Kopiera sedan certifikatet till den virtuella datorn där certifikatutfärdaren körs.
1. Växla till ca-datorn (aktiv huvudnod) för att signera klientcertifikatet.
1. Gå till klientdatorn (standby-huvudnod) `~/ssl` och navigera till mappen. Kopiera det signerade certifikatet till klientdatorn.

Detaljerna i varje steg ges nedan.

1. Logga in på klientmaskinen (standby-huvudnod).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Ta bort en befintlig ssl-katalog.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Skapa en java keystore och skapa en begäran om certifikatsignering. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Kopiera begäran om certifikatsignering till certifikatutfärdaren

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Växla till ca-datorn (aktiv huvudnod) och signera klientcertifikatet.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Kopiera signerat klientcertifikat från certifikatutfärdaren (aktiv huvudnod) till klientdatorn.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Kopiera ca-cert till klientdatorn

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Skapa klientbutik med signerat certifikat och importera ca cert till keystore och truststore:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Skapa en `client-ssl-auth.properties`fil . Den bör ha följande rader:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Verifiering

> [!Note]
> Om HDInsight 4.0 och Kafka 2.1 är installerade kan du använda konsoltillverkaren/konsumenterna för att verifiera konfigurationen. Om inte, kör Kafka-producenten på port 9092 och skicka meddelanden till ämnet och använd sedan Kafka-konsumenten på port 9093 som använder TLS.

### <a name="kafka-21-or-above"></a>Kafka 2.1 eller högre

1. Skapa ett ämne om det inte redan finns.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Starta konsolproducenten och `client-ssl-auth.properties` ange sökvägen till som en konfigurationsfil för producenten.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. Öppna en annan ssh-anslutning till klientdatorn `client-ssl-auth.properties` och starta konsolkonsumenten och ange sökvägen till som en konfigurationsfil för konsumenten.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1,1

1. Skapa ett ämne om det inte redan finns.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. Starta konsolproducenten och ange sökvägen till klient-ssl-auth.properties som en konfigurationsfil för producenten.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. Öppna en annan ssh-anslutning till klientdatorn `client-ssl-auth.properties` och starta konsolkonsumenten och ange sökvägen till som en konfigurationsfil för konsumenten.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Nästa steg

* [Vad är Apache Kafka på HDInsight?](apache-kafka-introduction.md)
