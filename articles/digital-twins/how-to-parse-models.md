---
title: Parsa och verifiera modeller
titleSuffix: Azure Digital Twins
description: Lär dig hur du använder parsar-biblioteket för att parsa DTDL-modeller.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2e13762698efd8d5df42ab6315c990d4096168cf
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145539"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Parsa och validera modeller med DTDL parser-biblioteket

[Modeller](concepts-models.md) i digitala Azure-dubbla grupper definieras med hjälp av DTDL (JSON-LD-baserade) digitala dubbla definitions språk. **Vi rekommenderar att du validerar dina modeller offline innan du laddar upp dem till din Azure Digital-instansen.**

För att du ska kunna göra detta finns ett .NET-bibliotek för DTDL-parsning på NuGet: [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

Du kan använda parser-biblioteket direkt i C#-koden eller använda det oberoende kod exempel projekt som bygger på avsnittet parser-bibliotek: [**DTDL-verifiering**](/samples/azure-samples/dtdl-validator/dtdl-validator).

## <a name="use-the-dtdl-validator-sample"></a>Använda DTDL-verifierings exemplet

[**DTDL-verifieraren**](/samples/azure-samples/dtdl-validator/dtdl-validator) är ett exempel projekt som kan validera modell dokument för att kontrol lera att DTDL är giltigt. Det bygger på .NET parser-biblioteket och är språk-oberoende. Du kan hämta den med hjälp av *hämtnings zip* -knappen i exempel länken.

Käll koden visar exempel för hur du använder parsar-biblioteket. Du kan använda validator-exemplet som ett kommando rads verktyg för att validera ett katalog träd med DTDL-filer. Det ger också ett interaktivt läge.

I mappen för DTDL-verifierings exemplet, se *Readme.MD* -filen för instruktioner om hur du paketerar exemplet i en egen körbar fil.

När du har skapat ett fristående paket och lagt till den körbara filen i din sökväg, kan du köra verifieraren med det här kommandot i en-konsol på din dator:

```cmd/sh
DTDLValidator
```

Med standard alternativen söker exemplet efter `*.json` filer i den aktuella katalogen och alla under kataloger. Du kan också lägga till följande alternativ om du vill att exemplet ska söka i den angivna katalogen och alla under kataloger för filer med fil namns tillägget *. dtdl* :

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Du kan lägga till `-i` alternativet för exemplet för att ange interaktivt läge:

```cmd/sh
DTDLValidator -i
```

Mer information om det här exemplet finns i käll koden eller kör `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>Använd .NET parser-biblioteket 

Biblioteket [**Microsoft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) ger modell åtkomst till DTDL-definitionerna, som fungerar som motsvarigheten till C#-reflektion för DTDL. Det här biblioteket kan användas oberoende av [Azures digitala](how-to-use-apis-sdks.md)multimängds SDK, särskilt för DTDL-verifiering i ett visuellt objekt eller en text redigerare. Det är användbart för att se till att dina modell definitions filer är giltiga innan du försöker överföra dem till tjänsten.

Om du vill använda parsar biblioteket anger du det med en uppsättning DTDL-dokument. Normalt skulle du kunna hämta dessa modell dokument från tjänsten, men du kan också ha dem tillgängliga lokalt, om klienten var ansvarig för att ladda upp dem till tjänsten på första platsen. 

Här är det allmänna arbets flödet för att använda parsern:
1. Hämta några eller alla DTDL-dokument från tjänsten.
2. Skicka de returnerade DTDL-dokumenten i minnet till parsern.
3. Parsern validerar uppsättningen av dokument som skickas till den och returnerar detaljerad fel information. Den här möjligheten är användbar i redigerings scenarier.
4. Använd parsa API: er för att fortsätta analysera modellerna som ingår i dokument uppsättningen. 

Funktionerna i parsern är:
* Hämta alla implementerade modell gränssnitt (innehållet i gränssnittets `extends` avsnitt).
* Hämta alla egenskaper, telemetri, kommandon, komponenter och relationer som har deklarerats i modellen. Det här kommandot hämtar också alla metadata som ingår i dessa definitioner och använder arv ( `extends` avsnitt) i kontot.
* Hämta alla komplexa modell definitioner.
* Avgör om en modell kan tilldelas från en annan modell.

> [!NOTE]
> I [IoT plug and Play-enheter (PNP)](../iot-pnp/overview-iot-plug-and-play.md) används en liten syntax som beskriver deras funktioner. Den här syntaxen är en semantiskt kompatibel delmängd av DTDL som används i Azure Digital-dubbla. När du använder parser-biblioteket behöver du inte veta vilken syntax variant som användes för att skapa DTDL för din digitala dubbla. Parsern kommer alltid att returnera samma modell för både PnP-och Azures digitala dubbla syntax.

### <a name="code-with-the-parser-library"></a>Koda med parsar biblioteket

Du kan använda parser-biblioteket direkt, för saker som att validera modeller i ditt eget program eller för att generera dynamiskt, modell drivna gränssnitt, instrument paneler och rapporter.

För att stödja tolknings kod exemplet nedan, bör du överväga flera modeller som definierats i en Azure Digitals-instans:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeMaker;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Component",
        "name": "coffeeMaker",
        "schema": "dtmi:com:contoso:coffeeMakerInterface;1"
      }
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeMakerInterface;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "waterTemp",
        "schema": "double"
      }
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:contoso:coffeeBar;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Relationship",
        "name": "foo",
        "target": "dtmi:com:contoso:coffeeMaker;1"
      },
      {
        "@type": "Property",
        "name": "capacity",
        "schema": "integer"
      }
    ]
  }
]
```

Följande kod visar ett exempel på hur du använder parser-biblioteket för att återspegla dessa definitioner i C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<DigitalTwinsModelData> mdata = client.GetModelsAsync(new GetModelsOptions { IncludeModelDefinition = true });
        List<string> models = new List<string>();
        await foreach (DigitalTwinsModelData md in mdata)
            models.Add(md.DtdlModel);
        ModelParser parser = new ModelParser();
        IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM = await parser.ParseAsync(models);

        List<DTInterfaceInfo> interfaces = new List<DTInterfaceInfo>();
        IEnumerable<DTInterfaceInfo> ifenum = 
            from entity in dtdlOM.Values
            where entity.EntityKind == DTEntityKind.Interface
            select entity as DTInterfaceInfo;
        interfaces.AddRange(ifenum);
        foreach (DTInterfaceInfo dtif in interfaces)
        {
            PrintInterfaceContent(dtif, dtdlOM);
        }

    } catch (RequestFailedException rex)
    {

    }
}

