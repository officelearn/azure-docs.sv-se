---
title: Translator Text API V 3.0-referens
titleSuffix: Azure Cognitive Services
description: Referens dokumentation för Translator Text API V 3.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: cb5a3b8572cebfd6c0731a9e572e966fda280be6
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772789"
---
# <a name="translator-text-api-v30"></a>Translator Text API v 3.0

## <a name="whats-new"></a>Nyheter

Version 3 av Translator Text API tillhandahåller ett modernt JSON-baserat webb-API. Det förbättrar användbarhet och prestanda genom att konsolidera befintliga funktioner i färre operationer och den innehåller nya funktioner.

 * Transkriberingsspråk att konvertera text på ett språk från ett skript till ett annat.
 * Översättning till flera språk i en begäran.
 * Språk identifiering, översättning och transkriberingsspråk i en begäran.
 * Ord lista för att söka efter alternativa översättningar av en term, för att hitta tillbaka – översättningar och exempel som visar termer som används i sammanhang.
 * Fler informativa språk identifierings resultat.

## <a name="base-urls"></a>Bas-URL: er

Microsoft Translator hanteras av flera data Center platser. De finns för närvarande i 10 [Azure-geografiska](https://azure.microsoft.com/global-infrastructure/regions)områden:

* **Amerika** Östra USA, södra centrala USA, västra centrala USA och västra USA 2 
* **Asien och stillahavsområdet:** Södra Korea, Östra Japan, Sydostasien och östra Australien
* **Östeuropa** Nord Europa och Västeuropa

Begär anden till Microsoft-Translator Text API hanteras i de flesta fall av data centret som är närmast den plats där begäran kommer. Om ett Data Center haveri skulle Miss lyckas kan begäran dirigeras utanför Azure-geografien.

Om du vill tvinga begäran att hanteras av ett visst Azure-geografi ändrar du den globala slut punkten i API-begäran till önskad regional slut punkt:

|Beskrivning|Azure geografi|Grundläggande URL|
|:--|:--|:--|
|Azure|Global (icke-regional)|   api.cognitive.microsofttranslator.com|
|Azure|USA|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asien och stillahavsområdet|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Prenumerera på Translator Text API eller [Cognitive Services multi-service](https://azure.microsoft.com/pricing/details/cognitive-services/) i Microsoft Cognitive Services och Använd din prenumerations nyckel (tillgänglig i Azure Portal) för att autentisera. 

Det finns tre huvuden som du kan använda för att autentisera din prenumeration. Den här tabellen innehåller information om hur varje används:

|Huvuden|Beskrivning|
|:----|:----|
|OCP-Apim-Subscription-Key|*Använd med Cognitive Services prenumeration om du skickar den hemliga nyckeln*.<br/>Värdet är Azures hemliga nyckel för din prenumeration till Translator Text API.|
|Authorization|*Använd med Cognitive Services prenumeration om du skickar en autentiseringstoken.*<br/>Värdet är Bearer-token: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Använd med Cognitive Services multi-service-prenumeration om du skickar en hemlig nyckel för flera tjänster.*<br/>Värdet är regionen för multi-service-prenumerationen. Det här värdet är valfritt när du inte använder en prenumeration på flera tjänster.|

###  <a name="secret-key"></a>Hemlig nyckel
Det första alternativet är att autentisera med hjälp `Ocp-Apim-Subscription-Key` av-huvudet. Lägg bara till `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` rubriken i din begäran.

### <a name="authorization-token"></a>Autentiseringstoken
Alternativt kan du byta hemlig nyckel för en åtkomsttoken. Denna token ingår i varje begäran som `Authorization` rubrik. Om du vill hämta en autentiseringstoken gör du `POST` en begäran till följande URL:

| Miljö     | URL för autentiseringstjänst                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Här är exempel begär Anden om att hämta en token som har fått en hemlig nyckel:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

En lyckad begäran returnerar kodad åtkomsttoken som oformaterad text i svars texten. En giltig token skickas till Translator-tjänsten som en Bearer-token i auktoriseringen.

```
Authorization: Bearer <Base64-access_token>
```

En autentiseringstoken är giltig i 10 minuter. Token ska användas igen när du gör flera anrop till Translator-API: erna. Men om programmet skickar begär anden till Translator-API under en längre tid måste programmet begära en ny åtkomsttoken med jämna mellanrum (t. ex. var 8: e minut).

### <a name="multi-service-subscription"></a>Prenumeration på flera tjänster

Alternativet senaste autentisering är att använda en kognitiv tjänsts prenumeration på flera tjänster. På så sätt kan du använda en enskild hemlig nyckel för att autentisera begär Anden för flera tjänster. 

När du använder en hemlig nyckel för flera tjänster måste du inkludera två autentiseringsscheman med din begäran. Den första skickar den hemliga nyckeln, den andra anger den region som är associerad med din prenumeration. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Region krävs för multi-service text API-prenumerationen. Den region du väljer är den enda region som du kan använda för text översättning när du använder prenumerations nyckeln för flera tjänster och måste vara samma region som du valde när du registrerade dig för din prenumeration på flera tjänster via Azure Portal.

Tillgängliga regioner är `australiaeast` `brazilsouth` ,`canadacentral` `centralindia` `centralus` ,,`centraluseuap`,,,, ,,,`eastus` `eastasia` `eastus2` `francecentral` `japaneast` `japanwest` `koreacentral`, `northcentralus`, ,,`northeurope`,,,,, och`southafricanorth`. `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2`

Om du skickar den hemliga nyckeln i frågesträngen med parametern `Subscription-Key`måste du ange regionen med `Subscription-Region`Frågeparametern.

Om du använder en Bearer-token måste du hämta token från region slut punkten `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`:.


## <a name="errors"></a>Fel

Ett standard fel svar är ett JSON-objekt med namnet/värde- `error`paret med namnet. Värdet är också ett JSON-objekt med egenskaper:

  * `code`: En server-definierad felkod.

  * `message`: En sträng som ger en läslig representation av felet.

Till exempel skulle en kund med en kostnads fri utvärderings prenumeration få följande fel när den kostnads fria kvoten har uppnåtts:

```
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Felkoden är ett 6-siffrigt tal som kombinerar den tresiffriga HTTP-statuskoden följt av ett 3-siffrigt nummer för att ytterligare kategorisera felet. Vanliga fel koder är:

| Kod | Beskrivning |
|:----|:-----|
| 400000| En av begärda indata är inte giltiga.|
| 400001| Parametern "scope" är ogiltig.|
| 400002| Parametern "Category" är ogiltig.|
| 400003| En språk specifikation saknas eller är ogiltig.|
| 400004| En mål skripts specifikation ("till skript") saknas eller är ogiltig.|
| 400005| En inmatad text saknas eller är ogiltig.|
| 400006| Kombinationen av språk och skript är ogiltig.|
| 400018| En identifierare för käll skript (från skript) saknas eller är ogiltig.|
| 400019| Ett av de angivna språken stöds inte.|
| 400020| Ett av elementen i matrisen med inmatad text är inte giltigt.|
| 400021| Parametern för API-versionen saknas eller är ogiltig.|
| 400023| Ett av de angivna språk paret är inte giltigt.|
| 400035| Käll språket ("från"-fältet) är inte giltigt.|
| 400036| Mål språket ("till"-fältet) saknas eller är ogiltigt.|
| 400042| Ett av de angivna alternativen ("alternativ"-fältet) är inte giltigt.|
| 400043| ID för klient spårning (ClientTraceId-fält eller X-ClientTranceId-huvud) saknas eller är ogiltigt.|
| 400050| Inmatad text är för lång. Visa [begränsningar för förfrågningar](../request-limits.md).|
| 400064| Parametern "översättning" saknas eller är ogiltig.|
| 400070| Antalet mål skript (ToScript parameter) matchar inte antalet mål språk (till parameter).|
| 400071| Värdet är inte giltigt för TextType.|
| 400072| Matrisen med inmatad text innehåller för många element.|
| 400073| Skript parametern är inte giltig.|
| 400074| Bröd texten i begäran är inte giltig JSON.|
| 400075| Kombinationen av språk par och kategori är ogiltig.|
| 400077| Maximal storlek för begäran har överskridits. Visa [begränsningar för förfrågningar](../request-limits.md).|
| 400079| Det anpassade systemet som begärdes för översättning mellan från och till språk finns inte.|
| 400080| Transkriberingsspråk stöds inte för språket eller skriptet.|
| 401000| Begäran är inte auktoriserad eftersom autentiseringsuppgifterna saknas eller är ogiltiga.|
| 401015| "De angivna autentiseringsuppgifterna är för Speech API. Den här begäran kräver autentiseringsuppgifter för text-API: et. Använd en prenumeration för att Translator Text API. "|
| 403000| Åtgärden är inte tillåten.|
| 403001| Åtgärden tillåts inte eftersom prenumerationen har överskridit den kostnads fria kvoten.|
| 405000| Metoden Request stöds inte för den begärda resursen.|
| 408001| Det begärda översättnings systemet förbereds. Försök igen om några minuter.|
| 408002| Tids gränsen för begäran överskreds i väntan på inkommande data ström. Klienten genererade inte någon begäran inom den tid då servern var beredd på att vänta. Klienten kan upprepa begäran utan ändringar vid ett senare tillfälle.|
| 415000| Content-Type-huvudet saknas eller är ogiltigt.|
| 429000, 429001, 429002| Servern avvisade begäran på grund av att klienten har överskridit gränsen för begäran.|
| 500000| Det uppstod ett oväntat fel. Om felet kvarstår rapporterar du det med datum/tid för felet, begärande-ID från svars huvudet X-RequestId och klient-ID: n från begär ande huvudet X-ClientTraceId.|
| 503000| Tjänsten är otillgänglig. Försök igen. Om felet kvarstår rapporterar du det med datum/tid för felet, begärande-ID från svars huvudet X-RequestId och klient-ID: n från begär ande huvudet X-ClientTraceId.|

