---
title: Referens för Project Answer Search
titlesuffix: Azure Cognitive Services
description: Referens för projekt svars Sök slut punkt.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 28449435479aef0d6a1d8aee3e53de1a78f401b3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220824"
---
# <a name="project-answer-search-v7-reference"></a>Projekt svars sökning v7-referens

Bing-svar SearchAPI använder en frågeparameter och returnerar en `searchResponse` med `answerType`: `facts` eller `entities`. 

Program som använder API: et för svars sökning för att skicka begär anden till slut punkten med en URL som ska förhandsgranskas i en frågeparameter.  Begäran måste innehålla `q=searchTerm` parameter och *OCP-APIM-Subscription-Key-* huvud.   

JSON-svaret kan tolkas för fakta och entiteter som innehåller information om Sök objektet.

## <a name="endpoint"></a>Slutpunkt
Skicka en begäran till följande slut punkt för att begära svars Sök resultat. Använd sidhuvudena och URL-parametrarna för att definiera ytterligare specifikationer.

Slut punkt GET: 
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

```

Begäran måste använda HTTPS-protokollet och inkludera följande frågeparameter:
-  `q=<URL>` – frågan som identifierar Sök objektet

Exempel som visar hur du gör förfrågningar finns i [ C# snabb start](c-sharp-quickstart.md) eller [Java snabb start](java-quickstart.md). 

Följande avsnitt innehåller teknisk information om svars objekt, frågeparametrar och rubriker som påverkar Sök resultaten. 
  
Information om rubriker som begär Anden ska inkludera finns i [sidhuvud](#headers).  
  
Information om frågeparametrar som begär Anden ska inkludera finns i [frågeparametrar](#query-parameters).  
  
Information om JSON-objekten som svaret innehåller finns i [Response Objects](#response-objects).

Maximal längd på fråge-URL är 2 048 tecken. För att se till att URL-längden inte överskrider gränsen ska den maximala längden för dina frågeparametrar innehålla färre än 1 500 tecken. Om URL: en överskrider 2 048 tecken returnerar servern 404 som inte hittas.  

Information om tillåten användning och visning av resultat finns i [använda och Visa krav](use-display-requirements.md). 

> [!NOTE]
> Vissa begärandehuvuden som är meningsfulla för andra Sök-API: er påverkar inte URL-förhands granskning
> - Pragma – anroparen har inte kontroll över om förhands granskning av URL använder cache
> - Cache-Control – anroparen har inte kontroll över om förhands granskning av URL använder cache
> - User-Agent
> 
> Dessutom är vissa parametrar för närvarande inte meningsfulla för URL för för hands versionen, men kan användas i framtiden för förbättrad globalisering. 
 
## <a name="headers"></a>Rubriker  
Följande är huvuden som en begäran och ett svar kan innehålla.  
  
|Huvud|Beskrivning|  
|------------|-----------------|  
|Godkänn|Valfritt begärandehuvud.<br /><br /> Standard medie typen är Application/JSON. Om du vill ange att svaret använder [JSON-LD](https://json-ld.org/), ställer du in accept-huvudet på Application/LD + JSON.|  
|<a name="acceptlanguage" />Accept-Language|Valfritt begärandehuvud.<br /><br /> En kommaavgränsad lista över språk som ska användas för användargränssnittssträngar. Listan är i fallande prioritetsordning. Mer information, bland annat om det förväntade formatet, finns i [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Det är huvudet och [setLang](#setlang)-frågeparametern utesluter varandra&mdash;ange inte båda två.<br /><br /> Om du anger den här rubriken måste du också ange parametern CC-fråga. För att fastställa vilken marknad som resultat ska returneras för använder Bing det första språk som stöds på listan och kombinerar det med parametervärdet `cc`. Om listan inte innehåller något språk som stöds hittar Bing det närmaste språket och marknaden som har stöd för begäran, eller så använder Bing en aggregerad eller standardmarknad för resultatet. För att avgöra vilken marknad som används i Bing kan du gå till BingAPIs-Market-huvudet.<br /><br /> Använd enbart det här huvudet och `cc`-frågeparametern om du anger flera språk. Annars kan du använda frågeparametrarna [mkt](#mkt) och [setLang](#setlang).<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.|  
|<a name="market" />BingAPIs-Market|Svarshuvud.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Exempel: sv-SE.|  
|<a name="traceid" />BingAPIs-TraceId|Svarshuvud.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår ska du avbilda detta ID. Om det inte går att fastställa och lösa problemet ska du ange ID:t tillsammans med annan information som du ger supportteamet.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Begärandehuvud som krävs.<br /><br /> Prenumerationsnyckeln som du fick när du registrerade dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Valfritt begärandehuvud<br /><br /> Som standard returnerar Bing cachelagrat innehåll om det finns. Om du vill förhindra att Bing returnerar cachelagrat innehåll ska du ställa in huvudet Pragma på no-cache (till exempel Pragma: no-cache).
|<a name="useragent" />User-Agent|Valfritt begärandehuvud.<br /><br /> Användaragenten som skapade begäran. Bing använder användaragenten för att ge mobila användare en optimerad upplevelse. Även om det är valfritt rekommenderar vi även att du alltid anger det här huvudet.<br /><br /> Användaragenten ska vara samma sträng som alla vanliga webbläsare skickar. Information om användaragenter finns i [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Här följer några exempel på användaragentsträngar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (KHTML, som Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfritt huvud för begäran och svar.<br /><br /> Bing använder det här huvudet för att ge användarna konsekvent beteende i Bing API-anrop. Bing ger ofta förhandsversioner av nya funktioner och förbättringar och använder klient-ID som en nyckel för att tilldela trafik till olika förhandsversioner. Om du inte använder samma klient-ID för en användare vid flera förfrågningar kan sedan Bing tilldela användaren flera motstridiga förhandsversioner. Om du tilldelas flera motstridiga förhandsversioner kan det leda till en inkonsekvent användarupplevelse. Om till exempel den andra begäran har en annan förhandsversionstilldelning än den första kan upplevelsen vara oväntad. Bing kan också använda klient-ID för att skräddarsy webbresultatet för klient-ID:ts sökhistorik, vilket ger användaren en mer omfattande upplevelse.<br /><br /> Bing använder också det här huvudet för att förbättra resultatets rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Relevansförbättringarna kan ge bättre resultat som levereras av Bing-API: er, vilka i sin tur möjliggör högre klickfrekvens för API-konsumenten.<br /><br /> **Viktigt!** Även om det är valfritt bör du överväga att använda det här huvudet som krävs. Bestående klient-ID för flera förfrågningar för samma slutanvändare och enhetskombination gör det möjligt 1) för API-konsumenten att få en konsekvent användarupplevelse och 2) att få högre klickfrekvens via resultat av högre kvalitet från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för det här huvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing-genererat klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran genererar Bing ett ID och returnerar det i svarshuvudet X-MSEdge-ClientID. Den enda gången som du inte får inkludera det här huvudet i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>Använd klient-ID för varje Bing API-begäran som din app gör för den här användaren på enheten.<br /><br/></li><li>**Uppmärksamhet:** Du måste se till att det här klient-ID: t inte kan länka till information som kan autentiseras för användar konton.</li><br/><li>Spara klient-ID:t. Om du vill bevara ID:t i en webbläsarapp ska du använda en beständig HTTP-cookie i alla sessioner. Använd inte en sessionscookie. För andra appar, till exempel mobilappar, använder du enhetens beständiga lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten ska du hämta klient-ID:t som du sparade.</li></ul><br /> **Obs!** Bing-svar kanske eller kanske inte omfattar det här huvudet. Om svaret innehåller det här huvudet ska du avbilda klient-ID:t och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs!** Om du inkluderar X-MSEdge-ClientID får du inte ta med cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfritt begärandehuvud.<br /><br /> Klientenhetens IPv4- eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder platsinformationen för att fastställa SafeSearch-beteende.<br /><br /> **Obs!** Även om det är valfritt rekommenderar vi att du alltid anger det här huvudet och huvudet X-Search-Location.<br /><br /> Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Om adressresultatet förvillas på en plats som inte är i närheten av enhetens verkliga plats kan det leda till att Bing presenterar felaktiga resultat.|  
|<a name="location" />X-Search-Location|Valfritt begärandehuvud.<br /><br /> En semikolonavgränsad lista med nyckel/värde-par som beskriver klientens geografiska plats. Bing använder platsinformationen till att fastställa ett säkert sökbeteende och returnera relevant lokalt innehåll. Ange nyckel/värde-par som \<nyckel\>:\<värde\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;latitud för klientens plats, i grader. Latituden måste vara större än eller lika med -90.0 och vara mindre än eller lika med +90.0. Negativa värden anger sydliga latituder och positiva värden anger nordliga latituder.<br /><br /></li><li>långt&mdash;longituden för klientens plats, i grader. Longituden måste vara större än eller lika med -180.0 och vara mindre än eller lika med +180.0. Negativa värden anger västliga longituder och positiva värden anger östliga longituder.<br /><br /></li><li>&mdash; radien i meter som anger koordinaternas horisontella noggrannhet. Skicka värdet som returneras av enhetens platstjänst. Typiska värden kan vara 22 m för GPS/Wi-Fi, 380 m för mobiltornstriangulering och 18 000 m för omvänd IP-sökning.<br /><br /></li><li>TS&mdash; UTC-tidsstämpeln för UTC-tid för när klienten var på platsen. (UNIX-tidsstämpeln är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>head&mdash;Optional. Klientens relativa riktning eller färdriktning. Ange färdriktningen som grader från 0 till 360 där du räknar medurs i förhållande till norr. Ange endast den här nyckeln om nyckeln `sp` är nollskiljd.<br /><br /></li><li>SP&mdash; den horisontella hastigheten i meter per sekund som klient enheten reser.<br /><br /></li><li>Alt&mdash; klient enhetens höjd i meter.<br /><br /></li><li>are&mdash;Valfritt. Radien, i meter, som anger koordinaternas lodräta exakthet. Standardvärdet är 50-kilo meter. Ange den här nyckeln endast om du anger nyckeln `alt`.<br /><br /></li></ul> **Obs:** Även om dessa nycklar är valfria, desto mer information som du anger, desto bättre blir plats resultaten.<br /><br /> **Obs:** Du uppmanas att alltid ange användarens geografiska plats. Det är särskilt viktigt att ange plats om klientens IP-adress inte exakt avspeglar användarens fysiska plats (till exempel om klienten använder VPN). För bästa resultat bör du inkludera det här huvudet och huvudet X-MSEdge-ClientIP, men du bör minst inkludera det här huvudet.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren kräver efterlevnad med alla tillämpliga lagar, inklusive lagar om användande av dessa huvud. I till exempel vissa jurisdiktioner, som Europa, finns det krav på att skaffa användarens medgivande innan du placerar vissa spårningsenheter på användarenheter.
  

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande frågeparametrar. Se kolumnen obligatorisk för obligatoriska parametrar. Du måste URL-koda parametrarna för frågan.  
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marknaden som resultatet kommer från. <br /><br />En lista över möjliga marknads värden finns i marknads koder.<br /><br /> **Obs:** URL: en för hands versions-API stöder för närvarande endast en-US marknad och språk.<br /><br />|String|Ja|  
|<a name="query" />q|URL till för hands version|String|Ja|  
|<a name="responseformat" />responseFormat|Den medietyp som ska användas för svaret. Följande är möjliga Skift läges känsliga värden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. Information om JSON-objekt som svaret innehåller finns i [Response Objects](#response-objects).<br /><br />  Om du anger JsonLd innehåller svars texten JSON-LD objekt som innehåller Sök resultaten. Information om JSON-LD finns i [JSON-LD](https://json-ld.org/).|String|Nej|  
|<a name="safesearch" />safeSearch|Ett filter som används för att filtrera innehåll som är olämpligt för barn. Här följer de möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Ut&mdash;returnera webb sidor med vuxen text, bilder eller videor.<br /><br/></li><li>Måttlig&mdash;returnerar webb sidor med vuxen text, men inte vuxna bilder eller videor.<br /><br/></li><li>Strikt&mdash;returnerar inte webb sidor med vuxen text, bilder eller videor.</li></ul><br /> Standardinställningen är Måttlig.<br /><br /> **Obs:** Om begäran kommer från en marknad som Bing: s vuxen princip kräver att `safeSearch` är inställd på Strict, ignorerar Bing `safeSearch`-värdet och använder strikt.<br/><br/>**Obs!** Beroende på om du använder frågeoperator `site:` finns det en risk att svaret har innehåll som är olämpligt för barn oberoende av vad frågeparametern `safeSearch` är inställd på. Använd endast `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn. |String|Nej|  
|<a name="setlang" />setLang|Språket som ska användas för användargränssnittssträngar. Ange språk med hjälp av den tvåstaviga språkkoden ISO 639-1. Språkkoden för engelska är till exempel EN. Standardvärdet är EN (engelska).<br /><br /> Även om det är valfritt bör du alltid ange språket. Normalt anger du `setLang` på samma språk som anges av `mkt` om inte användaren vill att gränssnittets strängar ska visas på ett annat språk.<br /><br /> Den här parametern och [Accept-Language](#acceptlanguage)-huvudet utesluter varandra&mdash;ange inte båda två.<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.|String|Nej| 


## <a name="response-objects"></a>Svars objekt  
Svars schema är antingen en [webb sida] eller ErrorResponse, som i Webbsökning-API: et. Om begäran Miss lyckas är objektet på den översta nivån [ErrorResponse](#errorresponse) -objektet.


|Objekt|Beskrivning|  
|------------|-----------------|  
|Historik|JSON-objekt på översta nivån som innehåller attribut för för hands versionen.|  
|Verkligen|JSON-objekt på översta nivån som innehåller fakta.| 
|[Entiteter|JSON-objekt på översta nivån som innehåller information om entiteter.| 

  
### <a name="error"></a>Fel  
Definierar det fel som inträffat.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />kod|Felkoden som identifierar fel kategorin. En lista över möjliga koder finns i [felkoder](#error-codes).|String|  
|<a name="error-message" />meddelande|En beskrivning av felet.|String|  
|<a name="error-moredetails" />moreDetails|En beskrivning som ger ytterligare information om felet.|String|  
|<a name="error-parameter" />parameter|Frågeparametern i den begäran som orsakade felet.|String|  
|<a name="error-subcode" />under kod|Felkoden som identifierar felet. Om `code` till exempel är InvalidRequest kan `subCode` vara ParameterInvalid eller ParameterInvalidValue. |String|  
|<a name="error-value" />värde|Värdet för frågeparametern är inte giltigt.|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
Objektet på den översta nivån som svaret innehåller när begäran Miss lyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typ tips.|String|  
|<a name="errors" />fel|En lista med fel som beskriver orsakerna till att begäran misslyckades.|[Fels](#error)|  

  
  
### <a name="license"></a>Licens  
Definierar under vilken licens texten eller fotot kan användas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Namnet på licensen.|String|  
|url|URL: en till en webbplats där användaren kan få mer information om licensen.<br /><br /> Använd namnet och URL: en för att skapa en hyperlänk.|String|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definierar en avtals regel för licens behörighet.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Ett typ tips, som är inställt på LicenseAttribution.|String|  
|licensavtalet|Den licens enligt vilken innehållet kan användas.|[Licensavtalet](#license)|  
|licenseNotice|Den licens som ska visas bredvid fältet mål. Till exempel "text under CC-BY-SA-licens".<br /><br /> Använd licensens namn och URL i fältet `license` om du vill skapa en hyperlänk till webbplatsen som beskriver licensens information. Ersätt sedan licens namnet i `licenseNotice` strängen (till exempel CC-BY-SA) med den hyperlänk som du nyss skapade.|String|  
|mustBeCloseToContent|Ett booleskt värde som anger om regelns innehåll måste placeras i närheten av det fält som regeln gäller för. Om det här **värdet är sant**måste innehållet placeras i nära närhet. Om det är **falskt**, eller om fältet inte finns, kan innehållet placeras på den som ringer upp.|Boolean|  
|targetPropertyName|Namnet på det fält som regeln gäller för.|String|  
  

### <a name="link"></a>Länk  
Definierar komponenterna i en hyperlänk.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typ tips.|String|  
|text|Visnings texten.|String|  
|url|EN URL. Använd webb adressen och visnings texten för att skapa en hyperlänk.|String|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definierar en avtals regel för länk behörighet.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Ett typ tips, som är inställt på LinkAttribution.|String|  
|mustBeCloseToContent|Ett booleskt värde som anger om regelns innehåll måste placeras i närheten av det fält som regeln gäller för. Om det här **värdet är sant**måste innehållet placeras i nära närhet. Om det är **falskt**, eller om fältet inte finns, kan innehållet placeras på den som ringer upp.|Boolean|  
|targetPropertyName|Namnet på det fält som regeln gäller för.<br /><br /> Om ett mål inte anges, gäller behörigheten för entiteten som helhet och visas omedelbart efter Entity-presentationen. Om det finns flera text-och länk behörighets regler som inte anger ett mål, ska du sammanfoga dem och visa dem med hjälp av etiketten "data från:". Till exempel "data från < Provider name1\> &#124; < provider name2\>".|String|  
|text|Behörighets texten.|String|  
|url|URL: en till leverantörens webbplats. Använd `text` och URL för att skapa en hyperlänk.|String|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definierar en avtals regel för medie behörighet.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Ett typ tips, som är inställt på MediaAttribution.|String|  
|mustBeCloseToContent|Ett booleskt värde som anger om regelns innehåll måste placeras i närheten av det fält som regeln gäller för. Om det här **värdet är sant**måste innehållet placeras i nära närhet. Om det är **falskt**, eller om fältet inte finns, kan innehållet placeras på den som ringer upp.|Boolean|  
|targetPropertyName|Namnet på det fält som regeln gäller för.|String|  
|url|Den URL som du använder för att skapa hyperlänken för medie innehållet. Om målet till exempel är en bild, använder du URL: en för att göra det möjligt att klicka på bilden.|String|  
  
  
  
### <a name="organization"></a>Organisation  
Definierar en utgivare.  
  
Observera att en utgivare kan ange sitt namn eller deras webbplats eller både och.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Utgivarens namn.|String|  
|url|URL: en till utgivarens webbplats.<br /><br /> Observera att utgivaren inte kan tillhandahålla en webbplats.|String|  
  
  

### <a name="webpage"></a>Historik  
Definierar information om en webb sida som förhands granskning.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|
|namn|Sid rubriken, inte nödvändigt vis HTML-titeln|String|
|url|Den URL som faktiskt crawlades (begäran kan ha följt omdirigeringar)|String|  
|beskrivning|Kort beskrivning av sidan och innehållet|String|  
|isFamilyFriendly|Mest exakt för objekt i webb indexet; Real tids hämtningar utför den här identifieringen enbart utifrån URL: en och inte sid innehållet|boolean|
|primaryImageOfPage/contentUrl|URL till en representativ bild som ska tas med i förhands granskningen|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Definierar den frågeterm som Bing används för begäran.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Ett booleskt värde som anger om den angivna frågan har vuxen avsikt. Värdet är **True** om frågan har vuxen avsikt; annars **false**.|Boolean|  
|alterationOverrideQuery|Frågesträngen som ska användas för att tvinga Bing att använda den ursprungliga strängen. Om frågesträngen t. ex. är *Saling downwind*, kommer frågesträngen för åsidosättning *+ Saling downwind*. Kom ihåg att koda frågesträngen som resulterar i *% 2Bsaling + downwind*.<br /><br /> Det här fältet tas med endast om den ursprungliga frågesträngen innehåller en stavnings fel.|String|  
|alteredQuery|Frågesträngen som används av Bing för att utföra frågan. Bing använder den ändrade frågesträngen om den ursprungliga frågesträngen innehåller stavfel. Om frågesträngen till exempel är `saling downwind`, kommer den ändrade frågesträngen att `sailing downwind`.<br /><br /> Det här fältet tas med endast om den ursprungliga frågesträngen innehåller en stavnings fel.|String|  
|askUserForLocation|Ett booleskt värde som anger om Bing kräver användarens plats för att ge korrekta resultat. Om du har angett användarens plats med hjälp av huvudena [X-MSEdge-ClientIP](#clientip) och [X-search-location](#location) kan du ignorera det här fältet.<br /><br /> För plats medveten frågor, till exempel "Dagens väder" eller "restauranger nära mig" som behöver användarens plats för att ge korrekta resultat, är det här fältet inställt på **Sant**.<br /><br /> För plats medveten frågor som omfattar platsen (till exempel "Seattle-väder") är det här fältet inställt på **falskt**. Det här fältet är också inställt på **falskt** för frågor som inte är medvetna, till exempel "bästa säljare".|Boolean|  
|originalQuery|Frågesträngen som anges i begäran.|String|  

### <a name="identifiable"></a>Särskilj

|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|id|Ett resurs-ID|String|
 
### <a name="rankinggroup"></a>RankingGroup
Definierar en Sök Resultat grupp, till exempel Mainline.

|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|items|En lista med Sök resultat som ska visas i gruppen.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definierar ett Sök Resultat objekt som ska visas.

|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|resultIndex|Ett nollbaserat index för objektet i svaret som ska visas. Om objektet inte innehåller det här fältet, visar du alla objekt i svaret. Du kan till exempel Visa alla nyhets artiklar i svaret på diskussions gruppen.|Integer|
|answerType|Svaret som innehåller objektet som ska visas. Till exempel nyheter.<br /><br />Använd typen för att hitta svaret i SearchResponse-objektet. Typen är namnet på ett SearchResponse-fält.<br /><br /> Använd bara svars typen om det här objektet innehåller fältet värde. Annars kan du ignorera det.|String|
|textualIndex|Index för det svar i textualAnswers som ska visas.| Osignerat heltal|
|värde|Det ID som identifierar antingen ett svar för att visa eller ett objekt i ett svar att visa. Om ID: t identifierar ett svar visas alla svars objekt.|Särskilj|

### <a name="rankingresponse"></a>RankingResponse  
Definierar var på sidan med Sök Resultat sidans innehåll ska placeras och i vilken ordning.  
  
|Namn|Värde|  
|----------|-----------|  
|<a name="ranking-mainline" />Mainline|Sök resultaten som ska visas i Mainline.|  
|<a name="ranking-pole" />Polen|Sök resultaten som ska få den mest synliga behandlingen (till exempel, som visas ovanför Mainline och sido panelen).|  
|<a name="ranking-sidebar" />sid panelen|Sök resultatet visas i sid listen.| 


### <a name="searchresponse"></a>SearchResponse  
Definierar objektet på den översta nivån som svaret innehåller när begäran lyckas.  
  
Observera att om tjänsten misstänker en denial of service-attack kommer begäran att lyckas (HTTP-statuskod är 200 OK). bröd texten i svaret är dock tom.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typ tips, som är inställt på SearchResponse.|String|  
|Historik|Ett JSON-objekt som definierar för hands versionen|sträng|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definierar en avtals regel för oformaterad text-behörighet.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Ett typ tips, som är inställt på TextAttribution.|String|  
|text|Behörighets texten.<br /><br /> Text tilldelningen gäller för hela enheten och ska visas direkt efter entitetens presentation. Om det finns flera text-eller länk behörighets regler som inte anger ett mål, ska du sammanfoga dem och visa dem med hjälp av etiketten "data från:".|String| 


## <a name="error-codes"></a>Felkoder

Följande är de möjliga HTTP-statuskod som en begäran returnerar.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|200|Lyckades.|  
|400|En av frågeparametrar saknas eller är ogiltig.|  
|401|Prenumerations nyckeln saknas eller är ogiltig.|  
|403|Användaren är autentiserad (t. ex. att de använde en giltig prenumerations nyckel) men de har inte behörighet till den begärda resursen.<br /><br /> Bing kan också returnera denna status om anroparen överskred sina frågor per månads kvot.|  
|410|Begäran använde HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokoll som stöds.|  
|429|Anroparen överskred sina frågor per sekund kvot.|  
|500|Oväntat Server fel.|

Om begäran Miss lyckas innehåller svaret ett [ErrorResponse](#errorresponse) -objekt som innehåller en lista med [fel](#error) objekt som beskriver vad som orsakade felet. Om felet är relaterat till en parameter, identifierar `parameter` fältet den parameter som är problemet. Om felet är relaterat till ett parameter värde identifierar `value` fältet det värde som inte är giltigt.

```json
{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidRequest", 
      "subCode": "ParameterMissing", 
      "message": "Required parameter is missing.", 
      "parameter": "q" 
    }
  ]
}

{
  "_type": "ErrorResponse", 
  "errors": [
    {
      "code": "InvalidAuthorization", 
      "subCode": "AuthorizationMissing", 
      "message": "Authorization is required.", 
      "moreDetails": "Subscription key is not recognized."
    }
  ]
}
```

Följande är möjliga felkoder och underordnade fel kod värden.

|Kod|Under kod|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP-statuskod är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockeras|Bing returnerar InvalidRequest när någon del av begäran är ogiltig. Till exempel saknas en obligatorisk parameter eller också är ett parameter värde ogiltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-status koden 400.<br/><br/>Om du använder HTTP-protokollet i stället för HTTPS returnerar Bing HttpNotAllowed och HTTP-statuskod är 410.
|RateLimitExceeded|Inga under koder|Bing returnerar RateLimitExceeded varje gång du överskrider dina frågor per sekund (frågor per sekund) eller frågor per månad (QPM)-kvot.<br/><br/>Om du överskrider frågor per sekund returnerar Bing HTTP-statuskod 429 och om du överskrider QPM returnerar Bing 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel saknas `Ocp-Apim-Subscription-Key` huvud eller så är prenumerations nyckeln inte giltig.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-status koden 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerations nyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-status koden 403.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för Node](node-quickstart.md)
- [Snabbstart för Python](python-quickstart.md)

