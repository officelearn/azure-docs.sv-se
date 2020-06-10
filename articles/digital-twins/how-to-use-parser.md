---
title: Analysera och validera modeller
titleSuffix: Azure Digital Twins
description: Lär dig hur du använder parsar-biblioteket för att parsa DTDL-modeller.
author: cschormann
ms.author: cschorm
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6346c1d2adca697649ebe879b97766672a23ae29
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613070"
---
# <a name="dtdl-client-side-parser-library"></a>DTDL-bibliotek på klient Sidan

[Modeller](concepts-models.md) i digitala Azure-dubblaer definieras med hjälp av JSON-baserade digitala ensamt definitions språk (DTDL). I de fall där det är användbart att parsa dina modeller, finns ett DTDL-parsningsfel på NuGet.org som ett bibliotek på klient sidan: [Microsoft. Azure. DigitalTwins. parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/).

Det här biblioteket ger modell åtkomst till DTDL-definitionerna, som fungerar som motsvarigheten till C#-reflektion för DTDL. Det här biblioteket kan användas oberoende av [Azures digitala](how-to-use-apis-sdks.md)multimängds SDK, särskilt för DTDL-verifiering i ett visuellt objekt eller en text redigerare. Det är användbart för att se till att dina modell definitions filer är giltiga innan du försöker överföra dem till tjänsten.

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

## <a name="use-the-dtdl-validator-sample"></a>Använda DTDL-verifierings exemplet

Det finns exempel kod som kan validera modell dokument för att kontrol lera att DTDL är giltig. Det bygger på DTDL parser-biblioteket och är språk-oberoende. Hitta detta: [DTDL Verification-exempel](https://github.com/Azure-Samples/DTDL-Validator).

Verifierings exemplet kan användas som kommando rads verktyg för att validera ett katalog träd med DTDL-filer. Det ger också ett interaktivt läge. Käll koden visar exempel för hur du använder parsar-biblioteket.

I mappen för DTDL-verifierings exemplet, se *Readme.MD* -filen för instruktioner om hur du paketerar exemplet i en egen körbar fil.

När du har skapat ett fristående paket och lagt till den körbara filen i din sökväg, kan du köra verifieraren med det här kommandot i en-konsol på din dator:

```cmd/sh
DTDLValidator
```

Med standard alternativen söker exemplet efter `*.json` filer i den aktuella katalogen och alla under kataloger. Du kan också lägga till följande alternativ om du vill att exemplet ska söka i den angivna katalogen och alla under kataloger för filer med fil namns tillägget *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

Du kan lägga till `-i` alternativet för exemplet för att ange interaktivt läge:

```cmd/sh
DTDLValidator -i
```

Mer information om det här exemplet finns i käll koden eller kör `DTDLValidator --help` .

## <a name="use-the-parser-library-in-code"></a>Använda parsar biblioteket i kod

Du kan också använda parsar biblioteket direkt för att validera modeller själv.

För att stödja tolknings kod exemplet nedan, bör du överväga flera modeller som definierats i en Azure Digitals-instans:

> [!TIP] 
> `dtmi:com:contoso:coffeeMaker`Modellen använder syntaxen för *kapacitets modell* , vilket innebär att den har installerats i tjänsten genom att ansluta en PnP-enhet som visar modellen.

```json
{
  "@id": " dtmi:com:contoso:coffeeMaker",
  "@type": "CapabilityModel",
  "implements": [
        { "name": "coffeeMaker", "schema": " dtmi:com:contoso:coffeeMakerInterface" }
  ]    
}
{
  "@id": " dtmi:com:contoso:coffeeMakerInterface",
  "@type": "Interface",
  "contents": [
      { "@type": "Property", "name": "waterTemp", "schema": "double" }  
  ]
}
{
  "@id": " dtmi:com:contoso:coffeeBar",
  "@type": "Interface",
  "contents": [
        { "@type": "relationship", "contains": " dtmi:com:contoso:coffeeMaker" },
        { "@type": "property", "name": "capacity", "schema": "integer" }
  ]    
}
```

Följande kod visar ett exempel på hur du använder parser-biblioteket för att återspegla dessa definitioner i C#:

```csharp
async void ParseDemo(DigitalTwinsClient client)
{
    try
    {
        AsyncPageable<ModelData> mdata = client.GetModelsAsync(null, true);
        List<string> models = new List<string>();
        await foreach (ModelData md in mdata)
            models.Add(md.Model);
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
* [Anvisningar: hantera en dubbel modell](how-to-manage-model.md)