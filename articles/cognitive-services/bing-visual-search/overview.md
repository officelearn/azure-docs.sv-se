---
title: Bing Visual Search API-översikt | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du hämtar information eller insikter om en bild som liknande bilder eller perioder källor.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: aa563d89b1834f5be952f13c31a2451d809709b1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006537"
---
# <a name="what-is-bing-visual-search-api"></a>Vad är API för Bing Visual Search?

Bing Visual Search-API ger en upplevelse som liknar avbildningsdetaljerna som visas på Bing.com/images. Med Visual Search kan du ladda upp en bild och få tillbaka insikter om avbildningen som snarlika bilder, perioder källor, webbsidor som innehåller bilden och mycket mer. I stället för att ladda upp en avbildning kan du även ange en token för insikter som du får från en avbildning i sökresultaten avbildningar (se [bilder i Bing](../bing-image-search/overview.md)).

Visuell sökning kan identifiera kändisar, monument och landmärken, konstverk, heminredning, dirigering, produkter, teckenläsning (OCR) och mer.

Följande är de insikter som att Visual Search kan du upptäcka.

- Snarlika bilder&mdash;en lista med avbildningar som är visuellt liknar av inmatad bild
- Visuellt likartade produkter&mdash;en lista med avbildningar som innehåller produkter som är visuellt likartade till produkten på inkommande bild
- Shopping källor&mdash;en lista över platser där du kan köpa objektet visas i den inkommande bilden
- Relaterade sökningar&mdash;en lista med relaterade sökningar som gjorts av andra eller som är baserat på innehållet i avbildningen
- Webbsidor med avbildningen&mdash;en lista över webbsidor som innehåller inmatad bild
- Recept&mdash;en lista över webbsidor som innehåller recept för att göra rätten på inkommande bild

Förutom dessa insikter returnerar Visual Search också en mängd olika villkor (taggar) som härletts från inmatad bild. Dessa taggar kan du utforska koncepten finns i avbildningen. Till exempel inmatad bild är av en berömda Idrottare, en av taggarna kan vara namnet på Idrottare, en annan tagg kan vara sport. Eller, om inmatad bild är av en apple-cirkel, taggarna kan Apple cirkel, Pies, efterrätter, så att användare kan utforska besläktade begrepp.

Visual sökresultatet innehåller också avgränsar rutorna för regioner intressanta i avbildningen. Till exempel om avbildningen innehåller flera kändisar, kan resultaten omfatta avgränsar rutorna för var och en av identifierade kändisar i avbildningen. Eller, om Bing känner av en produkt eller kläder i avbildningen kan resultatet kan innehålla en avgränsningsruta för den produkt som känns igen eller kläder objekt.

> [!IMPORTANT]
> Om du använder/bilder/information om slutpunkten till [få information om bilder](../bing-image-search/image-insights.md), bör du uppdatera din kod för att använda visuell sökning i stället eftersom det ger mer omfattande insikter.


## <a name="the-request"></a>Begäran

Här följer några alternativ för att få insikter om en bild. 

- Skicka en insights-token som du får från en avbildning i ett tidigare anrop till en av de [bilder i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) slutpunkter
- Skicka Webbadressen till en bild
- Ladda upp en bild (binär)


Om du skickar Visual Search en bild-token eller en URL, visar följande JSON-objekt som måste inkluderas i brödtexten i INLÄGGET. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

Den `imageInfo` objekt måste innehålla antingen den `url` eller `imageInsightsToken` fält, men inte båda. Ange den `url` fältet till en Internet-tillgänglig bild-URL. Maximal stöds avbildningens storlek är 1 MB.

Den `imageInsightsToken` måste anges till en insights-token. Anropa API för Bing-bild för att få en token för insights. Svaret innehåller en lista över `Image` objekt. Varje `Image` objektet innehåller en `imageInsightsToken` fält som innehåller token.

Den `cropArea` fältet är valfritt. Beskärningsområdet anger övre, vänstra hörnet och längst ned, högra hörnet i en region i närheten. Ange värden i intervallet 0,0 till och med 1.0. Värdena som är en del av den totala bredden eller höjden. Till exempel markerar exemplet ovan höger hälften av avbildningen som det intressanta området. Inkludera den om du vill begränsa insights-begäran till det intressanta området.

