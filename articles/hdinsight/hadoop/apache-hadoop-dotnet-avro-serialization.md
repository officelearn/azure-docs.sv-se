---
title: Serialisera data i Hadoop - Microsoft Avro Library - Azure
description: Lär dig mer om att serialisera och deserialisera data i Hadoop på HDInsight med hjälp av Microsoft Avro Library ska sparas i minnet, en databas eller fil.
keywords: avro, hadoop avro
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.custom: hdiseo17may2017
ms.openlocfilehash: 5928c6490c9de6c48b75800158b8298007d7b8ed
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246913"
---
# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serialisera data i Hadoop med Microsoft Avro Library

>[!NOTE]
>Avro SDK stöds inte längre av Microsoft. Biblioteket är communityn för öppen källkod som stöds. Källor för biblioteket finns i [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

Det här avsnittet visar hur du använder den [Microsoft Avro Library](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro) att serialisera objekt och andra datastrukturer till byteströmmar för att bevara dem i minnet, en databas eller en fil. Den visar även hur du deserialisera dem om du vill återställa de ursprungliga objekten.

[!INCLUDE [windows-only](../../../includes/hdinsight-windows-only.md)]

## <a name="apache-avro"></a>Apache Avro
Den <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Microsoft Avro Library</a> implementerar Apache Avros data serialisering system för Microsoft.NET-miljö. Apache Avro ger en kompakta binära dataöverföringsformat för serialisering. Den använder <a href="http://www.json.org" target="_blank">JSON</a> att definiera ett språkoberoende schema som meddelar försäkringar språksamverkan. Data serialiseras på ett språk kan läsas i en annan. För närvarande stöds C, C++, C#, Java, PHP, Python och Ruby. Detaljerad information om formatet finns i den <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">specifikation för Apache Avro</a>. 

>[!NOTE]
>Microsoft Avro Library stöder inte den RPC-anrop (RPC-anrop) delen av den här specifikationen.
>

Serialiserad representation av ett objekt i Avro-systemet består av två delar: schemat och faktiskt värde. Avro-schemat beskriver språkoberoende datamodellen serialiserade data med JSON. Den visas sida vid sida med en binär representation av data. Med schemat separat från binär representation gör det möjligt för varje objekt som ska skrivas med inga per värde omkostnader, att göra snabba serialisering och representationen små.

## <a name="the-hadoop-scenario"></a>Hadoop-scenario
Apache Avro serialiseringsformat används ofta i Azure HDInsight och andra Apache Hadoop-miljöer. Avro är ett enkelt sätt att representera komplexa datastrukturer i ett Hadoop-MapReduce-jobb. Formatet för Avro-filernas (Avro objektet container-fil) har utformats för att stödja den distribuerade MapReduce-programmeringsmodellen. Nyckelegenskap som gör att distributionen är att filerna är ”delbara” i den mening att söka efter valfri punkt i en fil och börja läsa från ett visst block.

## <a name="serialization-in-avro-library"></a>Serialisering i Avro-bibliotek
.NET-bibliotek för Avro stöder två olika sätt att serialisering av objekt:

* **reflektion** -JSON-schemat för typer skapas automatiskt från data kontrakt attribut för .NET-typer serialiseras.
* **allmän post** – en JSON-schema anges uttryckligen i en post som representeras av den [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) klassen när det finns inga .NET-typer som beskriver schemat för data som ska serialiseras.

När dataschemat är känt både skrivaren och läsare på dataströmmen, kan data skickas utan dess schema. I fall när en fil med Avro-objektet behållare används lagras schemat i filen. Andra parametrar, till exempel codec som används för datakomprimering, kan anges. Dessa scenarier som beskrivs i detalj och illustreras i följande kodexempel:

## <a name="install-avro-library"></a>Installera Avro-bibliotek
Följande krävs innan du installerar biblioteket:

* <a href="https://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
* <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 eller senare)

Observera att Newtonsoft.Json.dll beroendet ska hämtas automatiskt med installationen av Microsoft Avro Library. Proceduren finns i följande avsnitt:

Microsoft Avro Library distribueras som ett NuGet-paket som kan installeras från Visual Studio via följande procedur:

1. Välj den **projekt** -> fliken **hantera NuGet-paket...**
2. Sök efter ”Microsoft.Hadoop.Avro” i den **Sök Online** box.
3. Klicka på den **installera** bredvid knappen **Avro-bibliotek för Microsoft Azure HDInsight**.

Observera att Newtonsoft.Json.dll (> = 6.0.4) beroende också hämtas automatiskt med Microsoft Avro Library.

