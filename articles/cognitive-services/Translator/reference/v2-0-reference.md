---
title: Translator Text API v 2.0
titleSuffix: Azure Cognitive Services
description: Referens dokumentation för Translator Text API v 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242486"
---
# <a name="translator-text-api-v20"></a>Translator Text API v 2.0

> [!IMPORTANT]
> Den här versionen av Translator Text API är föråldrad. [Visa dokumentation för version 3 av Translator text API](v3-0-reference.md).

Version 2 av Translator Text API kan integreras sömlöst i appar, webbplatser, verktyg eller andra lösningar för att tillhandahålla användar upplevelser med flera språk. Du kan använda den på valfri maskin varu plattform och med alla operativ system för att utföra översättning av språk och andra språkbaserade uppgifter, t. ex. text språk identifiering och text till tal, enligt bransch standarder. Mer information finns i [Translator text API](../translator-info-overview.md).

## <a name="getting-started"></a>Komma igång
För att få åtkomst till Translator Text API måste du [Registrera dig för Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autentisering 
Alla anrop till Translator Text API kräver en prenumerations nyckel för autentisering. API: et stöder tre metoder för autentisering:

- En åtkomsttoken. Använd prenumerations nyckeln för att skapa en åtkomsttoken genom att göra en POST-begäran till Autentiseringstjänsten. Mer information finns i dokumentationen för token service. Skicka åtkomsttoken till Translator-tjänsten med hjälp av `Authorization`-huvudet eller parametern `access_token`. Åtkomsttoken är giltig i 10 minuter. Hämta en ny åtkomsttoken var tionde minut och fortsätt att använda samma åtkomsttoken för upprepade begär Anden under 10 minuter.
- En prenumerations nyckel som används direkt. Skicka prenumerations nyckeln som ett värde i `Ocp-Apim-Subscription-Key`-rubriken som ingår i din begäran till Translator Text API. När du använder prenumerations nyckeln direkt behöver du inte anropa tjänsten token Authentication för att skapa en åtkomsttoken.
- En [Azure Cognitive Services-prenumeration med flera tjänster](https://azure.microsoft.com/pricing/details/cognitive-services/). Med den här metoden kan du använda en enskild hemlig nyckel för att autentisera begär Anden för flera tjänster.
När du använder en hemlig nyckel för flera tjänster måste du inkludera två autentiseringsscheman med din begäran. Den första rubriken skickar den hemliga nyckeln. I den andra rubriken anges den region som är kopplad till din prenumeration:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Regionen krävs för API-prenumerationen för flera service text. Den region du väljer är den enda region som du kan använda för text översättning när du använder prenumerations nyckeln för flera tjänster. Det måste vara samma region som du valde när du registrerade dig för en prenumeration på flera tjänster på Azure Portal.

De tillgängliga regionerna är `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, 0, 1, 2, 3, 4, 5 och 6.

Din prenumerations nyckel och åtkomsttoken är hemligheter som ska döljas i vyn.

## <a name="profanity-handling"></a>Hantering av svordomar
Översättnings tjänsten behåller vanligt vis svordomar som finns i källan. Graden av svordomar och kontexten som gör ord svordomar olika beroende på kulturen. Det går att öka eller minska antalet svordomar på mål språket.

Om du vill förhindra svordomar i översättningen även om den är i käll texten, kan du använda alternativet för att filtrera efter svordomar för de metoder som har stöd för det. Med alternativet kan du välja om du vill se hur svordomar tas bort eller markeras med lämpliga taggar, eller om du vill tillåta svordomarna i målet. De godkända värdena för `ProfanityAction` är `NoAction` (standard), `Marked` och `Deleted`.


|ProfanityAction    |Åtgärd |Exempel källa (japanska)  |Exempel översättning (engelska)  |
|:--|:--|:--|:--|
|Noaction   |Standard. Samma som att inte ställa in alternativet. Svordomar skickas från källa till mål.        |彼はジャッカスです från en omfattande     |Han är en Jackass.   |
|Klassificera     |Svordomar ord omges av XML-taggar \<profanity > och \</svordoms >.       |彼はジャッカスです från en omfattande |Han är en \<profanity > Jackass @ no__t-1/svordoms >.  |
|Borttaget    |Svordomar ord tas bort från utdata utan ersättning.     |彼はジャッカスです från en omfattande |Han är en.   |

    
## <a name="excluding-content-from-translation"></a>Undanta innehåll från översättning
När du översätter innehåll med taggar som HTML (`contentType=text/html`) är det ibland användbart att undanta ett särskilt innehåll från översättningen. Du kan använda attributet `class=notranslate` för att ange innehåll som ska finnas kvar på det ursprungliga språket. I följande exempel kommer innehållet i det första `div`-elementet inte att översättas, men innehållet i det andra `div`-elementet översätts.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>Hämta/translate

### <a name="implementation-notes"></a>Implementerings anteckningar
Översätter en text sträng från ett språk till ett annat.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**RETUR värde:** En sträng som representerar den översatta texten.

Om du tidigare använde `AddTranslation` eller `AddTranslationArray` för att ange en översättning med en klassificering på 5 eller högre för samma käll mening returnerar `Translate` bara det bästa alternativet som är tillgängligt för ditt system. "Samma käll mening" betyder exakt samma (100% matchning), förutom versaler, blank steg, tagg värden och interpunktion i slutet av en mening. Om ingen klassificering lagras med en klassificering på 5 eller högre blir det returnerade resultatet den automatiska översättningen av Microsoft Translator.

### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning    |Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta  |saknas    |Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|saknas   |Krävs. En sträng som representerar texten som ska översättas. Texten får inte innehålla mer än 10 000 tecken.|DocumentDB|sträng|
|som|saknas   |Valfri. En sträng som representerar språk koden för den text som ska översättas. Till exempel en för engelska.|DocumentDB|sträng|
|till|saknas |Krävs. En sträng som representerar koden för det språk som texten ska översättas till.|DocumentDB|sträng|
|Innehålls|saknas    |Valfri. Formatet på texten som översätts. Format som stöds är `text/plain` (standard) och `text/html`. Alla HTML-element måste vara välformulerade, kompletta element.|DocumentDB|sträng|
|category|saknas   |Valfri. En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.|DocumentDB|sträng|
|Autentisering|saknas  |Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas  |Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|


### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-translatearray"></a>PUBLICERA/TranslateArray

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar översättningar för flera käll texter.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Här är formatet för begär ande texten:

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


* `AppId`: krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `AppId` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: valfritt. En sträng som representerar språk koden för den text som ska översättas. Om det här fältet lämnas tomt kommer svaret att innehålla resultatet av automatisk språk identifiering.
* `Options`: valfritt. Ett `Options`-objekt som innehåller följande värden. De är valfria och standard för de vanligaste inställningarna. De angivna elementen måste anges i alfabetisk ordning.
    - `Category`: en sträng som innehåller en kategori (domän) för översättningen. Standardvärdet är `general`.
    - `ContentType`: formatet på texten som översätts. De format som stöds är `text/plain` (standard), `text/xml` och `text/html`. Alla HTML-element måste vara välformulerade, kompletta element.
    - `ProfanityAction`: anger hur svordomar hanteras, vilket förklaras tidigare. Godkända värden är `NoAction` (standard), `Marked` och `Deleted`.
    - `State`: användar tillstånd för att hjälpa till att korrelera begäran och svaret. Samma innehåll kommer att returneras i svaret.
    - `Uri`: filtrera resultat efter denna URI. Standard: `all`.
    - `User`: filtrera resultat efter den här användaren. Standard: `all`.
* `Texts`: krävs. En matris som innehåller texten för översättning. Alla strängar måste vara på samma språk. Summan av all text som ska översättas får inte överstiga 10 000 tecken. Det maximala antalet mat ris element är 2 000.
* `To`: krävs. En sträng som representerar koden för det språk som texten ska översättas till.

Du kan utelämna valfria element. Element som är direkt underordnade till `TranslateArrayRequest` måste anges i alfabetisk ordning.

Metoden `TranslateArray` accepterar `application/xml` eller `text/xml` för `Content-Type`.

**RETUR värde:** En `TranslateArrayResponse`-matris. Varje `TranslateArrayResponse` har följande element:

* `Error`: anger ett fel om ett inträffar. Annars anges värdet null.
* `OriginalSentenceLengths`: en matris med heltal som anger längden på varje mening i käll texten. Längden på matrisen anger antalet meningar.
* `TranslatedText`: den översatta texten.
* `TranslatedSentenceLengths`: en matris med heltal som anger längden på varje mening i den översatta texten. Längden på matrisen anger antalet meningar.
* `State`: användar tillstånd för att hjälpa till att korrelera begäran och svaret. Returnerar samma innehåll som begäran.

Här är formatet för svars texten:

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

### <a name="response-class-status-200"></a>Svars klass (status 200)
Ett lyckat svar innehåller en matris med `TranslateArrayResponse`-matriser i det format som beskrivs ovan.

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|Autentisering|saknas  |Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod   |Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret. Vanliga fel är: <ul><li>Mat ris element får inte vara tomt.</li><li>Ogiltig kategori.</li><li>Från-språket är ogiltigt.</li><li>Till språk är ogiltigt.</li><li>Begäran innehåller för många element.</li><li>Från-språket stöds inte.</li><li>To-språket stöds inte.</li><li>Översättnings förfrågan har för mycket data.</li><li>HTML har felaktigt format.</li><li>För många strängar skickades i översättnings förfrågan.</li></ul>|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-getlanguagenames"></a>PUBLICERA/GetLanguageNames

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar egna namn för de språk som har skickats in som parametern `languageCodes`, lokaliserade till det skickade `locale`-språket.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

Begär ande texten innehåller en sträng mat ris som representerar de ISO 639-1-språkkoder för vilka du kan hämta de egna namnen. Här är ett exempel:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**RETUR värde:** En sträng mat ris som innehåller språk namn som stöds av Translator-tjänsten och som är lokaliserad till det begärda språket.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En sträng mat ris som innehåller språk namn som stöds av Translator-tjänsten och som lokaliseras till det begärda språket.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|språk|saknas |Krävs. En sträng som representerar något av följande, som används för att lokalisera språk namn: <ul><li>Kombinationen av en ISO 639 2-bokstav med gemener, som är associerad med ett språk och en ISO 3166 2-bokstav med versaler i under kultur kod. <li>En ISO 639-kultur med gemener.|DocumentDB|sträng|
|Autentisering|saknas  |Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas  |Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-getlanguagesfortranslate"></a>Hämta/GetLanguagesForTranslate

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar en lista med språk koder som representerar språk som stöds av översättnings tjänsten.  `Translate` och `TranslateArray` kan översätta mellan två av dessa språk.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**RETUR värde:** En sträng mat ris som innehåller de språk koder som stöds av Translator-tjänsten.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En sträng mat ris som innehåller de språk koder som stöds av Translator-tjänsten.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Autentisering|saknas  |Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-getlanguagesforspeak"></a>Hämta/GetLanguagesForSpeak

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar de språk som är tillgängliga för tal syntes.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**RETUR värde:** En sträng mat ris som innehåller de språk koder som stöds för tal syntes av tjänsten Translator.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En sträng mat ris som innehåller de språk koder som stöds för tal syntes av tjänsten Translator.

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|
 
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401|Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-speak"></a>Hämta/Speak

### <a name="implementation-notes"></a>Implementerings anteckningar
Returnerar en WAV-eller MP3-ström av den inskickade texten som talas på det önskade språket.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**RETUR värde:** En WAV-eller MP3-ström av den inskickade texten som talas på det önskade språket.

### <a name="response-class-status-200"></a>Svars klass (status 200)

binär

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|saknas   |Krävs. En sträng som innehåller en eller flera meningar som ska läsas upp för data strömmen på det angivna språket. Texten får inte överstiga 2 000 tecken.|DocumentDB|sträng|
|language|saknas   |Krävs. En sträng som representerar språk koden som stöds för det språk som texten ska mätas i. Koden måste vara en av de koder som returnerades av metoden `GetLanguagesForSpeak`.|DocumentDB|sträng|
|formatering|saknas|Valfri. En sträng som anger ID för innehålls typ. För närvarande är `audio/wav` och `audio/mp3` tillgängliga. Standardvärdet är `audio/wav`.|DocumentDB|sträng|
|Sätt|saknas    |Valfri. En sträng som anger egenskaper för det syntetiskt tal:<ul><li>`MaxQuality` och `MinSize` anger ljud signalens kvalitet. `MaxQuality` ger högsta kvalitet. `MinSize` ger den minsta fil storleken. Standardvärdet är `MinSize`.</li><li>`female` och `male` anger röstens önskade kön. Standardvärdet är `female`. Använd det lodräta fältet (<code>\|</code>) om du vill inkludera flera alternativ. Till exempel `MaxQuality|Male`.</li></li></ul>  |DocumentDB|sträng|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas  |Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-detect"></a>Hämta/detect

### <a name="implementation-notes"></a>Implementerings anteckningar
Identifierar språket för en del av texten.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**RETUR värde:** En sträng som innehåller en språk kod på två tecken för texten.

### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas  |Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|saknas|Krävs. En sträng som innehåller text vars språk ska identifieras. Texten får inte överstiga 10 000 tecken.|DocumentDB|  sträng|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key  |saknas    |Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|


## <a name="post-detectarray"></a>PUBLICERA/DetectArray

### <a name="implementation-notes"></a>Implementerings anteckningar

Identifierar språken i en sträng mat ris. Identifierar oberoende språket för varje enskilt mat ris element och returnerar ett resultat för varje rad i matrisen.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Här är formatet för begär ande texten:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Texten får inte överstiga 10 000 tecken.

**RETUR värde:** En sträng mat ris som innehåller en språk kod på två tecken för varje rad i den angivna matrisen.

Här är formatet för svars texten:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Svars klass (status 200)
`DetectArray` har slutförts. Returnerar en sträng mat ris som innehåller en språk kod på två tecken för varje rad i den angivna matrisen.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-addtranslation"></a>Hämta/AddTranslation

### <a name="implementation-notes"></a>Implementerings anteckningar

> [!IMPORTANT]
> **Utfasnings anteckning:** Den här metoden accepterar inte nya menings bidrag efter den 31 januari 2018. Du får ett fel meddelande. Se meddelandet om ändringar i samarbets översättnings ramverket (CTF).

Lägger till en översättning till översättnings minnet.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: program: XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp   |
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|originalText|saknas|Krävs. En sträng som innehåller den text som ska översättas. Den maximala längden på strängen är 1 000 tecken.|DocumentDB|sträng|
|translatedText|saknas |Krävs. En sträng som innehåller text som har översatts till mål språket. Den maximala längden på strängen är 2 000 tecken.|DocumentDB|sträng|
|som|saknas   |Krävs. En sträng som representerar språk koden för textens ursprungliga språk. Till exempel en för engelska och de för tyska.|DocumentDB|sträng|
|till|saknas|Krävs. En sträng som representerar språk koden för det språk som texten ska översättas till.|DocumentDB|sträng|
|rating|saknas |Valfri. Ett heltal som representerar strängens kvalitets betyg. Värdet är mellan-10 och 10. Standardvärdet är 1.|DocumentDB|heltal|
|Innehålls|saknas    |Valfri. Formatet på texten som översätts. De format som stöds är `text/plain` och `text/html`. Alla HTML-element måste vara välformulerade, kompletta element.    |DocumentDB|sträng|
|category|saknas|Valfri. En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.|DocumentDB|sträng|
|Användarvänlig|saknas|Krävs. En sträng som används för att spåra upphovs mannen för överföringen.|DocumentDB|sträng|
|URI|saknas|Valfri. En sträng som innehåller översättningens innehålls plats.|DocumentDB|sträng|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.  |sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410|`AddTranslation` stöds inte längre.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-addtranslationarray"></a>PUBLICERA/AddTranslationArray

### <a name="implementation-notes"></a>Implementerings anteckningar

> [!IMPORTANT]
> **Utfasnings anteckning:** Den här metoden accepterar inte nya menings bidrag efter den 31 januari 2018. Du får ett fel meddelande. Se meddelandet om ändringar i samarbets översättnings ramverket (CTF).

Lägger till en matris med översättningar i översättnings minnet. Den här metoden är en mat ris version av `AddTranslation`.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Här är formatet för begär ande texten:

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

* `AppId`: krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `AppId` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: krävs. En sträng som innehåller språk koden för käll språket. Måste vara ett av de språk som returnerades av metoden `GetLanguagesForTranslate`.
* `To`: krävs. En sträng som innehåller språk koden för mål språket. Måste vara ett av de språk som returnerades av metoden `GetLanguagesForTranslate`.
* `Translations`: krävs. En matris med översättningar som ska läggas till i översättnings minnet. Varje översättning måste innehålla `OriginalText`, `TranslatedText` och `Rating`. Den maximala storleken för varje `OriginalText` och `TranslatedText` är 1 000 tecken. Summan av alla `OriginalText`-och `TranslatedText`-element får inte överstiga 10 000 tecken. Det maximala antalet mat ris element är 100.
* `Options`: krävs. En uppsättning alternativ, inklusive `Category`, `ContentType`, `Uri` och `User`. `User` krävs. `Category`, `ContentType` och `Uri` är valfria. De angivna elementen måste anges i alfabetisk ordning.

### <a name="response-class-status-200"></a>Svars klass (status 200)
metoden `AddTranslationArray` lyckades. 

Efter den 31 januari 2018 kommer menings bidrag inte att accepteras. Tjänsten svarar med felkoden 410.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410    |`AddTranslation` stöds inte längre.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="get-breaksentences"></a>Hämta/BreakSentences

### <a name="implementation-notes"></a>Implementerings anteckningar
Delar upp en del av texten i meningar och returnerar en matris som innehåller längden på varje mening.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**RETUR värde:** En matris med heltal som representerar längden på meningarna. Längden på matrisen representerar antalet meningar. Värdena representerar längden på varje mening.

### <a name="response-class-status-200"></a>Svars klass (status 200)
En matris med heltal som representerar längden på meningarna. Längden på matrisen representerar antalet meningar. Värdena representerar längden på varje mening.

heltal

Svars innehålls typ: Application/XML

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas  |Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB| sträng|
|text|saknas   |Krävs. En sträng som representerar texten som ska delas upp i meningar. Den maximala storleken på texten är 10 000 tecken.|DocumentDB|sträng|
|language   |saknas    |Krävs. En sträng som representerar språk koden för inmatad text.|DocumentDB|sträng|
|Autentisering|saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.   |sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas|Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401|Ogiltiga autentiseringsuppgifter.|
|500|Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-gettranslations"></a>PUBLICERA/GetTranslations

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar en matris med översättningar för ett angivet språk par från Store och MT-motorn. `GetTranslations` skiljer sig från `Translate` i så att alla tillgängliga översättningar returneras.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

Bröd texten i begäran omfattar det valfria `TranslationOptions`-objektet, som har det här formatet:

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

Objektet `TranslateOptions` innehåller värdena i följande lista. De är valfria och standard för de vanligaste inställningarna. De angivna elementen måste anges i alfabetisk ordning.

* `Category`: en sträng som innehåller en kategori (domän) för översättningen. Standardvärdet är `general`.
* `ContentType`: det enda alternativ som stöds och standardvärdet är `text/plain`.
* `IncludeMultipleMTAlternatives`: en boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden är `true` och `false` (Skift läges känsligt). Standardvärdet är `false`, som bara returnerar ett alternativ. Om du ställer in flaggan på `true` kan du skapa artificiella alternativ som är helt integrerade med CTF (Collaborative Translation Framework). Funktionen gör det möjligt att returnera alternativ för meningar som inte har några översättningar i CTF genom att lägga till artificiella alternativ från den *n*-bästa listan över avkodaren.
    - Drivande. Klassificeringen tillämpas så här: 
         - Den bästa automatiska översättningen har en klassificering på 5.
       - Alternativen från CTF reflekterar behörigheten hos granskaren. De sträcker sig från-10 till + 10.
       - De automatiskt genererade översättnings alternativen (*n*-Best) har en klassificering på 0 och en matchnings grad på 100.
    - Antal alternativ. Antalet returnerade alternativ kan vara lika högt som det värde som anges i `maxTranslations`, men det kan vara lägre.
    - Språk par. Den här funktionen är inte tillgänglig för översättningar mellan förenklad kinesiska och traditionell kinesiska, i båda riktningarna. Den är tillgänglig för alla andra språk par som stöds av Microsoft Translator.
* `State`: användar tillstånd för att hjälpa till att korrelera begäran och svaret. Samma innehåll kommer att returneras i svaret.
* `Uri`: filtrera resultat efter denna URI. Om inget värde anges är standardvärdet `all`.
* `User`: filtrera resultat efter den här användaren. Om inget värde anges är standardvärdet `all`.

Begäran `Content-Type` bör vara `text/xml`.

**RETUR värde:** Här är svars formatet:

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

Det här svaret innehåller ett `GetTranslationsResponse`-element som innehåller följande värden:

* `Translations`: en matris med matchningar som hittats lagras i `TranslationMatch`-objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla små varianter av den ursprungliga texten (fuzzy Matching). Översättningarna sorteras: 100% matchar först och fuzzy matchar härnäst.
* `From`: om metoden inte anger ett `From` språk kommer det här värdet att komma från automatisk språk identifiering. Annars är det angivna `From`-språket.
* `State`: användar tillstånd för att hjälpa till att korrelera begäran och svaret. Innehåller värdet som anges i parametern `TranslateOptions`.

Objektet `TranslationMatch` består av följande värden:

* `Error`: felkoden, om det uppstår ett fel för en angiven indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: anger hur nära inmatad text som matchar den ursprungliga texten som finns i butiken. Systemet matchar inmatade meningar mot butiken, inklusive inexakta matchningar. Värdet returnerade intervall från 0 till 100, där 0 inte är något likhets värde och 100 är en exakt Skift läges känslig matchning.
* `MatchedOriginalText`: ursprunglig text som har matchats för det här resultatet. Det här värdet returneras endast om den matchade ursprungliga texten skiljer sig från inmatad text. Den används för att returnera käll texten för en Fuzzy-matchning. Värdet returneras inte för Microsoft Translator-resultat.
* `Rating`: anger utfärdaren för den person som fattar kvalitets beslutet. Maskin översättnings resultat har en klassificering på 5. Anonymt tillhandahållna översättningar har vanligt vis en klassificering från 1 till 4. Översättning av auktoritativt tillhandahållna i allmänhet har en klassificering från 6 till och med 10.
* `Count`: antalet gånger som översättningen med den här klassificeringen har marker ATS. Värdet är 0 för det automatiskt översatta svaret.
* `TranslatedText`: den översatta texten.

### <a name="response-class-status-200"></a>Svars klass (status 200)
Ett `GetTranslationsResponse`-objekt i det format som beskrivs ovan.

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|undanta|saknas|Krävs. Om `Authorization`-eller `Ocp-Apim-Subscription-Key`-huvudet används lämnar du fältet `appid` tomt. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.|DocumentDB|sträng|
|text|saknas|Krävs. En sträng som representerar texten som ska översättas. Den maximala storleken på texten är 10 000 tecken.|DocumentDB|sträng|
|som|saknas|Krävs. En sträng som representerar språk koden för den text som ska översättas.|DocumentDB|sträng|
|till |saknas    |Krävs. En sträng som representerar språk koden för det språk som texten ska översättas till.|DocumentDB|sträng|
|maxTranslations|saknas|Krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.|DocumentDB|heltal|
|Autentisering| saknas|Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma. Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sträng|  sidhuvud|
|OCP-APIM-Subscription-Key|saknas  |Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="post-gettranslationsarray"></a>PUBLICERA/GetTranslationsArray

### <a name="implementation-notes"></a>Implementerings anteckningar
Hämtar flera käll texter för olika översättnings kandidater.

URI för begäran är `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Här är formatet för begär ande texten:

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

* `AppId`: krävs. Lämna fältet `AppId` tomt om `Authorization`-huvudet används. Annars inkluderar du en sträng som innehåller `"Bearer" + " " + "access_token"`.
* `From`: krävs. En sträng som representerar språk koden för den text som ska översättas.
* `MaxTranslations`: krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.
* `Options`: valfritt. Ett `Options`-objekt som innehåller följande värden. De är valfria och standard för de vanligaste inställningarna. De angivna elementen måste anges i alfabetisk ordning.
    - `Category`: en sträng som innehåller en kategori (domän) för översättningen. Standardvärdet är `general`.
    - `ContentType`: det enda alternativ som stöds och standardvärdet är `text/plain`.
    - `IncludeMultipleMTAlternatives`: en boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden är `true` och `false` (Skift läges känsligt). Standardvärdet är `false`, som bara returnerar ett alternativ. Om du ställer in flaggan på `true` kan du skapa artificiella alternativ i översättning, helt integrerat med samarbets översättnings ramverket (CTF). Funktionen gör det möjligt att returnera alternativ för meningar som inte har några alternativ i CTF genom att lägga till artificiella alternativ från den *n*-bästa listan över avkodaren.
        - Klassificeringen tillämpas på följande sätt:
          - Den bästa automatiska översättningen har en klassificering på 5.
          - Alternativen från CTF reflekterar behörigheten hos granskaren. De sträcker sig från-10 till + 10.
          - De automatiskt genererade översättnings alternativen (*n*-Best) har en klassificering på 0 och en matchnings grad på 100.
        - Antal alternativ. Antalet returnerade alternativ kan vara lika högt som det värde som anges i `maxTranslations`, men det kan vara lägre.
        - Språk par. Den här funktionen är inte tillgänglig för översättningar mellan förenklad kinesiska och traditionell kinesiska, i båda riktningarna. Den är tillgänglig för alla andra språk par som stöds av Microsoft Translator.
* `State`: användar tillstånd för att hjälpa till att korrelera begäran och svaret. Samma innehåll kommer att returneras i svaret.
* `Uri`: filtrera resultat efter denna URI. Om inget värde anges är standardvärdet `all`.
* `User`: filtrera resultat efter den här användaren. Om inget värde anges är standardvärdet `all`.
* `Texts`: krävs. En matris som innehåller texten för översättning. Alla strängar måste vara på samma språk. Summan av all text som ska översättas får inte överstiga 10 000 tecken. Det maximala antalet mat ris element är 10.
* `To`: krävs. En sträng som representerar språk koden för det språk som texten ska översättas till.

Du kan utelämna valfria element. Element som är direkt underordnade till `GetTranslationsArrayRequest` måste anges i alfabetisk ordning.

Begäran `Content-Type` bör vara `text/xml`.

**RETUR värde:** Här är svars formatet:

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

Varje `GetTranslationsResponse`-element innehåller följande värden:

* `Translations`: en matris med matchningar som hittats lagras i `TranslationMatch`-objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla små varianter av den ursprungliga texten (fuzzy Matching). Översättningarna sorteras: 100% matchar först och fuzzy matchar härnäst.
* `From`: om metoden inte anger ett `From` språk kommer det här värdet att komma från automatisk språk identifiering. Annars är det angivna `From`-språket.
* `State`: användar tillstånd för att hjälpa till att korrelera begäran och svaret. Innehåller värdet som anges i parametern `TranslateOptions`.

@No__t-0-objektet innehåller följande värden:
* `Error`: felkoden, om det uppstår ett fel för en angiven indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: anger hur nära inmatad text som matchar den ursprungliga texten som finns i butiken. Systemet matchar inmatade meningar mot butiken, inklusive inexakta matchningar. Värdet returnerade intervall från 0 till 100, där 0 inte är något likhets värde och 100 är en exakt Skift läges känslig matchning.
* `MatchedOriginalText`: ursprunglig text som har matchats för det här resultatet. Det här värdet returneras endast om den matchade ursprungliga texten skiljer sig från inmatad text. Den används för att returnera käll texten för en Fuzzy-matchning. Värdet returneras inte för Microsoft Translator-resultat.
* `Rating`: anger utfärdaren för den person som fattar kvalitets beslutet. Maskin översättnings resultat har en klassificering på 5. Anonymt tillhandahållna översättningar har vanligt vis en klassificering från 1 till 4. Auktoritärt tillhandahållna översättningar har vanligt vis en klassificering från 6 till och med 10.
* `Count`: antalet gånger som översättningen med den här klassificeringen har marker ATS. Värdet är 0 för det automatiskt översatta svaret.
* `TranslatedText`: den översatta texten.


### <a name="response-class-status-200"></a>Svars klass (status 200)

sträng

Svars innehålls typ: Application/XML
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parameter typ|Datatyp|
|:--|:--|:--|:--|:--|
|Autentisering  |saknas    |Krävs om både fältet `appid` och rubriken `Ocp-Apim-Subscription-Key` lämnas tomma.  Autentiseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|OCP-APIM-Subscription-Key|saknas  |Krävs om både fältet `appid` och rubriken `Authorization` lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Felaktig begäran. Kontrol lera indataparametrarna och det detaljerade fel svaret.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Server fel. Om felet kvarstår kan du berätta för oss. Ange ett ungefärligt datum & tiden för begäran och med det ID för begäran som ingår i svars huvudet `X-MS-Trans-Info`.|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och låt oss veta om felet kvarstår.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Translator Text API v3](../migrate-to-v3.md)


