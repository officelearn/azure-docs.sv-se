---
title: CTF-rapportering (Collaborative Translation Framework) – Translator Text API
titleSuffix: Azure Cognitive Services
description: Använda CTF-rapportering (Collaborative Translation Framework).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 0c099584642de1939df5e1e7d9785006e8d25235
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732350"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Så här använder du CTF-rapportering (Collaborative Translation Framework)

> [!NOTE]
> Den här metoden är föråldrad. Den är inte tillgänglig i V 3.0 för Translator Text API.
> 
> Samarbets översättnings ramverket (CTF), som tidigare var tillgängligt för V 2.0 av Translator Text API, föråldrades den 1 februari 2018. Funktionerna AddTranslation och AddTranslationArray gör det möjligt för användarna att aktivera korrigeringar via ramverket för samarbets översättning. Efter den 31 januari 2018 accepterade inte dessa två funktioner nya menings bidrag och användare får ett fel meddelande. Dessa funktioner drogs tillbaka och kommer inte att ersättas.

Rapporterings-API: t för CTF (Collaborative Translation Framework) returnerar statistik och det faktiska innehållet i CTF-lagret. Detta API skiljer sig från metoden GetTranslations () eftersom den:
* Returnerar det översatta innehållet och dess totala antal endast från ditt konto (appId eller Azure Marketplace-konto).
* Returnerar det översatta innehållet och dess totala antal utan att behöva matcha käll meningen.
* Returnerar inte den automatiska översättningen (maskin översättning).

## <a name="endpoint"></a>Slutpunkt
Slut punkten för CTF repor ting API ärhttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metoder
| Name |    Beskrivning|
|:---|:---|
| GetUserTranslationCounts-metod | Hämta antalet översättningar som skapas av användaren. |
| GetUserTranslations-metod | Hämtar de översättningar som skapats av användaren. |

Med dessa metoder kan du:
* Hämta den fullständiga uppsättningen användar översättningar och korrigeringar under ditt konto-ID för nedladdning.
* Hämta listan över de frekventa deltagarna. Kontrol lera att rätt användar namn finns i AddTranslation ().
* Bygg ett användar gränssnitt som gör att dina betrodda användare kan se alla tillgängliga kandidater, om det är nödvändigt att begränsa till en del av din plats, baserat på URI-prefixet.

> [!NOTE]
> Båda metoderna är relativt långsamma och dyra. Vi rekommenderar att du använder dem sparsamt.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts-metod

Den här metoden hämtar antalet översättningar som skapas av användaren. Den innehåller listan över översättnings antal grupperade efter parametrarna uriPrefix, from, to, User, minRating och maxRating Request.

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
| appId | **Krävs** Om Authorization-huvudet används, lämnar du fältet AppID tomt annars anger du en sträng som innehåller "Bearer" + "" + åtkomsttoken.|
| uriPrefix | **Valfritt** En sträng som innehåller prefixet för URI för översättningen.|
| Från | **Valfritt** En sträng som representerar språk koden för översättnings texten. |
| till | **Valfritt** En sträng som representerar språk koden som texten ska översättas till.|
| minRating| **Valfritt** Ett heltals värde som representerar den översatta textens minsta kvalitets betyg. Det giltiga värdet är mellan-10 och 10. Standardvärdet är 1.|
| maxRating| **Valfritt** Ett heltals värde som representerar den översatta textens maximala kvalitets betyg. Det giltiga värdet är mellan-10 och 10. Standardvärdet är 1.|
| användare | **Valfritt** En sträng som används för att filtrera resultatet baserat på överföringens upphovs man. |
| category| **Valfritt** En sträng som innehåller översättningens kategori eller domän. Den här parametern stöder endast standard alternativet allmänt.|
| minDateUtc| **Valfritt** Det datum då du vill hämta översättningarna. Datumet måste vara i UTC-format. |
| maxDateUtc| **Valfritt** Datumet till när du vill hämta översättningarna. Datumet måste vara i UTC-format. |
| hoppa över| **Valfritt** Antalet resultat som du vill hoppa över på en sida. Om du till exempel vill hoppa över de första 20 raderna i resultaten och Visa från den 21: a resultat posten, anger du 20 för den här parametern. Standardvärdet för den här parametern är 0.|
| gå | **Valfritt** Antalet resultat som du vill hämta. Det maximala antalet för varje begäran är 100. Standardvärdet är 100.|

> [!NOTE]
> Parametrar för att hoppa över och utföra begär Anden möjliggör sid brytning för ett stort antal resultat poster.

**Returvärde**

