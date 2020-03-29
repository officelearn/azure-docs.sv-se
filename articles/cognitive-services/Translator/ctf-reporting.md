---
title: Ctf-rapportering (Collaborative Translation Framework) - API för översättare
titleSuffix: Azure Cognitive Services
description: Så här använder du CTF-rapportering (Collaborative Translation Framework).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595935"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Så här använder du CTF-rapportering (Collaborative Translation Framework)

> [!NOTE]
> Den här metoden är föråldrad. Den är inte tillgänglig i V3.0 i Translator Text API.
> 
> Collaborative Translations Framework (CTF), som tidigare var tillgängligt för V2.0 i Translator Text API, var föråldrad från och med den 1 februari 2018. Med funktionerna AddTranslation och AddTranslationArray kan användare aktivera korrigeringar via Collaborative Translation Framework. Efter den 31 januari 2018 accepterade dessa två funktioner inte nya meningsinlämningar och användarna får ett felmeddelande. Dessa funktioner har dragits tillbaka och kommer inte att ersättas.

Ctf-rapporterings-API (Collaborative Translation Framework) returnerar statistik och det faktiska innehållet i CTF-arkivet. Det här API:et skiljer sig från metoden GetTranslations() eftersom det:
* Returnerar det översatta innehållet och dess totala antal endast från ditt konto (appId eller Azure Marketplace-konto).
* Returnerar det översatta innehållet och dess totala antal utan att kräva en matchning av källdomen.
* Returnerar inte den automatiska översättningen (maskinöversättning).

## <a name="endpoint"></a>Slutpunkt
Slutpunkten för CTF Reporting API ärhttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metoder
| Namn |    Beskrivning|
|:---|:---|
| Metoden GetUserTranslationCounts | Få antal översättningar som skapas av användaren. |
| Metoden GetUserTranslations | Hämtar de översättningar som skapas av användaren. |

Med de här metoderna kan du:
* Hämta hela uppsättningen användaröversättningar och korrigeringar under ditt konto-ID för nedladdning.
* Hämta listan över de frekventa bidragsgivarna. Kontrollera att rätt användarnamn finns i AddTranslation().
* Skapa ett användargränssnitt som gör att betrodda användare kan se alla tillgängliga kandidater, om det behövs begränsat till en del av webbplatsen, baserat på URI-prefixet.

> [!NOTE]
> Båda metoderna är relativt långsamma och dyra. Det rekommenderas att använda dem sparsamt.

## <a name="getusertranslationcounts-method"></a>Metoden GetUserTranslationCounts

Den här metoden får antalet översättningar som skapas av användaren. Den innehåller en lista över översättningsantal grupperade efter parametrarna uriPrefix, från, till användare, minRating och maxRating.It provides the list of translation counts grouped by the uriPrefix, from, to, user, minRating, and maxRating request parameters.

**Syntax**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** Om auktoriseringshuvudet används lämnar du appid-fältet tomt annars anger du en sträng som innehåller "Bärare" + " + åtkomsttoken.|
| uriPrefix | **Valfritt** En sträng som innehåller prefix för URI för översättningen.|
| Från | **Valfritt** En sträng som representerar översättningstextens språkkod. |
| till | **Valfritt** En sträng som representerar språkkoden att översätta texten till.|
| minRating (minRating)| **Valfritt** Ett heltalsvärde som representerar den lägsta kvalitetsklassificeringen för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| maxRating (maxRating)| **Valfritt** Ett heltalsvärde som representerar den maximala kvalitetsklassificeringen för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| användare | **Valfritt** En sträng som används för att filtrera resultatet baserat på avsändaren av överföringen. |
| category| **Valfritt** En sträng som innehåller översättningens kategori eller domän. Den här parametern stöder endast standardalternativet allmänt.|
| minDateUtc| **Valfritt** Datumet från det att du vill hämta översättningarna. Datumet måste vara i UTC-format. |
| maxDateUtc| **Valfritt** Datumet till när du vill hämta översättningarna. Datumet måste vara i UTC-format. |
| hoppa över| **Valfritt** Antalet resultat som du vill hoppa över på en sida. Om du till exempel vill hoppa över de första 20 raderna i resultatet och visa från den 21:a resultatposten anger du 20 för den här parametern. Standardvärdet för den här parametern är 0.|
| Ta | **Valfritt** Antalet resultat som du vill hämta. Det maximala antalet för varje begäran är 100. Standardvärdet är 100.|

