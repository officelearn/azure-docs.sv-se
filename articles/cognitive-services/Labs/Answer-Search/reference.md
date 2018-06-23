---
title: Projektet svaret Sök referens - kognitiva Microsoft-tjänster | Microsoft Docs
description: Referens för projektet svaret Sök slutpunkt.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 8c95fac0c031ec62a9d98d6c3278bd3b3f345140
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354165"
---
# <a name="project-answer-search-v7-reference"></a>Projektreferens svaret Sök v7

Bing svaret SearchAPI tar en frågeparameter och returnerar ett `searchResponse` med `answerType`: `facts` eller `entities`. 

Program som använder API: N för svaret Sök skicka begäranden till slutpunkten med en URL för förhandsgranskning i en frågeparameter.  Begäran måste innehålla den `q=searchTerm` parameter och *Ocp-Apim-prenumeration-nyckeln* huvud.   

JSON-svar kan parsas för fakta och entiteter som innehåller information om Sök-objekt.

## <a name="endpoint"></a>Slutpunkt
Om du vill begära sökresultat svar att skicka en begäran till följande slutpunkt. Använda sidhuvuden och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt GET: 
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=<searchTerm>&subscription-key=0123456789ABCDEF&mkt=en-us

````

Begäran måste använda HTTPS-protokollet och inkluderar följande frågeparameter:
-  q =<URL> -frågan som identifierar objekt med Sök

