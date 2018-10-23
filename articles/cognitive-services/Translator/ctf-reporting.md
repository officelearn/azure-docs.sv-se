---
title: Collaborative Translation Framework (CTF) Reporting - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Så här använder Collaborative Translation Framework (CTF) reporting.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: aa59ce89bf8c2c4b31d85c572dcdfb3645f06884
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646021"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Så här använder du CTF-rapportering (Collaborative Translation Framework)

> [!NOTE]
> Den här metoden är inaktuell. Det är inte tillgängliga i version 3.0 av Translator Text API.

> Den samverkande översättningar Framework (CTF), tidigare var tillgängliga för V2.0 av Translator Text API är inaktuell från och med 1 februari 2018. Funktionerna AddTranslation och AddTranslationArray låter användare aktivera korrigeringar via Collaborative Translation Framework. Dessa två funktioner godkände inte nya mening bidrag efter 31 januari 2018, och användarna får ett felmeddelande. Dessa funktioner har dras tillbaka och ersätts inte.

>Liknande funktionalitet är tillgänglig i Translator Hub API, så att du kan skapa en anpassad översättningssystemet med terminologi och stil, och du kan anropa den med hjälp av kategori-ID i Translator Text API. Translator-hubb: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). Translator hubb API: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

Collaborative Translation Framework (CTF) Reporting API: et returnerar statistik och det faktiska innehållet i arkivet CTF. Detta API skiljer sig från metoden GetTranslations() eftersom den:
* Returnerar det översatta innehållet och det totala antalet från ditt konto (appId eller Azure Marketplace-konto).
* Returnerar det översatta innehållet och det totala antalet utan en matchning av källa meningen.
* Returnerar inte automatisk översättning (maskinöversättning).

## <a name="endpoint"></a>Slutpunkt
Slutpunkten för CTF Reporting API: et är http://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metoder
| Namn |    Beskrivning|
|:---|:---|
| GetUserTranslationCounts metod | Hämta antalet översättningar som har skapats av användaren. |
| GetUserTranslations metod | Hämtar översättningar som har skapats av användaren. |

Dessa metoder kan du:
* Hämta den fullständiga uppsättningen användare översättningar och ändringar under ditt konto-ID för hämtning.
* Hämta lista över vanliga deltagare. Se till att rätt användarnamn har angetts i AddTranslation().
* Skapa ett användargränssnitt (UI) som gör att dina betrodda användare att se alla tillgängliga kandidater, om det behövs begränsad till en del av din webbplats, baserat på URI-prefix.

> [!NOTE]
> Båda metoderna är relativt långsamt och dyrt. Det rekommenderas att använda dem sparsamt.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts metod

Den här metoden hämtar antalet översättningar som har skapats av användaren. Den innehåller en lista över translation antal grupperade efter uriPrefix, från, till användare, minRating och parametrarna som maxRating.

**Syntax**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslationCount[]GetUserTranslationCounts(
           string appId,
           string uriPrefix,
           string from,
           string to,
           int? minRating,
           int? maxRating,
           string user,
           string category
           DateTime? minDateUtc,
           DateTime? maxDateUtc,
           int? skip,
           int? take);