void PrintInterfaceContent(DTInterfaceInfo dtif, IReadOnlyDictionary<Dtmi, DTEntityInfo> dtdlOM, int indent=0)
{
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < indent; i++) sb.Append("  ");
    Console.WriteLine($"{sb}Interface: {dtif.Id} | {dtif.DisplayName}");
    SortedDictionary<string, DTContentInfo> contents = dtif.Contents;
    foreach (DTContentInfo item in contents.Values)
    {
        switch (item.EntityKind)
        {
            case DTEntityKind.Property:
                DTPropertyInfo pi = item as DTPropertyInfo;
                Console.WriteLine($"{sb}--Property: {pi.Name} with schema {pi.Schema}");
                break;
            case DTEntityKind.Relationship:
                DTRelationshipInfo ri = item as DTRelationshipInfo;
                Console.WriteLine($"{sb}--Relationship: {ri.Name} with target {ri.Target}");
                break;
            case DTEntityKind.Telemetry:
                DTTelemetryInfo ti = item as DTTelemetryInfo;
                Console.WriteLine($"{sb}--Telemetry: {ti.Name} with schema {ti.Schema}");
                break;
            case DTEntityKind.Component:
                DTComponentInfo ci = item as DTComponentInfo;
                Console.WriteLine($"{sb}--Component: {ci.Id} | {ci.Name}");
                dtdlOM.TryGetValue(ci.Id, out DTEntityInfo value);
                DTInterfaceInfo component = value as DTInterfaceInfo;
                PrintInterfaceContent(component, dtdlOM, indent + 1);
                break;
            default:
                break;
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

När du är klar med att skriva dina modeller kan du läsa så här laddar du upp dem (och utför andra hanterings åtgärder) med DigitalTwinsModels-API: erna:
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)