Den `filters` objektet innehåller ett plats-filter (se den `site` fältet) som du kan använda för att begränsa liknande bilder och liknande produkter resultat till en specifik domän. Till exempel om avbildningen är av en Surface Book, du kan ange `site` till www.microsoft.com. 

Om du vill hämta insikter om en lokal kopia av en avbildning kan du överföra avbildningen som binära data.

Mer information om att inkludera dessa alternativ i brödtexten i INLÄGGET finns i [innehållstyper som formuläret](#content-form-types).


### <a name="endpoint"></a>Slutpunkt

Visual Search-slutpunkten är: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Begäranden måste skickas som HTTP POST-begäranden. 


### <a name="query-parameters"></a>Frågeparametrar

Här följer din begäran bör ange frågeparametrar. Du bör innehålla minst de `mkt` frågeparameter.

|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="cc" />kopia|En 2-tecknet landskoden för det land där resultatet kommer från.<br /><br /> Om du anger parametern, måste du även ange den [acceptera språk](#acceptlanguage) rubrik. Bing använder det första språk som stöds det hittar i listan över språk och kombinerar det språk med landskoden som du anger för att fastställa att returnera resultat från marknaden. Om listan över språk som inte innehåller ett språk som stöds hittar Bing närmaste språk och marknaden som har stöd för begäran. Eller den kan använda en sammansatt eller standard marknaden för resultat i stället för den angivna.<br /><br /> Du bör använda den här Frågeparametern och `Accept-Language` frågeparameter endast om du anger flera språk, i annat fall bör du använda den `mkt` och `setLang` Frågeparametrar.<br /><br /> Den här parametern och [mkt](#mkt) Frågeparametern är ömsesidigt uteslutande&mdash;inte ange båda.|Sträng|Nej|  
|<a name="mkt" />mkt|Marknaden var resultatet kommer från. <br /><br /> **Obs:** du uppmanas att ange alltid marknaden, om det är möjligt. Ange marknaden hjälper Bing dirigera begäran och returnera ett lämpligt och optimal svar.<br /><br /> Den här parametern och [cc](#cc) Frågeparametern är ömsesidigt uteslutande&mdash;inte ange båda.|Sträng|Ja|  
|<a name="safesearch" />säker sökning|Ett filter som används för att filtrera innehåll för vuxna. Här följer möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Inaktivera&mdash;returnera webbsidor med vuxet text eller bilder.<br /><br/></li><li>Måttlig&mdash;returnera webbsidor med vuxet text, men inte vuxen avbildningar.<br /><br/></li><li>Strikt&mdash;inte returnerar webbsidor med vuxet text eller bilder.</li></ul><br /> Standardvärdet är Måttlig.<br /><br /> **Obs:** om förfrågan kommer från en marknad som Bing vuxet principen kräver att `safeSearch` vara inställd på strikt, Bing ignorerar den `safeSearch` värde och använder Strict.<br/><br/>**Obs:** om du använder den `site:` fråga-operator, finns risken att svaret kan innehålla vuxet innehåll, oavsett vad de `safeSearch` Frågeparametern är inställd. Använd `site:` endast om du är känner av innehållet på webbplatsen och ditt scenario har stöd för möjligheten att vuxet innehåll. |Sträng|Nej|  
|<a name="setlang" />setLang|Språket som ska användas för användaren gränssnittet strängar. Ange vilket språk med hjälp av ISO 639-1 2 bokstäver språkkoden. Språkkod för engelska är till exempel EN. Standardvärdet är EN (på engelska).<br /><br /> Även om det är valfritt, bör du alltid ange språket. Normalt anger du `setLang` på samma språk som anges av `mkt` om inte användaren vill användaren gränssnittet strängar som visas i ett annat språk.<br /><br /> Den här parametern och [acceptera språk](#acceptlanguage) rubrik är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> En användare gränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användaren gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svarsobjekten gäller även, det angivna språket.|Sträng|Nej| 

### <a name="headers"></a>Sidhuvuden

Följande är de rubriker som din begäran bör ange. Content-Type och Ocp-Apim-Subscription-Key-huvuden är de enda obligatoriska rubrikerna men du bör också innehålla användar-Agent, X-MSEdge-ClientID, X-MSEdge-ClientIP och X sökplats.


|Sidhuvud|Beskrivning|  
|------------|-----------------|  
|<a name="acceptlanguage" />Acceptera språk|Valfria rubriken.<br /><br /> En kommaavgränsad lista över språk som ska användas för användaren gränssnittet strängar. Listan är i fallande prioritetsordning. Läs mer om, inklusive förväntade formatet [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Den här rubriken och [setLang](#setlang) Frågeparametern är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> Om du ställer in den här rubriken, måste du även ange den [cc](#cc) frågeparameter. För att fastställa på marknaden och returnera resultat för Bing använder det första språk som stöds det söker efter i listan och kombinerar det med den `cc` parametervärde. Om listan inte innehåller ett språk som stöds, Bing hittar närmaste språk och marknaden som har stöd för begäran eller den använder en sammansatt eller default marknaden för resultaten. För att avgöra på marknaden som används i Bing, finns i rubriken BingAPIs marknad.<br /><br /> Använd den här rubriken och `cc` frågeparameter endast om du anger flera språk. Annars kan du använda den [mkt](#mkt) och [setLang](#setlang) Frågeparametrar.<br /><br /> En användare gränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användaren gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svarsobjekten gäller det angivna språket.|  
|<a name="contenttype" />Innehållstyp|Nödvändiga begärandehuvudet.<br /><br />Måste vara inställt på multipart/form-data och inkludera en gränsparameter (till exempel multipart/form-data; gräns =\<gräns sträng\>). Mer information finns i [innehållstyper som formuläret](#content-form-types).
|<a name="market" />BingAPIs marknad|Svarshuvudet.<br /><br /> På marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Till exempel en-US.|  
|<a name="traceid" />BingAPIs TraceId|Svarshuvudet.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår, avbilda detta ID. Om det inte går att fastställa och lösa problemet, kan du ange följande ID tillsammans med annan information som du anger supporten.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Nödvändiga begärandehuvudet.<br /><br /> Prenumerationsnyckel som du fick när du registrerat dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Begärandehuvud i valfritt<br /><br /> Bing returnerar cachelagrat innehåll om de är tillgängliga som standard. Om du vill förhindra att Bing returnera cachelagrat innehåll, ange rubriken Pragma till no cache (till exempel Pragma: no-cache).
|<a name="useragent" />Användaragent|Valfria rubriken.<br /><br /> Användaragenten skapade begäran. Bing använder användaragenten för att tillhandahålla mobila användare med en optimerad upplevelse. Även om det är valfritt, uppmuntras du att alltid ange den här rubriken.<br /><br /> Användaragenten ska vara samma sträng som skickar alla vanliga webbläsare. Läs om hur användaragenter [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Här följer några exempel på användaren agent strängar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/PEPPARKAKOR) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0 Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (t.ex. Gecko KHTML) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfria rubriken för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna konsekvent beteende i Bing API-anrop. Bing flygningar ofta nya funktioner och förbättringar och klient-ID används som en nyckel för att tilldela trafik på olika flyg. Om du inte använder samma klient-ID för en användare över flera förfrågningar, kan sedan Bing tilldela användaren till flera motstridiga flyg. Tilldelas till flera motstridiga flyg kan leda till en inkonsekvent användarupplevelse. Om andra begäran har en annan flygning uppdrag än först, till exempel vara upplevelsen oväntat. Bing kan också använda klient-ID för att skräddarsy Webbresultat till den klienten-ID: n sökhistorik, vilket ger en rikare upplevelse för användaren.<br /><br /> Bing använder också den här rubriken för att förbättra resultatet rangordning genom att analysera aktiviteten som genererats av en klient-ID. Förbättringar för relevans hjälpa med bättre resultat som levereras av Bing-API: er och i sin tur aktiverar högre klicka igenom priser för API-konsument.<br /><br /> **Viktigt:** även om det är valfritt, bör du överväga att den här rubriken som krävs. Spara klient-ID över flera förfrågningar för samma användare och enhet kombination kan (1) API konsumenten att ta emot en konsekvent användarupplevelse och 2) högre klicka igenom kostnader via bättre kvalitet med resultat från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för den här rubriken.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing skapas klient-ID.<br /><br/>Om du inte använder den här rubriken i begäran Bing genererar ett ID och returnerar det i rubriken X-MSEdge-ClientID. Den enda gången som du inte får innehålla den här rubriken i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>**Observera:** måste du se till att detta klient-ID inte är linkable till valfri autentiserad användarkontoinformation.</li><li>Använda klient-ID för varje API för Bing-begäran där din app för den här användaren på enheten.<br /><br/></li><li>Spara klient-ID. Använda en beständig HTTP-cookie för ID: T används i alla sessioner för att bevara ID i en browser-appen. Använd inte en sessions-cookie. För andra appar, till exempel mobilappar, använder du enhetens beständig lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten, hämta klient-ID som du sparade.</li></ul><br /> **Obs:** Bing svar kanske eller kanske inte omfattar den här rubriken. Om svaret innehåller den här rubriken, avbilda klient-ID och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs:** om du inkluderar X-MSEdge-ClientID, får inte innehålla cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfria rubriken.<br /><br /> Klientenheten IPv4 eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder Platsinformationen för att bestämma beteendet för säker sökning.<br /><br /> **Obs:** även om det är valfritt, du uppmuntras att alltid ange den här rubriken och X-Search-Location-huvudet.<br /><br /> Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Dölja adress resultaten på den plats som inte var som helst nära enhetens verkliga plats, vilket kan resultera i Bing som betjänar felaktiga resultat.|  
|<a name="location" />X-Sök-plats|Valfria rubriken.<br /><br /> En semikolonavgränsad lista med nyckel/värde-par som beskriver klientens geografisk plats. Bing använder platsinformationen att fastställa säker sökning beteende och returnera relevanta lokalt innehåll. Ange nyckel/värde-par som \<nyckel\>:\<värdet\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;krävs. Latitud för klientens plats i grader. Latitud måste vara större än eller lika med-90.0 och mindre än eller lika med +90.0. Negativa värden anger södra Latitude och positiva värden anger norra Latitude.<br /><br /></li><li>lång&mdash;krävs. Longitud för klientens plats i grader. Longitud måste vara större än eller lika med-180.0 och mindre än eller lika med +180.0. Negativa värden anger western longitudes och positiva värden anger östra longitudes.<br /><br /></li><li>RE&mdash;krävs. Radien, i meter, som anger vågrät riktighet koordinaterna. Skicka värdet som returneras av enhetens platstjänsten. Typiska värdena kan vara 22m för GPS/Wi-Fi, 380m för cellen tower triangulering och 18 000 m för omvänd IP-sökning.<br /><br /></li><li>TS&mdash;valfritt. UTC-UNIX-tidsstämpel för när klienten har på plats. (UNIX-tidsstämpel är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>HEAD&mdash;valfritt. Klientens relativa rubriken eller riktning resor. Ange riktning resor som grader från 0 till 360, räkna medurs i förhållande till SANT norra. Ange den här nyckeln endast om den `sp` nyckel är noll.<br /><br /></li><li>SP&mdash;valfritt. Den vågräta hastigheten (hastighet) i meter per sekund som färdas klientenheten.<br /><br /></li><li>ALT&mdash;valfritt. Klientenheten i meter höjd.<br /><br /></li><li>är&mdash;valfritt. Radien, i meter, som anger lodrät riktighet koordinaterna. Ange den här nyckeln endast om du anger den `alt` nyckel.<br /><br /></li></ul> **Obs:** även om många av nycklarna som är valfria, den information som du anger, desto mer exakta resultat för platsen är.<br /><br /> **Obs:** även om det är valfritt, du uppmuntras att alltid ange användarens geografiska plats. Att tillhandahålla platsen är särskilt viktigt om klientens IP-adress korrekt inte avspeglar användarens fysisk plats (till exempel om klienten använder VPN). För bästa resultat bör du inkludera den här rubriken och rubriken X-MSEdge-ClientIP, men minst bör du inkludera den här rubriken.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren Kräv efterlevnad med alla tillämpliga lagar, inklusive om användande av dessa rubriker. Till exempel vissa jurisdiktioner, till exempel Europa, det finns i kraven för att hämta användarens medgivande innan du placerar vissa spårnings-enheter på användarnas enheter.


<a name="content-form-types" />

### <a name="content-form-types"></a>Typer av innehåll formulär

Varje begäran måste innehålla Content-Type-huvud. Huvudet måste anges till: multipart/form-data. gräns =\<gräns sträng\>, där \<gräns sträng\> är en unik, täckande sträng som identifierar gränsen för formuläret. Till exempel gräns = boundary_1234 abcd.


Om du skickar Visual Search en bild-token eller URL visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla rubriken Content-Disposition och dess `name` parametern måste anges till ”knowledgeRequest”. Mer information om den `imageInfo` objekt, se [begäran](#the-request).


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Om du laddar upp en lokal avbildning visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla Content-Disposition-huvudet. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till valfri sträng. Content-Type-huvudet kan anges till alla vanliga mime-bildtypen. Innehållet i formuläret är den binära filen på avbildningen. Maximal avbildningens storlek kan du överföra är 1 MB. Den största av bredden eller höjden ska vara 1 500 bildpunkter eller mindre.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Nedan visas hur du anger region of interest för en överförd avbildning.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Exempelbegäran

Nedan visas en fullständig bild insights-begäran som skickar en bild-token och intressanta området. Du får insikter token vid ett tidigare anrop till /images/search.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="the-response"></a>Svaret

Om det finns insikter för avbildningen, svaret innehåller en eller flera `tags` som innehåller insikterna. Den `image` fältet innehåller insikter token för inmatad bild.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Den `tags` fält innehåller ett visningsnamn och en lista med åtgärder (insights). En av taggarna innehåller en `displayName` fält som har angetts till en tom sträng. Den här taggen innehåller insikterna som standard, till exempel webbsidor som innehåller den avbildning, snarlika bilder och perioder källor för poster som hittades i avbildningen. Eftersom hela bilden är av intresse, omfattar inte insikter Standardetiketten avgränsar rutorna för regionerna i närheten.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

En lista över analyser som standard finns i [standard insights](./default-insights-tag.md).



Resten av taggarna innehålla andra insikter som kan vara av intresse för användaren. Om avbildningen innehåller text, kan en av taggarna omfatta en TextResults och förståelse, som innehåller den tolkade texten. Eller, om Bing känner igen en entitet (person, plats eller sak) i bild, en av taggarna kan identifiera entiteten. Visuell sökning returnerar också en mängd olika villkor (taggar) som härletts från inmatad bild. Dessa taggar kan du utforska koncepten finns i avbildningen. Till exempel om det är avbildningen av en berömda Idrottare, kan en av taggarna vara sport, som innehåller länkar till bilder av sport.

Varje tagg innehåller ett visningsnamn som du kan använda för att kategorisera den inblick och förståelse, angränsande ruta som identifierar regionen av intresse som insikter som gäller för insikterna som själva och en miniatyrbild för avbildningen. Om avbildningen är av en person med en sport jersey, kan en av taggarna innehålla en avgränsningsruta som utrymmet på jersey och innehåller VisualSearch och ProductVisualSearch insikter. Och en annan tagg kan innehålla ImageResults insikter som innehåller en URL för ett /images/search API-begäran att hämta avbildningar som är relaterade appliceras eller en Bing.com URL som leder användaren till bildsökningsresultat för Bing.com.

Alla taggar än insikter Standardetiketten är bland annat avgränsar rutor som identifierar områden av intresse för avbildningen. Till exempel om avbildningen innehåller flera identifierade personer, taggar kan innehålla avgränsar rutorna för var och en av personerna eller om avbildningen innehåller erkända kläder objekt, taggar kan innehålla avgränsar rutorna för varje erkända kläder-objekt. Du kan använda rutorna omgivande om du vill skapa aktiva punkter jämfört med avbildningen som när du klickar på innehåller information om innehållet i den regionen för avbildningen. Du får inte innehålla aktiva punkter i en bild för omgivande rutor som identifierar hela bilden.

### <a name="text-recognition"></a>Textigenkänning

Om avbildningen innehåller text som tjänsten känner igen, innehåller en av taggarna en TextResults insight (åtgärd). Insikter `displayName` innehåller den tolkade texten. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Eftersom taggen `displayName` fältet innehåller ##TextRecognition, Använd inte som en kategorirubrik i UX. Att sak går för alla Visa namn som börjar med ##. I stället använda åtgärdens namn.


Textigenkänning kan också identifiera kontaktuppgifter för företag-kort, till exempel telefonnummer och e-postadresser. Rutan anger kontaktuppgifter för kortet. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Om bilden innehåller en känd enhet till exempel en person, plats eller sak, kan en av taggarna omfatta en entitet insikt. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```



## <a name="next-steps"></a>Nästa steg

Om du vill komma igång snabbt med din första begäran kan se Snabbstart: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Prova att använda API: et. Gå till [Visual Search API Testing-konsolen](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Bekanta dig med den [Visual Search API-referens](https://aka.ms/bingvisualsearchreferencedoc). Referensen innehåller listan över slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten. 

Se till att läsa [Bing Use and Display Requirements](./use-and-display-requirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.


