---
title: Referens för översättaretext-API V3.0
titleSuffix: Azure Cognitive Services
description: Referensdokumentation för Translator Text API V3.0. Version 3 av Translator Text API ger ett modernt JSON-baserat webb-API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 4/2/2020
ms.author: swmachan
ms.openlocfilehash: fcbaabac0961f1269a929fb4a56f81ac282bae29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619152"
---
# <a name="translator-text-api-v30"></a>Api för översättaretext v3.0

## <a name="whats-new"></a>Nyheter

Version 3 av Translator Text API ger ett modernt JSON-baserat webb-API. Det förbättrar användbarheten och prestanda genom att konsolidera befintliga funktioner till färre operationer och det ger nya funktioner.

 * Translitterering för att konvertera text på ett språk från ett skript till ett annat skript.
 * Översättning till flera språk i en begäran.
 * Språkidentifiering, översättning och translitterering i en begäran.
 * Ordlista för att slå upp alternativa översättningar av en term, för att hitta bakåtöversättningar och exempel som visar termer som används i sammanhanget.
 * Mer informativa språkidentifieringsresultat.

## <a name="base-urls"></a>Grundläggande webbadresser

Microsoft Translator visas på flera datacenterplatser. För närvarande finns de i 10 [Azure-geografiska områden:](https://azure.microsoft.com/global-infrastructure/regions)

* **Amerika:** Östra USA, Södra centrala USA, Västra centrala USA och västra USA 2 
* **Asien och Stillahavsområdet:** Sydkorea Syd, Japan Öst, Sydostasien och Östra Australien
* **Europa:** Norra Europa och Västeuropa

Begäranden till Microsoft Translator Text API hanteras i de flesta fall av det datacenter som ligger närmast den plats där begäran har sitt ursprung. I händelse av ett datacenterfel kan begäran dirigeras utanför Azure-geografin.

Om du vill tvinga begäran att hanteras av en viss Azure-geografi ändrar du den globala slutpunkten i API-begäran till önskad regional slutpunkt:

|Beskrivning|Azure geografi|Grundläggande URL|
|:--|:--|:--|
|Azure|Global (icke-regional)|   api.cognitive.microsofttranslator.com|
|Azure|USA|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asien och stillahavsområdet|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Autentisering

Prenumerera på Translator Text API eller [Cognitive Services multi-service](https://azure.microsoft.com/pricing/details/cognitive-services/) i Azure Cognitive Services och använd din prenumerationsnyckel (tillgänglig i Azure-portalen) för att autentisera. 

Det finns tre rubriker som du kan använda för att autentisera din prenumeration. I den här tabellen beskrivs hur var och en används:

|Rubriker|Beskrivning|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Använd med Cognitive Services-prenumeration om du skickar din hemliga nyckel*.<br/>Värdet är den hemliga Azure-nyckeln för din prenumeration på Translator Text API.|
|Auktorisering|*Använd med Cognitive Services-prenumeration om du skickar en autentiseringstoken.*<br/>Värdet är innehavartoken: `Bearer <token>`.|
|Ocp-Apim-Prenumeration-region|*Använd med Cognitive Services multiservice och regional översättare resurs.*<br/>Värdet är regionen för multiservice- eller regionalöversättarresursen. Det här värdet är valfritt när du använder en global översättarresurs.|

###  <a name="secret-key"></a>Hemlig nyckel
Det första alternativet är att `Ocp-Apim-Subscription-Key` autentisera med hjälp av huvudet. Lägg `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` till sidhuvudet i din begäran.

#### <a name="authenticating-with-a-global-resource"></a>Autentisera med en global resurs

När du använder en [global översättarresurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)måste du inkludera ett huvud för att anropa översättar-API:et.

|Rubriker|Beskrivning|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är den hemliga Azure-nyckeln för din prenumeration på Translator Text API.|

Här är en exempelbegäran om att anropa Translator API med hjälp av den globala översättarresursen

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autentisera med en regional resurs

När du använder en [regional översättarresurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Det finns 2 rubriker som du måste ringa översättaren API.

|Rubriker|Beskrivning|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är den hemliga Azure-nyckeln för din prenumeration på Translator Text API.|
|Ocp-Apim-Prenumeration-region| Värdet är översättarresursens region. |

Här är en exempelbegäran om att anropa Translator API med hjälp av den regionala översättarresursen

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autentisera med en multitjänstresurs

När du använder en Cognitive Service-resurs för flera tjänster. På så sätt kan du använda en enda hemlig nyckel för att autentisera begäranden om flera tjänster. 

När du använder en hemlig nyckel för flera tjänster måste du inkludera två autentiseringshuvuden med din begäran. Det finns 2 rubriker som du måste ringa översättaren API.

|Rubriker|Beskrivning|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är den hemliga Azure-nyckeln för din resurs för flera tjänster.|
|Ocp-Apim-Prenumeration-region| Värdet är regionen för multi-service-resursen. |

Regionen krävs för text-API-prenumerationen för flera tjänster. Den region du väljer är den enda region som du kan använda för textöversättning när du använder prenumerationsnyckeln för flera tjänster och måste vara samma region som du valde när du registrerade dig för din prenumeration med flera tjänster via Azure-portalen.

Tillgängliga områden `australiaeast` `brazilsouth`är `canadacentral` `centralindia`, `centralus` `centraluseuap`, `eastasia` `eastus`, `eastus2` `francecentral`, `japaneast` `japanwest`, `koreacentral` `northcentralus`, `northeurope` `southcentralus`, `southeastasia` `uksouth`, `westcentralus` `westeurope`, `westus`, , , , , , , , , , , , `westus2`, , och `southafricanorth`.

Om du skickar den hemliga nyckeln `Subscription-Key`i frågesträngen med parametern `Subscription-Region`måste du ange regionen med frågeparametern .

### <a name="authenticating-with-an-access-token"></a>Autentisera med en åtkomsttoken
Alternativt kan du byta ut din hemliga nyckel mot en åtkomsttoken. Den här token ingår i `Authorization` varje begäran som huvud. Om du vill hämta `POST` en auktoriseringstoken gör du en begäran till följande URL:

| Resurstyp     | URL för autentiseringstjänst                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regional eller multi-service | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Här är exempel begäranden om att få en token med en hemlig nyckel:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

En lyckad begäran returnerar den kodade åtkomsttoken som oformaterad text i svarstexten. Den giltiga token skickas till översättaren tjänsten som en bärare token i auktoriseringen.

```http
Authorization: Bearer <Base64-access_token>
```

En autentiseringstoken är giltig i 10 minuter. Token ska återanvändas när du ringer flera samtal till Translator API:er. Men om programmet gör förfrågningar till Translator API under en längre tid, måste programmet begära en ny åtkomsttoken med jämna mellanrum (till exempel var 8:e minut).

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk

Översättare tjänsten är nu tillgänglig med virtuella`WestUS2`nätverk `EastUS` `SouthCentralUS`funktioner `WestUS` `Central US EUAP`i `global`begränsade regioner ( , , , , , , , ). Information om hur du aktiverar virtuellt nätverk finns i [Konfigurera virtuella Azure Cognitive Services-nätverk](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal). 

När du har aktiverat den här funktionen måste du använda den anpassade slutpunkten för att anropa Translator API. Du kan inte använda den globala översättarslutpunkten ("api.cognitive.microsofttranslator.com") och du kan inte autentisera med en åtkomsttoken.

Du hittar den anpassade slutpunkten när du har skapat [översättarresursen](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).

|Rubriker|Beskrivning|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Värdet är den hemliga Azure-nyckeln för din prenumeration på Translator Text API.|
|Ocp-Apim-Prenumeration-region| Värdet är översättarresursens region. Det här värdet är valfritt om resursen`global`|

Här är en exempelbegäran om att anropa Translator API med hjälp av den anpassade slutpunkten

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Fel

Ett standardfelsvar är ett JSON-objekt `error`med namn/värdepar med namnet . Värdet är också ett JSON-objekt med egenskaper:

  * `code`: En serverdefinierad felkod.
  * `message`: En sträng som ger en läsbar representation av felet.

En kund med en kostnadsfri provprenumeration får till exempel följande felmeddelande när den kostnadsfria kvoten är uttömd:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Felkoden är ett 6-siffrigt nummer som kombinerar den tresiffriga HTTP-statuskoden följt av ett tresiffrigt nummer för att ytterligare kategorisera felet. Vanliga felkoder är:

| Kod | Beskrivning |
|:----|:-----|
| 400000| En av indata för begäran är ogiltig.|
| 400001| Parametern "scope" är ogiltig.|
| 400002| Parametern "kategori" är ogiltig.|
| 400003| En språkspecificerare saknas eller är ogiltig.|
| 400004| En målskriptspecificerare ("Till skript") saknas eller är ogiltig.|
| 400005| En indatatext saknas eller är ogiltig.|
| 400006| Kombinationen av språk och skript är ogiltig.|
| 400018| En källskriptspecificerare ("Från skript") saknas eller är ogiltig.|
| 400019| Ett av de angivna språken stöds inte.|
| 400020| Ett av elementen i matrisen med indatatext är ogiltigt.|
| 400021| PARAMETERN API-version saknas eller är ogiltig.|
| 400023| Ett av det angivna språkparet är ogiltigt.|
| 400035| Källspråket ("Från"-fältet) är ogiltigt.|
| 400036| Målspråket ("Till"-fältet) saknas eller är ogiltigt.|
| 400042| Ett av de angivna alternativen (fältet Alternativ) är ogiltigt.|
| 400043| Klientspårnings-ID (ClientTraceId-fält eller X-ClientTranceId-huvud) saknas eller är ogiltigt.|
| 400050| Indatatexten är för lång. Visa [begränsningar för begäran](../request-limits.md).|
| 400064| Parametern "översättning" saknas eller är ogiltig.|
| 400070| Antalet målskript (ToScript-parameter) matchar inte antalet målspråk (Till parametern).|
| 400071| Värdet är inte giltigt för TextType.|
| 400072| Matrisen med indatatext har för många element.|
| 400073| Skriptparametern är ogiltig.|
| 400074| Brödtexten för begäran är ogiltig JSON.|
| 400075| Språkparet och kategorikombinationen är ogiltiga.|
| 400077| Den maximala begäranden har överskridits. Visa [begränsningar för begäran](../request-limits.md).|
| 400079| Det anpassade system som begärs för översättning mellan från och till språket finns inte.|
| 400080| Transliteration stöds inte för språket eller skriptet.|
| 401000| Begäran är inte auktoriserad eftersom autentiseringsuppgifter saknas eller är ogiltiga.|
| 401015| "De autentiseringsuppgifter som tillhandahålls är för tal-API: et. Den här begäran kräver autentiseringsuppgifter för text-API:et. Använd en prenumeration på Translator Text API."|
| 403000| Åtgärden är inte tillåten.|
| 403001| Åtgärden är inte tillåten eftersom prenumerationen har överskridit sin kostnadsfria kvot.|
| 405000| Begäran-metoden stöds inte för den begärda resursen.|
| 408001| Det begärda översättningssystemet håller på att utarbetas. Försök igen om några minuter.|
| 408002| Tidssstr för begäran väntar på inkommande ström. Klienten har inte skapat en begäran inom den tid som servern var beredd att vänta. Klienten kan upprepa begäran utan ändringar vid ett senare tillfälle.|
| 415000| Content-Type-huvudet saknas eller är ogiltigt.|
| 429000, 429001, 429002| Servern avvisade begäran eftersom klienten har överskridit begärangränser.|
| 500000| Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med datum/tid för fel, begärandeidentifierare från svarshuvudet X-RequestId och klientidentifierare från förfrågershuvudet X-ClientTraceId.|
| 503000| Tjänsten är inte tillgänglig för tillfället. Försök igen. Om felet kvarstår rapporterar du det med datum/tid för fel, begärandeidentifierare från svarshuvudet X-RequestId och klientidentifierare från förfrågershuvudet X-ClientTraceId.|

## <a name="metrics"></a>Mått 
Mått kan du visa information översättare användning och tillgänglighet i Azure portal, under mått avsnitt som visas i nedanstående skärmdump. Mer information finns i [Data- och plattformsmått](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Översättare Mått](../media/translatormetrics.png)

I den här tabellen visas tillgängliga mått med en beskrivning av hur de används för att övervaka api-anrop för översättning.

| Mått | Beskrivning |
|:----|:-----|
| Totalt Antal Meddelanden| Totalt antal API-anrop.|
| TotaltTokenCalls| Totalt antal API-anrop via tokentjänst med hjälp av autentiseringstoken.|
| FramgångsrikaCalls| Antal lyckade samtal.|
| TotalErrors| Antal samtal med felsvar.|
| Blockerade samtal| Antal samtal som överskridit kurs- eller kvotgränsen.|
| ServerErrors| Antal samtal med serverns interna fel(5XX).|
| Kunderrors| Antal samtal med fel på klientsidan(4XX).|
| Svarstid| Varaktighet för att slutföra begäran i millisekunder.|
| Teckenöversatta| Totalt antal tecken i inkommande textbegäran.|
