---
title: Microsoft Translator samarbetsfunktioner översättning Reporting Framework (CTF)
description: Hur du använder samarbetsfunktioner översättning Framework (CTF) reporting.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: cefc630a82a56703ba4942bcad18f6e0a38b1ee5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352605"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Hur du använder reporting samarbetsfunktioner översättning Framework (CTF)

> [!NOTE]
> Den här metoden är föråldrad. Det är inte tillgänglig i V3.0 översättare Text API.

> Den gemensamma översättningar Framework (CTF), tidigare var tillgängliga för version 2.0 av API: et för översättare Text togs bort från och med den 1 februari 2018. Funktionerna AddTranslation och AddTranslationArray att användarna kan aktivera korrigeringar via samarbetsfunktioner översättning Framework. Dessa två funktioner accepterade inte nya meningen bidrag efter 31 januari 2018, och användarna får ett felmeddelande. De här funktionerna har som har återkallats och kommer inte att ersättas. 

>Liknande funktionalitet är tillgänglig i översättare hubb API, så att du kan skapa en anpassad översättning system med terminologi och format, och du kan anropa den med hjälp av kategori-ID i översättare Text API. Översättare hubb: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). Översättare hubb API: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

Samarbetsfunktioner översättning Framework (CTF) Reporting API returnerar statistik och det faktiska innehållet i arkivet CTF. Detta API är olika från metoden GetTranslations() eftersom den:
* Returnerar det översatta innehållet och det totala antalet endast från ditt konto (appId eller Azure Marketplace-kontot).
* Returnerar det översatta innehållet och det totala antalet utan en matchning för käll-sats.
* Returnerar inte automatisk översättning (maskinöversättning).

## <a name="endpoint"></a>Slutpunkt
CTF Reporting API-slutpunkten är http://api.microsofttranslator.com/v2/beta/ctfreporting.svc
                        

## <a name="methods"></a>Metoder
| Namn |    Beskrivning|
|:---|:---|
| GetUserTranslationCounts-metoden | Hämta antalet översättningar som har skapats av användaren. |
| GetUserTranslations-metoden | Hämtar översättningar som har skapats av användaren. |

Dessa metoder kan du:
* Hämta en fullständig uppsättning användare översättningar och korrigeringar under konto-ID för hämtning.
* Hämta listan över ofta deltagare. Se till att rätt användarnamn har angetts i AddTranslation().
* Skapa ett användargränssnitt (UI) som gör att din betrodda användare att se alla tillgängliga kandidater, om det behövs begränsat till en del av platsen, baserat på URI-prefix.

> [!NOTE]
> Båda metoderna är relativt långsamt och dyrt. Det rekommenderas att använda dem sparsamt.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts-metoden

Den här metoden hämtar antalet översättningar som har skapats av användaren. Den innehåller en lista över översättning antal grupperade efter uriPrefix, från, till användare, minRating och maxRating begäran parametrar.

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
| appId | **Krävs** om Authorization-huvud används, lämna appid fältet tomt eller ange en sträng som innehåller ”ägar” + ”” + åtkomst-token.|
| uriPrefix | **Valfria** en sträng som innehåller prefixet för URI: N för översättning.|
| från | **Valfria** en sträng som representerar språkkoden för Översättningstext. |
| till | **Valfria** en sträng som representerar språkkoden att översätta text till.|
| minRating| **Valfria** ett heltalsvärde som representerar minimikvalitet klassificering för den översatta texten. Giltigt värde är mellan -10 och 10. Standardvärdet är 1.|
| maxRating| **Valfria** ett heltalsvärde som representerar den högsta kvalitet klassificeringen för den översatta texten. Giltigt värde är mellan -10 och 10. Standardvärdet är 1.|
| Användare | **Valfria** en sträng som används för att filtrera resultatet baserat på avsändaren av överföring. |
| category| **Valfria** en sträng som innehåller kategori eller domänen för översättning. Den här parametern stöder endast standardalternativet Allmänt.|
| minDateUtc| **Valfria** datum när du vill hämta översättningar. Datumet måste vara i UTC-format. |
| maxDateUtc| **Valfria** datum till när du vill hämta översättningar. Datumet måste vara i UTC-format. |
| Hoppa över| **Valfria** antalet resultat som du vill hoppa över på en sida. Ange till exempel 20 för den här parametern om du vill den hoppa över 20 första raderna i resultatet och visa från 21 resultatposten. Standardvärdet för den här parametern är 0.|
| ta | **Valfria** antalet resultat som du vill hämta. Det maximala antalet för varje begäran är 100. Standardvärdet är 100.|

> [!NOTE]
> Hoppa över och vidtar parametrarna Aktivera sidbrytning för ett stort antal resultatposter.