Resultat uppsättningen innehåller en matris med **UserTranslationCount**. Varje UserTranslationCount har följande element:

| Field | Beskrivning |
|:---|:---|
| Antal| Antalet resultat som hämtas|
| Från | Käll språket|
| Omdöme| Den klassificering som används av avsändaren i metod anropet AddTranslation ()|
| Till| Mål språket|
| Uri| Den URI som används i metod anropet AddTranslation ()|
| Användare| Användar namnet|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern**maxDateUtc**måste vara större än eller lika med**minDateUtc**.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP är över kvoten.| <ul><li>Gränsen för antalet begär Anden per minut har nåtts.</li><li>Den begärda storleken är begränsad till 10000 tecken.</li><li>En timme och en daglig kvot gräns begränsar antalet tecken som Microsoft Translator-API: n accepterar.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID: t överskred tim-eller dags kvoten.|

> [!NOTE]
> Kvoten justeras för att säkerställa skälighet mellan alla användare av tjänsten.

**Visa kod exempel på GitHib**
* [C #](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)

## <a name="getusertranslations-method"></a>GetUserTranslations-metod

Den här metoden hämtar översättningarna som skapas av användaren. Den innehåller översättningarna grupperade efter parametrarna uriPrefix, from, to, User och minRating och maxRating Request.

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
| appId | **Krävs** Om Authorization-huvudet används, lämnar du fältet AppID tomt annars anger du en sträng som innehåller "Bearer" + "" + åtkomsttoken.|
| uriPrefix| **Valfritt** En sträng som innehåller prefixet för URI för översättningen.|
| Från| **Valfritt** En sträng som representerar språk koden för översättnings texten.|
| till| **Valfritt** En sträng som representerar språk koden som texten ska översättas till.|
| minRating| **Valfritt** Ett heltals värde som representerar den översatta textens minsta kvalitets betyg. Det giltiga värdet är mellan-10 och 10. Standardvärdet är 1.|
| maxRating| **Valfritt** Ett heltals värde som representerar den översatta textens maximala kvalitets betyg. Det giltiga värdet är mellan-10 och 10. Standardvärdet är 1.|
| användare| **Valfritt. En sträng som används för att filtrera resultatet baserat på överföringens upphovs punkt**|
| category| **Valfritt** En sträng som innehåller översättningens kategori eller domän. Den här parametern stöder endast standard alternativet allmänt.|
| minDateUtc| **Valfritt** Det datum då du vill hämta översättningarna. Datumet måste vara i UTC-format.|
| maxDateUtc| **Valfritt** Datumet till när du vill hämta översättningarna. Datumet måste vara i UTC-format.|
| hoppa över| **Valfritt** Antalet resultat som du vill hoppa över på en sida. Om du till exempel vill hoppa över de första 20 raderna i resultaten och Visa från den 21: a resultat posten, anger du 20 för den här parametern. Standardvärdet för den här parametern är 0.|
| gå| **Valfritt** Antalet resultat som du vill hämta. Det maximala antalet för varje begäran är 100. Standardvärdet är 50.|

> [!NOTE]
> Parametrar för att hoppa över och utföra begär Anden möjliggör sid brytning för ett stort antal resultat poster.

**Returvärde**

Resultat uppsättningen innehåller en matris med **UserTranslation**. Varje UserTranslation har följande element:

| Field | Beskrivning |
|:---|:---|
| CreatedDateUtc| Datumet då posten skapades med AddTranslation ()|
| Från| Käll språket|
| OriginalText| Käll språks texten som används när begäran skickas|
|Omdöme |Den klassificering som används av avsändaren i metod anropet AddTranslation ()|
|Till|    Mål språket|
|TranslatedText|    Översättningen som skickas i metod anropet AddTranslation ()|
|Uri|   Den URI som används i metod anropet AddTranslation ()|
|Användare   |Användar namnet|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern**maxDateUtc**måste vara större än eller lika med**minDateUtc**.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP är över kvoten.| <ul><li>Gränsen för antalet begär Anden per minut har nåtts.</li><li>Den begärda storleken är begränsad till 10000 tecken.</li><li>En timme och en daglig kvot gräns begränsar antalet tecken som Microsoft Translator-API: n accepterar.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID: t överskred tim-eller dags kvoten.|

> [!NOTE]
> Kvoten justeras för att säkerställa skälighet mellan alla användare av tjänsten.

**Visa kod exempel på GitHib**
* [C #](https://github.com/MicrosoftTranslator/CustomTranslator-API-CSharp)
* [PHP](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-PHP)
