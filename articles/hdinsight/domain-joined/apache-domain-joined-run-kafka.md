---
title: Självstudie – Apache Kafka & Enterprise Security – Azure HDInsight
description: Självstudie – lär dig hur du konfigurerar Apache Ranger-principer för Kafka i Azure HDInsight med Enterprise Security Package.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 6da2537464e39ecb2c613a97b19f2d8f316818af
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677552"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Självstudie: Konfigurera Apache Kafka-principer i HDInsight med Enterprise Security Package (förhandsversion)

Lär dig hur du konfigurerar Apache Ranger-principer för Apache Kafka-kluster med Enterprise Security Package (ESP). ESP-kluster är anslutna till en domän så att användare kan autentisera med autentiseringsuppgifter för domänen. I den här självstudien skapar du två Ranger-principer för att begränsa åtkomsten till `sales`- och `marketingspend`-ämnen.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa domänanvändare
> * Skapa Ranger-principer
> * Skapa ämnen i ett Kafka-kluster
> * Testa Ranger-principer

## <a name="prerequisite"></a>Förutsättning

Ett [HDInsight Kafka-kluster med Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Anslut till Apache Ranger Admin-gränssnittet

1. Anslut till Ranger-administratörsanvändargränssnittet från en webbläsare med hjälp av URL:en `https://ClusterName.azurehdinsight.net/Ranger/`. Kom ihåg att ändra `ClusterName` till namnet på ditt Kafka-kluster. Ranger-autentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för Hadoop-kluster. Om du vill förhindra att webbläsare använder cachade Hadoop-autentiseringsuppgifter använder du ett nytt InPrivate-webbläsarfönster för att ansluta till Ranger-administratörsgränssnittet.

2. Logga in med dina administratörsautentiseringsuppgifter för Azure Active Directory (AD). Azure AD-administratörsautentiseringsuppgifterna är inte samma som autentiseringsuppgifterna för HDInsight-kluster eller SSH-autentiseringsuppgifterna för Linux HDInsight-noder.

   ![HDInsight Apache Ranger-administratörs gränssnitt](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Skapa domänanvändare

Läs avsnittet om hur du [skapar ett HDInsight-kluster med Enterprise Security Package](./apache-domain-joined-configure-using-azure-adds.md) för att se hur du skapar domänanvändarna **sales_user** och **marketing_user**. I ett produktionsscenario kommer domänanvändarna från din Active Directory-klientorganisation.

## <a name="create-ranger-policy"></a>Skapa en Ranger-princip

Skapa en Ranger-princip för **sales_user** och **marketing_user**.

1. Öppna **Ranger-administratörsanvändargränssnittet**.

2. Välj ** \< kluster namn>_kafka** under **Kafka**. En förkonfigurerad princip kan visas.

3. Välj **Lägg till ny princip** och ange följande värden:

   |Inställningen  |Föreslaget värde  |
   |---------|---------|
   |Principnamn  |  hdi sales* policy   |
   |Ämne   |  sales* |
   |Välj användare  |  sales_user1 |
   |Behörigheter  | publish, consume, create |

   Följande jokertecken kan användas i ämnesnamnet:

   * ’*’ anger inga eller flera förekomster av tecken.
   * ’?’ anger ett enskilt tecken.

   ![Apache Ranger admin-gränssnitt skapa Policy1](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)

   Vänta en stund medan Ranger synkroniserar med Azure AD om en domänanvändare inte automatiskt har fyllts i för **Välj användare**.

4. Välj **Lägg till** för att spara principen.

5. Välj **Lägg till ny princip** och ange sedan följande värden:

   |Inställningen  |Föreslaget värde  |
   |---------|---------|
   |Principnamn  |  hdi marketing policy   |
   |Ämne   |  marketingspend |
   |Välj användare  |  marketing_user1 |
   |Behörigheter  | publish, consume, create |

   ![Apache Ranger admin-gränssnitt skapa Policy2](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Välj **Lägg till** för att spara principen.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Skapa ämnen i ett Kafka-kluster med ESP

Så här skapar du de två avsnitten `salesevents` och `marketingspend`:

1. Använd följande kommando för att öppna en SSH-anslutning till klustret:

   ```cmd
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Ersätt `DOMAINADMIN` med administratörsanvändaren för ditt kluster som konfigurerats under [skapa kluster](./apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp), och ersätt `CLUSTERNAME` med namnet på klustret. Ange lösenordet för administratörsanvändarkontot om du uppmanas till det. Mer information om hur du använder `SSH` med HDInsight finns i [Använda SSH med HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Använd följande kommandon för att spara klustrets namn som en variabel och installera ett JSON-parsningsverktyg, `jq`. Ange Kafka-klustrets namn när du uppmanas till detta.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Använd följande kommandon för att hämta värdarna för Kafka-koordinatortjänsterna. När du blir ombedd att göra det anger du lösenordet till klusteradministratörskontot.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   Innan du fortsätter kan du behöva konfigurera utvecklings miljön om du inte redan har gjort det. Du behöver komponenter som Java-JDK, Apache Maven och en SSH-klient med SCP. Mer information finns i [installations anvisningarna](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Ladda ned [exemplen på Apache Kafka-domänansluten producent/konsument](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Följ steg 2 och 3 under **Skapa och distribuera exemplet** i [Självstudie: Använda Apache Kafka-producenten och konsument-API:er](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example)

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

4. Följ steg 3 under **utveckla och distribuera exemplet** i [Självstudier: Använd Apache Kafka tillverkare och klient-API: er](../kafka/apache-kafka-producer-consumer-api.md#build-and-deploy-the-example) för att se till att de `kafka-producer-consumer.jar` också är tillgängliga för **sales_user**.

> [!NOTE]  
> I den här självstudien använder du Kafka-Producer-Consumer. jar under "DomainJoined-produce-konsument"-projekt (inte det som ingår i producent-konsument-projektet, som är för icke-domänanslutna scenarier).

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

   ![Ranger UI policy granska åtkomst händelser ](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det Kafka-kluster som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. I rutan **Sök** längst upp skriver du **HDInsight**.
1. Välj **HDInsight-kluster** under **Tjänster**.
1. I listan över HDInsight-kluster som visas klickar du på **...** intill det kluster som du skapade för den här självstudien. 
1. Klicka på **ta bort**. Klicka på **Ja**.

## <a name="troubleshooting"></a>Felsökning
Om Kafka-Producer-Consumer. jar inte fungerar i ett domänanslutet kluster bör du kontrol lera att du använder Kafka-Producer-Consumer. jar under "DomainJoined-produce-konsument"-projekt (inte det som finns i ett projekt med producent-konsument, som är för icke-domänanslutna scenarier).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kundhanterad nyckeldiskkryptering](../disk-encryption.md)
