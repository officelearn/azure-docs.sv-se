---
title: Uppgradera till Azure Search .NET SDK version 10 – Azure Search
description: Migrera kod till Azure Search .NET SDK version 10 från äldre versioner. Läs om vad som är nytt och vilka kod ändringar som krävs.
author: arv100kri
manager: nitinme
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: arjagann
ms.custom: seodec2018
ms.openlocfilehash: e4633a1c0543331b0ea9820703ed685fb99f2130
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182377"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-10"></a>Uppgradera till Azure Search .NET SDK version 10

Om du använder version 9,0 eller äldre av [Azure Search .NET SDK](https://aka.ms/search-sdk)hjälper den här artikeln dig att uppgradera ditt program till att använda version 10.

En mer allmän genom gång av SDK inklusive exempel finns i [så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Version 10 lägger till flera funktioner och fel korrigeringar, vilket ger samma funktions nivå som den senaste versionen av REST API versionen `2019-05-06`. I de fall där en ändring bryter befintlig kod vägleder vi dig genom de [steg som krävs för att lösa problemet](#UpgradeSteps).

> [!NOTE]
> Om du använder version 8,0-Preview eller äldre bör du först uppgradera till version 9 och sedan uppgradera till version 10. Instruktioner finns i [Uppgradera till Azure Search .NET SDK version 9](search-dotnet-sdk-migration-version-9.md) .
>
> Din Azure Search tjänst instans stöder flera REST API versioner, inklusive den senaste. Du kan fortsätta att använda en version när den inte längre är den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST API måste du ange API-versionen i varje begäran via parametern API-version. När du använder .NET SDK fastställer den version av SDK som du använder motsvarande version av REST API. Om du använder en äldre SDK kan du fortsätta att köra koden utan ändringar även om tjänsten uppgraderas till att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Vad är nytt i version 10
Version 10 av Azure Search .NET SDK riktar sig till den senaste allmänt tillgängliga versionen av Azure Search REST API`2019-05-06`() med dessa uppdateringar:

* Introduktion av två nya kompetenser [](cognitive-search-skill-conditional.md) för villkorliga färdigheter och [text översättning](cognitive-search-skill-text-translation.md).
* [Formaren kompetens](cognitive-search-skill-shaper.md) indata har omstruktureras för att hantera konsolidering från kapslade kontexter. Mer information finns i den här [exempel-JSON-definitionen](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Tillägg av två nya [fält mappnings funktioner](search-indexer-field-mappings.md):
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Vid vissa tillfällen kan fel och varningar som visas i [körnings status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) för indexerare ha ytterligare information som hjälp vid fel sökning. `IndexerExecutionResult`har uppdaterats för att återspegla det här beteendet.
* Enskilda kunskaper som definieras inom en [färdigheter](cognitive-search-defining-skillset.md) kan alternativt identifieras genom att ange en `name` egenskap.
* `ServiceLimits`visar gränser för [komplexa typer](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) och `IndexerExecutionInfo` visar relevanta indexerare gränser/kvoter.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Steg för att uppgradera

1. Uppdatera din NuGet-referens `Microsoft.Azure.Search` för att använda antingen NuGet Package Manager-konsolen eller genom att högerklicka på dina projekt referenser och välja "hantera NuGet-paket..." i Visual Studio.

2. När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet. 

3. Om det inte går att bygga upp måste du åtgärda varje build-fel. Mer information om hur du löser de olika möjliga build-problemen finns i avsnittet om att lösa [ändringar i version 10](#ListOfChanges) .

4. När du har åtgärdat eventuella build-fel eller varningar kan du göra ändringar i programmet för att dra nytta av nya funktioner om du vill. Nya funktioner i SDK beskrivs i [Vad är nytt i version 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Bryta ändringar i version 10

Det finns flera större ändringar i version 10 som kan kräva kod ändringar förutom att återskapa ditt program.

> [!NOTE]
> Listan över ändringar nedan är inte fullständig. Vissa ändringar kommer troligen inte att resultera i build-fel, men är tekniskt avbrotts kraft eftersom de bryter mot binär kompatibilitet med sammansättningar som är beroende av tidigare versioner av Azure Search .NET SDK-sammansättningar. Viktiga ändringar som faller under den här kategorin visas också tillsammans med rekommendationer. Återskapa ditt program när du uppgraderar till version 10 för att undvika eventuella binära kompatibilitetsproblem.

### <a name="custom-web-api-skill-definition"></a>Kunskaps definition för anpassad webb-API

Definitionen av den [anpassade webb-API-kompetensen](cognitive-search-custom-skill-web-api.md) angavs felaktigt i version 9 och äldre. 

Modellen för `WebApiSkill` angiven `HttpHeaders` som en objekt egenskap som _innehåller_ en ord lista. Om du skapar en färdigheter `WebApiSkill` med en konstruerad på det här sättet kan det leda till ett undantag eftersom REST API skulle anse att frågan är felaktigt utformad. Det här problemet har åtgärd ATS genom att `HttpHeaders` göra **en dictionary-egenskap** på den `WebApiSkill` översta nivån i själva modellen, som betraktas som en giltig begäran från REST API.

Till exempel, om du tidigare försökte instansiera a `WebApiSkill` enligt följande:

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

ändra det till följande för att undvika validerings felet från REST API:

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

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Formaren-kunskaper tillåter kapslad kontext konsolidering

Formaren-kunskaper kan nu tillåta inkapsling av inkapslade kontexter. För att aktivera den här ändringen har `InputFieldMappingEntry` vi ändrat så att den kan instansieras genom att bara `Source` ange en `SourceContext` egenskap eller både egenskaperna `Inputs` och.

Du behöver förmodligen inte göra några kod ändringar. Observera dock att endast en av dessa två kombinationer är tillåtna. Det innebär:

- Att skapa `InputFieldMappingEntry` en där `Source` endast initieras är giltig.
- Att skapa `InputFieldMappingEntry` en `SourceContext` där och `Inputs` är initierad är giltig.
- Alla andra kombinationer som inbegriper dessa tre egenskaper är ogiltiga.

Om du vill börja använda den här nya funktionen måste du kontrol lera att alla klienter har uppdaterats för att först använda version 10 innan du utför den ändringen. I annat fall finns det en möjlighet att en uppdatering av en klient (med en äldre version av SDK) till formaren-kompetensen kan resultera i verifierings fel.

> [!NOTE]
> Även om den underliggande `InputFieldMappingEntry` modellen har ändrats för att tillåta konsolidering från kapslade kontexter, är användningen endast giltig inom definitionen av en formaren-färdighet. Om du använder den här funktionen i andra färdigheter, medan den är giltig vid kompileringen, leder det till ett verifierings fel vid körning.

## <a name="skills-can-be-identified-by-a-name"></a>Kunskaper kan identifieras med ett namn

Varje färdighet i en färdigheter har nu en ny egenskap `Name`som kan initieras i din kod för att hjälpa till att identifiera kunskapen. Detta är valfritt – om inget anges (vilket är standard, om ingen explicit kod ändring gjorts), tilldelas den ett standard namn med det 1-baserade indexet för kunskapen i färdigheter, med prefixet #. Till exempel i följande färdigheter-definition (de flesta initierarna hoppades över för det kortfattat):

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

`SentimentSkill`tilldelas `#1`ett namn, `#2` `ShaperSkill` tilldelas, tilldelas`#3` , och så vidare. `WebApiSkill`

Om du väljer att identifiera färdigheter med ett anpassat namn, måste du först uppdatera alla instanser av dina klienter till version 10 av SDK. I annat fall finns det en möjlighet att en klient som använder en äldre version av SDK `null` : n `Name` kan hamna i en kunskaps egenskap, vilket gör att klienten kan återgå till standard namngivnings schemat.

## <a name="details-about-errors-and-warnings"></a>Information om fel och varningar

`ItemError`och `ItemWarning` modeller som kapslar in information om fel och varningar som inträffar under en indexerare-körning har ändrats för att inkludera tre nya egenskaper med målet att hjälpa till med fel sökning av indexeraren. Dessa egenskaper är:

- `Name`: Namnet på den källa som felet kom från. Det kan till exempel referera till en viss färdighet i den bifogade färdigheter.
- `Details`: Ytterligare utförlig information om felet eller varningen.
- `DocumentationLink`: En länk till en fel söknings guide för det aktuella felet eller varningen.

> [!NOTE]
> Vi har börjat strukturera våra fel och varningar för att inkludera dessa användbara uppgifter närhelst det är möjligt. Vi arbetar för att se till att alla fel och varningar är tillgängliga, men det är ett pågående arbete och ytterligare information kanske inte alltid fylls i.

## <a name="next-steps"></a>Nästa steg

- Ändringar i formaren-kompetensen har den mest potentiella påverkan på ny eller befintlig kod. Nästa steg är att gå tillbaka i det här exemplet som illustrerar ingångs strukturen: [Exempel på formaren skicklighets-JSON-definition](cognitive-search-skill-shaper.md)
- Gå igenom [introduktionen till kognitiv Sök guide](cognitive-search-concept-intro.md).
- Vi välkomnar din feedback om SDK. Om du stöter på problem kan du be oss om hjälp om [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Om du hittar en bugg kan du ange ett problem i [Azure .NET SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues)-lagringsplatsen. Var noga med att ange din ärende rubrik med "[Azure Search]".