```

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** om auktoriseringsrubriken används, lämna appid fältet tomt eller ange en sträng som innehåller ”ägar” + ”” + åtkomst-token.|
| uriPrefix | **Valfritt** en sträng som innehåller prefixet för URI: N för översättningen.|
| från | **Valfritt** en sträng som representerar språkkoden för Översättningstext. |
| till | **Valfritt** en sträng som representerar språkkoden att översätta text i.|
| minRating| **Valfritt** ett heltalsvärde som representerar minimikvalitet-klassificering för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| maxRating| **Valfritt** ett heltalsvärde som representerar den högsta kvalitet klassificeringen för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| Användare | **Valfritt** en sträng som används för att filtrera resultatet baserat på avsändaren av överföringen. |
| category| **Valfritt** en sträng som innehåller den kategori eller domän översättningens. Den här parametern stöder endast standardalternativet Allmänt.|
| minDateUtc| **Valfritt** datum när du vill hämta översättningar. Datumet måste vara i UTC-format. |
| maxDateUtc| **Valfritt** datum till när du vill hämta översättningar. Datumet måste vara i UTC-format. |
| hoppa över| **Valfritt** antalet resultat som du vill hoppa över på en sida. Till exempel om du vill alternativet Hoppa över de första 20 raderna i resultaten och vyn från 21 resultatposten, ange 20 för den här parametern. Standardvärdet för den här parametern är 0.|
| ta | **Valfritt** antalet resultat som du vill hämta. Det maximala antalet för varje begärande är 100. Standardvärdet är 100.|

> [!NOTE]
> Hoppa över och vidta parametrarna Aktivera sidnumrering för ett stort antal resultatposter.

**Returvärde**

Resultatet innehåller matris med de **UserTranslationCount**. Varje UserTranslationCount har följande element:

| Fält | Beskrivning |
|:---|:---|
| Antal| Antalet resultat som har hämtats|
| Från | Källspråk|
| Klassificering| Den klassificering som tillämpas av jobbets avsändare i metodanropet AddTranslation()|
| Till| Målspråket|
| URI| Den URI som används i metodanropet AddTranslation()|
| Användare| Användarnamnet|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern '**maxDateUtc**'måste vara större än eller lika med'**minDateUtc**”.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP-Adressen är över kvoten.| <ul><li>Gränsen för antalet begäranden per minut har nåtts.</li><li>Begäran är fortfarande begränsad 10000 tecken.</li><li>Begränsa antalet tecken som Microsoft Translator-API ska ta emot varje timme och en daglig kvot.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID överskridit kvoten timvis eller dagligen.|

> [!NOTE]
> Kvoten justeras för att säkerställa en rättvis av alla användare av tjänsten.

**Visa kodexempel på GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations metod

Den här metoden hämtar översättningar som har skapats av användaren. Det ger översättningarna grupperade efter uriPrefix, från, till, användare, och minRating och maxRating parametrarna.

**Syntax**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslation[] GetUserTranslations (
            string appId,
            string uriPrefix,
            string from,
            string to,
            int? minRating,
            int? maxRating,
            string user,
            string category
            DateTime? minDateUtc,
            DateTime? maxDateUtc,
            int? skip,
            int? take);
```

**Parametrar**

| Parameter | Beskrivning |
|:---|:---|
| appId | **Krävs** om auktoriseringsrubriken används, lämna appid fältet tomt eller ange en sträng som innehåller ”ägar” + ”” + åtkomst-token.|
| uriPrefix| **Valfritt** en sträng som innehåller prefixet för URI: N för översättningen.|
| från| **Valfritt** en sträng som representerar språkkoden för Översättningstext.|
| till| **Valfritt** en sträng som representerar språkkoden att översätta text i.|
| minRating| **Valfritt** ett heltalsvärde som representerar minimikvalitet-klassificering för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| maxRating| **Valfritt** ett heltalsvärde som representerar den högsta kvalitet klassificeringen för den översatta texten. Det giltiga värdet är mellan -10 och 10. Standardvärdet är 1.|
| Användare| **Valfritt. En sträng som används för att filtrera resultatet baserat på avsändaren av överföringen**|
| category| **Valfritt** en sträng som innehåller den kategori eller domän översättningens. Den här parametern stöder endast standardalternativet Allmänt.|
| minDateUtc| **Valfritt** datum när du vill hämta översättningar. Datumet måste vara i UTC-format.|
| maxDateUtc| **Valfritt** datum till när du vill hämta översättningar. Datumet måste vara i UTC-format.|
| hoppa över| **Valfritt** antalet resultat som du vill hoppa över på en sida. Till exempel om du vill alternativet Hoppa över de första 20 raderna i resultaten och vyn från 21 resultatposten, ange 20 för den här parametern. Standardvärdet för den här parametern är 0.|
| ta| **Valfritt** antalet resultat som du vill hämta. Det maximala antalet för varje begärande är 100. Standardvärdet är 50.|

> [!NOTE]
> Hoppa över och vidta parametrarna Aktivera sidnumrering för ett stort antal resultatposter.

**Returvärde**

Resultatet innehåller matris med de **UserTranslation**. Varje UserTranslation har följande element:

| Fält | Beskrivning |
|:---|:---|
| CreatedDateUtc| Skapad i posten med AddTranslation()|
| Från| Källspråk|
| OriginalText| Källspråk texten som ska användas när du skickar in begäran|
|Klassificering |Den klassificering som tillämpas av jobbets avsändare i metodanropet AddTranslation()|
|Till|    Målspråket|
|TranslatedText|    Översättningen som har skickats i metodanropet AddTranslation()|
|URI|   Den URI som används i metodanropet AddTranslation()|
|Användare   |Användarnamnet|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern '**maxDateUtc**'måste vara större än eller lika med'**minDateUtc**”.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP-Adressen är över kvoten.| <ul><li>Gränsen för antalet begäranden per minut har nåtts.</li><li>Begäran är fortfarande begränsad 10000 tecken.</li><li>Begränsa antalet tecken som Microsoft Translator-API ska ta emot varje timme och en daglig kvot.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID överskridit kvoten timvis eller dagligen.|

> [!NOTE]
> Kvoten justeras för att säkerställa en rättvis av alla användare av tjänsten.

**Visa kodexempel på GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
