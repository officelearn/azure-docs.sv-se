---
title: 'Bing Visual Sök-API: översikt | Microsoft Docs'
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Visar hur du hämtar information eller information om en avbildning som liknande bilder eller shopping källor.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 95f10d8ea7ebe1d40d45231a8ea40df81543fe8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354759"
---
# <a name="what-is-bing-visual-search-api"></a>Vad är Bing Visual Sök API?

Bing Visual Sök API ger en upplevelse som liknar bilden uppgifterna på Bing.com/images. Med Visual Search du överför en bild och få tillbaka insikter om avbildningen som visuellt liknande bilder, shopping källor, webbsidor som innehåller bilden och mycket mer. I stället för att ladda upp en bild, du kan också tillhandahålla en insikter token som du får från en avbildning i sökresultaten avbildningar (se [Bing bilder API](../bing-image-search/overview.md)).

Visual sökning kan identifiera Kändisars, minnesmärken och landmärken, bilder, heminredning, sätt, produkter, (teckenläsning) och mycket mer.

Följande är de insikter som Visual Search kan du identifiera.

- Visuellt liknande bilder&mdash;en lista över avbildningar som liknar visuellt inkommande avbildningen
- Visuellt liknande produkter&mdash;en lista över avbildningar som innehåller produkter som visuellt liknar den produkt som anges i källbilden
- Shopping källor&mdash;en lista med platser där du kan köpa det objekt som visas i källbilden
- Relaterade sökningar&mdash;en lista över relaterade sökningar som gjorts av andra eller som är baserade på innehållet i avbildningen
- Webbsidor som innehåller bilden&mdash;en lista över webbsidor som innehåller den inkommande bilden
- Recept&mdash;en lista över webbsidor som innehåller recept för att göra rätten visas i källbilden

Förutom dessa insikter returnerar Visual Sök också en mängd olika villkor (taggar) som härletts från den inkommande bilden. Dessa taggar Tillåt användare att utforska begrepp som hittades i avbildningen. Om inkommande bilden är av en berömda Idrottare, en taggar kan vara namnet på Idrottare, en annan tagg kunde sport. Eller, om indata bilden är av en apple cirkel, taggarna kunde Apple cirkeldiagram och pajer, desserter, så att användare kan utforska relaterade begrepp.

Visual sökresultatet innehåller också avgränsar kryssrutorna för områden av intresse för bilden. Till exempel kan bilden innehåller flera Kändisars, resultaten vara avgränsar rutor för varje identifierad Kändisars i bilden. Eller, om Bing känner av en produkt eller kläder i avbildningen kan resultatet kan omfatta avgränsningsram för tolkade produkt eller kläder objektet.

> [!IMPORTANT]
> Om du använder/bilder/information slutpunkten till [få insikter om bilden](../bing-image-search/image-insights.md), bör du uppdatera din kod för att använda Visual Sök i stället eftersom det ger mer omfattande insikter.


## <a name="the-request"></a>Begäran

Följande är alternativen för att hämta information om en avbildning. 

- Skicka en insikter token som du får från en avbildning i ett tidigare anrop till en av de [Bing bilder API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) slutpunkter
- Skicka URL till en bild
- Överför en bild (binär)


Om du skickar Visual Sök en bild-token eller en URL, visas följande JSON-objekt som måste inkluderas i brödtexten för efter. 

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

Den `imageInfo` objekt måste innehålla antingen den `url` och `imageInsightsToken` fältet men inte båda. Ange den `url` fältet till en tillgänglig Internet-bild-URL. Maximala stöds bildens storlek är 1 MB.

Den `imageInsightsToken` måste anges till en insights-token. Anropa Bing avbildningen API för att få en token för insikter. Svaret innehåller en lista över `Image` objekt. Varje `Image` objektet innehåller en `imageInsightsToken` som innehåller token.

