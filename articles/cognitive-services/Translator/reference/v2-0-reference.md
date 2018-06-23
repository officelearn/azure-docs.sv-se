---
title: Microsoft Translator Text API V2.0 referens | Microsoft Docs
titleSuffix: Cognitive Services
description: I referensdokumentationen för V2.0 Microsoft översättare Text-API.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: e32e28608d2fecf27b61acff74af7eb6849f0ba1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355149"
---
# <a name="translator-text-api-v20"></a>Översättare Text API v2.0

> [!IMPORTANT]
> Den här versionen av översättare Text API är inaktuell. [Dokumentation för v3 översättare Text API](v3-0-reference.md).

Microsoft översättare V2 Text API kan integreras sömlöst i dina program, webbplatser, verktyg eller andra lösningar för att tillhandahålla flera språk användarupplevelser. Utnyttja branschstandarder, kan den användas på alla plattformar för maskinvara och i alla operativsystem för översättningar och andra språk-relaterade åtgärder, till exempel identifiera språk för text eller text till tal. Klicka här för mer information om Microsoft översättare API.

## <a name="getting-started"></a>Komma igång
Åtkomst till Microsoft översättare Text API du behöver [registrera dig för Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Auktorisering
Alla anrop till Microsoft översättare Text API kräver en prenumeration för att autentisera. API: et stöder två lägen av autentisering:

* Med hjälp av en åtkomst-token. Använda den prenumeration nyckeln som refereras i **steg** 9 för att generera en åtkomst-token genom att göra en POST-begäran till Autentiseringstjänsten. Dokumentationen tokentjänsten för information. Skicka den åtkomst-token till tjänsten översättare med hjälp av Authorization-huvud eller access_token frågeparameter. Åtkomst-token är giltig i 10 minuter. Hämta en ny åtkomsttoken var 10: e minut och fortsätt att använda samma åtkomst-token för upprepad begäranden inom dessa 10 minuter.

* Med hjälp av en prenumeration för direkt. Ange din prenumeration nyckel som ett värde i `Ocp-Apim-Subscription-Key` huvud ingår i din begäran översättare-API: et. I det här läget behöver du inte anropa Autentiseringstjänsten token för att generera en åtkomst-token.

Överväg att din prenumeration nyckel och åtkomst-token som hemligheter som ska vara dold från vyn.

## <a name="profanity-handling"></a>Svordomar hantering
Normalt behåller tjänsten översättare svordomar som finns i datakällan i översättningen. Graden av svordomar och kontext som gör ord vulgärt skiljer sig åt mellan kulturer och därför graden av svordomar på språket som mål kan framhävas eller minskas.

Du kan använda svordomar filtrering alternativ för de metoder som har stöd för det. Om du vill undvika svordomar i översättning, oavsett förekomst av svordomar i källtexten. Alternativet kan du välja om du vill se svordomar bort eller markerats med lämpliga taggar eller ingen åtgärd vidtogs. Accepterade värden för `ProfanityAction` är `NoAction` (standard), markerat och `Deleted`.


|ProfanityAction    |Åtgärd |Exempel källan (japanska)  |Exempel översättning (på engelska)  |
|:--|:--|:--|:--|
|NoAction   |Standard. Samma som inte ange alternativet. Svordomar skickar från källan till målet.        |彼はジャッカスです。     |Han är en jackass.   |
|Markerad     |Vulgärt ord ska omges av XML-taggar <profanity> och </profanity>.     |彼はジャッカスです。 |Han är en <profanity>jackass</profanity>.    |
|Borttagen    |Vulgärt ord tas bort från utdata utan ersättning.     |彼はジャッカスです。 |Han är en.   |

    
## <a name="excluding-content-from-translation"></a>Exkludera innehållet från översättning
Vid översättning av innehåll med till exempel HTML-taggar (`contentType=text/html`), kan det vara bra att undanta specifika innehåll från översättning. Du kan använda attributet `class=notranslate` Ange innehåll som ska finnas kvar i det ursprungliga språket. I följande exempel innehåll i först `div` kommer inte att översätta elementet när innehållet i andra `div` element ska översättas.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>Hämta / översätta

### <a name="implementation-notes"></a>Implementeringsanteckningar
Omvandlar en textsträng från ett språk till en annan.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Returvärde:** en sträng som representerar den översatta texten.

Om du tidigare använt `AddTranslation` eller `AddTranslationArray` att ange en översättning med betyget 5 eller högre för samma käll-mening `Translate` returnerar endast översta valet är tillgänglig i systemet. ”Samma källa mening” innebär exakt samma (100%-matchning), förutom versaler, blanksteg, värden och skiljetecken i slutet av en mening. Om ingen klassificering lagras med betyget 5 eller senare vara automatisk översättning av Microsoft Translator det returnerade resultatet.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

sträng

Svarets Content-Type: application/xml 

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning    |Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID  |(tom)    |Krävs. Om rubriken Authorization eller Ocp-Apim-prenumeration-nyckel används, lämna appid fältet tomt eller innehåller en sträng som innehåller ”ägar” + ”” + ”access_token”.|DocumentDB|sträng|
|text|(tom)   |Krävs. En sträng som representerar texten som ska översätta. Storleken på text får inte överskrida 10000 tecken.|DocumentDB|sträng|
|från|(tom)   |Valfri. En sträng som representerar språkkoden för Översättningstext. Till exempel en för engelska.|DocumentDB|sträng|
|till|(tom) |Krävs. En sträng som representerar språkkoden att översätta text till.|DocumentDB|sträng|
|contentType|(tom)    |Valfri. Formatet på texten som översätts. Format som stöds är text/plain (standard) och text/html. All HTML-kod måste vara en giltig, fullständig element.|DocumentDB|sträng|
|category|(tom)   |Valfri. En sträng som innehåller översättningen kategorin (domän). Standardvärdet är ”Allmänt”.|DocumentDB|sträng|
|Auktorisering|(tom)  |Krävs om appid fältet eller huvudet Ocp-Apim-prenumeration-nyckel har angetts. Autentiseringstoken: ”ägar” + ”” + ”access_token”.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)  |Krävs om appid fältet eller Authorization-huvud har angetts.|sidhuvud|sträng|


### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="post-translatearray"></a>Bokför /TranslateArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `TranslateArray` metod för att hämta översättningar för flera texter och källa.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Formatet på begärandetexten bör vara följande:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Element i den `TranslateArrayRequest` är:


* `appid`: Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `from`: Valfria. En sträng som representerar språkkoden att översätta text från. Om den lämnas tom innehåller svaret resultatet av automatisk identifiering av språk.
* `options`: Valfria. En `Options` objekt som innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard. Angivet element måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller översättningen kategorin (domän). Som standard `general`.
    - `ContentType`: Formatet på texten som översätts. Format som stöds är `text/plain` (standard), `text/xml` och `text/html`. All HTML-kod måste vara en giltig, fullständig element.
    - `ProfanityAction`: Anger hur profanities hanteras enligt beskrivningen ovan. Accepterade värden för `ProfanityAction` är `NoAction` (standard), `Marked` och `Deleted`.
    - `State`: Användartillstånd för att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
    - `Uri`: Filtrera resultaten av den här URI: N. Standard: `all`.
    - `User`: Filtrera resultaten av den här användaren. Standard: `all`.
* `texts`: Krävs. En matris som innehåller texterna för översättning. Alla strängar måste vara av samma språk. Summan av alla texter översättas får inte överskrida 10000 tecken. Det maximala antalet matriselement är 2000.
* `to`: Krävs. En sträng som representerar språkkoden att översätta text till.

Valfritt element kan utelämnas. Element som är direkt underordnade TranslateArrayRequest måste anges i alfabetisk ordning.

TranslateArray metoden godkänner `application/xml` eller `text/xml` för `Content-Type`.

**Returvärde:** A `TranslateArrayResponse` matris. Varje `TranslateArrayResponse` har följande element:

* `Error`: Anger om ett har inträffat ett fel. Annars inställt på null.
* `OriginalSentenceLengths`: En matris med heltal som anger längden på varje mening i den ursprungliga källa texten. Längden på matrisen anger antalet meningar.
* `TranslatedText`: Den översatta texten.
* `TranslatedSentenceLengths`: En matris med heltal som anger längden på varje mening i översatta texten. Längden på matrisen anger antalet meningar.
* `State`: Användartillstånd för att korrelera begäran och svar. Returnerar samma innehåll som begäran.

Formatet på brödtext för svar är som följer.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
Ett lyckat svar inkluderar en matris med `TranslateArrayResponse` i formatet som beskrivs ovan.

sträng

