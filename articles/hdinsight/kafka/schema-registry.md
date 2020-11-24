---
title: Apache Kafka med ett samsigs schema register i Azure HDInsight
description: Distribuera ett HDInsight-hanterat Kafka-kluster med en Edge-nod i en Virtual Network och installera sedan nätverks hanterings schema på Edge-noden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500330"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka med ett samsigs schema register i Azure HDInsight

Kafka schema Registry innehåller serialiserare som ansluter till Apache Kafka klienter som hanterar lagring av meddelande scheman och hämtning för Kafka-meddelanden som skickas i Avro-format. Det var ett OSS-projekt genom att samföra, men är nu under [licens avtalet](https://www.confluent.io/blog/license-changes-confluent-platform/). Schema registret fungerar också i följande syfte:

* Lagra och hämta scheman för producenter och konsumenter.
* Använd bakåt/Forward/full-kompatibilitet för ämnen.
* Minska storleken på nytto lasten som skickas till Kafka.

I ett HDInsight-hanterat Kafka-kluster distribueras schema registret vanligt vis på en Edge-nod för att tillåta beräknings separering från huvudnoder.

Nedan visas en representativ arkitektur för hur schema registret distribueras i ett HDInsight-kluster. Schema registret visar internt en REST API för åtgärder på den.  Producenter och konsumenter kan interagera med schema registret inifrån VNet eller med hjälp av [KAFKA rest-proxyn](rest-proxy.md).

![HDInsight Kafka schema register](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Distribuera en HDInsight-hanterad Kafka med ett hanterings schema register

I det här avsnittet distribuerar vi ett HDInsight-hanterat Kafka-kluster med en Edge-nod i ett virtuellt nätverk och sedan installerar du tabellen för hanterings schema på Edge-noden.  

1. Klicka på knappen **distribuera till Azure** nedan för att logga in på Azure och öppna Resource Manager-mallen.

    [![Distribuera till Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. Fyll i fälten enligt beskrivningen nedan i mallen för anpassad distribution:

    |Egenskap |Beskrivning |
    |---|---|
    |Prenumeration|I list rutan väljer du den Azure-prenumeration som används för klustret.|
    |Resursgrupp|Välj din befintliga resurs grupp i list rutan eller Välj **Skapa ny**.|
    |Region|Välj en region där klustret skapas i list rutan.|
    |Klusternamn|Ange ett globalt unikt namn. Eller lämna som-är att använda standard namnet.|
    |Användarnamn för klusterinloggning|Ange användar namnet, standard är **administratör**.|
    |Lösenord för klusterinloggning|Ange lösen ordet.|
    |Användar namn för SSH|Ange användar namnet, standardvärdet är **sshuser**.|
    |SSH-lösenord|Ange lösen ordet.|

    Lämna de andra fälten som de är. Välj **Granska + skapa** för att fortsätta.

1. Granska distributions informationen och välj sedan **skapa** för att initiera distributionen. Det kan ta 45 minuter att slutföra distributionen.

## <a name="configure-the-confluent-schema-registry"></a>Konfigurera registrerings schema registret

I det här avsnittet konfigurerar vi det Kafka schema register som vi installerade på Edge-noden. Registrerings schema registret finns på  `/etc/schema-registry/schema-registry.properties` och mekanismerna för att starta och stoppa tjänstens körbara filer finns i  `/usr/bin/` mappen.

Schema registret måste känna till Zookeeper-tjänsten kan interagera med HDInsight Kafka-kluster. Följ stegen nedan för att hämta information om Zookeeper-kvorumet.

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustrets Edge-nod. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Konfigurera lösen ords variabel. Ersätt lösen ordet med klustrets inloggnings lösen ord och ange sedan kommandot:

    ```bash
    export password='PASSWORD'
    ```

1. Extrahera rätt bokstäver-kluster namn. Kör följande kommando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Extrahera Kafka Zookeeper-värdarna. Kör följande kommando:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Anteckna det här värdet eftersom det kommer att användas senare.

1. Extrahera Kafka Broker-värdarna. Kör följande kommando:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Öppna mappen schema register egenskaper i redigerings läge. Kör följande kommando:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Uppdatera värdet för `kafkastore.connection.url` med Zookeeper-strängen som identifierades tidigare.
    1. Uppdatera värdet för `debug` till `true` .

    Egenskaps filen ser nu ut ungefär så här:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Om du vill spara filen använder du **CTRL + X**, **Y** och **anger** sedan.

1. Starta schema registret och peka det för att använda den uppdaterade schema register egenskaps filen. Kör följande kommandon:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. Starta ett annat SSH-fönster med schema registret som körs i en SSH-session.  Registrera en ny version av ett schema under ämnet "Kafka-Key" och notera utdata:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Registrera en ny version av ett schema under ämnet "Kafka-Value" och notera utdata:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Visa alla ämnen och kontrol lera utdata:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Du kanske vill prova några andra [avancerade kommandon som anges här](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Skicka och konsumera Avro-data från Kafka med hjälp av schema register

I det här avsnittet ska vi läsa data från standar din data och skriva den till ett Kafka-ämne i ett format. Vi läser sedan data från Kafka-ämnena med en konsument med en Avro-formaterare för att transformera dessa data till läsbart format.

1. Skapa ett Kafka-ämne `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. Använd **Kafka Avro Console Producer** för att skapa ett schema, tilldela schemat till ämnet och börja skicka data till avsnittet i Avro-format. Se till att Kafka-avsnittet i föregående steg har skapats och att **$KAFKABROKERS** har ett värde.

    Det schema vi skickar är en nyckel: värde par

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Använd kommandot nedan för att starta **Kafka Avro-konsolens tillverkare**:

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. När producenten är redo att ta emot meddelanden börjar du skicka meddelandena i det fördefinierade Avro schema formatet. Använd Tabb-tangenten för att skapa avstånd mellan nyckeln och värdet.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Försök att ange slumpmässiga data som inte är schema i konsol producenten för att se hur producenten nu tillåter data som inte överensstämmer med det fördefinierade Avro-schemat.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. På en annan skärm startar du Kafka Avro-konsolens konsument

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Du bör börja se följande utdata:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Nästa steg

* [Använd Apache Kafka tillverkare och klient-API: er](apache-kafka-producer-consumer-api.md)