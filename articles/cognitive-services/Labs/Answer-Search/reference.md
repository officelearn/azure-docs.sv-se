---
title: Projektet Svarssökning referens – Microsoft Cognitive Services | Microsoft Docs
description: Referens för projektet Svarssökning slutpunkten.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: a12761c2d913cd7ffaa2cbc2cd42576c6bc96434
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866992"
---
# <a name="project-answer-search-v7-reference"></a>Projektet svar Search v7-referens

Bing svar SearchAPI tar en frågeparameter och returnerar en `searchResponse` med `answerType`: `facts` eller `entities`. 

Program som använder API: et för svar Search skicka begäranden till slutpunkten med en URL till förhandsversion i en frågeparameter.  Begäran måste innehålla den `q=searchTerm` parametern och *Ocp-Apim-Subscription-Key* rubrik.   

JSON-svar kan parsas för fakta och entiteter som innehåller information om objekt av search.

## <a name="endpoint"></a>Slutpunkt
Om du vill begära svar sökresultat, skicka en begäran till följande slutpunkt. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

Begäran måste använda HTTPS-protokollet och omfattar följande Frågeparametern:
-  q =<URL> -frågan som identifierar objektet för sökning

Exempel som visar hur du begär finns [C# Snabbstart](c-sharp-quickstart.md) eller [Java-quickstart](java-quickstart.md). 

Följande avsnitt innehåller teknisk information om svarsobjekt, frågeparametrar och rubriker som påverkar sökresultaten. 
  
Läs om hur rubriker som förfrågningar ska innehålla [rubriker](#headers).  
  
Läs om hur Frågeparametrar som förfrågningar ska innehålla [frågeparametrar](#query-parameters).  
  
Information om JSON-objekt att svaret innehåller, finns i [svarsobjekten](#response-objects).

Maximal URL-längd är 2 048 tecken. För att säkerställa att din URL-längd inte överskrider gränsen, ska den maximala längden på dina frågeparametrar vara mindre än 1 500 tecken. Om URL: en överskrider 2 048 tecken, returnerar servern 404 hittades inte.  

Information om tillåtna användning och visning av resultat finns i [använder och visa kraven](use-display-requirements.md). 

> [!NOTE]
> Vissa begärandehuvuden som är viktiga för andra search API: er påverkar inte URL-förhandsgranskning
> - Pragma – anroparen har ingen kontroll över om URL-förhandsgranskning använder cache
> - Cache-Control – anroparen har ingen kontroll över om URL-förhandsgranskning använder cache
> - Användaragent

> Dessutom vissa parametrar är inte för närvarande beskrivande för URL: en förhandsversion API, men kan användas i framtiden för förbättrad globalisering. 
 
## <a name="headers"></a>Sidhuvuden  
Följande är de rubriker som en begäran och svaret kan innehålla.  
  
|Sidhuvud|Beskrivning|  
|------------|-----------------|  
|Godkänn|Valfria rubriken.<br /><br /> Typ av media är application/json. Ange att svaret använda [JSON-LD](http://json-ld.org/), inställd program/ld + json Accept-huvud.|  
|<a name="acceptlanguage" />Acceptera språk|Valfria rubriken.<br /><br /> En kommaavgränsad lista över språk som ska användas för användaren gränssnittet strängar. Listan är i fallande prioritetsordning. Läs mer om, inklusive förväntade formatet [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Den här rubriken och [setLang](#setlang) Frågeparametern är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> Om du ställer in den här rubriken, måste du även ange den [cc](#cc) frågeparameter. För att fastställa på marknaden och returnera resultat för Bing använder det första språk som stöds det söker efter i listan och kombinerar det med den `cc` parametervärde. Om listan inte innehåller ett språk som stöds, Bing hittar närmaste språk och marknaden som har stöd för begäran eller den använder en sammansatt eller default marknaden för resultaten. För att avgöra på marknaden som används i Bing, finns i rubriken BingAPIs marknad.<br /><br /> Använd den här rubriken och `cc` frågeparameter endast om du anger flera språk. Annars kan du använda den [mkt](#mkt) och [setLang](#setlang) Frågeparametrar.<br /><br /> En användare gränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användaren gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svarsobjekten gäller det angivna språket.|  
|<a name="market" />BingAPIs marknad|Svarshuvudet.<br /><br /> På marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Till exempel en-US.|  
|<a name="traceid" />BingAPIs TraceId|Svarshuvudet.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår, avbilda detta ID. Om det inte går att fastställa och lösa problemet, kan du ange följande ID tillsammans med annan information som du anger supporten.|  
|<a name="subscriptionkey" />OCP-Apim-Subscription-Key|Nödvändiga begärandehuvudet.<br /><br /> Prenumerationsnyckel som du fick när du registrerat dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Begärandehuvud i valfritt<br /><br /> Bing returnerar cachelagrat innehåll om de är tillgängliga som standard. Om du vill förhindra att Bing returnera cachelagrat innehåll, ange rubriken Pragma till no cache (till exempel Pragma: no-cache).
|<a name="useragent" />Användaragent|Valfria rubriken.<br /><br /> Användaragenten skapade begäran. Bing använder användaragenten för att tillhandahålla mobila användare med en optimerad upplevelse. Även om det är valfritt, uppmuntras du att alltid ange den här rubriken.<br /><br /> Användaragenten ska vara samma sträng som skickar alla vanliga webbläsare. Läs om hur användaragenter [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Här följer några exempel på användaren agent strängar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/PEPPARKAKOR) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0 Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (t.ex. Gecko KHTML) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfria rubriken för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna konsekvent beteende i Bing API-anrop. Bing flygningar ofta nya funktioner och förbättringar och klient-ID används som en nyckel för att tilldela trafik på olika flyg. Om du inte använder samma klient-ID för en användare över flera förfrågningar, kan sedan Bing tilldela användaren till flera motstridiga flyg. Tilldelas till flera motstridiga flyg kan leda till en inkonsekvent användarupplevelse. Om andra begäran har en annan flygning uppdrag än först, till exempel vara upplevelsen oväntat. Bing kan också använda klient-ID för att skräddarsy Webbresultat till den klienten-ID: n sökhistorik, vilket ger en rikare upplevelse för användaren.<br /><br /> Bing använder också den här rubriken för att förbättra resultatet rangordning genom att analysera aktiviteten som genererats av en klient-ID. Förbättringar för relevans hjälpa med bättre resultat som levereras av Bing-API: er och i sin tur aktiverar högre klicka igenom priser för API-konsument.<br /><br /> **Viktigt:** även om det är valfritt, bör du överväga att den här rubriken som krävs. Spara klient-ID över flera förfrågningar för samma användare och enhet kombination kan (1) API konsumenten att ta emot en konsekvent användarupplevelse och 2) högre klicka igenom kostnader via bättre kvalitet med resultat från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för den här rubriken.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing skapas klient-ID.<br /><br/>Om du inte använder den här rubriken i begäran Bing genererar ett ID och returnerar det i rubriken X-MSEdge-ClientID. Den enda gången som du inte får innehålla den här rubriken i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>Använda klient-ID för varje API för Bing-begäran där din app för den här användaren på enheten.<br /><br/></li><li>**Observera:** måste du se till att detta klient-ID inte är linkable till alla authenticatable kontoinformation.</li><br/><li>Spara klient-ID. Använda en beständig HTTP-cookie för ID: T används i alla sessioner för att bevara ID i en browser-appen. Använd inte en sessions-cookie. För andra appar, till exempel mobilappar, använder du enhetens beständig lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten, hämta klient-ID som du sparade.</li></ul><br /> **Obs:** Bing svar kanske eller kanske inte omfattar den här rubriken. Om svaret innehåller den här rubriken, avbilda klient-ID och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs:** om du inkluderar X-MSEdge-ClientID, får inte innehålla cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfria rubriken.<br /><br /> Klientenheten IPv4 eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder Platsinformationen för att bestämma beteendet för säker sökning.<br /><br /> **Obs:** även om det är valfritt, du uppmuntras att alltid ange den här rubriken och X-Search-Location-huvudet.<br /><br /> Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Dölja adress resultaten på den plats som inte var som helst nära enhetens verkliga plats, vilket kan resultera i Bing som betjänar felaktiga resultat.|  
|<a name="location" />X-Sök-plats|Valfria rubriken.<br /><br /> En semikolonavgränsad lista med nyckel/värde-par som beskriver klientens geografisk plats. Bing använder platsinformationen att fastställa säker sökning beteende och returnera relevanta lokalt innehåll. Ange nyckel/värde-par som \<nyckel\>:\<värdet\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;latitud för klientens plats i grader. Latitud måste vara större än eller lika med-90.0 och mindre än eller lika med +90.0. Negativa värden anger södra Latitude och positiva värden anger norra Latitude.<br /><br /></li><li>lång&mdash;longituden för klientens plats i grader. Longitud måste vara större än eller lika med-180.0 och mindre än eller lika med +180.0. Negativa värden anger western longitudes och positiva värden anger östra longitudes.<br /><br /></li><li>RE&mdash; radius, i meter, som anger vågrät riktighet koordinaterna. Skicka värdet som returneras av enhetens platstjänsten. Typiska värdena kan vara 22m för GPS/Wi-Fi, 380m för cellen tower triangulering och 18 000 m för omvänd IP-sökning.<br /><br /></li><li>TS&mdash; The UTC UNIX-tidsstämpel för när klienten har på plats. (UNIX-tidsstämpel är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>HEAD&mdash;valfritt. Klientens relativa rubriken eller riktning resor. Ange riktning resor som grader från 0 till 360, räkna medurs i förhållande till SANT norra. Ange den här nyckeln endast om den `sp` nyckel är noll.<br /><br /></li><li>SP&mdash; vågrät hastighet (hastighet) i meter per sekund som färdas klientenheten.<br /><br /></li><li>ALT&mdash; klientenheten i meter höjd.<br /><br /></li><li>är&mdash;valfritt. Radien, i meter, som anger lodrät riktighet koordinaterna. RADIUS-standardvärdet är 50 kilometer. Ange den här nyckeln endast om du anger den `alt` nyckel.<br /><br /></li></ul> **Obs:** även om de här nycklarna är valfria, den information som du anger, desto mer exakta resultat för platsen är.<br /><br /> **Obs:** rekommenderar att alltid ange användarens geografiska plats. Att tillhandahålla platsen är särskilt viktigt om klientens IP-adress korrekt inte avspeglar användarens fysisk plats (till exempel om klienten använder VPN). För bästa resultat bör du inkludera den här rubriken och rubriken X-MSEdge-ClientIP, men minst bör du inkludera den här rubriken.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren Kräv efterlevnad med alla tillämpliga lagar, inklusive om användande av dessa rubriker. Till exempel vissa jurisdiktioner, till exempel Europa, det finns i kraven för att hämta användarens medgivande innan du placerar vissa spårnings-enheter på användarnas enheter.
  

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande Frågeparametrar. Se kolumnen krävs för obligatoriska parametrar. Du måste URL: en koda Frågeparametrar.  
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marknaden var resultatet kommer från. <br /><br />En lista över möjliga marknaden värden finns i [marknaden koder](#market-codes).<br /><br /> **Obs:** URL: en förhandsversion av API: et stöder för närvarande endast en-us-marknaden och språk.<br /><br />|Sträng|Ja|  
|<a name="query" />frågor och|URL: en för att förhandsgranska|Sträng|Ja|  
|<a name="responseformat" />responseFormat|Medietyp för att använda för svaret. Här följer möjliga skiftlägeskänsliga värden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. Information om JSON-objekt att svaret innehåller, finns i [Svarsobjekten](#response-objects).<br /><br />  Om du anger JsonLd innehåller svarstexten JSON-LD-objekt som innehåller sökresultaten. Information om JSON-LD finns [JSON-LD](http://json-ld.org/).|Sträng|Nej|  
|<a name="safesearch" />säker sökning|Ett filter som används för att filtrera innehåll för vuxna. Här följer möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Inaktivera&mdash;returnera webbsidor med vuxet text, bilder eller videor.<br /><br/></li><li>Måttlig&mdash;returnera webbsidor med vuxet text, men inte vuxen bilder eller videofiler.<br /><br/></li><li>Strikt&mdash;inte returnerar webbsidor med vuxet text, bilder eller videor.</li></ul><br /> Standardvärdet är Måttlig.<br /><br /> **Obs:** om förfrågan kommer från en marknad som Bing vuxet principen kräver att `safeSearch` har angetts till strikt Bing ignorerar den `safeSearch` värde och använder Strict.<br/><br/>**Obs:** om du använder den `site:` fråga-operator, finns risken att svaret kan innehålla vuxet innehåll, oavsett vad de `safeSearch` Frågeparametern är inställd. Använd `site:` endast om du är känner av innehållet på webbplatsen och ditt scenario har stöd för möjligheten att vuxet innehåll. |Sträng|Nej|  
|<a name="setlang" />setLang|Språket som ska användas för användaren gränssnittet strängar. Ange vilket språk med hjälp av ISO 639-1 2 bokstäver språkkoden. Språkkod för engelska är till exempel EN. Standardvärdet är EN (på engelska).<br /><br /> Även om det är valfritt, bör du alltid ange språket. Normalt anger du `setLang` på samma språk som anges av `mkt` om inte användaren vill användaren gränssnittet strängar som visas i ett annat språk.<br /><br /> Den här parametern och [acceptera språk](#acceptlanguage) rubrik är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> En användare gränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användaren gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svarsobjekten gäller även, det angivna språket.|Sträng|Nej| 


## <a name="response-objects"></a>Svarsobjekt  
Svarsschemat är antingen en [webbsida] eller ErrorResponse, som i API för webbsökning. Om begäran misslyckas kan det översta objektet är den [ErrorResponse](#errorresponse) objekt.


|Objekt|Beskrivning|  
|------------|-----------------|  
|[Webbsidan]|Översta nivån JSON-objekt som innehåller attribut för förhandsversionen.|  
|[Faktumet]|Översta nivån JSON-objekt som innehåller fakta.| 
|[Entiteter|Översta nivån JSON-objekt som innehåller entitetsinformation.| 

  
### <a name="error"></a>Fel  
Definierar de fel som inträffat.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Kod|Felkoden som identifierar kategorin för fel. Läs en lista över möjliga koder [felkoder](#error-codes).|Sträng|  
|<a name="error-message" />meddelande|En beskrivning av felet.|Sträng|  
|<a name="error-moredetails" />moreDetails|En beskrivning som ger ytterligare information om felet.|Sträng|  
|<a name="error-parameter" />Parametern|Frågeparametern i begäran som orsakade felet.|Sträng|  
|<a name="error-subcode" />Obligatorisk|Felkoden som identifierar felet. Till exempel om `code` är InvalidRequest, `subCode` kanske ParameterInvalid eller ParameterInvalidValue. |Sträng|  
|<a name="error-value" />värde|Värde för Frågeparametern som inte var giltig.|Sträng|  
  

### <a name="errorresponse"></a>ErrorResponse  
Det översta objekt som svaret innehåller när begäran misslyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Typ-tipset.|Sträng|  
|<a name="errors" />fel|En lista över fel som beskriver orsaker varför begäran misslyckades.|[Fel](#error)]|  

  
  
### <a name="license"></a>Licens  
Definierar den licens som text eller foto kan användas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Namnet på licensen.|Sträng|  
|url|URL till en webbplats där användaren kan få mer information om licensen.<br /><br /> Använd namn och Webbadress för att skapa en hyperlänk.|Sträng|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definierar en avtalsenliga regel för licens attribution.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Ett tips för typen som har angetts till LicenseAttribution.|Sträng|  
|licens|Licensen som innehållet kan användas.|[Licens](#license)|  
|licenseNotice|Licens för att visa bredvid fältet riktade. Till exempel ”Text under kopia av SA licens”.<br /><br /> Använd licensen namn och URL: en i den `license` fält som du vill skapa en hyperlänk till den webbplats som ger information om licensen. Ersätt sedan licens namn i den `licenseNotice` sträng (till exempel CC-av-SA) med länken som du nyss skapade.|Sträng|  
|mustBeCloseToContent|Ett booleskt värde som anger om innehållet i regeln måste placeras i närheten fältet som regeln gäller för. Om **SANT**, innehållet måste placeras i närheten. Om **FALSKT**, eller det här fältet finns inte, innehållet placeras anroparens gottfinnande.|Boolesk|  
|targetPropertyName|Namnet på fältet som regeln gäller för.|Sträng|  
  

### <a name="link"></a>Länk  
Definierar komponenterna i en hyperlänk.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Typ-tipset.|Sträng|  
|text|Texten som visas.|Sträng|  
|url|EN URL. Använd URL: en och visa text för att skapa en hyperlänk.|Sträng|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definierar en avtalsenliga regel för länken attribution.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Ett tips för typen som har angetts till LinkAttribution.|Sträng|  
|mustBeCloseToContent|Ett booleskt värde som anger om innehållet i regeln måste placeras i närheten fältet som regeln gäller för. Om **SANT**, innehållet måste placeras i närheten. Om **FALSKT**, eller det här fältet finns inte, innehållet placeras anroparens gottfinnande.|Boolesk|  
|targetPropertyName|Namnet på fältet som regeln gäller för.<br /><br /> Om målet inte anges, attribution gäller för entiteten som helhet och ska visas omedelbart efter entiteten presentationen. Om det finns flera text och länk attribution-regler som inte anger ett mål, bör du sammanfoga dem och visa dem med hjälp av en ”Data från”: etikett. Till exempel ”Data från < providern name1\> &#124; < providern name2\>”.|Sträng|  
|text|Attribution-text.|Sträng|  
|url|URL till leverantörens webbplats. Använd `text` och URL: en för att skapa av hyperlänk.|Sträng|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definierar en avtalsenliga regel för media attribution.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Ett tips för typen som har angetts till MediaAttribution.|Sträng|  
|mustBeCloseToContent|Ett booleskt värde som anger om innehållet i regeln måste placeras i närheten fältet som regeln gäller för. Om **SANT**, innehållet måste placeras i närheten. Om **FALSKT**, eller det här fältet finns inte, innehållet placeras anroparens gottfinnande.|Boolesk|  
|targetPropertyName|Namnet på fältet som regeln gäller för.|Sträng|  
|url|Den URL som används för att skapa hyperlänk för medieinnehållet. Till exempel om målet är en bild, skulle du använda URL: en för att skapa avbildningen klickbara.|Sträng|  
  
  
  
### <a name="organization"></a>Organisation  
Definierar en utgivare.  
  
Observera att en utgivare kan deras namn eller sin webbplats, eller bådadera.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Utgivarens namn.|Sträng|  
|url|URL till utgivarens webbplats.<br /><br /> Observera att utgivaren inte kan innehålla en webbplats.|Sträng|  
  
  

### <a name="webpage"></a>Webbsida  
Definierar informationen om en webbsida i förhandsversion.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|
|namn|En rubrik, inte nödvändigtvis HTML-rubrik|Sträng|
|url|Den URL som faktiskt har crawlats (begäran kan ha följt omdirigeringar)|Sträng|  
|beskrivning|Kort beskrivning på sidan och innehåll|Sträng|  
|isFamilyFriendly|Mest korrekta för objekt i web-index. i realtid hämtar gör denna identifiering som endast baseras på URL: en och inte sidinnehåll|boolesk|
|primaryImageOfPage/contentUrl|URL: en till en representativ avbildning som ska ingå i förhandsversionen|Sträng| 
  
  
### <a name="querycontext"></a>QueryContext  
Definierar frågekontexten som Bing används för begäran.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Ett booleskt värde som anger om den angivna frågan innehåller vuxna. Värdet är **SANT** om frågan har vuxna; annars **FALSKT**.|Boolesk|  
|alterationOverrideQuery|Frågesträngen du använder för att tvinga Bing för att använda den ursprungliga strängen. Om frågesträngen är till exempel *saling downwind*, frågesträng åsidosättning blir *+ saling downwind*. Kom ihåg att koda frågesträngen vilket resulterar i *% 2Bsaling + downwind*.<br /><br /> Det här fältet ingår endast om den ursprungliga frågesträngen innehåller en felstavning.|Sträng|  
|alteredQuery|Frågesträngen som används av Bing för att utföra frågan. Bing använder ändrats frågesträngen om den ursprungliga frågesträngen innehåller stavfel. Om frågesträngen är till exempel `saling downwind`, ändrats frågesträngen blir `sailing downwind`.<br /><br /> Det här fältet ingår endast om den ursprungliga frågesträngen innehåller en felstavning.|Sträng|  
|askUserForLocation|Ett booleskt värde som anger om Bing kräver användarens plats att tillhandahålla korrekta resultat. Om du har angett användarens plats med hjälp av den [X-MSEdge-ClientIP](#clientip) och [X sökplats](#location) rubriker, du kan ignorera det här fältet.<br /><br /> För plats medveten frågor, till exempel ”dagens väder” eller ”restauranger i närheten” som behöver användarens plats att tillhandahålla korrekta resultat, det här fältet är inställt på **SANT**.<br /><br /> För plats medveten frågor som innehåller plats (till exempel ”Seattle väder”), det här fältet är inställt på **FALSKT**. Det här fältet anges också **FALSKT** för frågor som inte är platser, till exempel ”bästa säljare”.|Boolesk|  
|originalQuery|Frågesträngen som anges i begäran.|Sträng|  

### <a name="identifiable"></a>Identifierbar
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|id|Resurs-ID|Sträng|
 
### <a name="rankinggroup"></a>RankingGroup
Definierar en sökresultat grupp, till exempel mainline.
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|objekt|En lista över sökresultaten till att visa i gruppen.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definierar ett objekt med Sök resultat att visa.
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|resultIndex|Ett Nollbaserat index för objekt i svaret ska visas. Om objektet inte innehåller det här fältet kan du visa alla objekt i svaret. Till exempel visa alla artiklar i Nyheter svaret.|Integer|
|answerType|Svaret som innehåller objekt att visa. Till exempel nyheter.<br /><br />Använd typen för att hitta svaret i SearchResponse-objektet. Typen är namnet på ett SearchResponse fält.<br /><br /> Dock använda svarstypen endast om det här objektet innehåller värdefältet. Annars kan du ignorera det.|Sträng|
|textualIndex|Index för svaret i textualAnswers ska visas.| Heltalet|
|värde|Det ID som identifierar ett svar ska visas eller ett objekt i ett svar ska visas. Om detta ID identifierar ett svar, visas alla objekt för svaret.|Identifierbar|

### <a name="rankingresponse"></a>RankingResponse  
Definierar där resultaten innehåll ska placeras på sökningen och i vilken ordning.  
  
|Namn|Värde|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Sökresultaten ska visas i den likriktade.|  
|<a name="ranking-pole" />pol|Sökresultat som ska ges mest synliga behandling (till exempel visas ovanför den likriktade och sidopanelen).|  
|<a name="ranking-sidebar" />sidopanelen|Sökresultaten ska visas i sidopanelen.| 


### <a name="searchresponse"></a>SearchResponse  
Definierar det översta objektet som svaret innehåller när begäran lyckas.  
  
Observera att om tjänsten misstänker ett DoS-angrepp, lyckas begäran (HTTP-statuskoden är 200 OK); brödtexten i svaret ska dock vara tom.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Typ-tipset som har angetts till SearchResponse.|Sträng|  
|Webbsida|Ett JSON-objekt som definierar förhandsversionen|sträng|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definierar en avtalsenliga regel för oformaterad text attribution.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Ett tips för typen som har angetts till TextAttribution.|Sträng|  
|text|Attribution-text.<br /><br /> Text attribution gäller för entitet som helhet och ska visas omedelbart efter entiteten presentationen. Om det finns flera text eller länkar attribution-regler som inte anger ett mål, bör du sammanfoga dem och visa dem med hjälp av en ”Data från”: etikett.|Sträng| 


## <a name="error-codes"></a>Felkoder

Här följer möjliga HTTP-statuskoder som returnerar en begäran.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|200|Lyckades.|  
|400|En av frågeparametrarna är saknas eller är inte giltig.|  
|401|Prenumerationsnyckeln saknas eller är inte giltig.|  
|403|Användaren har autentiserats (till exempel de använde en giltig prenumerationsnyckel), men de inte har behörighet till den begärda resursen.<br /><br /> Bing kan också returnera denna status om anroparen överskridits deras frågor per månad kvot.|  
|410|Begäran används HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokollet som stöds.|  
|429|Anroparen har överskridit sina frågor per sekund kvot.|  
|500|Oväntat serverfel.|

Om begäran misslyckas svaret innehåller en [ErrorResponse](#errorresponse) objekt som innehåller en lista över [fel](#error) objekt som beskriver vad som orsakade av fel. Om felet är relaterat till en parameter i `parameter` fältet identifierar den parameter som är problemet. Och om felet är relaterat till ett parametervärde den `value` fältet identifierar det värde som inte är giltig.

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

Här följer möjliga fel kod och underordnade fel kodvärden.

|Kod|Obligatorisk|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskod 400.<br/><br/>Bing returnerar HttpNotAllowed om du använder HTTP-protokollet i stället för HTTPS och HTTP-statuskoden är 410.
|RateLimitExceeded|Inga underordnade koder|Bing returnerar RateLimitExceeded varje gång du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Om du överskrider Indexlagring Bing returnerar HTTP-statuskod 429 och om du överskrider QPM Bing returnerar 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller prenumerationsnyckeln är inte giltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization, är HTTP-statuskod 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization, är HTTP-statuskod 403.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för C#](c-sharp-quickstart.md)
- [Snabbstart för Java](java-quickstart.md)
- [Snabbstart för noden](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)

