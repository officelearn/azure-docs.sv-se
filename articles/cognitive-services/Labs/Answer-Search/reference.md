---
title: Referens för Project Answer Search
titlesuffix: Azure Cognitive Services
description: Referens för projektet Svarssökning slutpunkten.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: project-answer-search
ms.topic: reference
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 1149f4d5ec0a3ef55c435d0555f944329cf5b890
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869609"
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
 
## <a name="headers"></a>Rubriker  
Följande är de rubriker som en begäran och svaret kan innehålla.  
  
|Huvud|Beskrivning|  
|------------|-----------------|  
|Godkänn|Valfritt begärandehuvud.<br /><br /> Typ av media är application/json. Ange att svaret använda [JSON-LD](http://json-ld.org/), inställd program/ld + json Accept-huvud.|  
|<a name="acceptlanguage" />Accept-Language|Valfritt begärandehuvud.<br /><br /> En kommaavgränsad lista över språk som ska användas för användargränssnittssträngar. Listan är i fallande prioritetsordning. Mer information, bland annat om det förväntade formatet, finns i [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Det är huvudet och [setLang](#setlang)-frågeparametern utesluter varandra&mdash;ange inte båda två.<br /><br /> Om du anger huvudet måste du även ange frågeparametern [cc](#cc). För att fastställa vilken marknad som resultat ska returneras för använder Bing det första språk som stöds på listan och kombinerar det med parametervärdet `cc`. Om listan inte innehåller något språk som stöds hittar Bing det närmaste språket och marknaden som har stöd för begäran, eller så använder Bing en aggregerad eller standardmarknad för resultatet. För att avgöra vilken marknad som används i Bing kan du gå till BingAPIs-Market-huvudet.<br /><br /> Använd enbart det här huvudet och `cc`-frågeparametern om du anger flera språk. Annars kan du använda frågeparametrarna [mkt](#mkt) och [setLang](#setlang).<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.|  
|<a name="market" />BingAPIs-Market|Svarshuvud.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Exempel: sv-SE.|  
|<a name="traceid" />BingAPIs-TraceId|Svarshuvud.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår ska du avbilda detta ID. Om det inte går att fastställa och lösa problemet ska du ange ID:t tillsammans med annan information som du ger supportteamet.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Begärandehuvud som krävs.<br /><br /> Prenumerationsnyckeln som du fick när du registrerade dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Valfritt begärandehuvud<br /><br /> Som standard returnerar Bing cachelagrat innehåll om det finns. Om du vill förhindra att Bing returnerar cachelagrat innehåll ska du ställa in huvudet Pragma på no-cache (till exempel Pragma: no-cache).
|<a name="useragent" />User-Agent|Valfritt begärandehuvud.<br /><br /> Användaragenten som skapade begäran. Bing använder användaragenten för att ge mobila användare en optimerad upplevelse. Även om det är valfritt rekommenderar vi även att du alltid anger det här huvudet.<br /><br /> Användaragenten ska vara samma sträng som alla vanliga webbläsare skickar. Information om användaragenter finns i [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Här följer några exempel på användaragentsträngar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (KHTML, som Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfritt huvud för begäran och svar.<br /><br /> Bing använder det här huvudet för att ge användarna konsekvent beteende i Bing API-anrop. Bing ger ofta förhandsversioner av nya funktioner och förbättringar och använder klient-ID som en nyckel för att tilldela trafik till olika förhandsversioner. Om du inte använder samma klient-ID för en användare vid flera förfrågningar kan sedan Bing tilldela användaren flera motstridiga förhandsversioner. Om du tilldelas flera motstridiga förhandsversioner kan det leda till en inkonsekvent användarupplevelse. Om till exempel den andra begäran har en annan förhandsversionstilldelning än den första kan upplevelsen vara oväntad. Bing kan också använda klient-ID för att skräddarsy webbresultatet för klient-ID:ts sökhistorik, vilket ger användaren en mer omfattande upplevelse.<br /><br /> Bing använder också det här huvudet för att förbättra resultatets rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Relevansförbättringarna kan ge bättre resultat som levereras av Bing-API: er, vilka i sin tur möjliggör högre klickfrekvens för API-konsumenten.<br /><br /> **Viktigt!** Även om det är valfritt bör du överväga att använda det här huvudet som krävs. Bestående klient-ID för flera förfrågningar för samma slutanvändare och enhetskombination gör det möjligt 1) för API-konsumenten att få en konsekvent användarupplevelse och 2) att få högre klickfrekvens via resultat av högre kvalitet från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för det här huvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing-genererat klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran genererar Bing ett ID och returnerar det i svarshuvudet X-MSEdge-ClientID. Den enda gången som du inte får inkludera det här huvudet i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>Använd klient-ID för varje Bing API-begäran som din app gör för den här användaren på enheten.<br /><br/></li><li>**Observera:** måste du se till att detta klient-ID inte är linkable till alla authenticatable kontoinformation.</li><br/><li>Spara klient-ID:t. Om du vill bevara ID:t i en webbläsarapp ska du använda en beständig HTTP-cookie i alla sessioner. Använd inte en sessionscookie. För andra appar, till exempel mobilappar, använder du enhetens beständiga lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten ska du hämta klient-ID:t som du sparade.</li></ul><br /> **Obs!** Bing-svar kanske eller kanske inte omfattar det här huvudet. Om svaret innehåller det här huvudet ska du avbilda klient-ID:t och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs!** Om du inkluderar X-MSEdge-ClientID får du inte ta med cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfritt begärandehuvud.<br /><br /> Klientenhetens IPv4- eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder platsinformationen för att fastställa SafeSearch-beteende.<br /><br /> **Obs!** Även om det är valfritt rekommenderar vi att du alltid anger det här huvudet och huvudet X-Search-Location.<br /><br /> Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Om adressresultatet förvillas på en plats som inte är i närheten av enhetens verkliga plats kan det leda till att Bing presenterar felaktiga resultat.|  
|<a name="location" />X-Search-Location|Valfritt begärandehuvud.<br /><br /> En semikolonavgränsad lista med nyckel/värde-par som beskriver klientens geografiska plats. Bing använder platsinformationen till att fastställa ett säkert sökbeteende och returnera relevant lokalt innehåll. Ange nyckel/värde-par som \<nyckel\>:\<värde\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;latitud för klientens plats i grader. Latituden måste vara större än eller lika med -90.0 och vara mindre än eller lika med +90.0. Negativa värden anger sydliga latituder och positiva värden anger nordliga latituder.<br /><br /></li><li>lång&mdash;longituden för klientens plats i grader. Longituden måste vara större än eller lika med -180.0 och vara mindre än eller lika med +180.0. Negativa värden anger västliga longituder och positiva värden anger östliga longituder.<br /><br /></li><li>RE&mdash; radius, i meter, som anger vågrät riktighet koordinaterna. Skicka värdet som returneras av enhetens platstjänst. Typiska värden kan vara 22 m för GPS/Wi-Fi, 380 m för mobiltornstriangulering och 18 000 m för omvänd IP-sökning.<br /><br /></li><li>TS&mdash; The UTC UNIX-tidsstämpel för när klienten har på plats. (UNIX-tidsstämpeln är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>head&mdash;Optional. Klientens relativa riktning eller färdriktning. Ange färdriktningen som grader från 0 till 360 där du räknar medurs i förhållande till norr. Ange endast den här nyckeln om nyckeln `sp` är nollskiljd.<br /><br /></li><li>SP&mdash; vågrät hastighet (hastighet) i meter per sekund som färdas klientenheten.<br /><br /></li><li>ALT&mdash; klientenheten i meter höjd.<br /><br /></li><li>are&mdash;Valfritt. Radien, i meter, som anger koordinaternas lodräta exakthet. RADIUS-standardvärdet är 50 kilometer. Ange den här nyckeln endast om du anger nyckeln `alt`.<br /><br /></li></ul> **Obs:** även om de här nycklarna är valfria, den information som du anger, desto mer exakta resultat för platsen är.<br /><br /> **Obs:** rekommenderar att alltid ange användarens geografiska plats. Det är särskilt viktigt att ange plats om klientens IP-adress inte exakt avspeglar användarens fysiska plats (till exempel om klienten använder VPN). För bästa resultat bör du inkludera det här huvudet och huvudet X-MSEdge-ClientIP, men du bör minst inkludera det här huvudet.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren kräver efterlevnad med alla tillämpliga lagar, inklusive lagar om användande av dessa huvud. I till exempel vissa jurisdiktioner, som Europa, finns det krav på att skaffa användarens medgivande innan du placerar vissa spårningsenheter på användarenheter.
  

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande Frågeparametrar. Se kolumnen krävs för obligatoriska parametrar. Du måste URL: en koda Frågeparametrar.  
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|  
|<a name="mkt" />mkt|Marknaden som resultatet kommer från. <br /><br />En lista över möjliga marknaden värden finns i [marknaden koder](#market-codes).<br /><br /> **Obs:** URL: en förhandsversion av API: et stöder för närvarande endast en-us-marknaden och språk.<br /><br />|Sträng|Ja|  
|<a name="query" />frågor och|URL: en för att förhandsgranska|Sträng|Ja|  
|<a name="responseformat" />responseFormat|Medietyp för att använda för svaret. Här följer möjliga skiftlägeskänsliga värden.<br /><ul><li>JSON</li><li>JSONLD</li></ul><br /> Standardvärdet är JSON. Information om JSON-objekt att svaret innehåller, finns i [Svarsobjekten](#response-objects).<br /><br />  Om du anger JsonLd innehåller svarstexten JSON-LD-objekt som innehåller sökresultaten. Information om JSON-LD finns [JSON-LD](http://json-ld.org/).|Sträng|Nej|  
|<a name="safesearch" />safeSearch|Ett filter som används för att filtrera innehåll som är olämpligt för barn. Här följer de möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Inaktivera&mdash;returnera webbsidor med vuxet text, bilder eller videor.<br /><br/></li><li>Måttlig&mdash;returnera webbsidor med vuxet text, men inte vuxen bilder eller videofiler.<br /><br/></li><li>Strikt&mdash;inte returnerar webbsidor med vuxet text, bilder eller videor.</li></ul><br /> Standardinställningen är Måttlig.<br /><br /> **Obs:** om förfrågan kommer från en marknad som Bing vuxet principen kräver att `safeSearch` har angetts till strikt Bing ignorerar den `safeSearch` värde och använder Strict.<br/><br/>**Obs!** Beroende på om du använder frågeoperator `site:` finns det en risk att svaret har innehåll som är olämpligt för barn oberoende av vad frågeparametern `safeSearch` är inställd på. Använd endast `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn. |Sträng|Nej|  
|<a name="setlang" />setLang|Språket som ska användas för användargränssnittssträngar. Ange språk med hjälp av den tvåstaviga språkkoden ISO 639-1. Språkkoden för engelska är till exempel EN. Standardvärdet är EN (engelska).<br /><br /> Även om det är valfritt bör du alltid ange språket. Normalt anger du `setLang` på samma språk som anges av `mkt` om inte användaren vill att gränssnittets strängar ska visas på ett annat språk.<br /><br /> Den här parametern och [Accept-Language](#acceptlanguage)-huvudet utesluter varandra&mdash;ange inte båda två.<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.|Sträng|Nej| 


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
|<a name="error-value" />Värde|Värde för Frågeparametern som inte var giltig.|Sträng|  
  

### <a name="errorresponse"></a>ErrorResponse  
Det översta objekt som svaret innehåller när begäran misslyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_typ|Typ-tipset.|Sträng|  
|<a name="errors" />Fel|En lista över fel som beskriver orsaker varför begäran misslyckades.|[Fel](#error)|  

  
  
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

