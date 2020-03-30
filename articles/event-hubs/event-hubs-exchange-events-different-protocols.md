---
title: Azure Event Hubs – Exchange-händelser med olika protokoll
description: Den här artikeln visar hur konsumenter och producenter som använder olika protokoll (AMQP, Apache Kafka och HTTPS) kan utbyta händelser när de använder Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: femila
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: femila
ms.openlocfilehash: 368cc568c40e878338e6b45205e74cba1d0b6378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372204"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Utbyta händelser mellan konsumenter och producenter som använder olika protokoll: AMQP, Kafka och HTTPS
Azure Event Hubs stöder tre protokoll för konsumenter och producenter: AMQP, Kafka och HTTPS. Vart och ett av dessa protokoll har sitt eget sätt att representera ett meddelande, så naturligtvis uppstår följande fråga: om ett program skickar händelser till en händelsehubb med ett protokoll och förbrukar dem med ett annat protokoll, vad gör de olika delarna och värdena i händelsen ser ut när de kommer till konsumenten? I den här artikeln beskrivs bästa praxis för både producent och konsument för att säkerställa att värdena i en händelse tolkas korrekt av det tidskrävande programmet.

Råden i den här artikeln täcker specifikt dessa klienter, med de listade versioner som används för att utveckla kodavsnitten:

* Kafka Java-klient (version 1.1.1 frånhttps://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs Client för Java (version 1.1.0 frånhttps://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs Client för .NET (version 2.1.0 frånhttps://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (version 5.0.0 frånhttps://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (endast stöder producenter)

Andra AMQP-klienter kan bete sig något annorlunda. AMQP har ett väldefinierat typsystem, men detaljerna för serialisering av språkspecifika typer till och från det typsystemet beror på klienten, liksom hur klienten ger åtkomst till de delar av ett AMQP-meddelande.

## <a name="event-body"></a>Händelsekropp
Alla Microsoft AMQP-klienter representerar händelsetexten som en otolkad påse med byte. Ett producerande program skickar en sekvens av byte till klienten och ett tidskrävande program tar emot samma sekvens från klienten. Tolkningen av bytesekvensen sker i programkoden.

När du skickar en händelse via HTTPS är händelsetexten det POSTed-innehållet, som också behandlas som otolkade byte. Det är lätt att uppnå samma tillstånd i en Kafka producent eller konsument med hjälp av den medföljande ByteArraySerializer och ByteArrayDeserializer som visas i följande kod:

### <a name="kafka-byte-producer"></a>Kafka byte[] producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte[] konsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Den här koden skapar en transparent byte pipeline mellan de två halvorna av programmet och gör det möjligt för programutvecklaren att manuellt serialisera och avserialisera på något sätt som önskas, inklusive att göra deserialisering beslut vid körning, till exempel baserat på typ typ eller avsändande information i användaruppsättningsegenskaper för händelsen.

Program som har en enda, fast händelsekroppstyp kan använda andra Kafka-serialiserare och deserialiserare för att konvertera data på ett transparent sätt. Tänk dig till exempel ett program som använder JSON. Konstruktionen och tolkningen av JSON-strängen sker på applikationsnivå. På eventhubbar-nivå är händelsetexten alltid en sträng, en sekvens av byte som representerar tecken i UTF-8-kodningen. I det här fallet kan Kafka-producenten eller konsumenten dra nytta av den medföljande StringSerializer eller StringDeserializer som visas i följande kod:

### <a name="kafka-utf-8-string-producer"></a>Kafka UTF-8 sträng producent
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka UTF-8 sträng konsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

För AMQP-sidan tillhandahåller både Java och .NET inbyggda sätt att konvertera strängar till och från UTF-8 byte-sekvenser. Microsoft AMQP-klienterna representerar händelser som en klass med namnet EventData. Följande exempel visar hur du serialiserar en UTF-8-sträng till en EventData-händelsetext i en AMQP-producent och hur du avserialiserar en EventData-händelsetext till en UTF-8-sträng i en AMQP-konsument.

### <a name="java-amqp-utf-8-string-producer"></a>Java AMQP UTF-8 sträng producent
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java AMQP UTF-8 sträng konsument
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET UTF-8 sträng producent
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET UTF-8 sträng konsument
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Eftersom Kafka är öppen källkod kan programutvecklaren inspektera implementeringen av serialiserare eller deserializer och implementera kod, som producerar eller förbrukar en kompatibel sekvens av byte på AMQP-sidan.

## <a name="event-user-properties"></a>Egenskaper för händelseanvändare

Egenskaper för användaruppsättning kan ställas in och hämtas från båda AMQP-klienter (i Microsoft AMQP-klienter kallas de egenskaper) och Kafka (där de kallas rubriker). HTTPS-avsändare kan ange användaregenskaper för en händelse genom att ange dem som HTTP-huvuden i POST-åtgärden. Kafka behandlar dock både händelsekroppar och händelserubrikvärden som bytesekvenser. I AMQP-klienter har egenskapsvärden typer som meddelas genom att egenskapsvärdena kodas enligt AMQP-typsystemet.

HTTPS är ett specialfall. Vid tidpunkten för sändning, alla egenskapsvärden är UTF-8 text. Event Hubs-tjänsten gör en begränsad tolkning för att konvertera lämpliga egenskapsvärden till AMQP-kodade 32-bitars och 64-bitars signerade heltal, 64-bitars flyttalsnummer och booleans. Alla egenskapsvärde som inte passar någon av dessa typer behandlas som en sträng.

Att blanda dessa metoder för egenskapsskrivning innebär att en Kafka-konsument ser den råa AMQP-kodade bytesekvensen, inklusive AMQP-typinformationen. Medan en AMQP-konsument ser den oskrivna bytesekvensen som skickas av Kafka-tillverkaren, som ansökan måste tolka.

För Kafka-konsumenter som tar emot egenskaper från AMQP- eller HTTPS-tillverkare använder du klassen AmqpDeserializer, som modelleras efter de andra deserializersna i Kafka-ekosystemet. Typinformationen i de AMQP-kodade bytesekvenserna tolkas för att deserialisera databyten till en Java-typ.

Vi rekommenderar att du inkluderar en egenskap i meddelanden som skickas via AMQP eller HTTPS. Kafka-konsumenten kan använda den för att avgöra om rubrikvärden behöver AMQP-avserialisering. Värdet på fastigheten är inte viktigt. Det behöver bara ett välkänt namn som Kafka konsumenten kan hitta i listan över rubriker och justera sitt beteende därefter.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP till Kafka del 1: skapa och skicka en händelse i C# (.NET) med egenskaper
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP till Kafka del 2: använd AmqpDeserializer för att deserialisera dessa egenskaper i en Kafka konsument
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Om programmet känner till den förväntade typen för en egenskap finns det avserialiseringsmetoder som inte kräver en cast efteråt, men de genererar ett fel om egenskapen inte är av den förväntade typen.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP till Kafka del 3: ett annat sätt att använda AmqpDeserializer i en Kafka konsument
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Att gå åt andra hållet är mer inblandat, eftersom rubriker som anges av en Kafka-producent alltid ses av en AMQP-konsument som råbyte (typ org.apache.qpid.proton.amqp.Binary för Microsoft Azure Event Hubs Client for Java eller `System.Byte[]` för Microsofts .NET AMQP-klienter). Den enklaste sökvägen är att använda en av de Kafka-levererade serialiserarna för att generera byte för rubrikvärdena på Kafka-producentsidan och sedan skriva en kompatibel deserialiseringskod på AMQP-konsumentsidan.

Precis som med AMQP-to-Kafka är den bästa metoden som vi rekommenderar att inkludera en fastighet i meddelanden som skickas via Kafka. AMQP-konsumenten kan använda egenskapen för att avgöra om huvudvärden behöver avserialisering. Värdet på fastigheten är inte viktigt. Det behöver bara ett välkänt namn som AMQP konsumenten kan hitta i listan över rubriker och justera sitt beteende därefter. Om Kafka-producenten inte kan ändras är det också möjligt för det tidskrävande programmet att kontrollera om egenskapsvärdet är av binär eller bytetyp och försök avserialisering baserat på typen.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka till AMQP del 1: skapa och skicka ett evenemang från Kafka med egenskaper
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka till AMQP del 2: manuellt deserialize dessa egenskaper i C # (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka till AMQP del 3: manuellt deserialize dessa egenskaper i Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln lärde du dig att strömma till eventhubbar utan att ändra dina protokollklienter eller köra egna kluster. Mer information om eventhubbar och händelsehubbar för Kafka finns i följande artiklar:  

* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Utforska fler exempel på Event Hubs för Kafka GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att [strömma händelser från Kafka lokalt till Event Hubs i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig hur du streamar till eventhubbar med hjälp av [inbyggda Kafka-program,](event-hubs-quickstart-kafka-enabled-event-hubs.md) [Apache Flink](event-hubs-kafka-flink-tutorial.md)eller [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)