Svarets Content-Type: application/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|(tom)) |Krävs om appid fältet eller huvudet Ocp-Apim-prenumeration-nyckel har angetts. Autentiseringstoken: ”ägar” + ”” + ”access_token”.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om appid fältet eller Authorization-huvud har angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod   |Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar. Vanliga fel är: <ul><li>Matriselementet får inte vara tomt</li><li>Ogiltigt kategorinamn</li><li>Från språk är ogiltigt</li><li>Språket är ogiltig</li><li>Begäran innehåller för många element</li><li>From-språket stöds inte</li><li>Till språket stöds inte</li><li>Översätta begäran har för mycket data</li><li>HTML är inte i rätt format</li><li>För många strängar skickades i översätta begäran</li></ul>|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="post-getlanguagenames"></a>Bokför /GetLanguageNames

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar eget namn för språk som skickades som parametern `languageCodes`, och lokalisering med skickade språkvariant-språket.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Begärandetexten innehåller en strängmatris som representerar ISO 639-1 språkkoder att hämta de egna namn för. Exempel:

```
<ArrayOfstring xmlns:i="http://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Returvärde:** en strängmatris med språk som stöds av tjänsten översättare lokaliserad till det begärda språket.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En strängmatris som innehåller namn för språk som stöds av tjänsten översättare lokaliserad till det begärda språket.

sträng

Svarets Content-Type: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Nationella inställningar|(tom) |Krävs. En sträng som representerar en kombination av en ISO 639 två bokstäver gemener kultur-kod som är associerad med ett språk och en ISO 3166 två bokstäver versaler subkultur kod för lokalisering språk namn eller en ISO 639 gemener Kulturkod ensamt.|DocumentDB|sträng|
|Auktorisering|(tom)  |Krävs om fältet appid eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)  |Krävs om fältet appid eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="get-getlanguagesfortranslate"></a>Hämta /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämta en lista med språkkoder som representerar språk som stöds av tjänsten översättning.  `Translate` och `TranslateArray` kan översätta mellan två av dessa språk.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Returvärde:** en strängmatris med språkkoder som stöds av tjänsterna översättare.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En strängmatris med språkkoder som stöds av tjänsterna översättare.

sträng

Svarets Content-Type: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Auktorisering|(tom)  |Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503|Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="get-getlanguagesforspeak"></a>Hämta /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar de språk som är tillgängliga för talsyntes.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Returvärde:** en strängmatris med språkkoder som stöds för tal-sammanfattning av tjänsten översättare.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En strängmatris med språkkoder som stöds för tal-sammanfattning av tjänsten översättare.

sträng

Svarets Content-Type: application/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|
 
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401|Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ange oss med ungefärliga datum och tid för begäran och begärande-ID som ingår i rubriken `X-MS-Trans-Info`.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="get-speak"></a>Hämta / tala

### <a name="implementation-notes"></a>Implementeringsanteckningar
Returnerar en wave eller mp3 dataström som talas i önskat språk skickades i texten.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Returvärde:** en wave eller mp3 dataström som talas i önskat språk skickades i texten.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

Binär

Svarets Content-Type: application/xml 

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|(tom)   |Krävs. En sträng som innehåller en mening eller meningar för angivna språket som ska läsas för wave-dataström. Storleken på texten som ska tala får inte överskrida 2000 tecken.|DocumentDB|sträng|
|Språk|(tom)   |Krävs. En sträng som representerar stöds språkkoden prata texten i. Koden måste finnas i listan över koder som returneras från metoden `GetLanguagesForSpeak`.|DocumentDB|sträng|
|Format|(tom)|Valfri. En sträng som anger innehållstyp-ID. För närvarande `audio/wav` och `audio/mp3` är tillgängliga. Standardvärdet är `audio/wav`.|DocumentDB|sträng|
|alternativ|(tom)    |<ul><li>Valfri. En sträng som anger egenskaperna för syntetiskt tal:<li>`MaxQuality` och `MinSize` är tillgängliga för att ange kvaliteten på ljud signaler. Med `MaxQuality`, du kan hämta röster med högsta kvalitet och `MinSize`, du kan hämta röster med den minsta storleken. Standardvärdet är `MinSize`.</li><li>`female` och `male` är tillgängliga för att ange röst önskade kön. Standardvärdet är `female`. Använder vertikalstreck '|` to include multiple options. For example  `MaxQuality|Han '.</li></li></ul> |DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)  |Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ange oss med ungefärliga datum och tid för begäran och begärande-ID som ingår i rubriken `X-MS-Trans-Info`.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="get-detect"></a>Hämta / identifiera

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `Detect` metod för att identifiera språk för en vald typ av text.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Returvärde:** en sträng som innehåller en två tecken språkkod för den angivna texten. .

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

sträng

Svarets Content-Type: application/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)  |Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|(tom)|Krävs. En sträng som innehåller text vars språk ska identifieras. Storleken på text får inte överskrida 10000 tecken.|DocumentDB| sträng|
|Auktorisering|(tom)|Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel  |(tom)    |Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ange oss med ungefärliga datum och tid för begäran och begärande-ID som ingår i rubriken `X-MS-Trans-Info`.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|


## <a name="post-detectarray"></a>Bokför /DetectArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `DetectArray` metod för att identifiera en strängmatris språket på samma gång. Utför oberoende identifiering av varje enskild matriselement och returnerar ett resultat för varje rad i matrisen.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Begärandetexten bör vara på följande sätt.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Storleken på text får inte överskrida 10000 tecken.

**Returvärde:** koder som en strängmatris som innehåller ett språk som två tecken för varje rad i Indatamatrisen.

Formatet på brödtext för svar är som följer.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
DetectArray lyckades. Returnerar en strängmatris som innehåller en två tecken språkkoder för varje rad i Indatamatrisen.

sträng

Svarets Content-Type: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om det `appid` fältet eller Authorization-huvud har angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="get-addtranslation"></a>Hämta /AddTranslation

### <a name="implementation-notes"></a>Implementeringsanteckningar

> [!IMPORTANT]
> **UTFASNINGEN Obs:** efter den 31 januari 2018 accepterar inte den här metoden nya meningen bidrag och du får ett felmeddelande. Läs det här meddelandet om ändringar av samarbetsfunktioner funktionerna för översättning.

Lägger till en översättning för översättning minne.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)

sträng

Svarets Content-Type: program: xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp   |
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|OriginalText|(tom)|Krävs. En sträng som innehåller den text som ska översätta från. Strängen har en maximal längd på 1000 tecken.|DocumentDB|sträng|
|TranslatedText|(tom) |Krävs. En sträng som innehåller översätta text på mål-språk. Strängen har en maximal längd på 2000 tecken.|DocumentDB|sträng|
|från|(tom)   |Krävs. En sträng som representerar språkkoden för Översättningstext. SV = engelska, de = tyska etc...|DocumentDB|sträng|
|till|(tom)|Krävs. En sträng som representerar språkkoden att översätta text till.|DocumentDB|sträng|
|rating|(tom) |Valfri. Ett heltal som representerar kvalitet klassificering för den här strängen. Värdet mellan -10 och 10. Standardvärdet är 1.|DocumentDB|heltal|
|contentType|(tom)    |Valfri. Formatet på texten som översätts. Format som stöds är ”text/plain” och ”text/html”. All HTML-kod måste vara en giltig, fullständig element.   |DocumentDB|sträng|
|category|(tom)|Valfri. En sträng som innehåller översättningen kategorin (domän). Standardvärdet är ”Allmänt”.|DocumentDB|sträng|
|Användare|(tom)|Krävs. En sträng som används för att spåra skickat programobjektet.|DocumentDB|sträng|
|URI|(tom)|Valfri. En sträng som innehåller innehållsplats för denna översättning.|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om fältet appid eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.    |sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|410|AddTranslation stöds inte längre.|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="post-addtranslationarray"></a>Bokför /AddTranslationArray

### <a name="implementation-notes"></a>Implementeringsanteckningar

> [!IMPORTANT]
> **UTFASNINGEN Obs:** efter den 31 januari 2018 accepterar inte den här metoden nya meningen bidrag och du får ett felmeddelande. Läs det här meddelandet om ändringar av samarbetsfunktioner funktionerna för översättning.

Lägger till en matris med översättningar för att lägga till översättning minne. Det här är en matris version av `AddTranslation`.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Formatet på begärandetexten är som följer.

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Element i elementet AddtranslationsRequest är:

* `AppId`: Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Krävs. En sträng som innehåller koden för språket som källa. Måste vara något av de språk som returneras av den `GetLanguagesForTranslate` metoden.
* `To`: Krävs. En sträng som innehåller koden för språket som mål. Måste vara något av de språk som returneras av den `GetLanguagesForTranslate` metoden.
* `Translations`: Krävs. En matris med översättningar som ska läggas till översättning minne. Varje översättning måste innehålla: originalText translatedText och klassificering. Storleken på varje originalText och translatedText är begränsad till 1000 tecken. Summan av alla originalText(s) och translatedText(s) får inte överskrida 10000 tecken. Det maximala antalet matriselement är 100.
* `Options`: Krävs. En uppsättning alternativ, inklusive kategori, ContentType, URI: N och användare. Användaren måste anges. Kategori, ContentType och Uri är valfria. Angivet element måste anges i alfabetisk ordning.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
AddTranslationArray metoden lyckades. Efter den 31 januari 2018 kommer meningen bidrag inte att accepteras. Tjänsten svarar med felkoden 410.

sträng

Svarets Content-Type: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|(tom)|Krävs om appid fältet eller huvudet Ocp-Apim-prenumeration-nyckel har angetts. Autentiseringstoken: ”ägar” + ”” + ”access_token”.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om appid fältet eller Authorization-huvud har angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|410    |AddTranslation stöds inte längre.|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ange oss med ungefärliga datum och tid för begäran och begärande-ID som ingår i rubriken `X-MS-Trans-Info`.|
|503|Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="get-breaksentences"></a>Hämta /BreakSentences

### <a name="implementation-notes"></a>Implementeringsanteckningar
Delar upp en del av texten i meningar och returnerar en matris med längden i varje mening.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Returvärde:** en matris med heltal som representerar längden på meningarna. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En matris med heltal som representerar längden på meningarna. Längden på matrisen är antalet meningar och värdena är längden på varje mening.

heltal

Svarets Content-Type: application/xml 

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)  |Krävs. Om rubriken Authorization eller Ocp-Apim-prenumeration-nyckel används, lämna appid fältet tomt eller innehåller en sträng som innehåller ”ägar” + ”” + ”access_token”.|DocumentDB| sträng|
|text|(tom)   |Krävs. En sträng som representerar texten som ska delas upp i meningar. Storleken på text får inte överskrida 10000 tecken.|DocumentDB|sträng|
|Språk   |(tom)    |Krävs. En sträng som representerar språkkoden för indata.|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om appid fältet eller huvudet Ocp-Apim-prenumeration-nyckel har angetts. Autentiseringstoken: ”ägar” + ”” + ”access_token”.    |sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)|Krävs om appid fältet eller Authorization-huvud har angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401|Ogiltiga autentiseringsuppgifter|
|500|Serverfel. Om felet kvarstår kan berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID ingår i rubriken X-MS-över-information.|
|503|Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="post-gettranslations"></a>Bokför /GetTranslations

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar en matris med översättningar för ett visst språk par från arkivet och Huvudmålservern-motorn. GetTranslations skiljer sig från Översätt som returneras alla tillgängliga översättningar.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Brödtexten i begäran innehåller det valfria TranslationOptions-objektet, vilket har följande format.

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

Den `TranslateOptions` objektet innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard. Angivet element måste anges i alfabetisk ordning.

* `Category`: En sträng som innehåller översättningen kategorin (domän). Standardvärdet är ”Allmänt”.
* `ContentType`: Det går endast att och standardvärdet är alternativet ”text/plain”.
* `IncludeMultipleMTAlternatives`: boolesk flagga som avgör om mer än en alternativ ska returneras från Huvudmålservern-motorn. Giltiga värden är true och false (skiftlägeskänsligt). Standard är FALSKT och innehåller endast 1 alternativ. Flagga som anger TRUE tillåter för att generera konstgjorda alternativ i översättning, helt integrerat med samarbetsfunktioner översättningar framework (CTF). Funktionen gör för att returnera alternativ för meningar som har inga alternativ i CTF, genom att lägga till konstgjorda alternativ från listan över avkodaren n bäst.
    - Klassificeringar klassificeringarna tillämpas enligt följande: 1) den bästa automatisk översättningen klassificeringsvärde 5. 2) alternativ från CTF återspeglar myndigheten granskare från -10 till + 10. 3) alternativ de automatiskt genererade (n bäst) översättning har en klassificering på 0 och har en matchning 100.
    - Antal alternativ antalet returnerade alternativ som är upp till maxTranslations, men kan vara mindre.
    - Språkpar den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningar. Den är tillgänglig för alla andra Microsoft Translator stöds språkpar.
* `State`: Användartillstånd för att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* `Uri`: Filtrera resultaten av den här URI: N. Standardvärdet är alla om inget värde har angetts.
* `User`: Filtrera resultaten av den här användaren. Standardvärdet är alla om inget värde har angetts.

Begära `Content-Type` ska vara `text/xml`.

**Returvärde:** har följande format i svaret.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Detta inkluderar en `GetTranslationsResponse` element som innehåller följande värden:

* `Translations`: En matris med matchar objekt hittades, lagras i TranslationMatch (se nedan). Översättningar kan innehålla små varianter av den ursprungliga texten (fuzzy matchar). Översättningar ska sorteras: 100% matchar först fuzzy matchar nedan.
* `From`: Om metoden inte angav en From-språk, är det här ett resultat av automatisk identifiering av språk. Annars blir den från språk.
* `State`: Användartillstånd för att korrelera begäran och svar. Innehåller samma värde som anges i parametern TranslateOptions.

TranslationMatch består av följande:

* `Error`: Om ett fel uppstod för en specifik indatasträng lagras felkoden. Annars är fältet tomt.
* `MatchDegree`: Systemet matchar inkommande meningar mot store, inklusive inexakta matchningar.  MatchDegree anger hur nära indatatexten matchar den ursprungliga texten finns i arkivet. Värdet som returneras intervall från 0 till 100, där 0 är ingen likhet och 100 är en exakt skiftlägeskänslig matchning.
MatchedOriginalText: Ursprungliga texten som matchades för resultatet. Returneras bara om den ursprungliga texten matchade skiljde sig indatatexten. Används för att returnera källtext på ungefärliga matchningen. Inte returneras för Microsoft Translator resultat.
* `Rating`: Anger behörighet för den person som du beslutar kvalitet. Maskinöversättning resultat har betyget 5. Anonymt angivna översättningar har ofta en klassificering av 1 till 4 samtidigt auktoritärt angivna översättningar har vanligtvis en klassificering av 6-10.
* `Count`: Antalet gånger som denna översättning med denna klassificering har valts. Värdet ska vara 0 för automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.

### <a name="response-class-status-200"></a>Svaret klass (Status 200)
En `GetTranslationsResponse` objekt i det format som beskrivs ovan.

sträng

Svarets Content-Type: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|AppID|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|(tom)|Krävs. En sträng som representerar texten som ska översätta. Storleken på text får inte överskrida 10000 tecken.|DocumentDB|sträng|
|från|(tom)|Krävs. En sträng som representerar språkkoden för Översättningstext.|DocumentDB|sträng|
|till |(tom)    |Krävs. En sträng som representerar språkkoden att översätta text till.|DocumentDB|sträng|
|maxTranslations|(tom)|Krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.|DocumentDB|heltal|
|Auktorisering| (tom)|Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sträng| sidhuvud|
|OCP-Apim-prenumeration-nyckel|(tom)  |Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ange oss med ungefärliga datum och tid för begäran och begärande-ID som ingår i rubriken `X-MS-Trans-Info`.|
|503|Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="post-gettranslationsarray"></a>Bokför /GetTranslationsArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Använd den `GetTranslationsArray` metod för att hämta flera översättning kandidater för flera texter och källa.

Begäran URI är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Formatet på begärandetexten är som följer.

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` innehåller följande element:

* `AppId`: Krävs. Om Authorization-huvud används, lämna appid fältet tomt eller innehåller en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Krävs. En sträng som representerar språkkoden för Översättningstext.
* `MaxTranslations`: Krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.
* `Options`: Valfria. Ett alternativ för objekt som innehåller de värden som anges nedan. De är valfria och de vanligaste inställningarna som standard. Angivet element måste anges i alfabetisk ordning.
    - Kategorin ': en sträng som innehåller översättningen kategorin (domän). Standardvärdet är Allmänt.
    - `ContentType`: Det går endast att och standard, alternativet är text/plain.
    - `IncludeMultipleMTAlternatives`: boolesk flagga som avgör om mer än en alternativ ska returneras från Huvudmålservern-motorn. Giltiga värden är true och false (skiftlägeskänsligt). Standard är FALSKT och innehåller endast 1 alternativ. Flagga som anger TRUE tillåter för att generera konstgjorda alternativ i översättning, helt integrerat med samarbetsfunktioner översättningar framework (CTF). Funktionen gör för att returnera alternativ för meningar som har inga alternativ i CTF, genom att lägga till konstgjorda alternativ från listan över avkodaren n bäst.
        - Klassificeringar klassificeringarna tillämpas enligt följande: 1) den bästa automatisk översättningen klassificeringsvärde 5. 2) alternativ från CTF återspeglar myndigheten granskare från -10 till + 10. 3) alternativ de automatiskt genererade (n bäst) översättning har en klassificering på 0 och har en matchning 100.
        - Antal alternativ antalet returnerade alternativ som är upp till maxTranslations, men kan vara mindre.
        - Språkpar den här funktionen är inte tillgänglig för översättningar mellan förenklad och traditionell kinesiska, båda riktningar. Den är tillgänglig för alla andra Microsoft Translator stöds språkpar.