Exempel som visar hur du begär finns [C# quickstart](c-sharp-quickstart.md) eller [Java quickstart](java-quickstart.md). 

Följande avsnitt innehåller teknisk information om svaret objekten, frågeparametrar och huvuden som påverkar sökresultatet. 
  
Information om huvuden som ska tas med begäranden finns [huvuden](#headers).  
  
Information om frågeparametrar förfrågningar måste innehålla finns [fråga parametrar](#query-parameters).  
  
För information om JSON objekt att svaret innehåller, se [svar objekt](#response-objects).

Maximal URL-längd är 2 048 tecken. För att säkerställa att URL-längd inte överskrider gränsen, ska den maximala längden på din frågeparametrar vara mindre än 1 500 tecken. Om URL: en överskrider 2 048 tecken, returnerar servern 404 hittades inte.  

Information om tillåtna användning och visa resultat finns [använder och visa krav](use-display-requirements.md). 

> [!NOTE]
> Vissa begärandehuvuden som är viktiga för andra sökningar API: er påverkar inte URL-förhandsgranskning
> - Pragma – anroparen inte har kontroll över om URL: en förhandsgranskning använder cache
> - Cache-Control – anroparen har inte kontroll över om URL: en förhandsgranskning använder cache
> - Användaragent

> Dessutom vissa parametrar är inte för närvarande meningsfulla för URL: en förhandsgranskning API men får användas i framtiden för förbättrad globalisering. 
 
## <a name="headers"></a>Sidhuvuden  
Följande är sidhuvuden som en förfrågan och svar kan innehålla.  
  
|Sidhuvud|Beskrivning|  
|------------|-----------------|  
|Godkänn|Valfria begärandehuvudet.<br /><br /> Standard-medietyp är application/json. Ange att svaret använda [JSON-LD](http://json-ld.org/), ange Accept-huvud till programmet/ld + json.|  
|<a name="acceptlanguage" />Acceptera språk|Valfria begärandehuvudet.<br /><br /> En kommaavgränsad lista över språk som ska användas för gränssnittet användarsträngar. Listan är i fallande preferensordning. Mer information, inklusive förväntat format finns i [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Det här huvudet och [setLang](#setlang) frågeparameter är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> Om du anger det här sidhuvudet, måste du också ange den [kopia](#cc) Frågeparametern. För att fastställa marknaden att returnera resultat för Bing använder det första språket den söker efter i listan och kombineras med de `cc` parametervärdet. Om listan inte innehåller ett språk som stöds, Bing hittar närmaste språk och marknaden som har stöd för begäran eller den använder en aggregerade eller standard marknaden för resultat. För att fastställa marknaden som används för Bing finns i rubriken BingAPIs marknaden.<br /><br /> Använd den här rubriken och `cc` Frågeparametern endast om du anger flera språk. Annars kan du använda den [mkt](#mkt) och [setLang](#setlang) fråga parametrar.<br /><br /> En användare gränssnittet strängen är en sträng som används som en etikett i ett användargränssnitt. Det finns några användare gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svaret objekt gäller det angivna språket.|  
|<a name="market" />BingAPIs marknaden|Svarsrubrik.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Till exempel en-US.|  
|<a name="traceid" />BingAPIs TraceId|Svarsrubrik.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår avbilda detta ID. Om det inte går att identifiera och lösa problemet, inkludera detta ID tillsammans med den information som du anger supportgruppen.|  
|<a name="subscriptionkey" />OCP-Apim-prenumeration-nyckel|Nödvändiga begärandehuvudet.<br /><br /> Nyckeln prenumeration som du fick när du registrerat dig för den här tjänsten i [kognitiva Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Valfritt begärandehuvudet<br /><br /> Som standard returnerar Bing cachelagrat innehåll om det är tillgängligt. Ange rubriken Pragma till no cache för att förhindra att Bing returnerar cachelagrat innehåll (till exempel Pragma: no-cache).
|<a name="useragent" />Användaragent|Valfria begärandehuvudet.<br /><br /> Användaragent som skapade begäran. Bing använder användaragenten för mobila användare med en optimerad upplevelse. Även om det är valfritt, uppmanas du att ange alltid det här sidhuvudet.<br /><br /> Användaragent ska vara samma sträng som används ofta webbläsare skickar. Information om användaragenter finns [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Följande är exempel på användaragent strängar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/PEPPARKAKOR) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4; 1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU-OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (t.ex. Gecko KHTML) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfria huvud för begäran och svar.<br /><br /> Bing använder den här rubriken för att ge användarna en konsekvent beteende i Bing-API-anrop. Bing flygningar ofta nya funktioner och förbättringar och klient-ID används som en nyckel för att tilldela trafik på olika flygplan. Om du inte använder samma klient-ID för en användare över flera förfrågningar, kan sedan Bing tilldela användaren till flera motstridiga flygplan. Tilldelas till flera motstridiga flygplan kan leda till ett inkonsekvent användarupplevelsen. Om andra begäran har en annan svarta tilldelning än först, till exempel vara upplevelsen oväntat. Bing kan också använda klient-ID för att skräddarsy Webbresultat klienten ID Sök tidigare, vilket ger en bättre upplevelse för användaren.<br /><br /> Bing använder också den här rubriken för att förbättra resultatet rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Förbättringar av betydelse hjälpa med bättre resultat som levereras med Bing-API: er och i sin tur aktiverar högre klicka igenom priser för API-klienten.<br /><br /> **Viktigt:** även om det är valfritt, bör du det här sidhuvudet krävs. Beständighet klient-ID över flera förfrågningar för samma användare och enhet kombination kan 1) API klienten att ta emot en konsekvent användarupplevelse och 2) högre klicka igenom priser via bättre kvalitet av resultat från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för det här sidhuvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing skapas klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran Bing genererar ett ID och returnerar i X-MSEdge-ClientID-Svarsrubrik. Den enda gången som du inte bör inkludera det här huvudet i en begäran är första gången användaren använder din app på enheten.<br /><br/></li><li>Använda klient-ID för varje begäran i Bing-API som gör att din app för denna användare på enheten.<br /><br/></li><li>**Obs:** måste du se till att detta klient-ID inte är linkable till alla authenticatable kontoinformation.</li><br/><li>Spara klient-ID. För att bevara ID i en webbläsaren att använda en beständig HTTP-cookie för detta ID används i alla sessioner. Använd inte en sessions-cookie. För andra appar, till exempel mobilappar, använder du enhetens beständig lagring för att bevara ID.<br /><br/>Nästa gång användaren använder din app på enheten, hämta klient-ID som du sparade.</li></ul><br /> **Obs:** Bing svar kanske eller kanske inte med det här sidhuvudet. Om svaret innehåller det här sidhuvudet, avbilda klient-ID och använda det för alla efterföljande Bing-förfrågningar för användare på enheten.<br /><br /> **Obs:** om du inkluderar X-MSEdge-ClientID, får inte innehålla cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfria begärandehuvudet.<br /><br /> Klientenheten IPv4 eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder Platsinformationen för att fastställa säker sökbeteendet.<br /><br /> **Obs:** även om det är valfritt, du uppmanas att ange alltid detta huvud och huvudet X sökplats.<br /><br /> Obfuscate inte adressen (till exempel genom att ändra den sista oktetten 0). Obfuscating adress resultaten på den plats som inte var som helst nära enhetens verkliga plats, vilket kan medföra Bing betjänar felaktiga resultat.|  
|<a name="location" />X sökplats|Valfria begärandehuvudet.<br /><br /> En semikolonavgränsad lista över nyckel/värde-par som beskriver klientens geografisk plats. Bing använder platsinformation för att fastställa säker sökbeteendet och returnera relevanta lokalt innehåll. Ange nyckel/värde-par som \<nyckeln\>:\<värdet\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;latitud för klientens plats i grader. Latitud måste vara större än eller lika med-90.0 och mindre än eller lika med +90.0. Negativa värden anger södra latituderna och positiva värden anger norra latituderna.<br /><br /></li><li>lång&mdash;longituden för klientens plats i grader. Longituden måste vara större än eller lika med-180.0 och mindre än eller lika med +180.0. Negativa värden anger western longitudes och positiva värden anger eastern longitudes.<br /><br /></li><li>RE&mdash; radius, i meter, som anger koordinaterna vågräta noggrannhet. Skicka värdet som returneras av enhetens platstjänsten. Typiska värdena kan vara 22m för GPS-WiFi, 380m för cellen torn triangulering och 18 000 m för omvänd sökning av IP.<br /><br /></li><li>TS&mdash; i UTC UNIX tidsstämpeln för när klienten har på plats. (UNIX-tidsstämpel är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>HEAD&mdash;valfritt. Klientens relativa rubrik eller resa riktning. Ange riktningen för resa som grader från 0 till 360, inventering medurs i förhållande till true Nord. Ange den här nyckeln om den `sp` nyckel är noll.<br /><br /></li><li>SP&mdash; vågräta hastighet (hastighet) i meter per sekund som färdas klientenheten.<br /><br /></li><li>ALT&mdash; höjd klientenheten i meter.<br /><br /></li><li>är&mdash;valfritt. Radien, i meter, som anger den lodräta riktigheten koordinaterna. RADIUS-standardvärdet 50 kilometer. Ange den här nyckeln om du anger den `alt` nyckel.<br /><br /></li></ul> **Obs:** men nycklarna är valfritt, det mer information som du anger desto mer exakt plats är.<br /><br /> **Obs:** du uppmanas att alltid ange användarens geografisk plats. Att tillhandahålla platsen är särskilt viktigt om klientens IP-adress inte korrekt speglar användarens fysisk plats (till exempel om klienten använder VPN). För bästa resultat bör du bör ta med detta huvud och huvudet X-MSEdge-ClientIP, men minst bör du ta med det här sidhuvudet.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren kräver följer alla tillämpliga lagar, inklusive om användning av dessa huvuden. Till exempel vissa jurisdiktioner, till exempel Europa, det finns i kraven för att hämta användarens medgivande innan släpps vissa spårning på användarens enheter.
  

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande Frågeparametrar. Finns den nödvändiga kolumnen för obligatoriska parametrar. Du måste URL koda Frågeparametrar.  
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marknaden var resultatet ska hämtas. <br /><br />En lista över möjliga marknaden värden, se [marknaden koder](#market-codes).<br /><br /> **Obs:** API för Certifikatwebbadress Preview stöder för närvarande endast en-us marknaden och språk.<br /><br />|Sträng|Ja|  
|<a name="query" />Q|URL: en för förhandsgranskning|Sträng|Ja|  
|<a name="responseformat" />responseFormat|Medietypen för att använda för svaret. Följande är möjliga skiftlägeskänsliga värden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. För information om JSON objekt att svaret innehåller, se [svar objekt](#response-objects).<br /><br />  Om du anger JsonLd innehåller svarstexten LD JSON-objekt som innehåller sökresultaten. Information om JSON-LD finns [JSON-LD](http://json-ld.org/).|Sträng|Nej|  
|<a name="safesearch" />säker sökning|Ett filter som används för att filtrera vuxet innehåll. Följande är möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Inaktivera&mdash;returnera webbsidor med vuxna text, bilder och videor.<br /><br/></li><li>Måttlig&mdash;returnera webbsidor med vuxna text, men inte vuxna bilder eller videofiler.<br /><br/></li><li>Strikt&mdash;inte returnerar webbsidor med vuxna text, bilder och videor.</li></ul><br /> Standardvärdet är Måttlig.<br /><br /> **Obs:** om begäran kommer från en marknad som Bing vuxna principen kräver att `safeSearch` har angetts till strikt Bing ignorerar det `safeSearch` värdet och använder Strict.<br/><br/>**Obs:** om du använder den `site:` frågeoperatorn, finns risken att svaret kan innehålla innehåll för vuxna oavsett vad de `safeSearch` frågeparameter har angetts till. Använd `site:` om du är medveten om innehållet på webbplatsen och ditt scenario stöder möjligheten att vuxet innehåll. |Sträng|Nej|  
|<a name="setlang" />setLang|Språket som ska användas för gränssnittet användarsträngar. Ange språk med hjälp av koden 2 bokstäver ISO 639-1. Språkkod för engelska är till exempel EN. Standardvärdet är EN (på engelska).<br /><br /> Även om det är valfritt, bör du alltid ange språk. Normalt anger du `setLang` till samma språk som anges av `mkt` om användaren vill användarsträngar för gränssnitt som visas i ett annat språk.<br /><br /> Den här parametern och [acceptera språk](#acceptlanguage) huvud är ömsesidigt uteslutande&mdash;inte ange båda.<br /><br /> En användare gränssnittet strängen är en sträng som används som en etikett i ett användargränssnitt. Det finns några användare gränssnittet strängar i JSON-svar-objekt. Alla länkar till Bing.com egenskaper i svaret objekt gäller också det angivna språket.|Sträng|Nej| 


## <a name="response-objects"></a>Svaret objekt  
Schemat för svar är antingen en [webbsida] eller ErrorResponse som webb-API för sökning. Om begäran inte det översta objektet är den [ErrorResponse](#errorresponse) objekt.


|Objekt|Beskrivning|  
|------------|-----------------|  
|[Webbsidan]|Övre nivå JSON-objekt som innehåller attribut för förhandsgranskning.|  
|[Faktumet]|Övre nivå JSON-objekt som innehåller fakta.| 
|[Entiteter|Övre nivå JSON-objekt som innehåller information om entiteten.| 

  
### <a name="error"></a>Fel  
Definierar de fel som inträffade.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Koden|Felkoden som identifierar kategori av fel. En lista över möjliga koder finns [felkoder](#error-codes).|Sträng|  
|<a name="error-message" />meddelande|En beskrivning av felet.|Sträng|  
|<a name="error-moredetails" />moreDetails|En beskrivning som ger ytterligare information om felet.|Sträng|  
|<a name="error-parameter" />Parametern|Frågeparametern i begäran som orsakade felet.|Sträng|  
|<a name="error-subcode" />Delkoden|Den felkod som identifierar felet. Till exempel om `code` är InvalidRequest, `subCode` kanske ParameterInvalid eller ParameterInvalidValue. |Sträng|  
|<a name="error-value" />värdet|Parametern fråga-värde som inte är giltig.|Sträng|  
  

### <a name="errorresponse"></a>ErrorResponse  
Det huvudobjekt som svaret innehåller när begäran misslyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Tips för typen.|Sträng|  
|<a name="errors" />Fel|En lista med fel som beskriver de orsakerna till begäran inte.|[Fel](#error)]|  

  
  
### <a name="license"></a>Licens  
Definierar den licens som text eller foto kan användas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Namnet på licensen.|Sträng|  
|url|URL till en webbplats där användaren kan få mer information om licensen.<br /><br /> Använd namn och Webbadress för att skapa en hyperlänk.|Sträng|  
  

### <a name="licenseattribution"></a>LicenseAttribution  
Definierar en avtal regel för licens information.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Ett tips för typen som har angetts till LicenseAttribution.|Sträng|  
|licens|Licensen som innehållet kan användas.|[Licens](#license)|  
|licenseNotice|Licensen för att visa bredvid fältet riktade. Till exempel ”Text under kopia av SA licens”.<br /><br /> Använd licensen namn och URL: en i den `license` fältet för att skapa en hyperlänk till den webbplats som innehåller information om licensen. Ersätt sedan licens namn i den `licenseNotice` sträng (till exempel CC-av-SA) med hyperlänken som du nyss skapade.|Sträng|  
|mustBeCloseToContent|Ett booleskt värde som anger om innehållet i regeln måste placeras i närhet av det fält som regeln gäller för. Om **SANT**, innehållet måste placeras i närheten. Om **FALSKT**, eller finns inte i det här fältet, innehållet placeras anroparens gottfinnande.|Boolesk|  
|targetPropertyName|Namnet på fältet som regeln gäller för.|Sträng|  
  

### <a name="link"></a>Länk  
Definierar komponenterna i en hyperlänk.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Tips för typen.|Sträng|  
|text|Texten som visas.|Sträng|  
|url|EN URL. Använd URL: en och visa text för att skapa en hyperlänk.|Sträng|  
  

### <a name="linkattribution"></a>LinkAttribution  
Definierar en avtal regel för länken information.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Ett tips för typen som har angetts till LinkAttribution.|Sträng|  
|mustBeCloseToContent|Ett booleskt värde som anger om innehållet i regeln måste placeras i närhet av det fält som regeln gäller för. Om **SANT**, innehållet måste placeras i närheten. Om **FALSKT**, eller finns inte i det här fältet, innehållet placeras anroparens gottfinnande.|Boolesk|  
|targetPropertyName|Namnet på fältet som regeln gäller för.<br /><br /> Om ett mål har angetts gäller för entiteten som helhet tilldelning och ska visas omedelbart efter entitet presentationen. Om det finns flera text och länka tillskrivningar-regler som inte anger ett mål, bör du sammanfoga dem och visa dem med hjälp av en ”Data från”: etikett. Till exempel ”Data från < providern name1\> &#124; < providern name2\>”.|Sträng|  
|text|Texten som information.|Sträng|  
|url|URL till leverantörens webbplats. Använd `text` och skapa en hyperlänk-URL.|Sträng|  
  
  
### <a name="mediaattribution"></a>MediaAttribution  
Definierar en avtal regel för media information.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Ett tips för typen som har angetts till MediaAttribution.|Sträng|  
|mustBeCloseToContent|Ett booleskt värde som anger om innehållet i regeln måste placeras i närhet av det fält som regeln gäller för. Om **SANT**, innehållet måste placeras i närheten. Om **FALSKT**, eller finns inte i det här fältet, innehållet placeras anroparens gottfinnande.|Boolesk|  
|targetPropertyName|Namnet på fältet som regeln gäller för.|Sträng|  
|url|Den URL som används för att skapa hyperlänken för medieinnehållet. Om målet är en bild, skulle du till exempel använda URL: en för att göra bilden klickbara.|Sträng|  
  
  
  
### <a name="organization"></a>Organisation  
Definierar en utgivare.  
  
Observera att en utgivare får sitt namn eller sin webbplats eller båda.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Utgivarens namn.|Sträng|  
|url|URL till utgivarens webbplats.<br /><br /> Observera att utgivaren inte tillhandahåller en webbplats.|Sträng|  
  
  

### <a name="webpage"></a>Webbsida  
Definierar information om en webbsida i förhandsgranskningen.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|
|namn|En rubrik, inte nödvändigtvis HTML-rubrik|Sträng|
|url|Den URL som faktiskt har crawlats (begäran kan ha följt omdirigeringar)|Sträng|  
|description|Kort beskrivning av den sidan och innehåll|Sträng|  
|isFamilyFriendly|Mest korrekta för objekt i web-index. realtid hämtar gör denna identifiering utifrån enbart URL och inte sidinnehåll|boolesk|
|primaryImageOfPage/contentUrl|URL till en representativ bild ska ingå i förhandsversionen|Sträng| 
  
  
### <a name="querycontext"></a>QueryContext  
Definierar den frågekontexten Bing används för begäran.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|adultIntent|Ett booleskt värde som anger om den angivna frågan har vuxna avsikt. Värdet är **SANT** om frågan har vuxna avsikt; annars **FALSKT**.|Boolesk|  
|alterationOverrideQuery|Frågesträngen du använder för att tvinga Bing för att använda den ursprungliga strängen. Om exempelvis frågesträng *saling downwind*, frågesträngen Åsidosätt blir *+ saling downwind*. Kom ihåg att koda frågesträngen vilket resulterar i *% 2Bsaling + downwind*.<br /><br /> Det här fältet ingår endast om den ursprungliga frågesträngen innehåller stavning fel.|Sträng|  
|alteredQuery|Frågesträngen som används av Bing för att utföra frågan. Bing använder ändras frågesträngen om den ursprungliga frågesträngen innehåller stavfel. Om exempelvis frågesträng `saling downwind`, kommer att ändras frågesträngen `sailing downwind`.<br /><br /> Det här fältet ingår endast om den ursprungliga frågesträngen innehåller stavning fel.|Sträng|  
|askUserForLocation|Ett booleskt värde som anger om Bing kräver användarens plats att tillhandahålla korrekta resultat. Om du har angett användarens plats med hjälp av den [X-MSEdge-ClientIP](#clientip) och [X sökplats](#location) sidhuvud, du kan ignorera det här fältet.<br /><br /> Var medveten om frågor, till exempel ”dagens väder” eller ”hotell närheten” som behöver användarens plats att tillhandahålla korrekta resultat i det här fältet har värdet **SANT**.<br /><br /> Var medveten om frågor som innehåller plats (till exempel ”Seattle väder”), i det här fältet har värdet **FALSKT**. Det här fältet också anges **FALSKT** för frågor som inte var medveten om, till exempel ”bästa säljare”.|Boolesk|  
|originalQuery|Frågesträngen som anges i begäran.|Sträng|  

### <a name="identifiable"></a>Identifierbar
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|id|Resurs-ID|Sträng|
 
### <a name="rankinggroup"></a>RankingGroup
Definierar en grupp i sökresultat, exempelvis mainline.
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|objekt|En lista över sökresultat ska visas i gruppen.|RankingItem|

### <a name="rankingitem"></a>RankingItem
Definierar ett objekt med Sök resultat att visa.
|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|resultIndex|Ett Nollbaserat index för objekt i svaret ska visas. Om objektet inte innehåller det här fältet kan du visa alla objekt i svaret. Till exempel visa alla nyhetsartiklar nyheter svar.|Integer|
|answerType|Svaret som innehåller objekt som ska visas. Till exempel nyheter.<br /><br />Använda denna typ kan hitta svaret i SearchResponse-objektet. Typen är namnet på ett SearchResponse fält.<br /><br /> Använd svarstypen dock bara om det här objektet innehåller värdefältet. i annat fall hoppar över det.|Sträng|
|textualIndex|Index för svaret i textualAnswers ska visas.| Osignerat heltal|
|värde|Det ID som identifierar ett svar för att visa eller ett objekt i ett svar för att visa. Om ID som identifierar ett svar, visa alla objekt för svaret.|Identifierbar|

### <a name="rankingresponse"></a>RankingResponse  
Definierar där på sökfunktionen resultat sidinnehållet ska placeras och i vilken ordning.  
  
|Namn|Värde|  
|----------|-----------|  
|<a name="ranking-mainline" />mainline|Sökresultat ska visas i den likriktade.|  
|<a name="ranking-pole" />pol|Sökresultat som ska ges mest synliga behandling (till exempel visas ovanför den likriktade och sidopanelen).|  
|<a name="ranking-sidebar" />sidopanelen|Sökresultat ska visas i sidopanelen.| 


### <a name="searchresponse"></a>SearchResponse  
Definierar det huvudobjekt som svaret innehåller när begäran lyckas.  
  
Observera att om tjänsten misstänker ett DoS-angrepp, lyckas begäran (HTTP-statuskoden är 200 OK); dock är brödtexten i svaret tom.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Skriv-tipset som har angetts till SearchResponse.|Sträng|  
|Webbsida|Ett JSON-objekt som definierar förhandsversionen|sträng|  
  
  
### <a name="textattribution"></a>TextAttribution  
Definierar en avtal regel för oformaterad text information.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_skriv|Ett tips för typen som har angetts till TextAttribution.|Sträng|  
|text|Texten som information.<br /><br /> Text information ska visas omedelbart efter entitet presentationen gäller entiteten som helhet. Om det finns flera text eller länk tillskrivningar-regler som inte anger ett mål, bör du sammanfoga dem och visa dem med hjälp av en ”Data från”: etikett.|Sträng| 


## <a name="error-codes"></a>Felkoder

Följande är möjliga http-statuskoder som returnerar en begäran.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|200|Lyckades.|  
|400|En av frågeparametrarna är saknas eller är inte giltigt.|  
|401|Nyckeln prenumeration saknas eller är inte giltig.|  
|403|Användaren har autentiserats (till exempel de som används en giltig prenumeration nyckel), men de har inte behörighet till den begärda resursen.<br /><br /> Bing kan också returnera denna status om anroparen överskridit sina frågor per månad kvoten.|  
|410|Begäran används HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokollet som stöds.|  
|429|Anroparen har överskridit sina frågor per andra kvoten.|  
|500|Oväntat serverfel.|

Om begäran inte, svaret innehåller ett [ErrorResponse](#errorresponse) objekt som innehåller en lista över [fel](#error) objekt som beskriver vad som orsakade felet. Om felet är relaterat till en parameter i `parameter` fältet identifierar parametern som är problemet. Och om felet är relaterat till ett parametervärde den `value` identifierar det värde som inte är giltigt för fältet.

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

Följande är möjliga fel kod och underordnade fel kodvärden.

|Kod|Delkoden|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskoden 400.<br/><br/>Om du använder HTTP-protokollet i stället för HTTPS Bing returnerar HttpNotAllowed och HTTP-statuskoden är 410.
|RateLimitExceeded|Inga underordnade koder|Bing returnerar RateLimitExceeded när du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvoten.<br/><br/>Om du överskrider QPS Bing returnerar HTTP-statuskod 429 och om du överskrider QPM Bing returnerar 403.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan verifiera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller nyckeln prenumerationen är inte giltigt.<br/><br/>Redundans inträffar om du anger fler än en autentiseringsmetod.<br/><br/>Om felet InvalidAuthorization är 401 HTTP-statuskoden.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om nyckeln för prenumerationen har inaktiverats eller har upphört att gälla. <br/><br/>Om felet InsufficientAuthorization är 403 HTTP-statuskoden.

## <a name="next-steps"></a>Nästa steg
- [C#-Snabbstart](c-sharp-quickstart.md)
- [Java-Snabbstart](java-quickstart.md)
- [Noden Snabbstart](node-quickstart.md)
- [Python-Snabbstart](python-quickstart.md)