> [!NOTE]
> Parametrarna hoppa över och ta begäran aktiverar sidnumrering för ett stort antal resultatposter.

**Returvärde**

Resultatuppsättningen innehåller matrisen för **UserTranslationCount**. Varje UserTranslationCount har följande element:

| Field | Beskrivning |
|:---|:---|
| Antal| Antalet resultat som hämtas|
| Från | Källspråket|
| Omdöme| Den klassificering som används av inlämnaren i metodanropet AddTranslation()|
| Till| Målspråket|
| Uri| Den URI som tillämpas i metodanropet AddTranslation()|
| Användare| Användarnamnet|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern '**maxDateUtc**' måste vara större än eller lika med '**minDateUtc**'.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP är över kvoten.| <ul><li>Gränsen för antalet begäranden per minut nås.</li><li>Begäran storlek förblir begränsad på 10000 tecken.</li><li>En timme och en daglig kvot begränsar antalet tecken som Microsoft Translator API accepterar.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID överskred tim- eller dagskvoten.|

> [!NOTE]
> Kvoten kommer att anpassas för att säkerställa rättvisa bland alla användare av tjänsten.

**Visa kodexempel på GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [Php](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Metoden GetUserTranslations

Den här metoden hämtar de översättningar som skapas av användaren. Den innehåller översättningar grupperade av parametrarna uriPrefix, från, till användare och minRating och maxRating.

**Syntax**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** Om auktoriseringshuvudet används lämnar du appid-fältet tomt annars anger du en sträng som innehåller "Bärare" + " + åtkomsttoken.|
| uriPrefix| **Valfritt** En sträng som innehåller prefix för URI för översättningen.|
| Från| **Valfritt** En sträng som representerar översättningstextens språkkod.|
| till| **Valfritt** En sträng som representerar språkkoden att översätta texten till.|
| minRating (minRating)| **Valfritt** Ett heltalsvärde som representerar den lägsta kvalitetsklassificeringen för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| maxRating (maxRating)| **Valfritt** Ett heltalsvärde som representerar den maximala kvalitetsklassificeringen för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| användare| **Valfri. En sträng som används för att filtrera resultatet baserat på upphovsmannen till inlämningen**|
| category| **Valfritt** En sträng som innehåller översättningens kategori eller domän. Den här parametern stöder endast standardalternativet allmänt.|
| minDateUtc| **Valfritt** Datumet från det att du vill hämta översättningarna. Datumet måste vara i UTC-format.|
| maxDateUtc| **Valfritt** Datumet till när du vill hämta översättningarna. Datumet måste vara i UTC-format.|
| hoppa över| **Valfritt** Antalet resultat som du vill hoppa över på en sida. Om du till exempel vill hoppa över de första 20 raderna i resultatet och visa från den 21:a resultatposten anger du 20 för den här parametern. Standardvärdet för den här parametern är 0.|
| Ta| **Valfritt** Antalet resultat som du vill hämta. Det maximala antalet för varje begäran är 100. Standardvärdet är 50.|

> [!NOTE]
> Parametrarna hoppa över och ta begäran aktiverar sidnumrering för ett stort antal resultatposter.

**Returvärde**

Resultatuppsättningen innehåller matrisen för **UserTranslation**. Varje UserTranslation har följande element:

| Field | Beskrivning |
|:---|:---|
| CreatedDateUtc| Inrättandet av transaktionen med AddTranslation()|
| Från| Källspråket|
| OriginalText| Källspråkstexten som används när begäran skickas|
|Omdöme |Den klassificering som används av inlämnaren i metodanropet AddTranslation()|
|Till|    Målspråket|
|Översatttext|    Översättningen som skickas in i metoden AddTranslation()|
|Uri|   Den URI som tillämpas i metodanropet AddTranslation()|
|Användare   |Användarnamnet|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern '**maxDateUtc**' måste vara större än eller lika med '**minDateUtc**'.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP är över kvoten.| <ul><li>Gränsen för antalet begäranden per minut nås.</li><li>Begäran storlek förblir begränsad på 10000 tecken.</li><li>En timme och en daglig kvot begränsar antalet tecken som Microsoft Translator API accepterar.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID överskred tim- eller dagskvoten.|

> [!NOTE]
> Kvoten kommer att anpassas för att säkerställa rättvisa bland alla användare av tjänsten.

**Visa kodexempel på GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [Php](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
