---
title: Uppgradera till Azure Cognitive Search .NET SDK version 10
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Cognitive Search .NET SDK version 10 från äldre versioner. Läs om vad som är nytt och vilka kodändringar som krävs.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847513"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Uppgradera till Azure Cognitive Search .NET SDK version 10

Om du använder version 9.0 eller äldre av [Azure Search .NET SDK](https://aka.ms/search-sdk)hjälper den här artikeln dig att uppgradera programmet så att det använder version 10.

Azure Search har bytt namn till Azure Cognitive Search i version 10, men namnområden och paketnamn är oförändrade. Tidigare versioner av SDK (9.0 och tidigare) fortsätter att använda det tidigare namnet. Mer information om hur du använder SDK, inklusive exempel, finns i [Så här använder du Azure Cognitive Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 10 lägger till flera funktioner och buggfixar, vilket för den till `2019-05-06`samma funktionella nivå som den senaste utgåvan av REST API-versionen . I de fall en ändring bryter befintlig kod går vi igenom de [steg som krävs för att lösa problemet](#UpgradeSteps).

> [!NOTE]
> Om du använder version 8.0-förhandsversion eller äldre bör du först uppgradera till version 9 och sedan uppgradera till version 10. Instruktioner [finns i Uppgradera till Azure Search .NET SDK version 9.](search-dotnet-sdk-migration-version-9.md)
>
> Söktjänstinstansen stöder flera REST API-versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar koden för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK avgör den version av SDK du använder motsvarande version av REST API.When using the .NET SDK, the version of the SDK you're using determines the corresponding version of the REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Nyheter i version 10
Version 10 av Azure Cognitive Search .NET SDK riktar sig`2019-05-06`till den senaste allmänt tillgängliga versionen av REST API ( ) med dessa uppdateringar:

* Introduktion av två nya färdigheter - [Villkorlig färdighet](cognitive-search-skill-conditional.md) och [textöversättning skicklighet](cognitive-search-skill-text-translation.md).
* [Shaper-färdighetsindata](cognitive-search-skill-shaper.md) har omstrukturerats för att hantera konsolidering från kapslade kontexter. Mer information finns i det här [exemplet JSON definition](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Tillägg av två nya [fältmappningsfunktioner:](search-indexer-field-mappings.md)
    - [urlEncode (urlEncode)](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode (urlDecode)](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Vid vissa tillfällen kan fel och varningar som visas i [indexeringskörningsstatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) ha ytterligare information som hjälper till att felsöka. `IndexerExecutionResult`har uppdaterats för att återspegla detta beteende.
* Individuella färdigheter som definieras i en [kompetens](cognitive-search-defining-skillset.md) kan `name` eventuellt identifieras genom att ange en egenskap.
* `ServiceLimits`visar gränser för `IndexerExecutionInfo` komplexa [typer](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) och visar relevanta indexeringsgränser/kvoter.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera

1. Uppdatera din NuGet-referens för `Microsoft.Azure.Search` att använda antingen NuGet Package Manager Console eller genom att högerklicka på dina projektreferenser och välja "Hantera NuGet-paket..." i Visual Studio.

2. När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. 

3. Om din version misslyckas måste du åtgärda varje byggfel. Mer information om hur du löser varje potentiellt byggfel [finns i Bryta ändringar i version 10.](#ListOfChanges)

4. När du har åtgärdat eventuella byggfel eller varningar kan du göra ändringar i ditt program för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK [beskrivs i Nyheter i version 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Bryta ändringar i version 10

Det finns flera brytande ändringar i version 10 som kan kräva kodändringar förutom att återskapa ditt program.

> [!NOTE]
> Listan över ändringar nedan är inte uttömmande. Vissa ändringar kommer sannolikt inte att resultera i byggfel, men bryter tekniskt eftersom de bryter binär kompatibilitet med sammansättningar som är beroende av tidigare versioner av Azure Cognitive Search .NET SDK-sammansättningar. Betydande förändringar som faller under denna kategori listas också tillsammans med rekommendationer. Återskapa ditt program när du uppgraderar till version 10 för att undvika binära kompatibilitetsproblem.

### <a name="custom-web-api-skill-definition"></a>Anpassad färdighetsdefinition för webb-API

Definitionen av [custom web API-färdigheten](cognitive-search-custom-skill-web-api.md) angavs felaktigt i version 9 och äldre. 

Modellen för `WebApiSkill` angiven `HttpHeaders` som en objektegenskap som _innehåller_ en ordlista. Skapa en kompetens `WebApiSkill` med en konstruerad på detta sätt skulle resultera i ett undantag eftersom REST API skulle anse begäran dåligt bildas. Det här problemet har korrigerats genom att `HttpHeaders` göra `WebApiSkill` en **toppnivåordlistagenskap** på själva modellen - vilket anses vara en giltig begäran från REST API.

Om du till exempel tidigare har försökt `WebApiSkill` instansiera en så här:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

ändra den till följande för att undvika valideringsfelet från REST API:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Med Shaper-färdighet kan kapslad kontextkonsolidering

Shaper-färdighet kan nu tillåta indatakonsolidering från kapslade kontexter. För att aktivera den `InputFieldMappingEntry` här ändringen ändrade vi så att `Source` den kan instansieras genom att bara ange en egenskap, eller både `SourceContext` och `Inputs` egenskaper.

Du behöver troligen inte göra några kodändringar. Observera dock att endast en av dessa två kombinationer är tillåten. Detta innebär:

- Det `InputFieldMappingEntry` är `Source` giltigt att skapa en plats där endast är initierat.
- Det `InputFieldMappingEntry` är `SourceContext` giltigt `Inputs` att skapa en plats och initieras.
- Alla andra kombinationer som involverar dessa tre egenskaper är ogiltiga.

Om du bestämmer dig för att börja använda den här nya funktionen kontrollerar du att alla dina klienter uppdateras för att använda version 10 först, innan du distribuerar ändringen. Annars finns det en möjlighet att en uppdatering av en klient (med hjälp av en äldre version av SDK) till Shaper-färdigheten kan resultera i valideringsfel.

> [!NOTE]
> Även om den `InputFieldMappingEntry` underliggande modellen har ändrats för att tillåta konsolidering från kapslade kontexter, är dess användning endast giltig inom definitionen av en Shaper-färdighet. Om du använder den här funktionen i andra kunskaper, medan den är giltig vid kompilering, resulterar det i ett valideringsfel vid körning.

## <a name="skills-can-be-identified-by-a-name"></a>Färdigheter kan identifieras med ett namn

Varje färdighet inom en kompetens `Name`har nu en ny egenskap , som kan initieras i din kod för att identifiera färdigheten. Detta är valfritt - när ospecificerat (vilket är standard, om ingen explicit kodändring gjordes), tilldelas det ett standardnamn med hjälp av 1-baserat index för färdigheten i färdigheter, föregås med "#" tecken. I följande skillset-definition (de flesta initieringar hoppas över för korthet):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`tilldelas ett namn `#1` `WebApiSkill` , tilldelas `#2` `ShaperSkill` , tilldelas `#3` och så vidare.

Om du väljer att identifiera kunskaper med ett eget namn, se till att uppdatera alla instanser av dina klienter till version 10 av SDK först. Annars finns det en möjlighet att en klient som använder `null` en `Name` äldre version av SDK kan ut egenskapen för en färdighet, vilket gör att klienten att falla tillbaka på standardnamngivningsschemat.

## <a name="details-about-errors-and-warnings"></a>Information om fel och varningar

`ItemError`och `ItemWarning` modeller som kapslar in information om fel och varningar (respektive) som uppstår under en indexeringskörning har ändrats för att inkludera tre nya egenskaper med målet att hjälpa till att felsöka indexeraren. Dessa egenskaper är:

- `Name`: Namnet på den källa vid vilken felet uppstod. Det kan till exempel hänvisa till en viss färdighet i den bifogade kompetensen.
- `Details`: Ytterligare utförlig information om felet eller varningen.
- `DocumentationLink`: En länk till en felsökningsguide för det specifika felet eller varningen.

> [!NOTE]
> Vi har börjat strukturera våra fel och varningar så att de innehåller dessa användbara detaljer när det är möjligt. Vi arbetar för att se till att dessa detaljer finns för alla fel och varningar, men det är ett pågående arbete och dessa ytterligare detaljer kanske inte alltid befolkas.

## <a name="next-steps"></a>Nästa steg

- Ändringar i Shaper-färdigheten har störst potentiell inverkan på ny eller befintlig kod. Som ett nästa steg, se till att se över det här exemplet som illustrerar indatastrukturen: [Shaper-färdighet JSON definition exempel](cognitive-search-skill-shaper.md)
- Gå igenom [AI-anrikningsöversikten](cognitive-search-concept-intro.md).
- Vi välkomnar din feedback på SDK. Om du stöter på problem, gärna be oss om hjälp på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar ett fel kan du lämna in ett problem i [Azure .NET SDK GitHub-databasen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixa din problemtitel med "[Azure Cognitive Search]".

