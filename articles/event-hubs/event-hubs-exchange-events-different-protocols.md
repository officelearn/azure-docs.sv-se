---
title: Exchange-händelser mellan konsumenter och producenter som använder olika protokoll i Azure Event Hubs | Microsoft Docs
description: Den här artikeln visar hur konsumenter och producenter som använder olika protokoll (AMQP-, Apache Kafka- och HTTPS) kan utbyta händelser när du använder Azure Event Hubs.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/05/2018
ms.author: bahariri
ms.openlocfilehash: 54edeafbe950cde88c2c6051f95825cfaed52ac7
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51284947"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Exchange-händelser mellan konsumenter och producenter som använder olika protokoll: AMQP, Kafka och HTTPS
Händelsehubbar i Azure har stöd för tre protokoll för konsumenter och producenter: AMQP, Kafka och HTTPS. Var och en av dessa protokoll har egna sätt att representera ett meddelande naturligt följande frågan: om ett program skickar händelser till en Händelsehubb med ett protokoll och använder dem med ett annat protokoll, göra vad de olika delarna och värdena för den händelse se ut när de anländer till konsumenten? Den här artikeln beskriver Metodtips för både producent och konsument att se till att värden i en händelse tolkas korrekt av den konsumerande appen.

Råd i den här artikeln innehåller dessa klienter med de listade versionerna som används i att utveckla kodfragmenten:

* Kafka-Java-klienten (version 1.1.1 från https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs-klienten för Java (version 1.1.0 från https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs-klienten för .NET (version 2.1.0 från https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (version 5.0.0 från https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (stöder endast producenter)

Andra AMQP-klienter kan bete sig lite annorlunda. AMQP har väldefinierade typsystemet, men ärendets natur serialisering av språkspecifik-typer till och från den typsystemet beror på klienten, precis hur klienten ger åtkomst till delar av en AMQP-meddelandet.

## <a name="event-body"></a>Händelsemeddelandet
Alla klienter Microsoft AMQP representerar händelsemeddelandet som ett Feltolkat egenskapsuppsättning byte. En berörda program skickar en sekvens av byte till klienten och en användningsprogram får den samma sekvensen från klienten. Tolkning av byte-sekvens sker inom programkoden.

När du skickar en händelse via HTTPS är händelsen bokförd innehållet, vilket också behandlas som byte som inte tolkats. Det är enkelt att uppnå samma tillstånd i en Kafka-producent eller konsument med hjälp av angivna ByteArraySerializer och ByteArrayDeserializer enligt följande kod:

### <a name="kafka-byte-producer"></a>Kafka byte []-producent

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka byte []-konsument
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Den här koden skapar en pipeline för transparent byte mellan de två delarna av programmet och kan programutvecklare att manuellt serialisera och deserialisera på något sätt som önskas, inklusive att göra deserialisering beslut vid körning, till exempel baserat på typ eller avsändarinformation i egenskaperna för anges av användaren på händelsen.

Program som har en enda, fast brödtext händelsetyp kanske använda andra Kafka serializers och deserializers att transparent konverterar data. Tänk dig ett program som använder JSON. Konstruktion och tolkning av JSON-sträng sker på programnivå. På nivån Event Hubs är händelsemeddelandet alltid en sträng, en sekvens med byte som representerar tecken i UTF-8-kodning. I det här fallet kan Kafka-producent och konsument dra nytta av den angivna StringSerializer eller StringDeserializer enligt följande kod:

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

Ger inbyggd möjligheter att konvertera strängar till och från UTF-8 byte sekvenser för AMQP-sida både Java och .NET. Microsoft AMQP klienterna representerar händelser som en klass med namnet EventData. I följande exempel visas hur du serialisera en UTF-8-sträng till ett EventData händelsetext i en AMQP-producent och hur att deserialisera en EventData händelsetext till en UTF-8-sträng i en AMQP-konsument.

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

### <a name="c-net-utf-8-string-producer"></a>C#.NET UTF-8 sträng producent
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C#.NET UTF-8 sträng konsument
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Eftersom Kafka är öppen källkod, kan programutvecklare inspektera implementeringen av alla serialiserare eller deserializer och implementera kod som producerar eller förbrukar en kompatibel sekvens av byte på AMQP-sida.

## <a name="event-user-properties"></a>Händelseegenskaper för användare

Egenskaper för anges av användaren kan ange och hämtas från båda AMQP-klienter (i Microsoft AMQP-klienter kallas de egenskaper) och Kafka (där de heter rubriker). HTTPS avsändare kan ange egenskaper för användare på en händelse genom att ange dem som HTTP-huvuden i POST-åtgärd. Dock behandlar Kafka både händelse organ och värden i huvudet som byte sekvenser. Medan egenskapsvärden har typer som förmedlas av kodning egenskapsvärden enligt AMQP-typsystemet i AMQP-klienter.

HTTPS är ett specialfall. Vid sändning, är alla egenskapsvärden UTF-8-text. Event Hubs-tjänsten har en begränsad mängd tolkning konvertera lämpliga egenskapsvärden till AMQP-kodad 32-bitars och 64-bitars signerade heltal, flyttal-64-bitars och booleska värden. Valfritt egenskapsvärde som inte motsvarar någon av dessa typer behandlas som en sträng.

Blanda dessa metoder att egenskapen att skriva innebär att en Kafka-konsument ser raw AMQP-kodad byte-sekvens, inklusive informationen AMQP. Medan en AMQP-förbrukare ser ej typbestämd byte-sekvens som skickas av Kafka-producent som programmet måste tolka.

För Kafka-konsumenter som tar emot egenskaper från producenter AMQP eller HTTPS, använder du klassen AmqpDeserializer som modelleras efter andra deserializers i Kafka-ekosystemet. Den tolkar informationen i AMQP-kodad byte-sekvenser att deserialisera data-byte i en Java-datatypen.

Som bästa praxis rekommenderar vi att du inkluderar en egenskap i meddelanden som skickas via AMQP eller HTTPS. Kafka-konsument kan använda den för att fastställa om värden i huvudet behöver AMQP deserialisering. Värdet för egenskapen är inte viktigt. Den behöver bara ett välkänt namn att Kafka-konsument kan hitta i listan över sidhuvuden och justeras sitt beteende.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP till Kafka del 1: skapa och skicka en händelse i C# (.NET) med egenskaper
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP till Kafka del 2: Använd AmqpDeserializer för att deserialisera dessa egenskaper i en Kafka-konsument
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

Om programmet vet den förväntade typen för en egenskap, deserialisering metoder som inte kräver en typkonvertering efteråt, men de returnerade ett fel om egenskapen inte är av den förväntade typen.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP till Kafka del 3: ett sätt att använda AmqpDeserializer i en Kafka-konsument
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

Kommer den andra riktningen mer ingår, eftersom huvuden på en Kafka-producent ses alltid av en AMQP-konsument som byte (Skriv org.apache.qpid.proton.amqp.Binary för Microsoft Azure Event Hubs-klient för Java, eller `System.Byte[]` för Microsofts .NET AMQP-klienter). Det enklaste sättet är att använda en av serializers Kafka anger att generera byte för huvudvärden på sidan för Kafka-producent och sedan skriva en kompatibel deserialisering kod på AMQP konsument sida.

Precis som med AMQP till Kafka, är den bästa metoden som vi rekommenderar att inkludera en egenskap i meddelanden som skickas via Kafka. AMQP konsumenten kan använda egenskapen för att fastställa om värden i huvudet behöver deserialisering. Värdet för egenskapen är inte viktigt. Den behöver bara ett välkänt namn att AMQP konsumenten kan hitta i listan över sidhuvuden och justeras sitt beteende. Om Kafka-producent inte ändras, är det också möjligt för den konsumerande appen kontrollera om egenskapens värde är av typen binary eller byte och försöka deserialisering beroende på vilken.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka till AMQP del 1: skapa och skicka en händelse från Kafka med egenskaper
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

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka till AMQP del 2: manuellt deserialisera dessa egenskaper i C# (.NET)
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

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka till AMQP del 3: manuellt deserialisera dessa egenskaper i Java
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
I den här artikeln har du lärt dig hur du strömmar till Kafka-aktiverade Event Hubs utan att ändra dina protokollklienter och utan att köra dina egna kluster. Läs mer om Event Hubs och Event Hubs för Kafka i följande artiklar:  

* [Läs om Event Hubs](event-hubs-what-is-event-hubs.md)
* [Läs om Event Hubs för Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Utforska fler exempel på Event Hubs för Kafka GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Använd [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) för att [strömma händelser från Kafka lokalt till Kafka-aktiverade Event Hubs i molnet.](event-hubs-kafka-mirror-maker-tutorial.md)
* Lär dig att strömma till Kafka aktiverade Event Hubs med hjälp av [interna Kafka program](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), eller [Akka strömmar](event-hubs-kafka-akka-streams-tutorial.md)