**Returvärde**

Resultatmängden innehåller matris av den **UserTranslationCount**. Varje UserTranslationCount har följande element:

| Fält | Beskrivning |
|:---|:---|
| Antal| Antalet resultat som har hämtats|
| Från | Käll-språk|
| Klassificering| Den klassificering som används av avsändaren i metodanropet AddTranslation()|
| Till| Mål-språk|
| URI| URI: N används i metodanropet AddTranslation()|
| Användare| Användarnamn|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern '**maxDateUtc**'måste vara större än eller lika med'**minDateUtc**'.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP är över kvoten.| <ul><li>Gränsen för antalet begäranden per minut har nåtts.</li><li>Begäran om storleken är begränsad på 10000 tecknen.</li><li>Begränsa antalet tecken som Microsoft översättare API ska ta emot en varje timme och en daglig kvot.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID överskred kvoten timvis eller dagligen.|

> [!NOTE]
> Kvoten justeras för att säkerställa skälighet av alla användare av tjänsten.

**Visa kodexempel på GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations-metoden

Den här metoden hämtar översättningar som har skapats av användaren. Det ger översättningar grupperade efter uriPrefix, från, till användare, och minRating och maxRating parametrar för begäran.

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
| appId | **Krävs** om Authorization-huvud används, lämna appid fältet tomt eller ange en sträng som innehåller ”ägar” + ”” + åtkomst-token.|
| uriPrefix| **Valfria** en sträng som innehåller prefixet för URI: N för översättning.|
| från| **Valfria** en sträng som representerar språkkoden för Översättningstext.|
| till| **Valfria** en sträng som representerar språkkoden att översätta text till.|
| minRating| **Valfria** ett heltalsvärde som representerar minimikvalitet klassificering för den översatta texten. Giltigt värde är mellan -10 och 10. Standardvärdet är 1.|
| maxRating| **Valfria** ett heltalsvärde som representerar den högsta kvalitet klassificeringen för den översatta texten. Giltigt värde är mellan -10 och 10. Standardvärdet är 1.|
| Användare| **Valfritt. En sträng som används för att filtrera resultatet baserat på avsändaren att**|
| category| **Valfria** en sträng som innehåller kategori eller domänen för översättning. Den här parametern stöder endast standardalternativet Allmänt.| 
| minDateUtc| **Valfria** datum när du vill hämta översättningar. Datumet måste vara i UTC-format.| 
| maxDateUtc| **Valfria** datum till när du vill hämta översättningar. Datumet måste vara i UTC-format.|
| Hoppa över| **Valfria** antalet resultat som du vill hoppa över på en sida. Ange till exempel 20 för den här parametern om du vill den hoppa över 20 första raderna i resultatet och visa från 21 resultatposten. Standardvärdet för den här parametern är 0.|
| ta| **Valfria** antalet resultat som du vill hämta. Det maximala antalet för varje begäran är 100. Standardvärdet är 50.|

> [!NOTE]
> Hoppa över och vidtar parametrarna Aktivera sidbrytning för ett stort antal resultatposter.

**Returvärde**

Resultatmängden innehåller matris av den **UserTranslation**. Varje UserTranslation har följande element:

| Fält | Beskrivning |
|:---|:---|
| CreatedDateUtc| Skapandedatum i posten med AddTranslation()|
| Från| Käll-språk|
| OriginalText| Käll-språk som används när du skickar begäran|
|Klassificering |Den klassificering som används av avsändaren i metodanropet AddTranslation()|
|Till|    Mål-språk|
|TranslatedText|    Översättningen som har skickats i metodanropet AddTranslation()|
|URI|   URI: N används i metodanropet AddTranslation()|
|Användare   |Användarnamn|

**Undantag**

| Undantag | Meddelande | Villkor |
|:---|:---|:---|
| ArgumentOutOfRangeException | Parametern '**maxDateUtc**'måste vara större än eller lika med'**minDateUtc**'.| Värdet för parametern **maxDateUtc** är mindre än värdet för parametern **minDateUtc**.|
| TranslateApiException | IP är över kvoten.| <ul><li>Gränsen för antalet begäranden per minut har nåtts.</li><li>Begäran om storleken är begränsad på 10000 tecknen.</li><li>Begränsa antalet tecken som Microsoft översättare API ska ta emot en varje timme och en daglig kvot.</li></ul>|
| TranslateApiException | AppId är över kvoten.| Program-ID överskred kvoten timvis eller dagligen.|

> [!NOTE]
> Kvoten justeras för att säkerställa skälighet av alla användare av tjänsten.

**Visa kodexempel på GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)


