Den `cropArea` fältet är valfritt. Beskär område anger övre, vänstra hörnet och längst ned, till höger i en region av intresse. Ange värdena i intervallet 0,0 till 1,0. Värdena är en del av den totala bredden eller höjden. Till exempel markerar i ovanstående exempel höger halvan av bilden som intressant område. Inkludera den om du vill begränsa insikter begäran till regionen av intresse.

Den `filters` objekt innehåller ett filter för webbplatsen (finns i `site` fält) som du kan använda för att begränsa liknande bilder och liknande produkter resultat till en specifik domän. Till exempel om bilden är av en Surface Book, du kan ange `site` till www.microsoft.com. 

Överför avbildningen som binära data om du vill få insikter om en lokal kopia av en bild.

Mer information om dessa alternativ, inklusive i själva efter finns [innehållstyper formuläret](#content-form-types).


### <a name="endpoint"></a>Slutpunkt

Visual Sök-slutpunkten är: https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Förfrågningar måste skickas som en HTTP POST-begäranden. 


### <a name="query-parameters"></a>Frågeparametrar

Följande är din begäran ska ange frågeparametrar. Du bör ta minst den `mkt` Frågeparametern.

|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="cc" />kopia|En 2-tecknet landskoden för det land där resultatet kommer från.<br /><br /> Om du anger den här parametern, måste du också ange den [acceptera språk](#acceptlanguage) huvud. Bing används det första språk som stöds den hittar från en lista med språk och kombinerar språket landskoden som du anger för att fastställa att returnera resultat från marknaden. Om listan språk inte innehåller ett språk som stöds, hittar Bing närmaste språk och marknaden som har stöd för begäran. Eller kan använda en aggregerade eller standard marknaden resultat i stället för det angivna.<br /><br /> Du bör använda den här Frågeparametern och `Accept-Language` Frågeparametern endast om du anger flera språk; i annat fall bör du använda den `mkt` och `setLang` fråga parametrar.<br /><br /> Den här parametern och [mkt](#mkt) frågeparameter är ömsesidigt uteslutande&mdash;inte ange båda.|Sträng|Nej|  
|<a name="mkt" />mkt|Marknaden var resultatet ska hämtas. <br /><br /> **Obs:** du rekommenderas att alltid ange marknaden, om den är känd. Ange marknaden hjälper Bing vidarebefordra begäran och returnera ett lämpligt och optimala svar.<br /><br /> Den här parametern och [kopia](#cc) frågeparameter är ömsesidigt uteslutande&mdash;inte ange båda.|Sträng|Ja|  
|<a name="safesearch" />säker sökning|Ett filter som används för att filtrera vuxet innehåll. Följande är möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Inaktivera&mdash;returnera webbplatser med pornografiskt innehåll text och bilder.<br /><br/></li><li>Måttlig&mdash;returnera webbplatser med pornografiskt innehåll text, men inte vuxna bilder.<br /><br/></li><li>Strikt&mdash;inte returnerar webbplatser med pornografiskt innehåll text och bilder.</li></ul><br /> Standardvärdet är Måttlig.<br /><br /> **Obs:** om begäran kommer från en marknad som Bing vuxna principen kräver att `safeSearch` anges till Strict, Bing ignorerar det `safeSearch` värdet och använder Strict.<br/><br/>**Obs:** om du använder den `site:` frågeoperatorn, finns risken att svaret kan innehålla innehåll för vuxna oavsett vad de `safeSearch` frågeparameter har angetts till. Använd `site:` om du är medveten om innehållet på webbplatsen och ditt scenario stöder möjligheten att vuxet innehåll. |Sträng|Nej|  
|<a name="setlang" />setLang|Språket som ska användas för gränssnittet användarsträngar. Ange språk med hjälp av koden 2 bokstäver ISO 639-1. Språkkod för engelska är till exempel EN. Standardvärdet är EN (på engelska).<br /><br /> Även om det är valfritt, bör du alltid ange språk. Normalt anger du `setLang` till samma språk som anges av `mkt` om användaren vill användarsträngar för gränssnitt som visas i ett annat språk.<br /><br /> Den här parametern och [acceptera språk](#acceptlanguage) huvud är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> En användare gränssnittet strängen är en sträng som används som en etikett i ett användargränssnitt. Det finns några användare gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svaret objekt gäller också det angivna språket.|Sträng|Nej| 

### <a name="headers"></a>Sidhuvuden

Följande är sidhuvuden som din begäran ska anges. Content-Type och Ocp-Apim-prenumeration-nyckel huvuden är endast obligatoriska huvuden men du bör också innehålla användaragent, ClientID-MSEdge-X, X-MSEdge-ClientIP och X sökplats.


|Sidhuvud|Beskrivning|  
|------------|-----------------|  
|<a name="acceptlanguage" />Acceptera språk|Valfria begärandehuvudet.<br /><br /> En kommaavgränsad lista över språk som ska användas för gränssnittet användarsträngar. Listan är i fallande preferensordning. Mer information, inklusive förväntat format finns i [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Det här huvudet och [setLang](#setlang) frågeparameter är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> Om du anger det här sidhuvudet, måste du också ange den [kopia](#cc) Frågeparametern. För att fastställa marknaden att returnera resultat för Bing använder det första språket den söker efter i listan och kombineras med de `cc` parametervärdet. Om listan inte innehåller ett språk som stöds, Bing hittar närmaste språk och marknaden som har stöd för begäran eller den använder en aggregerade eller standard marknaden för resultat. För att fastställa marknaden som används för Bing finns i rubriken BingAPIs marknaden.<br /><br /> Använd den här rubriken och `cc` Frågeparametern endast om du anger flera språk. Annars kan du använda den [mkt](#mkt) och [setLang](#setlang) fråga parametrar.<br /><br /> En användare gränssnittet strängen är en sträng som används som en etikett i ett användargränssnitt. Det finns några användare gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svaret objekt gäller det angivna språket.|  
|<a name="contenttype" />Innehållstyp|Nödvändiga begärandehuvudet.<br /><br />Måste anges till multipart-formulärdata och inkludera en gränsparameter (till exempel multipart-formulärdata; boundary =\<gräns sträng\>). Mer information finns i [innehållstyper formuläret](#content-form-types).
|<a name="market" />BingAPIs marknaden|Svarsrubrik.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Till exempel en-US.|  
|<a name="traceid" />BingAPIs TraceId|Svarsrubrik.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår avbilda detta ID. Om det inte går att identifiera och lösa problemet, inkludera detta ID tillsammans med den information som du anger supportgruppen.|  
|<a name="subscriptionkey" />OCP-Apim-prenumeration-nyckel|Nödvändiga begärandehuvudet.<br /><br /> Nyckeln prenumeration som du fick när du registrerat dig för den här tjänsten i [kognitiva Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Valfritt begärandehuvudet<br /><br /> Som standard returnerar Bing cachelagrat innehåll om det är tillgängligt. Ange rubriken Pragma till no cache för att förhindra att Bing returnerar cachelagrat innehåll (till exempel Pragma: no-cache).
|<a name="useragent" />Användaragent|Valfria begärandehuvudet.<br /><br /> Användaragent som skapade begäran. Bing använder användaragenten för mobila användare med en optimerad upplevelse. Även om det är valfritt, uppmanas du att ange alltid det här sidhuvudet.<br /><br /> Användaragent ska vara samma sträng som används ofta webbläsare skickar. Information om användaragenter finns [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Följande är exempel på användaragent strängar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/PEPPARKAKOR) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (t.ex. Gecko KHTML) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfria huvud för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna en konsekvent beteende i Bing-API-anrop. Bing flygningar ofta nya funktioner och förbättringar och klient-ID används som en nyckel för att tilldela trafik på olika flygplan. Om du inte använder samma klient-ID för en användare över flera förfrågningar, kan sedan Bing tilldela användaren till flera motstridiga flygplan. Tilldelas till flera motstridiga flygplan kan leda till ett inkonsekvent användarupplevelsen. Om andra begäran har en annan svarta tilldelning än först, till exempel vara upplevelsen oväntat. Bing kan också använda klient-ID för att skräddarsy Webbresultat klienten ID Sök tidigare, vilket ger en bättre upplevelse för användaren.<br /><br /> Bing använder också den här rubriken för att förbättra resultatet rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Förbättringar av betydelse hjälpa med bättre resultat som levereras med Bing-API: er och i sin tur aktiverar högre klicka igenom priser för API-klienten.<br /><br /> **Viktigt:** även om det är valfritt, bör du det här sidhuvudet krävs. Beständighet klient-ID över flera förfrågningar för samma användare och enhet kombination kan 1) API klienten att ta emot en konsekvent användarupplevelse och 2) högre klicka igenom priser via bättre kvalitet av resultat från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för det här sidhuvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing skapas klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran Bing genererar ett ID och returnerar i X-MSEdge-ClientID-Svarsrubrik. Den enda gången som du inte bör inkludera det här huvudet i en begäran är första gången användaren använder din app på enheten.<br /><br/></li><li>**Obs:** måste du se till att detta klient-ID inte är linkable till alla autentiserade användarkontoinformation.</li><li>Använda klient-ID för varje begäran i Bing-API som gör att din app för denna användare på enheten.<br /><br/></li><li>Spara klient-ID. För att bevara ID i en webbläsaren att använda en beständig HTTP-cookie för detta ID används i alla sessioner. Använd inte en sessions-cookie. För andra appar, till exempel mobilappar, använder du enhetens beständig lagring för att bevara ID.<br /><br/>Nästa gång användaren använder din app på enheten, hämta klient-ID som du sparade.</li></ul><br /> **Obs:** Bing svar kanske eller kanske inte med det här sidhuvudet. Om svaret innehåller det här sidhuvudet, avbilda klient-ID och använda det för alla efterföljande Bing-förfrågningar för användare på enheten.<br /><br /> **Obs:** om du inkluderar X-MSEdge-ClientID, får inte innehålla cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfria begärandehuvudet.<br /><br /> Klientenheten IPv4 eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder Platsinformationen för att fastställa säker sökbeteendet.<br /><br /> **Obs:** även om det är valfritt, du uppmanas att ange alltid detta huvud och huvudet X sökplats.<br /><br /> Obfuscate inte adressen (till exempel genom att ändra den sista oktetten 0). Obfuscating adress resultaten på den plats som inte var som helst nära enhetens verkliga plats, vilket kan medföra Bing betjänar felaktiga resultat.|  
|<a name="location" />X sökplats|Valfria begärandehuvudet.<br /><br /> En semikolonavgränsad lista över nyckel/värde-par som beskriver klientens geografisk plats. Bing använder platsinformation för att fastställa säker sökbeteendet och returnera relevanta lokalt innehåll. Ange nyckel/värde-par som \<nyckeln\>:\<värdet\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;krävs. Latitud för klientens plats i grader. Latitud måste vara större än eller lika med-90.0 och mindre än eller lika med +90.0. Negativa värden anger södra latituderna och positiva värden anger norra latituderna.<br /><br /></li><li>lång&mdash;krävs. Longituden för klientens plats i grader. Longituden måste vara större än eller lika med-180.0 och mindre än eller lika med +180.0. Negativa värden anger western longitudes och positiva värden anger eastern longitudes.<br /><br /></li><li>RE&mdash;krävs. Radien, i meter, som anger koordinaterna vågräta noggrannhet. Skicka värdet som returneras av enhetens platstjänsten. Typiska värdena kan vara 22m för GPS-WiFi, 380m för cellen torn triangulering och 18 000 m för omvänd sökning av IP.<br /><br /></li><li>TS&mdash;valfria. UNIX UTC-tidsstämpel för när klienten har på plats. (UNIX-tidsstämpel är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>HEAD&mdash;valfritt. Klientens relativa rubrik eller resa riktning. Ange riktningen för resa som grader från 0 till 360, inventering medurs i förhållande till true Nord. Ange den här nyckeln om den `sp` nyckel är noll.<br /><br /></li><li>SP&mdash;valfria. Den vågräta hastigheten (hastighet) i meter per sekund som färdas klientenheten.<br /><br /></li><li>ALT&mdash;valfritt. Klientenheten i meter höjd.<br /><br /></li><li>är&mdash;valfritt. Radien, i meter, som anger den lodräta riktigheten koordinaterna. Ange den här nyckeln om du anger den `alt` nyckel.<br /><br /></li></ul> **Obs:** men många nycklarna är valfritt, det mer information som du anger desto mer exakt plats är.<br /><br /> **Obs:** även om det är valfritt, du uppmanas att alltid ange användarens geografisk plats. Att tillhandahålla platsen är särskilt viktigt om klientens IP-adress inte korrekt speglar användarens fysisk plats (till exempel om klienten använder VPN). För bästa resultat bör du bör ta med detta huvud och huvudet X-MSEdge-ClientIP, men minst bör du ta med det här sidhuvudet.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren kräver följer alla tillämpliga lagar, inklusive om användning av dessa huvuden. Till exempel vissa jurisdiktioner, till exempel Europa, det finns i kraven för att hämta användarens medgivande innan släpps vissa spårning på användarens enheter.


<a name="content-form-types" />

### <a name="content-form-types"></a>Typer av innehåll formulär

Varje begäran måste innehålla ett Content-Type-huvudet. Huvudet måste anges till: multipart/form-data. gräns =\<gräns sträng\>, där \<gräns sträng\> är en unik, täckande sträng som identifierar gränsen för formulärdata. Till exempel gräns = boundary_1234 abcd.


Om du skickar Visual Sök en bild-token eller en URL, visar följande formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla rubriken Content-Disposition och dess `name` parametern måste anges till ”knowledgeRequest”. Mer information om den `imageInfo` objekt, se [begäran](#the-request).


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

Om du överför en lokal image visas nedan formulärdata måste du inkludera i brödtexten i INLÄGGET. Formulärdata måste innehålla rubriken Content-Disposition. Dess `name` parametern måste anges till ”bild” och `filename` parameter kan anges till en sträng. Content-Type-huvudet kan anges till alla vanliga mime-bildtypen. Innehållet i formuläret är binär för bilden. Du kan ladda upp maximala bildstorleken är 1 MB. 


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Nedan visas hur du anger intressant överförda bildens område.

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

Nedan visas en klar bild insikter begäran som skickar ett image-token och intressant område. Du kan få insikter-token från ett tidigare anrop till /images/search.


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

Om det finns insikter för avbildningen, svaret innehåller en eller flera `tags` som innehåller insikter. Den `image` fältet innehåller insikter token för inkommande avbildningen.

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

Den `tags` fältet innehåller ett visningsnamn och en lista med åtgärder (insikter). En av taggarna innehåller en `displayName` fält som har angetts till en tom sträng. Taggen innehåller insikter som standard, till exempel webbsidor som innehåller bilden, likartade bilder och shopping källor för objekt hittades i bilden. Eftersom hela bilden intressanta innehåller insikter Standardetiketten avgränsar kryssrutorna för regionerna av intresse.


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

En lista över insikter som standard finns [standard insikter](./default-insights-tag.md).



De återstående taggarna innehåller andra insikter som kan vara av intresse för användaren. Om bilden innehåller text, kan en av taggarna omfatta en TextResults kunskaper som innehåller den tolkade texten. Eller, om Bing känner igen en entitet (person, plats eller sak) i bilden, en av taggarna kan identifiera enheten. Visual Sök också returnerar en mängd olika villkor (taggar) som härletts från den inkommande bilden. Dessa taggar Tillåt användare att utforska begrepp som hittades i avbildningen. Till exempel om den inkommande avbildningen berömda Idrottare, kan ett taggar vara sport, som innehåller länkar till avbildningar av sport.

Varje tagg innehåller ett visningsnamn som du kan använda för att kategorisera insight, angränsande ruta som identifierar regionen intresse som gäller information och insikter själva en miniatyrbild för bilden. Om avbildningen är av en person med en sport jersey, kan en taggar innehålla en avgränsningsram som utrymmet på jersey och innehåller VisualSearch och ProductVisualSearch insikter. Och en annan tagg kan innehålla överblick ImageResults som innehåller en URL för en /images/search API-begäran att hämta avbildningar som är relaterade appliceras eller en Bing.com URL som tar användaren att sökresultaten Bing.com avbildningen.

Alla taggar än insikter Standardtaggen omfattar avgränsar rutor som identifierar områden av intresse för bilden. Till exempel om bilden innehåller flera tolkade personer, taggar kan omfatta avgränsar rutor för var och en av personerna eller om bilden innehåller tolkade kläder objekt, taggar kan omfatta avgränsar kryssrutorna för varje identifierad kläder-objekt. Du kan använda rutorna omgivande innehåller information om innehållet i den regionen där bilden om du vill skapa aktiva punkter över bilden som när du klickar på. Du bör inte innehålla aktiva punkter i en bild för omgivande rutor som identifierar hela bilden.

### <a name="text-recognition"></a>Textigenkänning

Om bilden innehåller text som identifierar tjänsten, innehåller en av taggarna en TextResults insight (åtgärd). Information `displayName` innehåller den tolkade texten. 

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

Eftersom taggen `displayName` fältet innehåller ##TextRecognition Använd inte det som en kategori title i UX. Att försätta för alla namn som börjar med ##. I stället använda åtgärdens namn.


Text recognition kan identifiera kontaktinformation på visitkort, till exempel telefonnummer och e-postadresser. Markeringsrutan identifierar platsen för kontaktinformation på kortet. 

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

Om bilden innehåller en känd enhet, till exempel en person, en plats eller en sak, omfatta en av taggarna en entitet analys. Enheter kan också innehålla kunskap som visas i följande exempel:

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
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "Trivia",
          "data" : {
            "value" : [
              {
                "name" : "Where was the cornerstone of the statue of liberty laid",
                "text" : "<the answer>",
                "hostPageUrl" : "http:\/\/contoso.com\/history\/...",
              },
              {
                "name" : "Why Is the Statue of Liberty Green",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/why-statue-of-liberty-is-green",
              },
              {
                "name" : "What is the Statue of Liberty made of",
                "text" : "<the answer>",
                "hostPageUrl" : "https:\/\/www.contoso.com\/art-literature\/statue-liberty-made",
              }
            ]
          }
        }
      ]
    }
```



## <a name="next-steps"></a>Nästa steg

Om du vill komma igång snabbt med din första begäran finns i Snabbstart: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md)  |  [Python](quickstarts/python.md).

Prova att använda API: et. Gå till [Visual Sök API Testing-konsolen](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac). 


Bekanta dig med de [API-referens för Visual Sök](https://aka.ms/bingvisualsearchreferencedoc). Referensen innehåller listan över slutpunkter, rubriker och frågeparametrar som du vill använda för att begära sökresultat. Den omfattar också definitioner av objekt som svar. 

Se till att läsa [Bing användas och visa krav](./use-and-display-requirements.md) så att du inte delar någon av reglerna om hur du använder sökresultatet.


