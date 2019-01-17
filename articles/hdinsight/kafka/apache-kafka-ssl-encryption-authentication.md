---
title: Konfigurera SSL-kryptering och autentisering för Apache Kafka i Azure HDInsight
description: Konfigurera SSL-kryptering för kommunikation mellan Kafka-klienter och Kafka-Meddelandeköer samt mellan Kafka-meddelandeköer. Konfigurera SSL-autentisering av klienter.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355821"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Konfigurera Secure Sockets Layer (SSL)-kryptering och autentisering för Apache Kafka i Azure HDInsight

Den här artikeln beskriver hur du ställer in SSL-kryptering från Apache Kafka-klienter till Apache Kafka-meddelandeköer och även mellan Apache Kafka-meddelandeköer. Du får också de steg som krävs för att konfigurera autentisering av klienter (kallas ibland dubbelriktat SSL).

## <a name="server-setup"></a>Serverinstallation

Det första steget är att konfigurera nyckellagret och truststore på asynkrona meddelandeköer i Kafka och importera certifikatutfärdaren (CA) och broker-certifikaten till dessa lager.

> [!Note] 
> Den här guiden använder självsignerade certifikat, men den mest säkra lösningen är att använda certifikat från betrodda certifikatutfärdare.

1. Skapa en mapp med namnet ssl och exportera server-lösenord som en miljövariabel. Resten av den här guiden, Ersätt `<server_password>` med det faktiska administratörslösenordet för servern.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Därefter skapa en java keystore (kafka.server.keystore.jks) och ett CA-certifikat.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Skapa sedan en signering begäran att hämta certifikatet som skapades i föregående steg som signerats av CA: N.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Nu kan skicka signering begäran till Certifikatutfärdaren och få det här certifikatet som signerade. Eftersom vi använder ett självsignerat certifikat kan vi registrera certifikatet med vår CA: N med hjälp av den `openssl` kommando.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Skapa ett tillförlitlighetslager och importera certifikatet från Certifikatutfärdaren.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. Importera offentlig CA-certifikatet till keystore.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. Importera det signerade certifikatet till keystore.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

Importera det signerade certifikatet till keystore är det sista steget som behövs för att konfigurera truststore och keystore för Kafka-meddelandeköer.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Uppdatera konfigurationen för att använda SSL och starta om asynkrona meddelandeköer

Vi har installationen varje Kafka mäkla med en keystore och truststore och importerat rätt certifikat.  Sedan ändrar konfigurationsegenskaper relaterade Kafka med Ambari och starta sedan om asynkrona meddelandeköer i Kafka.

1. Logga in på Azure Portal och välj din Azure HDInsight Apache Kafka-kluster.
1. Gå till Ambari UI genom att klicka på **Ambari home** under **Klusterinstrumentpaneler**.
1. Under **Kafka-Meddelandeköer** ange den **lyssnare** egenskap `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Under **avancerade kafkas asynkrona meddelandekö** ange den **security.inter.broker.protocol** egenskap `SSL`

    ![Redigera ssl-konfigurationsegenskaper för kafka i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Under **anpassad kafkas asynkrona meddelandekö** ange den **ssl.client.auth** egenskap `required`. Det här steget är endast krävs om du ställer in autentisering samt kryptering.

    ![Redigera ssl-konfigurationsegenskaper för kafka i Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Lägga till konfigurationsegenskaper i Kafka `server.properties` fil att annonsera IP-adresser i stället för det fullständigt kvalificerade domännamn (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Kontrollera att de tidigare ändringarna har gjorts korrekt genom att du kan också kontrollera att följande rader finns i Kafka `server.properties` fil.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Starta om alla Kafka-meddelandeköer.

## <a name="client-setup-with-authentication"></a>Klientkonfiguration (med autentisering)

> [!Note]
> Följande steg krävs bara om du ställer in båda SSL-kryptering **och** autentisering. Om du endast konfigurerar kryptering, fortsätter du till [klientkonfiguration utan autentisering](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

1. Exportera klientlösenordet. Ersätt `<client_password>` med det faktiska administratörslösenordet på Kafka-klientdator.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Skapa en java keystore och hämta ett signerat certifikat för den asynkrona meddelandekön. Kopiera sedan certifikatet till den virtuella datorn där Certifikatutfärdaren körs.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Växla till CA-datorn (wn0) att registrera klientcertifikatet.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Gå till klientdatorn (hn1) och gå till den `~/ssl` mapp. Kopiera det signerade certifikatet till klientdatorn.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Visa filen `client-ssl-auth.properties` med kommandot `$cat client-ssl-auth.properties`. Den bör ha följande rader:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Klientkonfiguration (utan autentisering)

1. Exportera klientlösenordet. Ersätt `<client_password>` med det faktiska administratörslösenordet på Kafka-klientdator.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Gå till klientdatorn (hn1) och gå till den `~/ssl` mapp. Kopiera det signerade certifikatet till klientdatorn.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Visa filen `client-ssl-auth.properties` med kommandot `$cat client-ssl-auth.properties`. Den bör ha följande rader:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>Nästa steg

* [Vad är Apache Kafka på HDInsight?](/../azure/hdinsight/kafka/apache-kafka-introduction)