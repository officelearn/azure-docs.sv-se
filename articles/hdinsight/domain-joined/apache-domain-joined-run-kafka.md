---
title: Konfigurera Apache Kafka-principer i HDInsight med Enterprise Security Package – Azure
description: Lär dig hur du konfigurerar Apache Ranger-principer för Kafka i Azure HDInsight med Enterprise Security Package.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 6434f7cae3c3fa402efad00b2f6bfb0bc405f9e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64730248"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Självstudie: Konfigurera Apache Kafka-principer i HDInsight med Enterprise Security Package (förhandsversion)

Lär dig hur du konfigurerar Apache Ranger-principer för Apache Kafka-kluster med Enterprise Security Package (ESP). ESP-kluster är anslutna till en domän så att användare kan autentisera med autentiseringsuppgifter för domänen. I den här självstudien skapar du två Ranger-principer för att begränsa åtkomsten till `sales*`- och `marketingspend`-ämnen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa domänanvändare
> * Skapa Ranger-principer
> * Skapa ämnen i ett Kafka-kluster
> * Testa Ranger-principer

## <a name="before-you-begin"></a>Innan du börjar

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).

* Logga in på [Azure Portal](https://portal.azure.com/).

* Skapa ett [HDInsight Kafka-kluster med Enterprise Security Package](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet

1. Anslut till Ranger-administratörsanvändargränssnittet från en webbläsare med hjälp av URL:en `https://<ClusterName>.azurehdinsight.net/Ranger/`. Kom ihåg att ändra `<ClusterName>` till namnet på ditt Kafka-kluster.

    > [!NOTE]  
    > Ranger-autentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för Hadoop-kluster. Om du vill förhindra att webbläsare använder cachade Hadoop-autentiseringsuppgifter använder du ett nytt InPrivate-webbläsarfönster för att ansluta till Ranger-administratörsgränssnittet.

2. Logga in med dina administratörsautentiseringsuppgifter för Azure Active Directory (AD). Azure AD-administratörsautentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för HDInsight-kluster eller SSH-autentiseringsuppgifterna för Linux HDInsight-noder.

   ![Apache Ranger-administratörsanvändargränssnitt](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Skapa domänanvändare

Läs avsnittet om hur du [skapar ett HDInsight-kluster med Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) för att se hur du skapar domänanvändarna **sales_user** och **marketing_user**. I ett produktionsscenario kommer domänanvändarna från din Active Directory-klientorganisation.

## <a name="create-ranger-policy"></a>Skapa en Ranger-princip

Skapa en Ranger-princip för **sales_user** och **marketing_user**.

1. Öppna **Ranger-administratörsanvändargränssnittet**.

2. Klicka på **\<ClusterName>_kafka** under **Kafka**. En förkonfigurerad princip kan visas.

3. Klicka på **Lägg till ny princip** och ange följande värden:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Principnamn  |  hdi sales* policy   |
   |Avsnitt   |  sales* |
   |Välj användare  |  sales_user1 |
   |Behörigheter  | publish, consume, create |

   Följande jokertecken kan användas i ämnesnamnet:

   * ’*’ anger inga eller flera förekomster av tecken.
   * ’?’ anger ett enskilt tecken.

   ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Vänta en stund medan Ranger synkroniserar med Azure AD om en domänanvändare inte automatiskt har fyllts i för **Välj användare**.

4. Klicka på **Lägg till** för att spara principen.

5. Klicka på **Lägg till ny princip** och ange sedan följande värden:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Principnamn  |  hdi marketing policy   |
   |Avsnitt   |  marketingspend |
   |Välj användare  |  marketing_user1 |
   |Behörigheter  | publish, consume, create |

   ![Skapa en princip i Apache Ranger-administratörsanvändargränssnittet](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Klicka på **Lägg till** för att spara principen.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Skapa ämnen i ett Kafka-kluster med ESP

Så här skapar du de två avsnitten `salesevents` och `marketingspend`:

1. Använd följande kommando för att öppna en SSH-anslutning till klustret:

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Ersätt `DOMAINADMIN` med administratörsanvändaren för ditt kluster som konfigurerats under [skapa kluster](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp), och ersätt `CLUSTERNAME` med namnet på klustret. Ange lösenordet för administratörsanvändarkontot om du uppmanas till det. Mer information om hur du använder `SSH` med HDInsight finns i [Använda SSH med HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

2. Använd följande kommandon för att spara klustrets namn som en variabel och installera ett JSON-parsningsverktyg, `jq`. Ange Kafka-klustrets namn när du uppmanas till detta.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Använd följande kommandon för att hämta värdarna för Kafka-koordinatortjänsterna. När du blir ombedd att göra det anger du lösenordet till klusteradministratörskontot.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Innan du fortsätter kan du behöva konfigurera din distributionsmiljö, om du inte redan har gjort det. Du behöver komponenter som Java JDK, Apache Maven och en SSH-klient med scp. Mer information finns i [installationsinstruktionerna](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Ladda ned [exemplen på Apache Kafka-domänansluten producent/konsument](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Följ steg 2 och 3 under **Skapa och distribuera exemplet** i [Självstudie: Använda Apache Kafka-producenten och konsument-API:er](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Kör följande kommandon:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testa Ranger-principerna

Baserat på Ranger-principerna som konfigurerats kan **sales_user** skapa/använda ämnet `salesevents` men inte ämnet `marketingspend`. På motsvarande sätt kan **marketing_user** skapa/använda ämnet `marketingspend` men inte ämnet `salesevents`.

1. Öppna en ny SSH-anslutning till klustret. Använd följande kommando för att logga in som **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Kör följande kommando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Använd koordinatornamnen från föregående avsnitt till att ange följande miljövariabler:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Exempel: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Följ steg 3 under **Skapa och distribuera exemplet** i [Självstudie: Använd producent- och konsument-API:er för Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) så att `kafka-producer-consumer.jar` också är tillgänglig för **sales_user**.

5. Kontrollera att **sales_user1** kan producera till ämnet `salesevents` genom att köra följande kommando:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Kör följande kommando för att konsumera från ämnet `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Kontrollera att du kan läsa meddelandena.

7. Kontrollera att **sales_user1** inte kan producera till ämnet `marketingspend` genom att köra följande i samma ssh-fönster:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Ett auktoriseringsfel inträffar och kan ignoreras.

8. Observera att **marketing_user1** inte kan konsumera från ämnet `salesevents`.

   Upprepa steg 1–4 ovan, men nu som **marketing_user1**.

   Kör följande kommando för att konsumera från ämnet `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Det går inte att visas tidigare meddelanden.

9. Granska åtkomsthändelserna i Ranger-användargränssnittet.

   ![Principgranskning i Ranger-användargränssnittet](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Nästa steg

* [Ta med din egen nyckel till Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [En introduktion till Apache Hadoop-säkerhet med Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