* `State`: Användartillstånd för att korrelera begäran och svar. Samma innehåll kommer att returneras i svaret.
* `Uri`: Filtrera resultaten av den här URI: N. Standardvärdet är alla om inget värde har angetts.
* `User`: Filtrera resultaten av den här användaren. Standardvärdet är alla om inget värde har angetts.
* `Texts`: Krävs. En matris som innehåller texterna för översättning. Alla strängar måste vara av samma språk. Summan av alla texter översättas får inte överskrida 10000 tecken. Det maximala antalet matriselement är 10.
* `To`: Krävs. En sträng som representerar språkkoden att översätta text till.

Valfritt element kan utelämnas. Element som är direkt underordnade `GetTranslationsArrayRequest` måste anges i alfabetisk ordning.

Begära `Content-Type` ska vara `text/xml`.

**Returvärde:** har följande format i svaret.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Varje `GetTranslationsResponse` elementet innehåller följande värden:

* `Translations`: En matris med matchar hittas, lagras i `TranslationMatch` objekt (se nedan). Översättningar kan innehålla små varianter av den ursprungliga texten (fuzzy matchar). Översättningar ska sorteras: 100% matchar först fuzzy matchar nedan.
* `From`: Om metoden inte angav en `From` språk, detta blir resultatet av automatisk identifiering av språk. Annars blir den från språk.
* `State`: Användartillstånd för att korrelera begäran och svar. Innehåller samma värde som anges i den `TranslateOptions` parameter.

