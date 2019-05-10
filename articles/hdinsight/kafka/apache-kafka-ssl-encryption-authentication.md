---
title: Konfigurera SSL-kryptering och autentisering för Apache Kafka i Azure HDInsight
description: Konfigurera SSL-kryptering för kommunikation mellan Kafka-klienter och Kafka-Meddelandeköer samt mellan Kafka-meddelandeköer. Konfigurera SSL-autentisering av klienter.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 5d567074a0038915cc43a585b34c9c71ccf3eb1b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65464985"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurera Secure Sockets Layer (SSL)-kryptering och autentisering för Apache Kafka i Azure HDInsight

Den här artikeln visar hur du konfigurerar SSL-kryptering mellan Apache Kafka-klienter och Apache Kafka-meddelandeköer. Den också visar hur du konfigurerar autentisering av klienter (kallas ibland dubbelriktat SSL).

> [!Important]
> Det finns två klienter som du kan använda för Kafka-program: en Java-klient och en konsolklient. Endast Java-klientens `ProducerConsumer.java` kan använda SSL för både producera och använda. Producent konsolklienten `console-producer.sh` fungerar inte med SSL.

## <a name="apache-kafka-broker-setup"></a>Apache Kafka-Meddelandeköer installationen

Kafka SSL broker installationen använder fyra HDInsight-kluster virtuella datorer på följande sätt:

* Huvudnoden 0 - certifikatutfärdaren (CA)
* arbetsnod 0, 1 och 2 - förmedlar

> [!Note] 
> Den här guiden använder självsignerade certifikat, men den mest säkra lösningen är att använda certifikat från betrodda certifikatutfärdare.

Sammanfattning av installationsprocessen broker är följande:

1. Följande steg ska upprepas på var och en av de tre arbetsnoderna:

    1. Generera ett certifikat.
    1. Skapa ett certifikat som förfrågan.
    1. Skicka cert förfrågan till den certifikatutfärdaren (CA).
    1. Logga in på ca: N och signera begäran.
    1. SCP det signerade certifikatet tillbaka till arbetsnoden.
    1. SCP det offentliga certifikatet för Certifikatutfärdaren till arbetsnoden.

1. När du har alla certifikat, placera certifikat i certifikatarkivet för.
1. Gå till Ambari och ändra konfigurationerna.

Använd följande detaljerade instruktioner för att slutföra installationen broker:

> [!Important]
> I följande kodavsnitt wnX är en förkortning för en av de tre arbetsnoderna och ska ersättas med `wn0`, `wn1` eller `wn2` efter behov. `WorkerNode0_Name` och `HeadNode0_Name` ska ersättas med namnen på respektive datorer, till exempel `wn0-abcxyz` eller `hn0-abcxyz`.

1. Utför installationen på huvudnoden 0, som för HDInsight ska fylla rollen för den certifikatutfärdaren (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utföra installationen av samma på var och en av Meddelandeköer (arbetsnoder 0, 1 och 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Utför följande steg med hjälp av kodfragmentet nedan på alla arbetsnoder.
    1. Skapa en keystore och fyller den med ett nytt privata certifikat.
    1. Skapa en förfrågan om certifikatsignering.
    1. SCP signering certifikatbegäran till Certifikatutfärdaren (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Ändra till CA-datorn och logga alla mottagna CERT Logga begäranden:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Skicka självsignerade certifikat till arbetsnoderna från CA: N (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Skicka Certifikatutfärdarens offentliga certifikat till varje arbetsnod.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Lägg till det offentliga certifikatet för CA: er till truststore och keystore på varje arbetsnod. Lägg sedan till worker nodens eget signerat certifikat till keystore

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Uppdatera konfigurationen av Kafka för att använda SSL och starta om asynkrona meddelandeköer

Du har nu ställa in alla Kafka-Meddelandeköer med en keystore och truststore och importerat rätt certifikat. Sedan ändrar konfigurationsegenskaper relaterade Kafka med Ambari och starta sedan om asynkrona meddelandeköer i Kafka.

Utför följande steg för att slutföra konfigurationen ändringen:

1. Logga in på Azure Portal och välj din Azure HDInsight Apache Kafka-kluster.
1. Gå till Ambari UI genom att klicka på **Ambari home** under **Klusterinstrumentpaneler**.
1. Under **Kafka-Meddelandeköer** ange den **lyssnare** egenskap `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Under **avancerade kafkas asynkrona meddelandekö** ange den **security.inter.broker.protocol** egenskap `SSL`

    ![Redigera ssl-konfigurationsegenskaper för Kafka i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Under **anpassad kafkas asynkrona meddelandekö** ange den **ssl.client.auth** egenskap `required`. Det här steget är endast krävs om du ställer in autentisering och kryptering.

    ![Redigera ssl-konfigurationsegenskaper för kafka i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Lägga till konfigurationsegenskaper i Kafka `server.properties` fil att annonsera IP-adresser i stället för det fullständigt kvalificerade domännamn (FQDN).

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

1. Kontrollera att de tidigare ändringarna har gjorts korrekt genom att du kan också kontrollera att följande rader finns i Kafka `server.properties` fil.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Starta om alla Kafka-meddelandeköer.
1. Börja admin-klienten med producent och konsument-alternativ för att kontrollera att både producenter och konsumenter fungerar på port 9093.

## <a name="client-setup-with-authentication"></a>Klientkonfiguration (med autentisering)

> [!Note]
> Följande steg krävs bara om du ställer in båda SSL-kryptering **och** autentisering. Om du endast konfigurerar kryptering, fortsätter du till [klientkonfiguration utan autentisering](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Utför följande steg för att slutföra klientinstallationen:

1. Logga in på klientdatorn (hn1).
1. Skapa en java keystore och hämta ett signerat certifikat för den asynkrona meddelandekön. Kopiera sedan certifikatet till den virtuella datorn där Certifikatutfärdaren körs.
1. Växla till CA-datorn (hn0) att registrera klientcertifikatet.
1. Gå till klientdatorn (hn1) och gå till den `~/ssl` mapp. Kopiera det signerade certifikatet till klientdatorn.

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

Slutligen kan visa filen `client-ssl-auth.properties` med kommandot `cat client-ssl-auth.properties`. Den bör ha följande rader:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Klientkonfiguration (utan autentisering)

Om du inte behöver autentisering, är stegen för att konfigurera SSL-kryptering:

1. Logga in på klientdatorn (hn1) och gå till den `~/ssl` mapp
1. Kopiera det signerade certifikatet till klientdatorn från den CA-datorn (wn0).
1. Importera CA-certifikat till truststore
1. Importera CA-certifikat till keystore

De här stegen visas i följande kodavsnitt.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Slutligen kan visa filen `client-ssl-auth.properties` med kommandot `cat client-ssl-auth.properties`. Den bör ha följande rader:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Nästa steg

* [Vad är Apache Kafka på HDInsight?](apache-kafka-introduction.md)
