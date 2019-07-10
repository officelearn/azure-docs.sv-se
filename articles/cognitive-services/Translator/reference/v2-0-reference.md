---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: Referensdokumentation för Translator Text API-v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: a29e123c44ca198ce19db451ee4c624b6f993538
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705434"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> Den här versionen av Translator Text API är inaktuell. [Visa dokumentationen för version 3 av Translator Text API](v3-0-reference.md).

Version 2 av Translator Text API kan integreras smidigt i dina appar, webbplatser, verktyg eller andra lösningar som ger flerspråkig användarupplevelser. Du kan använda det på valfri maskinvaruplattform och i alla operativsystem som utför översättningar och andra språk-relaterade aktiviteter, till exempel text språkidentifiering och text till tal, enligt branschstandarder. Mer information finns i [Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Komma igång
Om du vill komma åt Translator Text API kan du behöva [registrera dig för Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autentisering 
Alla anrop till Translator Text API krävs en prenumerationsnyckel för autentisering. API: et stöder tre autentiseringsmetoder:

- En åtkomsttoken. Använd prenumerationsnyckel som refereras till i steg 9 för att skapa en åtkomsttoken genom att göra en POST-begäran till Autentiseringstjänsten. Se token service-dokumentationen för mer information. Skicka åtkomsttoken till Translator-tjänst med hjälp av den `Authorization` rubrik eller `access_token` frågeparameter. Åtkomsttoken är giltig i 10 minuter. Skaffa en ny åtkomsttoken var tionde minut och fortsätt att använda samma åtkomst-token för upprepade begäranden under 10 minuter.
- En prenumerationsnyckel som används direkt. Skicka din prenumerationsnyckel som ett värde i den `Ocp-Apim-Subscription-Key` rubrik som ingår i din begäran om att Translator Text API. När du använder prenumerationsnyckeln direkt kan behöver du inte anropa token authentication-tjänsten för att skapa en åtkomsttoken.
- En [flera tjänster Azure Cognitive Services-prenumeration](https://azure.microsoft.com/pricing/details/cognitive-services/). Den här metoden kan du använda en enda hemlig nyckel för att autentisera begäranden för flera tjänster.
När du använder en hemlig nyckel som har flera tjänster kan behöva du omfattar två autentiseringshuvuden din förfrågan. Rubriken första skickar den hemliga nyckeln. Andra rubriken anger den region som är associerade med din prenumeration:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Regionen måste anges för flera tjänster Text API-prenumeration. Den region som du väljer är den enda regionen som du kan använda för textöversättning när du använder flera tjänster prenumerationsnyckeln. Det måste vara i samma region som du valde när du registrerat dig för prenumerationen flera tjänster på Azure portal.

De tillgängliga regionerna är `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, och `westus2`.

Prenumerationsnyckel och åtkomst-token finns hemligheter som ska vara dold från vyn.

## <a name="profanity-handling"></a>Hantering av olämpligt språk
Normalt sett behåller tjänsten Translator svordomar som finns i källan. Graden av svordomar och kontext som gör ord olämpliga skiljer sig baserat på kultur. Graden av svordomar i målspråket kunde så ökas eller minskas.

Du kan använda svordomar filtrering för de metoder som stöder det. Om du vill förhindra svordomar i översättningen även när den är i källtext. Alternativet kan du välja om du vill se svordomar tagits bort eller markerats med lämpliga taggar, eller om du vill tillåta svordomar i målet. Godkända värden för `ProfanityAction` är `NoAction` (standard), `Marked`, och `Deleted`.


|ProfanityAction    |Åtgärd |Exempel källan (japanska)  |Exempel translation (på engelska)  |
|:--|:--|:--|:--|
|NoAction   |Standard. Samma som inte ange alternativet. Svordomar skickas från källan till målet.        |彼はジャッカスです。     |Han är en jackass.   |
|Markerad     |Olämpliga ord. ska omges av XML-taggar \<svordomar > och \</profanity >.       |彼はジャッカスです。 |Han är en \<svordomar > jackass\</profanity >.  |
|Borttaget    |Olämpliga ord. tas bort från utdata utan ersättning.     |彼はジャッカスです。 |Han är en.   |

    
## <a name="excluding-content-from-translation"></a>Exkludera innehåll från översättning
När du översätta innehåll med taggar som HTML (`contentType=text/html`), kan det vara praktiskt att utesluta översättningen specifikt innehåll. Du kan använda attributet `class=notranslate` och ange innehåll som ska vara på dess ursprungliga språk. I följande exempel innehållet i först `div` elementet inte översättas, men innehållet i andra `div` elementet kommer att översättas.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Implementeringsanteckningar
Omvandlar en textsträng från ett språk till ett annat.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Returvärdet:** En sträng som representerar den översatta texten.

Om du tidigare använt `AddTranslation` eller `AddTranslationArray` att ange en översättning med en klassificering på 5 eller högre för samma käll-mening `Translate` returnerar endast toppvalet som är tillgänglig i systemet. ”Samma källa mening” innebär att exakt den samma (100%-matchning), förutom versaler, blanksteg, taggvärden och skiljetecken i slutet av en mening. Om ingen klassificering lagras med en klassificering på 5 eller senare, kommer det returnerade resultatet att automatisk översättning av Microsoft Translator.

### <a name="response-class-status-200"></a>Svaret klass (status 200)

string

Svar: application/xml

### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning    |Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid  |(tom)    |Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|text|(tom)   |Krävs. En sträng som representerar text för översättning. Texten får inte innehålla fler än 10 000 tecken.|query|string|
|from|(tom)   |Valfri. En sträng som representerar språkkoden för den text som översätts. Till exempel en för engelska.|query|string|
|till|(tom) |Krävs. En sträng som representerar koden för språk att översätta text i.|query|string|
|contentType|(tom)    |Valfri. Formatet på den text som översätts. Format som stöds är `text/plain` (standard) och `text/html`. HTML-element måste vara korrekt strukturerad, fullständig element.|query|string|
|category|(tom)   |Valfri. En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är `general`.|query|string|
|Auktorisering|(tom)  |Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|


### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar översättningar för flera källa texter.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Här är formatet för förfrågans text:

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

Dessa element är i `TranslateArrayRequest`:


* `AppId`: Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `AppId` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Valfri. En sträng som representerar språkkoden för den text som översätts. Om fältet lämnas tomt används innehåller svaret resultatet av automatisk språkidentifiering.
* `Options`: Valfri. En `Options` objekt som innehåller följande värden. De är valfria och de vanligaste inställningarna som standard. Angivna elementen måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är `general`.
    - `ContentType`: Formatet på den text som översätts. Format som stöds är `text/plain` (standard), `text/xml`, och `text/html`. HTML-element måste vara korrekt strukturerad, fullständig element.
    - `ProfanityAction`: Anger hur profanities hanteras, enligt beskrivningen tidigare. Godkända värden är `NoAction` (standard), `Marked`, och `Deleted`.
    - `State`: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
    - `Uri`: Filtrera resultaten av den här URI: N. Standard: `all`.
    - `User`: Filtrera resultaten av den här användaren. Standard: `all`.
* `Texts`: Krävs. En matris som innehåller text för översättning. Alla strängar måste vara på samma språk. Summan av alla text för översättning får inte överstiga 10 000 tecken. Det maximala antalet matriselement är 2 000.
* `To`: Krävs. En sträng som representerar koden för språk att översätta text i.

Du kan utelämna valfria element. Element som är direkt underordnade till `TranslateArrayRequest` måste anges i alfabetisk ordning.

Den `TranslateArray` metoden godkänner `application/xml` eller `text/xml` för `Content-Type`.

**Returvärdet:** En `TranslateArrayResponse` matris. Varje `TranslateArrayResponse` har dessa element:

* `Error`: Anger ett fel om någon inträffar. Annars inställt på null.
* `OriginalSentenceLengths`: En matris med heltal som anger längden på varje mening i källtext. Längden på matrisen anger hur många meningar.
* `TranslatedText`: Den översatta texten.
* `TranslatedSentenceLengths`: En matris med heltal som anger längden på varje mening i den översatta texten. Längden på matrisen anger hur många meningar.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Returnerar samma innehåll som begäran.

Här är formatet för svarstexten:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

### <a name="response-class-status-200"></a>Svaret klass (status 200)
Ett lyckat svar innehåller en matris med `TranslateArrayResponse` matriser i formatet som beskrivs tidigare.

string

Svar: application/xml

### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|(tom)  |Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod   |Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret. Vanliga fel är: <ul><li>Matriselement måste anges.</li><li>Ogiltig kategori.</li><li>Från språk är ogiltigt.</li><li>Språk är ogiltigt.</li><li>Begäran innehåller för många element.</li><li>From-språk stöds inte.</li><li>Till språk stöds inte.</li><li>Översätt begäran har för mycket data.</li><li>HTML är inte i rätt format.</li><li>För många strängar skickades i översätta begäran.</li></ul>|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar egna namn för språk som har skickats in som parameter `languageCodes`lokaliserat till den överförda `locale` språk.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Begärandetexten innehåller en strängmatris som representerar ISO 639-1-språk-koder för att hämta de egna namn. Här är ett exempel:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Returvärdet:** En strängmatris som innehåller språknamn som stöds av tjänsten Translator lokaliserad till det begärda språket.

### <a name="response-class-status-200"></a>Svaret klass (status 200)
En strängmatris som innehåller namn på språk som stöds av tjänsten Translator lokaliserad till det begärda språket.

string

Svar: application/xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|Nationella inställningar|(tom) |Krävs. En sträng som representerar en av följande, som används för att lokalisera Språknamnen: <ul><li>Kombinationen av en ISO 639 två bokstäver gemener kultur-kod som är associerad med ett språk och en ISO 3166 två bokstäver versaler subkultur kod. <li>En gemen kultur-kod för ISO 639 ensamt.|query|string|
|Auktorisering|(tom)  |Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar en lista med språkkoder som representerar språk som stöds av tjänsten översättning.  `Translate` och `TranslateArray` kan översätta mellan två av dessa språk.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Returvärdet:** En strängmatris som innehåller språkkoder som stöds av tjänsten Translator.

### <a name="response-class-status-200"></a>Svaret klass (status 200)
En strängmatris som innehåller språkkoder som stöds av tjänsten Translator.

string

Svar: application/xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|Auktorisering|(tom)  |Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar språk som är tillgängliga för talsyntes.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Returvärdet:** En strängmatris som innehåller språkkoder som stöds för talsyntes av Translator-tjänsten.

### <a name="response-class-status-200"></a>Svaret klass (status 200)
En strängmatris som innehåller språkkoder som stöds för talsyntes av Translator-tjänsten.

string

Svar: application/xml

### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|
 
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401|Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Implementeringsanteckningar
Returnerar en WAV eller MP3-dataström med skickas i texten, sägs på önskat språk.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Returvärdet:** En WAV eller MP3-dataström med skickas i texten, sägs på önskat språk.

### <a name="response-class-status-200"></a>Svaret klass (status 200)

binary

Svar: application/xml

### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|text|(tom)   |Krävs. En sträng som innehåller en eller flera meningar som ska talas för strömmen i det angivna språket. Texten får inte överskrida 2 000 tecken.|query|string|
|language|(tom)   |Krävs. En sträng som representerar språkkod som stöds för det språk som du vill tala texten. Koden måste vara ett nummer som returneras av-metoden `GetLanguagesForSpeak`.|query|string|
|format|(tom)|Valfri. En sträng som anger innehållstypen-ID. För närvarande `audio/wav` och `audio/mp3` är tillgängliga. Standardvärdet är `audio/wav`.|query|string|
|options|(tom)    |Valfri. En sträng som anger egenskaperna för syntetiskt tal:<ul><li>`MaxQuality` och `MinSize` ange ljudsignalen kvalitet. `MaxQuality` ger högsta kvalitet. `MinSize` innehåller den minsta filstorleken. Standardvärdet är `MinSize`.</li><li>`female` och `male` ange önskad kön för röst. Standardvärdet är `female`. Använder vertikalstreck (<code>\|</code>) med flera alternativ. Till exempel `MaxQuality|Male`.</li></li></ul>  |query|string|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Implementeringsanteckningar
Identifierar språket i en del av texten.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Returvärdet:** En sträng som innehåller en två-teckens språkkod för texten.

### <a name="response-class-status-200"></a>Svaret klass (status 200)

string

Svar: application/xml

### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)  |Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|text|(tom)|Krävs. En sträng som innehåller text vars språk ska identifieras. Texten får inte överstiga 10 000 tecken.|query|  string|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key  |(tom)    |Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Implementeringsanteckningar

Identifierar språk i en matris med strängar. Oberoende identifierar språket för varje enskild matriselement och returnerar ett resultat för varje rad i matrisen.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Här är formatet för förfrågans text:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Texten får inte överstiga 10 000 tecken.

**Returvärdet:** En strängmatris som innehåller en två-teckens språkkod för varje rad i Indatamatrisen.

Här är formatet för svarstexten:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Svaret klass (status 200)
`DetectArray` lyckades. Returnerar en strängmatris som innehåller en två-teckens språkkod för varje rad i Indatamatrisen.

string

Svar: application/xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-addtranslation"></a>Hämta /AddTranslation

### <a name="implementation-notes"></a>Implementeringsanteckningar

> [!IMPORTANT]
> **Utfasningen Obs:** Den här metoden Acceptera inte nya mening bidrag efter den 31 januari 2018. Du får ett felmeddelande. Se ett meddelande om ändringarna till Collaborative Translation Framework (CTF).

Lägger till en translation translation-minne.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Svaret klass (status 200)

string

Svar: program: xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp   |
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(tom)|Krävs. En sträng som innehåller text för översättning. Den maximala längden på strängen är 1 000 tecken.|query|string|
|translatedText|(tom) |Krävs. En sträng som innehåller text översättas till mål-språk. Den maximala längden på strängen är 2 000 tecken.|query|string|
|from|(tom)   |Krävs. En sträng som representerar språkkoden för det ursprungliga språket i texten. Till exempel, en för engelska och de för tyska.|query|string|
|till|(tom)|Krävs. En sträng som representerar språkkod för språket som ska Översätt text i.|query|string|
|rating|(tom) |Valfri. Ett heltal som motsvarar kvalitet klassificering för strängen. Värdet är mellan -10 och 10. Standardvärdet är 1.|query|integer|
|contentType|(tom)    |Valfri. Formatet på den text som översätts. Format som stöds är `text/plain` och `text/html`. HTML-element måste vara korrekt strukturerad, fullständig element.    |query|string|
|category|(tom)|Valfri. En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är `general`.|query|string|
|Användare|(tom)|Krävs. En sträng som används för att spåra avsändaren av överföringen.|query|string|
|URI: N|(tom)|Valfri. En sträng som innehåller innehållsplats översättningens.|query|string|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.  |sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410|`AddTranslation` stöds inte längre.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Implementeringsanteckningar

> [!IMPORTANT]
> **Utfasningen Obs:** Den här metoden Acceptera inte nya mening bidrag efter den 31 januari 2018. Du får ett felmeddelande. Se ett meddelande om ändringarna till Collaborative Translation Framework (CTF).

Lägger till en matris med översättningar i översättningsminnen. Den här metoden är en matris version av `AddTranslation`.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Här är formatet för förfrågans text:

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

Dessa element är i `AddtranslationsRequest`:

* `AppId`: Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `AppId` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Krävs. En sträng som innehåller språkkod för språket som källa. Måste vara något av de språk som returneras av den `GetLanguagesForTranslate` metoden.
* `To`: Krävs. En sträng som innehåller språkkod för språket som mål. Måste vara något av de språk som returneras av den `GetLanguagesForTranslate` metoden.
* `Translations`: Krävs. En matris med översättningar för att lägga till i översättningsminnen. Varje översättning måste innehålla `OriginalText`, `TranslatedText`, och `Rating`. Den maximala storleken för varje `OriginalText` och `TranslatedText` är 1 000 tecken. Summan av alla `OriginalText` och `TranslatedText` element får inte överstiga 10 000 tecken. Det maximala antalet matriselement är 100.
* `Options`: Krävs. En uppsättning alternativ, inklusive `Category`, `ContentType`, `Uri`, och `User`. `User` krävs. `Category`, `ContentType`, och `Uri` är valfria. Angivna elementen måste anges i alfabetisk ordning.

### <a name="response-class-status-200"></a>Svaret klass (status 200)
`AddTranslationArray` metoden har slutförts. 

Efter den 31 januari 2018 godkännas mening bidrag inte. Tjänsten svarar med felkoden 410.

string

Svar: application/xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410    |`AddTranslation` stöds inte längre.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Implementeringsanteckningar
Delar upp ett textavsnitt i meningar och returnerar en matris som innehåller längden på varje mening.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Returvärdet:** En matris med heltal som representerar längden på meningarna. Längden på matrisen representerar antalet meningar. Värdena representerar längden på varje mening.

### <a name="response-class-status-200"></a>Svaret klass (status 200)
En matris med heltal som representerar längden på meningarna. Längden på matrisen representerar antalet meningar. Värdena representerar längden på varje mening.

integer

Svar: application/xml

### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)  |Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query| string|
|text|(tom)   |Krävs. En sträng som representerar texten som ska delas upp i meningar. Den maximala storleken på texten är 10 000 tecken.|query|string|
|language   |(tom)    |Krävs. En sträng som representerar språkkoden för den inmatade texten.|query|string|
|Auktorisering|(tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.   |sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)|Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400|Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401|Ogiltiga autentiseringsuppgifter.|
|500|Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar en matris med översättningar för ett visst språk par från arkivet och MT-motorn. `GetTranslations` skiljer sig från `Translate` i att den returnerar alla tillgängliga översättningar.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Brödtexten i begäran innehåller det valfria `TranslationOptions` objekt som har det här formatet:

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

Den `TranslateOptions` objektet innehåller värdena i listan nedan. De är valfria och de vanligaste inställningarna som standard. Angivna elementen måste anges i alfabetisk ordning.

* `Category`: En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är `general`.
* `ContentType`: Det är det enda alternativet som stöds och standard, `text/plain`.
* `IncludeMultipleMTAlternatives`: En boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden är `true` och `false` (skiftlägeskänsligt). Standardvärdet är `false`, vilket returnerar endast en alternativ. Ställa in flaggan `true` gör möljligt artificiella alternativ, helt integrerat med Collaborative Translation Framework (CTF). Med funktionen kan returnerande alternativ för meningar som har inga översättningar i CTF genom att lägga till artificiella alternativ från den *n*-bästa lista över avkodaren eller inte.
    - Klassificeringar. Klassificeringarna som används så här: 
         - Den bästa automatisk översättningen har en klassificering på 5.
       - Alternativ från CTF återspeglar utfärdaren av granskaren. De kan vara mellan -10 till + 10.
       - Den automatiskt genererade (*n*-bästa) translation alternativ har en klassificering på 0 och en matchning grad av 100.
    - Antal alternativ. Antalet returnerade alternativ kan vara så mycket som värdet som anges i `maxTranslations`, men det kan vara lägre.
    - Språkpar. Den här funktionen är inte tillgängligt för översättningar mellan kinesiska (förenklad) och traditionell kinesiska, i båda riktningarna. Det är tillgängligt för alla andra språkpar stöds av Microsoft Translator.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* `Uri`: Filtrera resultaten av den här URI: N. Om inget värde anges är standardvärdet `all`.
* `User`: Filtrera resultaten av den här användaren. Om inget värde anges är standardvärdet `all`.

Begär `Content-Type` ska vara `text/xml`.

**Returvärdet:** Här är formatet för svaret:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

Det här svaret innehåller en `GetTranslationsResponse` element som innehåller följande värden:

* `Translations`: En matris med matchningar hittas, lagras i `TranslationMatch` objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla liten varianter av den ursprungliga texten (partiell matchning). Översättningarna ska sorteras: 100% matchar först fuzzy matchningar bredvid.
* `From`: Om metoden inte anger en `From` språk, kommer det här värdet från automatisk språkidentifiering. Annars blir angivna `From` språk.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Innehåller värdet som angetts i den `TranslateOptions` parametern.

Den `TranslationMatch` består av följande värden:

* `Error`: Felkod, om ett fel inträffar för en specifik indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: Anger hur nära indatatexten matchar den ursprungliga texten finns i arkivet. Systemet matchar inkommande meningar mot butiken, inklusive inexakt matchningar. Värdet returneras adressintervallen från 0 till 100, där 0 är ingen likheter och 100 stämmer samma skiftlägeskänsliga.
* `MatchedOriginalText`: Originaltexten som var matchade för det här resultatet. Det här värdet returneras bara om den ursprungliga matchade texten var skiljer sig från den inmatade texten. Den används för att returnera en ungefärlig matchning källtext. Det här värdet returneras inte för Microsoft Translator resultat.
* `Rating`: Anger behörighet för den person som du beslutar kvalitet. Maskinöversättning resultat har en klassificering på 5. Anonymt angivna översättningar generellt sett ha betyget mellan 1 och 4. Auktoritärt angivna översättningar har vanligtvis en klassificering från 6 till och med 10.
* `Count`: Antal gånger som den här translation med denna klassificering har valts. Värdet är 0 för automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.

### <a name="response-class-status-200"></a>Svaret klass (status 200)
En `GetTranslationsResponse` objekt i formatet som beskrivs tidigare.

string

Svar: application/xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|appid|(tom)|Krävs. Om den `Authorization` eller `Ocp-Apim-Subscription-Key` huvud används, lämna den `appid` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.|query|string|
|text|(tom)|Krävs. En sträng som representerar text för översättning. Den maximala storleken på texten är 10 000 tecken.|query|string|
|from|(tom)|Krävs. En sträng som representerar språkkoden för den text som översätts.|query|string|
|till |(tom)    |Krävs. En sträng som representerar språkkod för språket som ska Översätt text i.|query|string|
|maxTranslations|(tom)|Krävs. Ett heltal som representerar det maximala antalet översättningar ska returneras.|query|integer|
|Auktorisering| (tom)|Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|string|  sidhuvud|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Implementeringsanteckningar
Hämtar flera translation kandidater för flera källa texter.

Förfrågans URI är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Här är formatet för förfrågans text:

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

* `AppId`: Krävs. Om den `Authorization` huvud används, lämna den `AppId` fält tomma. I annat fall är en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: Krävs. En sträng som representerar språkkoden för den text som översätts.
* `MaxTranslations`: Krävs. Ett heltal som representerar det maximala antalet översättningar ska returneras.
* `Options`: Valfri. En `Options` objekt som innehåller följande värden. De är valfria och de vanligaste inställningarna som standard. Angivna elementen måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller kategorin (domän) för översättningen. Standardvärdet är `general`.
    - `ContentType`: Det är det enda alternativet som stöds och standard, `text/plain`.
    - `IncludeMultipleMTAlternatives`: En boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden är `true` och `false` (skiftlägeskänsligt). Standardvärdet är `false`, vilket returnerar endast en alternativ. Ställa in flaggan `true` gör det möjligt för generering av artificiella alternativ i översättning, helt integrerat med samarbetsfunktioner översättningar Framework (CTF). Med funktionen kan returnerande alternativ för meningar som har inga alternativ i CTF genom att lägga till artificiella alternativ från den *n*-bästa lista över avkodaren eller inte.
        - Klassificeringarna klassificeringarna tillämpas så här:
          - Den bästa automatisk översättningen har en klassificering på 5.
          - Alternativ från CTF återspeglar utfärdaren av granskaren. De kan vara mellan -10 till + 10.
          - Den automatiskt genererade (*n*-bästa) translation alternativ har en klassificering på 0 och en matchning grad av 100.
        - Antal alternativ. Antalet returnerade alternativ kan vara så mycket som värdet som anges i `maxTranslations`, men det kan vara lägre.
        - Språkpar. Den här funktionen är inte tillgängligt för översättningar mellan kinesiska (förenklad) och traditionell kinesiska, i båda riktningarna. Det är tillgängligt för alla andra språkpar stöds av Microsoft Translator.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* `Uri`: Filtrera resultaten av den här URI: N. Om inget värde anges är standardvärdet `all`.
* `User`: Filtrera resultaten av den här användaren. Om inget värde anges är standardvärdet `all`.
* `Texts`: Krävs. En matris som innehåller text för översättning. Alla strängar måste vara på samma språk. Summan av alla text för översättning får inte överstiga 10 000 tecken. Det maximala antalet matriselement är 10.
* `To`: Krävs. En sträng som representerar språkkod för språket som ska Översätt text i.

Du kan utelämna valfria element. Element som är direkt underordnade till `GetTranslationsArrayRequest` måste anges i alfabetisk ordning.

Begär `Content-Type` ska vara `text/xml`.

**Returvärdet:** Här är formatet för svaret:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
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

Varje `GetTranslationsResponse` elementet innehåller dessa värden:

* `Translations`: En matris med matchningar hittas, lagras i `TranslationMatch` objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla liten varianter av den ursprungliga texten (partiell matchning). Översättningarna ska sorteras: 100% matchar först fuzzy matchningar bredvid.
* `From`: Om metoden inte anger en `From` språk, kommer det här värdet från automatisk språkidentifiering. Annars blir angivna `From` språk.
* `State`: Användarens tillstånd för att korrelera begäran och svaret. Innehåller värdet som angetts i den `TranslateOptions` parametern.

Den `TranslationMatch` objektet innehåller följande värden:
* `Error`: Felkod, om ett fel inträffar för en specifik indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: Anger hur nära indatatexten matchar den ursprungliga texten finns i arkivet. Systemet matchar inkommande meningar mot butiken, inklusive inexakt matchningar. Värdet returneras adressintervallen från 0 till 100, där 0 är ingen likheter och 100 stämmer samma skiftlägeskänsliga.
* `MatchedOriginalText`: Originaltexten som var matchade för det här resultatet. Det här värdet returneras bara om den ursprungliga matchade texten var skiljer sig från den inmatade texten. Den används för att returnera en ungefärlig matchning källtext. Det här värdet returneras inte för Microsoft Translator resultat.
* `Rating`: Anger behörighet för den person som du beslutar kvalitet. Maskinöversättning resultat har en klassificering på 5. Anonymt angivna översättningar generellt sett ha betyget mellan 1 och 4. Auktoritärt angivna översättningar generellt sett ha en klassificering från 6 till och med 10.
* `Count`: Antal gånger som den här translation med denna klassificering har valts. Värdet är 0 för automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.


### <a name="response-class-status-200"></a>Svaret klass (status 200)

string

Svar: application/xml
 
### <a name="parameters"></a>Parametrars

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering  |(tom)    |Krävs om både den `appid` fält och `Ocp-Apim-Subscription-Key` rubrik är tom.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|string|
|OCP-Apim-Subscription-Key|(tom)  |Krävs om både den `appid` fält och `Authorization` rubrik är tom.|sidhuvud|string|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Reason|
|:--|:--|
|400    |Felaktig begäran. Kontrollera indataparametrarna och det detaljerade felsvaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Om felet kvarstår, berätta för oss. Ge oss med ungefärliga datum och tid för begäran och med begäran-ID som ingår i svarshuvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och berätta för oss om felet kvarstår.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Translator Text API v3 ](../migrate-to-v3.md)