`TranslationMatch` objektet består av följande:
* `Error`: Om ett fel uppstod för en specifik indatasträng lagras felkoden. Annars är fältet tomt.
* `MatchDegree`: Systemet matchar inkommande meningar mot store, inklusive inexakta matchningar.  `MatchDegree` Anger hur nära indatatexten matchar den ursprungliga texten finns i arkivet. Värdet som returneras intervall från 0 till 100, där 0 är ingen likhet och 100 är en exakt skiftlägeskänslig matchning.
* `MatchedOriginalText`: Ursprungliga texten som matchades för resultatet. Returneras bara om den ursprungliga texten matchade skiljde sig indatatexten. Används för att returnera källtext på ungefärliga matchningen. Inte returneras för Microsoft Translator resultat.
* `Rating`: Anger behörighet för den person som du beslutar kvalitet. Maskinöversättning resultat har betyget 5. Anonymt angivna översättningar har ofta en klassificering av 1 till 4 samtidigt auktoritärt angivna översättningar har vanligtvis en klassificering av 6-10.
* `Count`: Antalet gånger som denna översättning med denna klassificering har valts. Värdet ska vara 0 för automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.


### <a name="response-class-status-200"></a>Svaret klass (Status 200)

sträng

Svarets Content-Type: application/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertypen|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering  |(tom)    |Krävs om den `appid` fältet eller `Ocp-Apim-Subscription-Key` -sidhuvudet har inte angetts. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-Apim-prenumeration-nyckel|(tom)  |Krävs om den `appid` fältet eller `Authorization` -sidhuvudet har inte angetts.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och detaljerade felsvar.|
|401    |Ogiltiga autentiseringsuppgifter|
|500    |Serverfel. Om felet kvarstår kan berätta för oss. Ange oss med ungefärliga datum och tid för begäran och begärande-ID som ingår i rubriken `X-MS-Trans-Info`.|
|503    |Tjänsten är tillfälligt otillgänglig. Försök igen och kontakta oss om felet kvarstår.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till v3 översättare Text API](../migrate-to-v3.md)










