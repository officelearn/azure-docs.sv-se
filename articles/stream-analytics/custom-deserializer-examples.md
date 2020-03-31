---
title: Läs indata i alla format med .NET-anpassade deserializers i Azure Stream Analytics
description: I den här artikeln beskrivs serialiseringsformatet och gränssnitten som definierar anpassade .NET-deserialiserare för Azure Stream Analytics-moln- och kantjobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845255"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Läs indata i valfritt format med .NET-anpassade deserializers

Med .NET-anpassade deserializers kan ditt Azure Stream Analytics-jobb läsa data från format utanför de tre [inbyggda dataformaten](stream-analytics-parsing-json.md). I den här artikeln beskrivs serialiseringsformatet och gränssnitten som definierar .NET-anpassade deserializers för Azure Stream Analytics-moln- och kantjobb. Det finns också exempel deserializers för protokollbuffert och CSV-format.

## <a name="net-custom-deserializer"></a>.NET anpassad deserializer

Följande kodexempel är de gränssnitt som definierar `StreamDeserializer<T>`den anpassade deserializern och implementerar .

`UserDefinedOperator`är basklassen för alla anpassade streamingoperatörer. Det initierar `StreamingContext`, vilket ger sammanhang som innehåller mekanism för publicering av diagnostik som du måste felsöka eventuella problem med din deserializer.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Följande kodavsnitt är deserialisering för strömmande data. 

Överhoppningsbara fel bör `IStreamingDiagnostics` avges med hjälp av att skickas genom `UserDefinedOperator`'s Initialize-metoden. Alla undantag behandlas som fel och deserializer kommer att återskapas. Efter ett visst antal fel går jobbet till en misslyckad status.

`StreamDeserializer<T>`deserialiserar en ström till `T`objekt av typen . Följande villkor skall vara uppfyllda:

1. T är en klass eller en struct.
1. Alla offentliga fält i T är antingen
    1. En av [sbyte, byte, kort, ushort, int, uint, lång, DateTime, sträng, flyta, dubbel] eller deras nullable motsvarigheter.
    1. En annan struktur eller klass som följer samma regler.
    1. Matris av `T2` typ som följer samma regler.
    1. IList`T2` där T2 följer samma regler.
    1. Har inga rekursiva typer.

Parametern `stream` är den ström som innehåller det serialiserade objektet. `Deserialize`returnerar en `T` samling instanser.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`tillhandahåller sammanhang som innehåller en mekanism för publicering av diagnostik för användaroperator.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`är diagnostiken för användardefinierade operatorer, inklusive serialiserare, deserializer och användardefinierade funktioner.

`WriteError`skriver ett felmeddelande till diagnostikloggar och skickar felet till diagnostik.

`briefMessage`är ett kort felmeddelande. Det här meddelandet visas i diagnostik och används av produktteamet för felsökning. Ta inte med känslig information och behåll meddelandet mindre än 200 tecken

`detailedMessage`är ett detaljerat felmeddelande som bara läggs till i dina diagnostikloggar i ditt lagringsutrymme. Det här meddelandet bör vara mindre än 2000 tecken.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exempel på deserialiserare

I det här avsnittet visas hur du skriver anpassade deserializers för Protobuf och CSV. Ytterligare exempel, till exempel AVRO-format för Event Hub Capture, finns i [Azure Stream Analytics på GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Format för protokollbuffert (Protobuf)

Det här är ett exempel med hjälp av protokollbuffertformat.

Anta följande definition av protokollbuffert.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

Om `protoc.exe` du kör från **Google.Protobuf.Tools** NuGet genereras en CS-fil med definitionen. Den genererade filen visas inte här.

Följande kodavsnitt är deserializer-implementeringen förutsatt att den genererade filen ingår i projektet. Denna implementering är bara ett tunt omslag över den genererade filen.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Följande kodavsnitt är en enkel CSV-deserializer som också visar spridningsfel.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Serialiseringsformat för REST-API:er

Varje Stream Analytics-indata har ett **serialiseringsformat**. Mer information om inmatningsalternativ finns i dokumentationen [för INgående REST API.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)

Följande Javascript-kod är ett exempel på serialiseringsformatet .NET deserializer när REST API:et ska användas:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`bör vara en klass `StreamDeserializer<T>`som implementerar . Detta beskrivs i följande avsnitt.

## <a name="region-support"></a>Stöd för regionen

Den här funktionen är tillgänglig i följande regioner:

* USA, västra centrala
* Europa, norra
* USA, östra
* USA, västra
* USA, östra 2
* Europa, västra

Du kan [begära stöd](https://aka.ms/ccodereqregion) för ytterligare regioner.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>När är den här funktionen tillgänglig i alla Azure-regioner?

Den här funktionen är tillgänglig i [6 regioner](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Om du är intresserad av att använda den här funktionen i en annan region kan du [skicka en begäran](https://aka.ms/ccodereqregion). Stöd för alla Azure-regioner finns i översikten.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Kan jag komma åt MetadataPropertyValue från mina indata som liknar GetMetadataPropertyValue-funktionen?

Den här funktionen stöds inte. Om du behöver den här funktionen kan du rösta för den här begäran på [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Kan jag dela min deserializer implementering med communityn så att andra kan dra nytta av det?

När du har implementerat din deserializer kan du hjälpa andra genom att dela den med communityn. Skicka in koden till [Azure Stream Analytics GitHub repo](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Efterföljande moment

* [.NET-anpassade deserializers för Azure Stream Analytics-molnjobb](custom-deserializer.md)
