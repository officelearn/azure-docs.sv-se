---
title: Översättare Text API v2.0
titleSuffix: Azure Cognitive Services
description: Referensdokumentation för Translator Text API v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242486"
---
# <a name="translator-text-api-v20"></a>Översättare Text API v2.0

> [!IMPORTANT]
> Den här versionen av Translator Text API har inaktuell. [Visa dokumentation för version 3 av Translator Text API](v3-0-reference.md).

Version 2 av Translator Text API kan integreras sömlöst i dina appar, webbplatser, verktyg eller andra lösningar för att ge flerspråkiga användarupplevelser. Du kan använda den på valfri maskinvaruplattform och med alla operativsystem för att utföra språköversättning och andra språkrelaterade uppgifter, som textspråksidentifiering och text till tal, enligt branschstandarder. Mer information finns i [Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Komma igång
Om du vill komma åt Translator Text API måste du [registrera dig för Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autentisering 
Alla anrop till Translator Text API kräver en prenumerationsnyckel för autentisering. API:et stöder tre autentiseringsmetoder:

- En åtkomsttoken. Använd prenumerationsnyckeln för att skapa en åtkomsttoken genom att göra en POST-begäran till autentiseringstjänsten. Mer information finns i dokumentationen till tokentjänsten. Skicka åtkomsttoken till översättaretjänsten `Authorization` med hjälp `access_token` av huvudet eller frågeparametern. Åtkomsttoken är giltig i 10 minuter. Hämta en ny åtkomsttoken var 10:e minut och fortsätt använda samma åtkomsttoken för upprepade begäranden under de 10 minuterna.
- En prenumerationsnyckel som används direkt. Skicka din prenumerationsnyckel som `Ocp-Apim-Subscription-Key` ett värde i huvudet som ingår i din begäran till Translator Text API. När du använder prenumerationsnyckeln direkt behöver du inte anropa tokenautentiseringstjänsten för att skapa en åtkomsttoken.
- En [Azure Cognitive Services-prenumeration med flera tjänster](https://azure.microsoft.com/pricing/details/cognitive-services/). Med den här metoden kan du använda en enda hemlig nyckel för att autentisera begäranden för flera tjänster.
När du använder en hemlig nyckel för flera tjänster måste du inkludera två autentiseringshuvuden med din begäran. Den första rubriken passerar den hemliga nyckeln. Det andra huvudet anger den region som är associerad med din prenumeration:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

Regionen krävs för text-API-prenumerationen med flera tjänster. Den region du väljer är den enda region som du kan använda för textöversättning när du använder prenumerationsnyckeln för flera tjänster. Det måste vara samma region som du valde när du registrerade dig för din prenumeration med flera tjänster på Azure-portalen.

De tillgängliga `australiaeast`områdena `brazilsouth` `canadacentral`är `centralindia` `centraluseuap`, `eastasia` `eastus`, `eastus2` `japaneast`, `northeurope` `southcentralus`, `southeastasia` `uksouth`, `westcentralus` `westeurope`, `westus`, `westus2`, , , , , , , , , och .

Din prenumerationsnyckel och åtkomsttoken är hemligheter som ska döljas.

## <a name="profanity-handling"></a>Svordomar hantering
Normalt behåller översättaretjänsten svordomar som finns i källan. Graden av svordomar och det sammanhang som gör ord profana skiljer sig åt beroende på kultur. Så graden av svordomar i målspråket kan ökas eller minskas.

Om du vill förhindra svordomar i översättningen även när det finns i källtexten kan du använda filtreringsalternativet svordomar för de metoder som stöder den. Med alternativet kan du välja om du vill se svordomar borttagna eller markerade med lämpliga taggar, eller om du vill tillåta svordomar i målet. De godkända `ProfanityAction` värdena `NoAction` är `Marked`(standard), och `Deleted`.


|SvordomarÅtgärder    |Åtgärd |Exempelkälla (japanska)  |Exempelöversättning (engelska)  |
|:--|:--|:--|:--|
|Ingen åtgärd   |Standard. Samma som att inte ange alternativet. Svordomar kommer att passera från källa till mål.        |中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン     |Han är en idiot.   |
|Markerade     |Profana ord kommer att \<omges av XML-taggar \<svordomar> och / svordomar>.       |中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン |Han är \<ett svordomar>jackass\</svordomar>.  |
|Borttagen    |Profana ord tas bort från utdata utan ersättning.     |中ンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンンン |Han är en.   |

    
## <a name="excluding-content-from-translation"></a>Exklusive innehåll från översättning
När du översätter innehåll med`contentType=text/html`taggar, till exempel HTML ( ) är det ibland användbart att utesluta specifikt innehåll från översättningen. Du kan använda `class=notranslate` attributet för att ange innehåll som ska finnas kvar på originalspråket. I följande exempel översätts `div` inte innehållet i det första elementet, `div` men innehållet i det andra elementet översätts.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>FÅ /Översätt

### <a name="implementation-notes"></a>Genomförandeanteckningar
Översätter en textsträng från ett språk till ett annat.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/Translate`är .

**Returvärde:** En sträng som representerar den översatta texten.

Om du `AddTranslation` tidigare `AddTranslationArray` har använt eller anger en översättning med en klassificering `Translate` på 5 eller högre för samma källmening returnerar du bara det bästa val som är tillgängligt för ditt system. "Samma käll mening" betyder exakt samma (100% matchning), med undantag för versaler, blanktecken, taggvärden och interpunktion i slutet av en mening. Om ingen klassificering lagras med en klassificering på 5 eller högre blir det returnerade resultatet den automatiska översättningen av Microsoft Translator.

### <a name="response-class-status-200"></a>Svarsklass (status 200)

sträng

Svarsinnehållstyp: program/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning    |Parametertyp|datatyp|
|:--|:--|:--|:--|:--|
|Appid  |(tom)    |Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|text|(tom)   |Krävs. En sträng som representerar texten att översätta. Texten får inte innehålla fler än 10 000 tecken.|DocumentDB|sträng|
|Från|(tom)   |Valfri. En sträng som representerar språkkoden för den text som översätts. Till exempel en för engelska.|DocumentDB|sträng|
|till|(tom) |Krävs. En sträng som representerar språkets kod att översätta texten till.|DocumentDB|sträng|
|Contenttype|(tom)    |Valfri. Formatet på texten som översätts. Format som `text/plain` stöds är `text/html`(standard) och . Alla HTML-element måste vara välformade, kompletta element.|DocumentDB|sträng|
|category|(tom)   |Valfri. En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.|DocumentDB|sträng|
|Auktorisering|(tom)  |Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)  |Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|


### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Genomförandeanteckningar
Hämtar översättningar för flera källtexter.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`är .

Här är formatet på förfrågan kroppen:

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

Dessa element `TranslateArrayRequest`är i:


* `AppId`: Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `AppId` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .
* `From`: Valfritt. En sträng som representerar språkkoden för den text som översätts. Om det här fältet lämnas tomt inkluderas resultatet av automatisk språkidentifiering.
* `Options`: Valfritt. Ett `Options` objekt som innehåller följande värden. De är alla valfria och standard till de vanligaste inställningarna. Angivna element måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.
    - `ContentType`: Formatet på den text som översätts. Formaten som `text/plain` stöds är `text/xml`(standard), och `text/html`. Alla HTML-element måste vara välformade, kompletta element.
    - `ProfanityAction`: Anger hur svordomar hanteras, som tidigare förklarats. Godkända värden `NoAction` är `Marked`(standard), och `Deleted`.
    - `State`: Användartillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
    - `Uri`: Filtrera resultaten efter denna URI. Standard: `all`.
    - `User`: Filtrera resultat av den här användaren. Standard: `all`.
* `Texts`: Krävs. En matris som innehåller texten för översättning. Alla strängar måste vara på samma språk. Summan av all text som ska översättas får inte överstiga 10 000 tecken. Det maximala antalet matriselement är 2 000.
* `To`: Krävs. En sträng som representerar språkets kod att översätta texten till.

Du kan utelämna valfria element. Element som är `TranslateArrayRequest` direkta underordnade måste listas i alfabetisk ordning.

Metoden `TranslateArray` accepterar `application/xml` `text/xml` eller `Content-Type`för .

**Returvärde:** En `TranslateArrayResponse` matris. Var `TranslateArrayResponse` och en har dessa element:

* `Error`: Anger ett fel om ett inträffar. Annars inställd på null.
* `OriginalSentenceLengths`: En matris med heltal som anger längden på varje mening i källtexten. Matrisens längd anger antalet meningar.
* `TranslatedText`: Den översatta texten.
* `TranslatedSentenceLengths`: En matris med heltal som anger längden på varje mening i den översatta texten. Matrisens längd anger antalet meningar.
* `State`: Användartillstånd för att korrelera begäran och svaret. Returnerar samma innehåll som begäran.

Här är formatet på svarstexten:

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

### <a name="response-class-status-200"></a>Svarsklass (status 200)
Ett lyckat svar `TranslateArrayResponse` innehåller en matris i det format som beskrivs tidigare.

sträng

Svarsinnehållstyp: program/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|(tom)  |Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod   |Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet. Vanliga fel är: <ul><li>Matriselementet får inte vara tomt.</li><li>Ogiltig kategori.</li><li>Från språket är ogiltigt.</li><li>För språket är ogiltigt.</li><li>Begäran innehåller för många element.</li><li>Språket Från stöds inte.</li><li>Till-språket stöds inte.</li><li>Översätt begäran har för mycket data.</li><li>HTML är inte i rätt format.</li><li>För många strängar skickades i översättbegäran.</li></ul>|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Genomförandeanteckningar
Hämtar egna namn för de språk `languageCodes`som skickas in `locale` som parameter – lokaliserade till det godkända språket.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`är .

Förfrågadstexten innehåller en strängmatris som representerar språkkoderna ISO 639-1 för vilka de egna namnen ska hämtas. Här är ett exempel:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Returvärde:** En strängmatris som innehåller språknamn som stöds av översättaretjänsten, lokaliserad till det begärda språket.

### <a name="response-class-status-200"></a>Svarsklass (status 200)
En strängmatris som innehåller språknamn som stöds av översättaretjänsten, lokaliserad till det begärda språket.

sträng

Svarsinnehållstyp: program/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|språk|(tom) |Krävs. En sträng som representerar något av följande, som används för att lokalisera språknamnen: <ul><li>Kombinationen av en ISO 639 två bokstäver gemener kulturkod i samband med ett språk och en ISO 3166 två bokstäver subkultur kod. <li>En ISO 639 gemener kultur kod av sig själv.|DocumentDB|sträng|
|Auktorisering|(tom)  |Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)  |Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Genomförandeanteckningar
Hämtar en lista över språkkoder som representerar språk som stöds av översättningstjänsten.  `Translate`och `TranslateArray` kan översätta mellan två av dessa språk.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`är .

**Returvärde:** En strängmatris som innehåller de språkkoder som stöds av översättaretjänsten.

### <a name="response-class-status-200"></a>Svarsklass (status 200)
En strängmatris som innehåller de språkkoder som stöds av översättaretjänsten.

sträng

Svarsinnehållstyp: program/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|Auktorisering|(tom)  |Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="get-getlanguagesforspeak"></a>FÅ /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Genomförandeanteckningar
Hämtar de språk som är tillgängliga för talsyntes.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`är .

**Returvärde:** En strängmatris som innehåller de språkkoder som stöds för talsyntes av översättaretjänsten.

### <a name="response-class-status-200"></a>Svarsklass (status 200)
En strängmatris som innehåller de språkkoder som stöds för talsyntes av översättaretjänsten.

sträng

Svarsinnehållstyp: program/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|
 
### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401|Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="get-speak"></a>FÅ /Tala

### <a name="implementation-notes"></a>Genomförandeanteckningar
Returnerar en WAV- eller MP3-ström av den in skickade texten, som talas på önskat språk.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/Speak`är .

**Returvärde:** En WAV- eller MP3-ström av den in skickade texten, som talas på önskat språk.

### <a name="response-class-status-200"></a>Svarsklass (status 200)

binary

Svarsinnehållstyp: program/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|text|(tom)   |Krävs. En sträng som innehåller en eller flera meningar som ska sägas upp för strömmen, på det angivna språket. Texten får inte överstiga 2 000 tecken.|DocumentDB|sträng|
|language|(tom)   |Krävs. En sträng som representerar språkkoden som stöds på det språk som texten ska läsas på. Koden måste vara en av de koder som returneras med metoden `GetLanguagesForSpeak`.|DocumentDB|sträng|
|format|(tom)|Valfri. En sträng som anger innehållstypenS ID. För `audio/wav` närvarande, och `audio/mp3` är tillgängliga. Standardvärdet är `audio/wav`.|DocumentDB|sträng|
|alternativ|(tom)    |Valfri. En sträng som anger egenskaperna för det syntetiserade talet:<ul><li>`MaxQuality`och `MinSize` ange ljudsignalens kvalitet. `MaxQuality`ger högsta kvalitet. `MinSize`den minsta filstorleken. Standard är `MinSize`.</li><li>`female`och `male` ange önskat kön rösten. Standardvärdet är `female`. Använd det lodräta fältet (<code>\|</code>) för att inkludera flera alternativ. Till exempel `MaxQuality|Male`.</li></li></ul>  |DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)  |Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="get-detect"></a>HÄMTA /Identifiera

### <a name="implementation-notes"></a>Genomförandeanteckningar
Identifierar språket i ett textavsnitt.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/Detect`är .

**Returvärde:** En sträng som innehåller en språkkod med två tecken för texten.

### <a name="response-class-status-200"></a>Svarsklass (status 200)

sträng

Svarsinnehållstyp: program/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)  |Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|text|(tom)|Krävs. En sträng som innehåller text vars språk ska identifieras. Texten får inte överstiga 10 000 tecken.|DocumentDB|  sträng|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key  |(tom)    |Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Genomförandeanteckningar

Identifierar språken i en matris med strängar. Identifierar oberoende språk för varje enskilt matriselement och returnerar ett resultat för varje rad i matrisen.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`är .

Här är formatet på förfrågan kroppen:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

Texten får inte överstiga 10 000 tecken.

**Returvärde:** En strängmatris som innehåller en språkkod med två tecken för varje rad i indatamatrisen.

Här är formatet på svarstexten:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Svarsklass (status 200)
`DetectArray`lyckades. Returnerar en strängmatris som innehåller en språkkod med två tecken för varje rad i indatamatrisen.

sträng

Svarsinnehållstyp: program/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma.  Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="get-addtranslation"></a>FÅ /AddTranslation

### <a name="implementation-notes"></a>Genomförandeanteckningar

> [!IMPORTANT]
> **Anmärkning om utfasning:** Efter den 31 januari 2018 kommer den här metoden inte att acceptera nya meningsanlagningar. Du får ett felmeddelande. Se meddelandet om ändringar i Collaborative Translation Framework (CTF).

Lägger till en översättning i översättningsminnet.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`är .

### <a name="response-class-status-200"></a>Svarsklass (status 200)

sträng

Svarsinnehållstyp: program: xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp   |
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|originalText|(tom)|Krävs. En sträng som innehåller texten att översätta. Strängens maximala längd är 1 000 tecken.|DocumentDB|sträng|
|översatttext|(tom) |Krävs. En sträng som innehåller text översatt till målspråket. Strängens maximala längd är 2 000 tecken.|DocumentDB|sträng|
|Från|(tom)   |Krävs. En sträng som representerar språkkoden för textens originalspråk. Till exempel en för engelska och de för tyska.|DocumentDB|sträng|
|till|(tom)|Krävs. En sträng som representerar språkets språkkod att översätta texten till.|DocumentDB|sträng|
|rating|(tom) |Valfri. Ett heltal som representerar kvalitetsklassificeringen för strängen. Värdet är mellan -10 och 10. Standard är 1.|DocumentDB|heltal|
|Contenttype|(tom)    |Valfri. Formatet på texten som översätts. Formaten som `text/plain` stöds `text/html`är och . Alla HTML-element måste vara välformade, kompletta element.    |DocumentDB|sträng|
|category|(tom)|Valfri. En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.|DocumentDB|sträng|
|användare|(tom)|Krävs. En sträng som används för att spåra upphovsmannen till inlämningen.|DocumentDB|sträng|
|Uri|(tom)|Valfri. En sträng som innehåller innehållets plats för översättningen.|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma.  Auktoriseringstoken: `"Bearer" + " " + "access_token"`.  |sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410|`AddTranslation`stöds inte längre.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Genomförandeanteckningar

> [!IMPORTANT]
> **Anmärkning om utfasning:** Efter den 31 januari 2018 kommer den här metoden inte att acceptera nya meningsanlagningar. Du får ett felmeddelande. Se meddelandet om ändringar i Collaborative Translation Framework (CTF).

Lägger till en matris med översättningar i översättningsminnet. Den här metoden är `AddTranslation`en matrisversion av .

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`är .

Här är formatet på förfrågan kroppen:

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

Dessa element `AddtranslationsRequest`är i:

* `AppId`: Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `AppId` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .
* `From`: Krävs. En sträng som innehåller källspråkets språkkod. Måste vara ett av de `GetLanguagesForTranslate` språk som returneras med metoden.
* `To`: Krävs. En sträng som innehåller målspråkets språkkod. Måste vara ett av de `GetLanguagesForTranslate` språk som returneras med metoden.
* `Translations`: Krävs. En rad översättningar som ska läggas till i översättningsminnet. Varje översättning `OriginalText`måste `TranslatedText`innehålla `Rating`, och . Den maximala storleken `OriginalText` `TranslatedText` på varje och är 1000 tecken. Summan av `OriginalText` alla `TranslatedText` och element kan inte överstiga 10 000 tecken. Det maximala antalet matriselement är 100.
* `Options`: Krävs. En uppsättning alternativ, `Category` `ContentType`inklusive `Uri`, `User`, och . `User` måste anges. `Category`, `ContentType`och `Uri` är valfria. Angivna element måste anges i alfabetisk ordning.

### <a name="response-class-status-200"></a>Svarsklass (status 200)
`AddTranslationArray`metoden lyckades. 

Efter den 31 januari 2018 kommer inte fängelsestraff att accepteras. Tjänsten svarar med felkod 410.

sträng

Svarsinnehållstyp: program/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma.  Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|410    |`AddTranslation`stöds inte längre.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="get-breaksentences"></a>FÅ /BreakSentences

### <a name="implementation-notes"></a>Genomförandeanteckningar
Bryter ett textavsnitt i meningar och returnerar en matris som innehåller längden på varje mening.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`är .

**Returvärde:** En matris med heltal som representerar längden på meningarna. Matrisens längd representerar antalet meningar. Värdena representerar längden på varje mening.

### <a name="response-class-status-200"></a>Svarsklass (status 200)
En matris med heltal som representerar längden på meningarna. Matrisens längd representerar antalet meningar. Värdena representerar längden på varje mening.

heltal

Svarsinnehållstyp: program/xml

### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)  |Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB| sträng|
|text|(tom)   |Krävs. En sträng som representerar texten som ska delas upp i meningar. Den maximala storleken på texten är 10 000 tecken.|DocumentDB|sträng|
|language   |(tom)    |Krävs. En sträng som representerar språkkoden för indatatexten.|DocumentDB|sträng|
|Auktorisering|(tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.   |sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)|Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400|Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401|Ogiltiga autentiseringsuppgifter.|
|500|Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Genomförandeanteckningar
Hämtar en rad översättningar för ett visst språkpar från butiken och MT-motorn. `GetTranslations`skiljer sig `Translate` från genom att den returnerar alla tillgängliga översättningar.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`är .

Brödtexten i begäran `TranslationOptions` innehåller det valfria objektet, som har det här formatet:

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

Objektet `TranslateOptions` innehåller värdena i följande lista. De är alla valfria och standard till de vanligaste inställningarna. Angivna element måste anges i alfabetisk ordning.

* `Category`: En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.
* `ContentType`: Det enda alternativ som stöds, och standard, är `text/plain`.
* `IncludeMultipleMTAlternatives`: En boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden `true` `false` är och (skiftlägeskänsliga). Standard är `false`, som returnerar bara ett alternativ. Ställa in `true` flaggan så att det kan skapas artificiella alternativ, helt integrerade med Collaborative Translation Framework (CTF). Funktionen gör det möjligt att returnera alternativ för meningar som inte har några översättningar i CTF genom att lägga till artificiella alternativ från *den n-bästa*listan för avkodaren.
    - Betyg. Betygen tillämpas så här: 
         - Den bästa automatiska översättningen har betyget 5.
       - Alternativen från CTF återspeglar granskarens auktoritet. De sträcker sig från -10 till +10.
       - De automatiskt genererade *(n*-bästa) översättningsalternativen har betyget 0 och en matchningsgrad på 100.
    - Antal alternativ. Antalet returnerade alternativ kan vara lika högt som `maxTranslations`det värde som anges i , men det kan vara lägre.
    - Språkpar. Den här funktionen är inte tillgänglig för översättningar mellan förenklad kinesiska och traditionell kinesiska, i båda riktningarna. Den är tillgänglig för alla andra språkpar som stöds av Microsoft Translator.
* `State`: Användartillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* `Uri`: Filtrera resultaten efter denna URI. Om inget värde har angetts är `all`standardvärdet .
* `User`: Filtrera resultat av den här användaren. Om inget värde har angetts är `all`standardvärdet .

Begäran `Content-Type` ska `text/xml`vara .

**Returvärde:** Här är formatet på svaret:

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

Det här `GetTranslationsResponse` svaret innehåller ett element som innehåller följande värden:

* `Translations`: En matris med de `TranslationMatch` matchningar som hittats, lagrat i objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla små varianter av originaltexten (suddig matchning). Översättningarna sorteras: 100% matcher först, luddiga matcher nästa.
* `From`: Om metoden inte anger `From` ett språk kommer det här värdet från automatisk språkidentifiering. Annars blir det det `From` angivna språket.
* `State`: Användartillstånd för att korrelera begäran och svaret. Innehåller värdet som anges `TranslateOptions` i parametern.

Objektet `TranslationMatch` består av dessa värden:

* `Error`: Felkoden, om ett fel uppstår för en viss indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: Anger hur nära indatatexten matchar den ursprungliga texten som finns i arkivet. Systemet matchar indatameningar mot butiken, inklusive inexakta matchningar. Värdet som returneras varierar från 0 till 100, där 0 inte är någon likhet och 100 är en exakt, skiftlägeskänslig matchning.
* `MatchedOriginalText`: Originaltext som matchades för det här resultatet. Det här värdet returneras endast om den matchade originaltexten skiljer sig från indatatexten. Den används för att returnera källtexten för en suddig matchning. Det här värdet returneras inte för Microsoft Translator-resultat.
* `Rating`: Anger myndigheten för den person som fattar kvalitetsbeslutet. Machine Translation resultat har en rating på 5. Anonymt angivna översättningar har i allmänhet ett betyg från 1 till 4. Auktoritativt förutsatt översättningar kommer i allmänhet att ha ett betyg från 6 till 10.
* `Count`: Antalet gånger denna översättning med detta betyg har valts. Värdet är 0 för det automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.

### <a name="response-class-status-200"></a>Svarsklass (status 200)
Ett `GetTranslationsResponse` objekt i det format som beskrivits tidigare.

sträng

Svarsinnehållstyp: program/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Appid|(tom)|Krävs. `Authorization` Om `Ocp-Apim-Subscription-Key` eller huvudet används lämnar `appid` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .|DocumentDB|sträng|
|text|(tom)|Krävs. En sträng som representerar texten att översätta. Den maximala storleken på texten är 10 000 tecken.|DocumentDB|sträng|
|Från|(tom)|Krävs. En sträng som representerar språkkoden för den text som översätts.|DocumentDB|sträng|
|till |(tom)    |Krävs. En sträng som representerar språkets språkkod att översätta texten till.|DocumentDB|sträng|
|maxTranslations|(tom)|Krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.|DocumentDB|heltal|
|Auktorisering| (tom)|Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma. Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sträng|  sidhuvud|
|Ocp-Apim-Subscription-Key|(tom)  |Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503|Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Genomförandeanteckningar
Hämtar flera översättningskandidater för flera källtexter.

Begäran URI `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`är .

Här är formatet på förfrågan kroppen:

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

`GetTranslationsArrayRequest`innehåller följande element:

* `AppId`: Krävs. Om `Authorization` huvudet används lämnar `AppId` du fältet tomt. Annars kan du inkludera `"Bearer" + " " + "access_token"`en sträng som innehåller .
* `From`: Krävs. En sträng som representerar språkkoden för den text som översätts.
* `MaxTranslations`: Krävs. Ett heltal som representerar det maximala antalet översättningar som ska returneras.
* `Options`: Valfritt. Ett `Options` objekt som innehåller följande värden. De är alla valfria och standard till de vanligaste inställningarna. Angivna element måste anges i alfabetisk ordning.
    - `Category`: En sträng som innehåller översättningens kategori (domän). Standardvärdet är `general`.
    - `ContentType`: Det enda alternativ som stöds, och standard, är `text/plain`.
    - `IncludeMultipleMTAlternatives`: En boolesk flagga för att ange om mer än ett alternativ ska returneras från MT-motorn. Giltiga värden `true` `false` är och (skiftlägeskänsliga). Standard är `false`, som returnerar bara ett alternativ. Ställa in `true` flaggan för att möjliggöra generering av artificiella alternativ i översättning, helt integrerad med Collaborative Translations Framework (CTF). Funktionen gör det möjligt att returnera alternativ för meningar som inte har några alternativ i CTF genom att lägga till artificiella alternativ från *den n-bästa*listan för avkodaren.
        - Betyg Betygen tillämpas så här:
          - Den bästa automatiska översättningen har betyget 5.
          - Alternativen från CTF återspeglar granskarens auktoritet. De sträcker sig från -10 till +10.
          - De automatiskt genererade *(n*-bästa) översättningsalternativen har betyget 0 och en matchningsgrad på 100.
        - Antal alternativ. Antalet returnerade alternativ kan vara lika högt som `maxTranslations`det värde som anges i , men det kan vara lägre.
        - Språkpar. Den här funktionen är inte tillgänglig för översättningar mellan förenklad kinesiska och traditionell kinesiska, i båda riktningarna. Den är tillgänglig för alla andra språkpar som stöds av Microsoft Translator.
* `State`: Användartillstånd för att korrelera begäran och svaret. Samma innehåll returneras i svaret.
* `Uri`: Filtrera resultaten efter denna URI. Om inget värde har angetts är `all`standardvärdet .
* `User`: Filtrera resultat av den här användaren. Om inget värde har angetts är `all`standardvärdet .
* `Texts`: Krävs. En matris som innehåller texten för översättning. Alla strängar måste vara på samma språk. Summan av all text som ska översättas får inte överstiga 10 000 tecken. Det maximala antalet matriselement är 10.
* `To`: Krävs. En sträng som representerar språkets språkkod att översätta texten till.

Du kan utelämna valfria element. Element som är `GetTranslationsArrayRequest` direkta underordnade måste listas i alfabetisk ordning.

Begäran `Content-Type` ska `text/xml`vara .

**Returvärde:** Här är formatet på svaret:

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

Varje `GetTranslationsResponse` element innehåller följande värden:

* `Translations`: En matris med de `TranslationMatch` matchningar som hittats, lagrat i objekt (beskrivs i följande avsnitt). Översättningarna kan innehålla små varianter av originaltexten (suddig matchning). Översättningarna sorteras: 100% matcher först, luddiga matcher nästa.
* `From`: Om metoden inte anger `From` ett språk kommer det här värdet från automatisk språkidentifiering. Annars blir det det `From` angivna språket.
* `State`: Användartillstånd för att korrelera begäran och svaret. Innehåller värdet som anges `TranslateOptions` i parametern.

Objektet `TranslationMatch` innehåller följande värden:
* `Error`: Felkoden, om ett fel uppstår för en viss indatasträng. Annars är det här fältet tomt.
* `MatchDegree`: Anger hur nära indatatexten matchar den ursprungliga texten som finns i arkivet. Systemet matchar indatameningar mot butiken, inklusive inexakta matchningar. Värdet som returneras varierar från 0 till 100, där 0 inte är någon likhet och 100 är en exakt, skiftlägeskänslig matchning.
* `MatchedOriginalText`: Originaltext som matchades för det här resultatet. Det här värdet returneras endast om den matchade originaltexten skiljer sig från indatatexten. Den används för att returnera källtexten för en suddig matchning. Det här värdet returneras inte för Microsoft Translator-resultat.
* `Rating`: Anger myndigheten för den person som fattar kvalitetsbeslutet. Machine Translation resultat har en rating på 5. Anonymt angivna översättningar har i allmänhet ett betyg från 1 till 4. Auktoritativt förutsatt översättningar har i allmänhet ett betyg från 6 till 10.
* `Count`: Antalet gånger denna översättning med detta betyg har valts. Värdet är 0 för det automatiskt översatta svaret.
* `TranslatedText`: Den översatta texten.


### <a name="response-class-status-200"></a>Svarsklass (status 200)

sträng

Svarsinnehållstyp: program/xml
 
### <a name="parameters"></a>Parametrar

|Parameter|Värde|Beskrivning|Parametertyp|Datatyp|
|:--|:--|:--|:--|:--|
|Auktorisering  |(tom)    |Krävs om `appid` både fältet `Ocp-Apim-Subscription-Key` och huvudet lämnas tomma.  Auktoriseringstoken: `"Bearer" + " " + "access_token"`.|sidhuvud|sträng|
|Ocp-Apim-Subscription-Key|(tom)  |Krävs om `appid` både fältet `Authorization` och huvudet lämnas tomma.|sidhuvud|sträng|

### <a name="response-messages"></a>Svarsmeddelanden

|HTTP-statuskod|Orsak|
|:--|:--|
|400    |Dålig begäran. Kontrollera indataparametrar och det detaljerade felmeddelandet.|
|401    |Ogiltiga autentiseringsuppgifter.|
|500    |Serverfel. Kontakta oss om felet kvarstår. Vänligen förse oss med det ungefärliga datumet & tidpunkten för begäran och med `X-MS-Trans-Info`begäran ID som ingår i svarshuvudet .|
|503    |Tjänsten är inte tillgänglig för tillfället. Försök igen och meddela oss om felet kvarstår.|

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Migrera till Translator Text API v3](../migrate-to-v3.md)


