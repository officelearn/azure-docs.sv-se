---
title: Förstå den digitala modellen med dubbla modeller | Microsoft Docs
description: Som utvecklare får du lära dig hur du använder DTDL-parsern för att validera modeller
author: rido-min
ms.author: rmpablos
ms.date: 04/29/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: peterpr
ms.openlocfilehash: 20c4452a32c791f33e08c883d8cec89a345ab188
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352432"
---
# <a name="understand-the-digital-twins-model-parser"></a>Förstå Digitals modell tolkare för dubbla modeller

Det digitala endefinierade definitions språket (DTDL) beskrivs i [DTDL-specifikationen](https://github.com/Azure/opendigitaltwins-dtdl). Användare kan använda ett _digitalt_ NuGet-paket för att validera och fråga en modell som definierats i flera filer.

## <a name="install-the-dtdl-model-parser"></a>Installera DTDL modell parser

Parsern är tillgänglig i NuGet.org med ID: [Microsoft. Azure. DigitalTwins. parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Om du vill installera parsern använder du en kompatibel NuGet-paket hanterare, till exempel en i Visual Studio eller i `dotnet` cli.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

## <a name="use-the-parser-to-validate-a-model"></a>Använd parsern för att validera en modell

Modellen som du vill validera kan bestå av ett eller flera gränssnitt som beskrivs i JSON-filer. Du kan använda parsern för att läsa in alla filer i en specifik mapp och använda parsern för att validera alla filer som helhet, inklusive alla referenser mellan filerna:

1. Skapa en `IEnumerable<string>` med en lista över alla modell innehåll:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instansiera `ModelParser` och anropa `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Sök efter verifierings fel. Om parsern hittar eventuella fel, genererar den en `AggregateException` lista med detaljerade fel meddelanden:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (AggregateException ae)
    {
        foreach (var e in ae.InnerExceptions)
        {
            Console.WriteLine(e.Message);
        }
    }
    ```

1. Granska `Model` . Om verifieringen lyckas kan du använda modellens parser-API för att kontrol lera modellen. Följande kodfragment visar hur du itererar över alla modeller som har parsats och visar de befintliga egenskaperna:

    ```csharp
    foreach (var m in parseResult)
    {
        Console.WriteLine(m.Key);
        foreach (var item in m.Value.AsEnumerable<DTEntityInfo>())
        {
            var p = item as DTInterfaceInfo;
            if (p!=null)
            {
                Console.WriteLine($"\t{p.Id}");
                Console.WriteLine($"\t{p.Description.FirstOrDefault()}");
            }
            Console.WriteLine("--------------");
        }
    }
    ```

## <a name="next-steps"></a>Nästa steg

API: t för modell tolkare som granskas i den här artikeln möjliggör många scenarier för att automatisera eller validera uppgifter som är beroende av DTDL-modeller. Du kan till exempel dynamiskt bygga ett gränssnitt från informationen i modellen.
