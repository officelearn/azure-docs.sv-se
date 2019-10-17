---
title: Konfigurera SSL-kryptering och autentisering för Apache Kafka i Azure HDInsight
description: Konfigurera SSL-kryptering för kommunikation mellan Kafka-klienter och Kafka-utjämnare samt mellan Kafka-utjämnare. Konfigurera SSL-autentisering av klienter.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 19a817124afb9afcee25b5f2bff73b8a17e16519
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431273"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurera Secure Sockets Layer (SSL) kryptering och autentisering för Apache Kafka i Azure HDInsight

Den här artikeln visar hur du konfigurerar SSL-kryptering mellan Apache Kafka klienter och Apache Kafka-utjämnare. Det visar också hur du konfigurerar autentisering av klienter (kallas ibland tvåvägs SSL).

> [!Important]
> Det finns två klienter som du kan använda för Kafka-program: en Java-klient och en konsol klient. Endast Java-klienten `ProducerConsumer.java` kan använda SSL för både produktion och användning. Konsolens producerande klient `console-producer.sh` fungerar inte med SSL.

## <a name="apache-kafka-broker-setup"></a>Installation av Apache Kafka Broker

Installations programmet för Kafka-SSL-Broker kommer att använda fyra virtuella HDInsight-kluster med virtuella datorer på följande sätt:

* huvudnoden 0 – certifikat utfärdare (CA)
* Worker-nod 0, 1 och 2-mäklare

> [!Note] 
> Den här guiden använder självsignerade certifikat, men den säkraste lösningen är att använda certifikat som utfärdats av betrodda certifikat utfärdare.

Sammanfattningen av installations processen för Broker är följande:

1. Följande steg upprepas på var och en av de tre arbetsnoderna:

    1. Generera ett certifikat.
    1. Skapa en begäran om certifikat signering.
    1. Skicka certifikat signerings förfrågan till certifikat utfärdaren (CA).
    1. Logga in på CA: n och signera begäran.
    1. SCP det signerade certifikatet tillbaka till arbetsnoden.
    1. SCP-certifikatets offentliga certifikat till arbets noden.

1. När du har alla certifikaten ska du placera certifikaten i certifikat arkivet.
1. Gå till Ambari och ändra konfigurationerna.

Använd följande detaljerade anvisningar för att slutföra konfigurationen av Service Broker:

> [!Important]
> I följande kodfragment är wnX en förkortning för en av de tre arbetsnoderna och bör ersättas med `wn0`, `wn1` eller `wn2` efter behov. `WorkerNode0_Name` och `HeadNode0_Name` ersätts med namnen på respektive datorer, till exempel `wn0-abcxyz` eller `hn0-abcxyz`.

1. Utför den första installationen på Head-noden 0, som för HDInsight ska fylla rollen för certifikat utfärdaren (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utför samma inledande installation på var och en av utjämnarna (arbetsnoderna 0, 1 och 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utför följande steg på varje arbetsnoder med hjälp av kodfragmentet nedan.
    1. Skapa ett nyckel lager och fyll i det med ett nytt privat certifikat.
    1. Skapa en begäran om certifikat signering.
    1. SCP-begäran om certifikat signering till CA: n (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Ändra till CA-datorn och signera alla mottagna signerings begär Anden för certifikat:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Skicka de signerade certifikaten tillbaka till arbetsnoderna från CA: n (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Skicka certifikat utfärdarens offentliga certifikat till varje arbets nod.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. På varje arbetsnoden lägger du till det offentliga certifikat utfärdaren i trustStore och nyckel arkivet. Lägg sedan till arbetsnodens egna signerade certifikat i nyckel lagret

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Uppdatera Kafka-konfigurationen för att använda SSL och starta om mäklare

Du har nu konfigurerat varje Kafka-koordinator med ett nyckel lager och trustStore och importerat rätt certifikat. Ändra sedan de relaterade konfigurations egenskaperna för Kafka med hjälp av Ambari och starta sedan om Kafka-utjämnare.

Slutför konfigurations ändringen genom att utföra följande steg:

1. Logga in på Azure Portal och välj ditt Azure HDInsight Apache Kafka-kluster.
1. Gå till Ambari-ANVÄNDARGRÄNSSNITTET genom att klicka på **Ambari start** under **kluster instrument paneler**.
1. Under **Kafka Broker** anger du egenskapen **listeners** till `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Under **Advanced Kafka-Broker** ställer du in egenskapen **Security. Inter. Broker. Protocol** till `SSL`

    ![Redigera Kafka egenskaper för SSL-konfiguration i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Under **anpassad Kafka-Broker** ställer du in egenskapen **SSL. client. auth** på `required`. Det här steget krävs bara om du konfigurerar autentisering och kryptering.

    ![Redigera Kafka egenskaper för SSL-konfiguration i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Kör kommandona nedan som lägger till konfigurations egenskaper till Kafka-`server.properties`-filen för att annonsera IP-adresser i stället för det fullständigt kvalificerade domän namnet (FQDN)..

    ```bash
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

1. För att kontrol lera att de tidigare ändringarna har gjorts korrekt kan du välja att kontrol lera att följande rader finns i Kafka-`server.properties`-filen.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Starta om alla Kafka-utjämnare.
1. Starta administratörs klienten med producent-och konsument alternativ för att kontrol lera att både tillverkare och konsumenter arbetar på port 9093.

## <a name="client-setup-with-authentication"></a>Klient installation (med autentisering)

> [!Note]
> Följande steg krävs endast om du konfigurerar både SSL-kryptering **och** autentisering. Om du bara konfigurerar kryptering kan du gå vidare till [klient konfigurationen utan autentisering](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Slutför klient installationen genom att slutföra följande steg:

1. Logga in på klient datorn (HN1).
1. Skapa ett Java-nyckel lager och hämta ett signerat certifikat för koordinatorn. Kopiera sedan certifikatet till den virtuella dator där CA: n körs.
1. Växla till CA-datorn (hn0) för att signera klient certifikatet.
1. Gå till klient datorn (HN1) och navigera till mappen `~/ssl`. Kopiera det signerade certifikatet till klient datorn.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Till sist visar du filen `client-ssl-auth.properties` med kommandot `cat client-ssl-auth.properties`. Den bör ha följande rader:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Klient installation (utan autentisering)

Om du inte behöver autentisering är stegen för att ställa in endast SSL-kryptering:

1. Logga in på klient datorn (HN1) och navigera till mappen `~/ssl`
1. Kopiera det signerade certifikatet till klient datorn från CA-datorn (wn0).
1. Importera CA-certifikatet till trustStore
1. Importera certifikat utfärdarens certifikat till nyckel arkivet

De här stegen visas i följande kodfragment.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Slutligen visar du filen `client-ssl-auth.properties` med kommandot `cat client-ssl-auth.properties`. Den bör ha följande rader:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Nästa steg

* [Vad är Apache Kafka i HDInsight?](apache-kafka-introduction.md)