Microsoft Avro Library källkoden finns på [Github](https://github.com/Azure/azure-sdk-for-net/tree/master/src/ServiceManagement/HDInsight/Microsoft.Hadoop.Avro).

## <a name="compile-schemas-using-avro-library"></a>Kompilera scheman med Avro-bibliotek
Microsoft Avro Library innehåller en kod generation verktyg som gör att skapa C#-typer som automatiskt baserat på den tidigare definierade JSON-scheman. Verktyget code generation distribueras inte som en binär körbar fil, men de är lätta att bygga via följande procedur:

1. Hämta ZIP-filen med den senaste versionen av HDInsight SDK källkoden från <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK för Hadoop</a>. (Klicka på den **ladda ned** ikon, inte den **hämtar** fliken.)
2. Extrahera HDInsight SDK till en katalog på datorn med .NET Framework 4 installerat och anslutet till Internet för att ladda ned nödvändiga beroendet NuGet-paket. Nedan förutsätter vi att källkoden ska extraheras till C:\SDK.
3. Gå till mappen C:\SDK\src\Microsoft.Hadoop.Avro.Tools och kör build.bat. (Filen anropar MSBuild från 32-bitars fördelningen av .NET Framework. Om du vill använda 64-bitarsversionen redigera build.bat, efter kommentarerna i filen.) Kontrollera att versionen har utförts. (På vissa system, MSBuild kan ge varningar. Dessa varningar påverkar inte verktyget så länge det finns inga build-fel.)
4. Den kompilerade som finns i C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.

Om du vill bekanta dig med att kommandoradssyntaxen, kör du följande kommando från mappen där verktyget code generation finns: `Microsoft.Hadoop.Avro.Tools help /c:codegen`

Du kan skapa C# klasser från JSON-schema-exempelfilen medföljer källkoden för att testa verktyget. Kör följande kommando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Detta ska skapa två C#-filer i den aktuella katalogen: SensorData.cs och Location.cs.

Information om den logik som verktyget code generation använder vid konvertering av JSON-schemat för C#-typer finns i filen GenerationVerification.feature finns i C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Namnområden extraheras från JSON-schema med hjälp av logiken i filen som nämns i föregående stycke. Namnområden som extraheras från schemat företräde framför det har angetts med parametern /n i verktyget från kommandoraden. Om du vill åsidosätta namnområden som ingår i schemat kan du använda parametern /nf. Till exempel ändra alla namnområden från SampleJSONSchema.avsc till my.own.nspace genom att köra följande kommando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="about-the-samples"></a>Om exemplen
Sex exemplen i det här avsnittet visar olika scenarier som stöds av Microsoft Avro Library. Microsoft Avro Library är utformade att fungera med alla stream. I det här manipuleras data via minne strömmar i stället för filen strömmar eller databaser för enkelhetens skull. Den metod som användes i en produktionsmiljö är beroende av de exakta krav, datakälla och volym, prestandabegränsningar och andra faktorer.

De två första exemplen visar hur du serialisera och deserialisera data i stream minnesbuffertar genom att använda reflektion och allmän poster. Schemat i båda fallen antas delas mellan läsare och skrivare out-of-band.

De tredje och fjärde exempel visar hur du serialisera och deserialisera data med hjälp av behållarfiler för Avro-objektet. När data lagras i en behållare Avro-fil, lagras dess schema alltid med det eftersom schemat måste delas för deserialisering.

Exemplet som innehåller de fyra första exemplen kan laddas ned från den <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-86055923" target="_blank">kodexempel för Azure</a> plats.

Det femte exemplet visar hur du använder en anpassad komprimerings-codec för behållarfiler i Avro-objektet. Ett sampel som innehåller koden för det här exemplet kan laddas ned från den <a href="https://code.msdn.microsoft.com/Serialize-data-with-the-67159111" target="_blank">kodexempel för Azure</a> plats.

Sjätte exemplet visar hur du använder Avro-serialisering för att överföra data till Azure Blob storage och analysera dem med hjälp av Hive med HDInsight (Hadoop)-kluster. Den kan hämtas från den <a href="https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3" target="_blank">kodexempel för Azure</a> plats.

Här finns länkar till sex exemplen beskrivs i avsnittet:

* <a href="#Scenario1">**Serialisering med reflektion** </a> -JSON-schemat för typer serialiseras skapas automatiskt från data kontrakt attribut.
* <a href="#Scenario2">**Serialisering med allmän post** </a> -JSON-schemat anges uttryckligen i en post när inga .NET är tillgänglig för reflektion.
* <a href="#Scenario3">**Serialisering med reflektion objektet behållarfiler** </a> -JSON-schemat är automatiskt har byggt och delat tillsammans med den serialiserade data via en Avro objektet container-fil.
* <a href="#Scenario4">**Serialisering med allmän post objektet behållarfiler** </a> -JSON-schemat som uttryckligen anges innan serialisering och delas tillsammans med data via en Avro objektet container-fil.
* <a href="#Scenario5">**Serialisering med en anpassad komprimerings-codec objektet behållarfiler** </a> -exemplet visar hur du skapar en Avro objektet container-fil med en anpassad .NET-implementering av komprimerings-codec Deflate data.
* <a href="#Scenario6">**Använda Avro för att överföra data för tjänsten Microsoft Azure HDInsight** </a> -exemplet illustrerar hur Avro serialisering interagerar med HDInsight-tjänsten. En aktiv Azure-prenumeration och åtkomst till ett Azure HDInsight-kluster krävs för att köra det här exemplet.

## <a name="Scenario1"></a>Exempel 1: Serialisering med reflektion
JSON-schemat för typer kan automatiskt genereras av Microsoft Avro Library via reflektion från data kontrakt attributen för C#-objekten serialiseras. Microsoft Avro Library skapar en [ **IAvroSeralizer<T>**  ](https://msdn.microsoft.com/library/dn627341.aspx) att identifiera de fält som ska serialiseras.

I det här exemplet objekt (en **SensorData** klass med en medlem **plats** struct) är serialiserat till en minnesström och den här strömmen i sin tur avserialiseras. Resultatet jämförs sedan den första instansen att bekräfta att den **SensorData** objekt återställas är identisk med ursprungligt.

Schemat i det här exemplet förutsätts kan delas mellan läsare och skrivare, så Avro-objektformat för behållaren inte krävs. Ett exempel på hur du serialisera och deserialisera data i minnesbuffertar genom att använda reflektion med behållare objektformat när schemat måste delas med data som finns i <a href="#Scenario3">serialisering med reflektionobjektetbehållarfiler</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-2-serialization-with-a-generic-record"></a>Exempel 2: Serialisering med en allmän post
En JSON-schema kan uttryckligen anges i en allmän post när reflektion inte kan användas eftersom data inte kan återges via .NET-klasser med ett datakontrakt. Den här metoden är långsammare än att använda reflektion. I sådana fall kan kan schema för data också vara dynamisk, det vill säga inte känd vid kompilering. Data som visas som fil med kommaavgränsade värden (CSV) filer vars schema är okänd tills det transformeras till Avro-format vid körning är ett exempel på den här typen av dynamisk scenario.

Det här exemplet visar hur du skapar och använder en [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) uttryckligen ange en JSON-schema, så fyller den med data och sedan hur du serialisera och deserialisera den. Den första instansen att bekräfta att posten återställas är identisk med ursprungligt jämförs sedan resultatet.

Schemat i det här exemplet förutsätts kan delas mellan läsare och skrivare, så Avro-objektformat för behållaren inte krävs. Ett exempel på hur du serialisera och deserialisera data i minnesbuffertar med hjälp av en allmän post med behållare objektformat när schemat måste ingå i den serialiserade informationen finns i den <a href="#Scenario4">serialisering med objektet behållarfiler med allmän post</a> exempel.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn is then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exempel 3: Serialisering med objektet behållarfiler och serialisering med reflektion
Det här exemplet påminner om scenariot i den <a href="#Scenario1"> första exemplet</a>, där schemat implicit har angetts med reflektion. Skillnaden är att här antas schemat inte vara kända för läsare som deserializes den. Den **SensorData** objekt som ska serialiseras och deras implicit angivna schema lagras i en Avro objektet container-fil som representeras av den [ **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) klass.

Data är serialiserat i det här exemplet med [ **SequentialWriter<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx) och avserialiserade med [ **SequentialReader<SensorData>**  ](https://msdn.microsoft.com/library/dn627340.aspx). Resultatet sedan jämförs inledande instanserna så identitet.

Data i filen objektet container komprimeras via standard [ **Deflate** ] [ deflate-100] komprimerings-codec från .NET Framework 4. Se den <a href="#Scenario5"> femte exempel</a> i det här avsnittet om hur du använder en senare och överlägsen version av den [ **Deflate** ] [ deflate-110] komprimerings-codec tillgängligt i .NET Framework 4.5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


## <a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exempel 4: Serialisering med objektet behållarfiler och serialisering med allmän post
Det här exemplet påminner om scenariot i den <a href="#Scenario2"> andra exemplet</a>, där schemat uttryckligen anges med JSON. Skillnaden är att här antas schemat inte vara kända för läsare som deserializes den.

Test-datauppsättning har samlats in i en lista över [ **AvroRecord** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) objekt via ett uttryckligen definierade JSON-schema och lagras sedan i en behållare-fil för objektet som representeras av den [  **AvroContainer** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) klass. Den här behållaren skapar en skrivare som används för att serialisera data komprimeras upp till en minnesström som sedan sparas till en fil. Den [ **Codec.Null** ](https://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) parametern används för att skapa läsaren anger att dessa data inte komprimeras.

Data är sedan läsa från filen och avserialiseras till en samling objekt. Den här samlingen är jämfört med den ursprungliga listan med Avro-poster för att bekräfta att de är identiska.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data is not compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




## <a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exempel 5: Serialisering med objektet behållarfiler med en anpassad komprimerings-codec
Det femte exemplet visar hur du använder en anpassad komprimerings-codec för behållarfiler i Avro-objektet. Ett sampel som innehåller koden för det här exemplet kan laddas ned från den [kodexempel för Azure](https://code.msdn.microsoft.com/Serialize-data-with-the-67159111) plats.

Den [Avro-specifikationen](http://avro.apache.org/docs/current/spec.html#Required+Codecs) tillåter användning av ett valfritt komprimerings-codec (förutom **Null** och **Deflate** standardinställningarna). Det här exemplet är inte implementerar en ny codec, till exempel Snappy (nämns som en valfri codec i den [Avro-specifikationen](http://avro.apache.org/docs/current/spec.html#snappy)). Den visar hur du använder .NET Framework 4.5-implementering av den [ **Deflate** ] [ deflate-110] codec, vilket ger en bättre komprimeringsalgoritm baserat på den [zlib ](http://zlib.net/) Komprimeringsbiblioteket än standardversionen för .NET Framework 4.

    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It catches the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it relies on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which deserializes all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

## <a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exempel 6: Använda Avro för att överföra data för tjänsten Microsoft Azure HDInsight
I sjätte exempel illustrerar några programmeringstekniker som rör interagera med tjänsten Azure HDInsight. Ett sampel som innehåller koden för det här exemplet kan laddas ned från den [kodexempel för Azure](https://code.msdn.microsoft.com/Using-Avro-to-upload-data-ae81b1e3) plats.

Exemplet gör följande:

* Ansluter till ett befintligt kluster för HDInsight-tjänsten.
* Serialiserar flera CSV-filer och överför resultatet till Azure Blob storage. (CSV-filer ska distribueras tillsammans med exemplet och som representerar ett utdrag ur AMEX lager historiska data distribueras av [Infochimps](http://www.infochimps.com/) under 1970 2010. Exemplet läser CSV-fildata, konverterar poster till instanser av den **lager** klassen och Serialiserar dem genom att använda reflektion. Definition av lagerartiklar har skapats från en JSON-schema via verktyget Microsoft Avro Library code generation.
* Skapar en ny extern tabell som kallas **aktier** i Hive och länkar den till data laddades upp i föregående steg.
* Kör en fråga med hjälp av Hive via den **aktier** tabell.

Dessutom utför exemplet en Rensa procedur före och efter utför större operationer. Alla relaterade Azure Blob-data och mappar tas bort under rensningen, och Hive-tabellen har släppts. Du kan även anropa metoden Rensa från kommandoraden för exemplet.

Exemplet har följande krav:

* En aktiv prenumeration på Microsoft Azure och dess prenumerations-ID.
* Ett hanteringscertifikat för prenumerationen med motsvarande privata nyckel. Certifikatet ska installeras i den aktuella användaren privat lagringen på den dator som används för att köra exemplet.
* En aktiv HDInsight-kluster.
* Ett Azure Storage-konto länkade till HDInsight-klustret från föregående krav, tillsammans med motsvarande primära eller sekundära åtkomstnyckeln.

All information från kraven ska anges till exempelkonfigurationsfilen innan exemplet körs. Det finns två sätt att göra det:

* Redigera filen app.config i rotkatalogen exemplet och skapa exemplet
* Skapa först exemplet och redigerar AvroHDISample.exe.config i katalogen build

I båda fallen kan alla ändringar görs den **<appSettings>** inställningar. Följ kommentarerna i filen.
Exemplet körs från kommandoraden genom att köra följande kommando (där .zip-filen med exemplet antogs extraheras till C:\AvroHDISample; om annars använder sökvägen till relevanta):

    AvroHDISample run C:\AvroHDISample\Data

Om du vill rensa klustret kör du följande kommando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
