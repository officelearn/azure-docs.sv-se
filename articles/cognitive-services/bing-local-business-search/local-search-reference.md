---
title: Bing Lokal Business Search API v7 Referens
titleSuffix: Azure Cognitive Services
description: Den här artikeln innehåller teknisk information om svarsobjekten och frågeparametrarna och rubrikerna som påverkar sökresultaten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: db764a73aa1bb18ef2fc0f8f6e5ffe8fd60d388c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74075699"
---
# <a name="bing-local-business-search-api-v7-reference"></a>Bing Lokal Business Search API v7 referens

API:et för sökinformation för lokalt företag skickar en sökfråga till Bing för att få resultat som inkluderar restauranger, hotell eller andra lokala företag. För platser kan frågan ange namnet på det lokala företaget eller en kategori (till exempel restauranger nära mig). Entitetsresultat omfattar personer, platser eller saker. Plats i detta sammanhang är affärsenheter, stater, länder /regioner, etc.  

Det här avsnittet innehåller teknisk information om svarsobjekten och frågeparametrarna och rubrikerna som påverkar sökresultaten. Exempel som visar hur du gör förfrågningar finns i Snabbstart för [Lokal företagssökning C#](quickstarts/local-quickstart.md) eller [Java-snabbstart](quickstarts/local-search-java-quickstart.md)för lokal företagssökning . 
  
Information om rubriker som begäranden ska innehålla finns i [Rubriker](#headers).  
  
Information om frågeparametrar som begäranden ska innehålla finns i [Frågeparametrar](#query-parameters).  
  
Information om JSON-objekten som svaret innehåller finns i [Svarsobjekt](#response-objects).

Information om tillåten användning och visning av resultat finns i [Använd och visa krav](use-display-requirements.md).


  
## <a name="endpoint"></a>Slutpunkt  
Om du vill begära lokala företagsresultat skickar du en GET-begäran till: 

``` 
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search

```
  
Begäran måste använda HTTPS-protokollet.  
  
> [!NOTE]
> Den maximala URL-längden är 2 048 tecken. För att säkerställa att url-längden inte överskrider gränsen bör den maximala längden på frågeparametrarna vara mindre än 1 500 tecken. Om URL:en överskrider 2 048 tecken returnerar servern 404 Hittades inte.  
  
  
## <a name="headers"></a>Rubriker  
Följande är de rubriker som en begäran och ett svar kan innehålla.  
  
|Huvud|Beskrivning|  
|------------|-----------------|  
|Acceptera|Valfritt begärandehuvud.<br /><br /> Standardmedietypen är application/json. Om du vill ange att svaret använder [JSON-LD](https://json-ld.org/)anger du acceptera huvudet till application/ld+json.|  
|<a name="acceptlanguage" />Accept-Language|Valfritt begärandehuvud.<br /><br /> En kommaavgränsad lista över språk som ska användas för användargränssnittssträngar. Listan är i fallande prioritetsordning. Mer information, bland annat om det förväntade formatet, finns i [RFC2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Det är huvudet och [setLang](#setlang)-frågeparametern utesluter varandra&mdash;ange inte båda två.<br /><br /> Om du anger huvudet måste du även ange frågeparametern cc. För att fastställa vilken marknad som resultat ska returneras för använder Bing det första språk som stöds på listan och kombinerar det med parametervärdet `cc`. Om listan inte innehåller något språk som stöds hittar Bing det närmaste språket och marknaden som har stöd för begäran, eller så använder Bing en aggregerad eller standardmarknad för resultatet. För att avgöra vilken marknad som används i Bing kan du gå till BingAPIs-Market-huvudet.<br /><br /> Använd enbart det här huvudet och `cc`-frågeparametern om du anger flera språk. Annars kan du använda frågeparametrarna [mkt](#mkt) och [setLang](#setlang).<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.|  
|<a name="market" />BingAPIs-Market|Svarshuvud.<br /><br /> Marknaden som används av begäran. Formuläret är \<languageCode\>-\<countryCode\>. Exempel: sv-SE.|  
|<a name="traceid" />BingAPIs-TraceId|Svarshuvud.<br /><br /> ID för loggposten som innehåller information om begäran. När ett fel uppstår ska du avbilda detta ID. Om det inte går att fastställa och lösa problemet ska du ange ID:t tillsammans med annan information som du ger supportteamet.|  
|<a name="subscriptionkey" />Ocp-Apim-Subscription-Key|Begärandehuvud som krävs.<br /><br /> Prenumerationsnyckeln som du fick när du registrerade dig för den här tjänsten i [Cognitive Services](https://www.microsoft.com/cognitive-services/).|  
|<a name="pragma" />Pragma|Valfritt begärandehuvud<br /><br /> Som standard returnerar Bing cachelagrat innehåll om det finns. Om du vill förhindra att Bing returnerar cachelagrat innehåll ska du ställa in huvudet Pragma på no-cache (till exempel Pragma: no-cache).
|<a name="useragent" />User-Agent|Valfritt begärandehuvud.<br /><br /> Användaragenten som skapade begäran. Bing använder användaragenten för att ge mobila användare en optimerad upplevelse. Även om det är valfritt rekommenderar vi även att du alltid anger det här huvudet.<br /><br /> Användaragenten ska vara samma sträng som alla vanliga webbläsare skickar. Information om användaragenter finns i [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Här följer några exempel på användaragentsträngar.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (kompatibel; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; som Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 som Mac OS X) AppleWebKit/536.26 (KHTML; som Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) som Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 som Mac OS X) AppleWebKit/537.51.1 (KHTML, som Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>|
|<a name="clientid" />X-MSEdge-ClientID|Valfritt huvud för begäran och svar.<br /><br /> Bing använder det här huvudet för att ge användarna konsekvent beteende i Bing API-anrop. Bing ger ofta förhandsversioner av nya funktioner och förbättringar och använder klient-ID som en nyckel för att tilldela trafik till olika förhandsversioner. Om du inte använder samma klient-ID för en användare vid flera förfrågningar kan sedan Bing tilldela användaren flera motstridiga förhandsversioner. Om du tilldelas flera motstridiga förhandsversioner kan det leda till en inkonsekvent användarupplevelse. Om till exempel den andra begäran har en annan förhandsversionstilldelning än den första kan upplevelsen vara oväntad. Bing kan också använda klient-ID för att skräddarsy webbresultatet för klient-ID:ts sökhistorik, vilket ger användaren en mer omfattande upplevelse.<br /><br /> Bing använder också det här huvudet för att förbättra resultatets rangordning genom att analysera aktiviteten som genererats av ett klient-ID. Relevansförbättringarna kan ge bättre resultat som levereras av Bing-API: er, vilka i sin tur möjliggör högre klickfrekvens för API-konsumenten.<br /><br /> **Viktigt!** Även om det är valfritt bör du överväga att använda det här huvudet som krävs. Bestående klient-ID för flera förfrågningar för samma slutanvändare och enhetskombination gör det möjligt 1) för API-konsumenten att få en konsekvent användarupplevelse och 2) att få högre klickfrekvens via resultat av högre kvalitet från Bing-API: er.<br /><br /> Följande är de grundläggande användningsregler som gäller för det här huvudet.<br /><ul><li>Varje användare som använder ditt program på enheten måste ha ett unikt, Bing-genererat klient-ID.<br /><br/>Om du inte använder det här huvudet i begäran genererar Bing ett ID och returnerar det i svarshuvudet X-MSEdge-ClientID. Den enda gången som du inte får inkludera det här huvudet i en begäran är första gången du använder din app på enheten.<br /><br/></li><li>Använd klient-ID för varje Bing API-begäran som din app gör för den här användaren på enheten.<br /><br/></li><li>**OBSERVERA:** Du måste se till att det här klient-ID:t inte kan länkas till någon autentiseringsbar användarkontoinformation.</li><br/><li>Spara klient-ID:t. Om du vill bevara ID:t i en webbläsarapp ska du använda en beständig HTTP-cookie i alla sessioner. Använd inte en sessionscookie. För andra appar, till exempel mobilappar, använder du enhetens beständiga lagring för att bevara ID.<br /><br/>Nästa gång användaren använder appen på enheten ska du hämta klient-ID:t som du sparade.</li></ul><br /> **Obs!** Bing-svar kanske eller kanske inte omfattar det här huvudet. Om svaret innehåller det här huvudet ska du avbilda klient-ID:t och använda det för alla efterföljande Bing-begäranden för användaren på enheten.<br /><br /> **Obs!** Om du inkluderar X-MSEdge-ClientID får du inte ta med cookies i begäran.|  
|<a name="clientip" />X-MSEdge-ClientIP|Valfritt begärandehuvud.<br /><br /> Klientenhetens IPv4- eller IPv6-adress. IP-adressen används för att identifiera användarens plats. Bing använder platsinformationen för att fastställa SafeSearch-beteende.<br /><br /> **Obs!** Även om det är valfritt rekommenderar vi att du alltid anger det här huvudet och huvudet X-Search-Location.<br /><br /> Förvräng inte adressen (till exempel genom att ändra den sista oktetten till 0). Om adressresultatet förvillas på en plats som inte är i närheten av enhetens verkliga plats kan det leda till att Bing presenterar felaktiga resultat.|  
|<a name="location" />X-Search-Location|Valfritt begärandehuvud.<br /><br /> En semikolonavgränsad lista med nyckel/värde-par som beskriver klientens geografiska plats. Bing använder platsinformationen till att fastställa ett säkert sökbeteende och returnera relevant lokalt innehåll. Ange nyckel/värde-par som \<nyckel\>:\<värde\>. Följande är de nycklar som används för att ange användarens plats.<br /><br /><ul><li>lat&mdash;Latitud för kundens plats, i grader. Latituden måste vara större än eller lika med -90.0 och vara mindre än eller lika med +90.0. Negativa värden anger sydliga latituder och positiva värden anger nordliga latituder.<br /><br /></li><li>lång&mdash;Longitud för kundens plats, i grader. Longituden måste vara större än eller lika med -180.0 och vara mindre än eller lika med +180.0. Negativa värden anger västliga longituder och positiva värden anger östliga longituder.<br /><br /></li><li>re&mdash; Radien, mäter in, som specificerar den horisontella exaktheten av koordinaterna. Skicka värdet som returneras av enhetens platstjänst. Typiska värden kan vara 22 m för GPS/Wi-Fi, 380 m för mobiltornstriangulering och 18 000 m för omvänd IP-sökning.<br /><br /></li><li>ts&mdash; UTC UNIX-tidsstämpeln för när klienten var på platsen. (UNIX-tidsstämpeln är antalet sekunder sedan den 1 januari 1970.)<br /><br /></li><li>head&mdash;Optional. Klientens relativa riktning eller färdriktning. Ange färdriktningen som grader från 0 till 360 där du räknar medurs i förhållande till norr. Ange endast den här nyckeln om nyckeln `sp` är nollskiljd.<br /><br /></li><li>sp&mdash; Den horisontella hastigheten (hastighet), i meter per sekund, att klientenheten färdas.<br /><br /></li><li>alt&mdash; Höjden på klientenheten, i meter.<br /><br /></li><li>are&mdash;Valfritt. Radien, i meter, som anger koordinaternas lodräta exakthet. Radie standard 50 kilometer. Ange den här nyckeln endast om du anger nyckeln `alt`.<br /><br /></li></ul> **OBS:** Även om dessa nycklar är valfria, desto mer information som du anger, desto mer exakt platsresultat är.<br /><br /> **OBS:** Du uppmanas att alltid ange användarens geografiska plats. Det är särskilt viktigt att ange plats om klientens IP-adress inte exakt avspeglar användarens fysiska plats (till exempel om klienten använder VPN). För bästa resultat bör du inkludera det här huvudet och huvudet X-MSEdge-ClientIP, men du bör minst inkludera det här huvudet.|

> [!NOTE] 
> Kom ihåg att användningsvillkoren kräver efterlevnad med alla tillämpliga lagar, inklusive lagar om användande av dessa huvud. I till exempel vissa jurisdiktioner, som Europa, finns det krav på att skaffa användarens medgivande innan du placerar vissa spårningsenheter på användarenheter.
  

## <a name="query-parameters"></a>Frågeparametrar  
Begäran kan innehålla följande frågeparametrar. Se kolumnen Obligatoriskt för obligatoriska parametrar. Du måste URL koda frågeparametrarna.  
  
  
|Namn|Värde|Typ|Krävs|  
|----------|-----------|----------|--------------|
|<a name="count" />antal|Antalet resultat som ska returneras, med början `offset` i det index som anges av parametern.|String|Inga|   
|<a name="localCategories" />lokalaKategorier|Lista över alternativ som definierar sökning efter företagskategori.  Se [sökning i lokala företagskategorier](local-categories.md)|String|Inga|  
|<a name="mkt" />mkt|Marknaden som resultatet kommer från. <br /><br />En lista över möjliga marknadsvärden finns i Marknadskoder.<br /><br /> **OBS:** Api:et för lokal företagssökning stöder för närvarande endast en-us-marknaden och språket.<br /><br />|String|Ja|
|<a name="offset"/>Förskjutning|Indexet för att starta resultat `count` som anges av parametern.|Integer|Inga|  
|<a name="query" />F|Användarens sökterm.|String|Inga|  
|<a name="responseformat" />svarFormat|Den medietyp som ska användas för svaret. Följande är möjliga fall-okänsliga värden.<br /><ul><li>JSON</li><li>JSONLD (på ett sätt som är upp till</li></ul><br /> Standard är JSON. Information om JSON-objekten som svaret innehåller finns i [Svarsobjekt](#response-objects).<br /><br />  Om du anger JsonLd innehåller svarstexten JSON-LD-objekt som innehåller sökresultaten. Information om JSON-LD finns i [JSON-LD](https://json-ld.org/).|String|Inga|  
|<a name="safesearch" />safeSearch|Ett filter som används för att filtrera innehåll som är olämpligt för barn. Här följer de möjliga skiftlägeskänsliga filtervärdena.<br /><ul><li>Off&mdash;Return webbsidor med vuxen text, bilder eller videor.<br /><br/></li><li>Moderera&mdash;returwebbsidor med vuxen text, men inte vuxenbilder eller videor.<br /><br/></li><li>Strikt&mdash;Returnera inte webbsidor med vuxen text, bilder eller videor.</li></ul><br /> Standardinställningen är Måttlig.<br /><br /> **OBS:** Om begäran kommer från en marknad som Bings vuxenpolicy kräver som `safeSearch` är `safeSearch` inställd på Strikt, ignorerar Bing värdet och använder Strikt.<br/><br/>**Obs!** Beroende på om du använder frågeoperator `site:` finns det en risk att svaret har innehåll som är olämpligt för barn oberoende av vad frågeparametern `safeSearch` är inställd på. Använd endast `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn. |String|Inga|  
|<a name="setlang" />setLang|Språket som ska användas för användargränssnittssträngar. Ange språk med hjälp av den tvåstaviga språkkoden ISO 639-1. Språkkoden för engelska är till exempel EN. Standardvärdet är EN (engelska).<br /><br /> Även om det är valfritt bör du alltid ange språket. Normalt anger du `setLang` på samma språk som anges av `mkt` om inte användaren vill att gränssnittets strängar ska visas på ett annat språk.<br /><br /> Den här parametern och [Accept-Language](#acceptlanguage)-huvudet utesluter varandra&mdash;ange inte båda två.<br /><br /> En användargränssnittssträng är en sträng som används som en etikett i ett användargränssnitt. Det finns några användargränssnittssträngar i JSON-svarsobjekt. Alla länkar till Bing.com-egenskaper i svarsobjekten använder det angivna språket.|String|Inga| 


## <a name="response-objects"></a>Svarsobjekt  
Följande är JSON-svarsobjekt som svaret kan innehålla. Om begäran lyckas är objektet på den översta nivån i svaret [SearchResponse-objektet.](#searchresponse) Om begäran misslyckas är objektet på den översta nivån [ErrorResponse-objektet.](#errorresponse)


|Objekt|Beskrivning|  
|------------|-----------------|  
|[Plats](#place)|Definierar information om ett lokalt företag som en restaurang eller ett hotell.|  

  
### <a name="error"></a>Fel  
Definierar felet som uppstod.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|<a name="error-code" />Koden|Felkoden som identifierar felkategorin. En lista över möjliga koder finns i [Felkoder](#error-codes).|String|  
|<a name="error-message" />Meddelande|En beskrivning av felet.|String|  
|<a name="error-moredetails" />merDetaljer|En beskrivning som innehåller ytterligare information om felet.|String|  
|<a name="error-parameter" />Parametern|Frågeparametern i begäran som orsakade felet.|String|  
|<a name="error-subcode" />underkod|Felkoden som identifierar felet. Om till `code` exempel InvalidRequest `subCode` är Det kan vara ParameterInvalid eller ParameterInvalidValue. |String|  
|<a name="error-value" />värde|Frågeparameterns värde som inte var giltigt.|String|  
  

### <a name="errorresponse"></a>ErrorResponse  
Det objekt på den översta nivån som svaret innehåller när begäran misslyckas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typ tips.|String|  
|<a name="errors" />Fel|En lista över fel som beskriver orsakerna till att begäran misslyckades.|[Fel](#error)[]|  

  
  
### <a name="license"></a>Licens  
Definierar den licens under vilken texten eller fotot kan användas.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Namnet på licensen.|String|  
|url|Url:en till en webbplats där användaren kan få mer information om licensen.<br /><br /> Använd namnet och URL:en för att skapa en hyperlänk.|String|  


### <a name="link"></a>Länk  
Definierar komponenterna i en hyperlänk.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typ tips.|String|  
|text|Visningstexten.|String|  
|url|En URL. Använd URL:en och visa text för att skapa en hyperlänk.|String|  
  


  
### <a name="organization"></a>Organisation  
Definierar en utgivare.  
  
Observera att en utgivare kan uppge sitt namn eller sin webbplats eller båda.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|namn|Förlagets namn.|String|  
|url|Url:en till utgivarens webbplats.<br /><br /> Observera att utgivaren kanske inte tillhandahåller en webbplats.|String|  
  
  

### <a name="place"></a>Plats  
Definierar information om ett lokalt företag, till exempel en restaurang eller ett hotell.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typtips, som kan ställas in på något av följande:<br /><br /><ul><li>Hotell</li><li>Lokalt Företag<br /></li><li>Restaurang</ul><li>|String|  
|adress|Postadressen till var enheten är belägen.|Postadress|  
|enhetPresentationInfo|Ytterligare information om entiteten, till exempel tips som du kan använda för att bestämma entitetens typ. Till exempel, oavsett om det är en restaurang eller ett hotell. Fältet `entityScenario` är inställt på ListItem.|EntitetPresentationInfo|  
|namn|Entitetens namn.|String|  
|telefon|Enhetens telefonnummer.|String|  
|url|URL:en till entitetens webbplats.<br /><br /> Använd den här URL:en-adressen tillsammans med entitetens namn för att skapa en hyperlänk som användaren tar till entitetens webbplats när du klickar på den.|String|  
|webSearchUrl (olikartade)|Url:en till Bings sökresultat för den här platsen.|String| 
  
  
### <a name="querycontext"></a>QueryContext  
Definierar frågekontexten som Bing använde för begäran.  
  
|Element|Beskrivning|Typ|  
|-------------|-----------------|----------|  
|vuxenIntent|Ett booleskt värde som anger om den angivna frågan har en vuxen avsikt. Värdet är **sant** om frågan har en vuxen avsikt. annars **falskt**.|Boolean|  
|ändringÖvergiftQuery|Frågesträngen som ska användas för att tvinga Bing att använda den ursprungliga strängen. Om frågesträngen till exempel *är saling medvind*kommer åsidosättningsfrågesträngen att *+saling medvind*. Kom ihåg att koda frågesträngen som resulterar i *%2Baling+medvind*.<br /><br /> Det här fältet inkluderas bara om den ursprungliga frågesträngen innehåller ett stavfel.|String|  
|alteredQuery|Frågesträngen som används av Bing för att utföra frågan. Bing använder den ändrade frågesträngen om den ursprungliga frågesträngen innehöll stavfel. Om frågesträngen till `saling downwind`exempel är kommer den `sailing downwind`ändrade frågesträngen att vara .<br /><br /> Det här fältet inkluderas bara om den ursprungliga frågesträngen innehåller ett stavfel.|String|  
|frågaAnvändareFörlokalisering|Ett booleskt värde som anger om Bing kräver användarens plats för att ge korrekta resultat. Om du har angett användarens plats med hjälp av rubrikerna [X-MSEdge-ClientIP](#clientip) och [X-Search-Location](#location) kan du ignorera det här fältet.<br /><br /> För platsmedvetna frågor, till exempel "dagens väder" eller "restauranger nära mig" som behöver användarens plats för att ge korrekta resultat, är det här fältet inställt på **true**.<br /><br /> För platsmedvetna frågor som innehåller platsen (till exempel "Seattle väder"), är det här fältet inställt på **false**. Det här fältet är också inställt på **false** för frågor som inte är platsmedvetna, till exempel "bästsäljare".|Boolean|  
|originalQuery|Frågesträngen som anges i begäran.|String|  

### <a name="identifiable"></a>Identifierbar

|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|id|En resursidentifierare|String|
 
### <a name="rankinggroup"></a>RankingGrupp
Definierar en sökresultatgrupp, till exempel huvudlinje.

|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|Objekt|En lista med sökresultat som ska visas i gruppen.|RankingItm|

### <a name="rankingitem"></a>RankingItm
Definierar ett sökresultatobjekt som ska visas.

|Namn|Värde|Typ|  
|-------------|-----------------|----------|
|resultatIndex|Ett nollbaserat index för objektet i svaret som ska visas. Om objektet inte innehåller det här fältet visar du alla objekt i svaret. Visa till exempel alla nyhetsartiklar i svaret Nyheter.|Integer|
|answerType (svarstyp)|Svaret som innehåller det objekt som ska visas. Till exempel Nyheter.<br /><br />Använd typen för att hitta svaret i SearchResponse-objektet. Typen är namnet på ett SearchResponse-fält.<br /><br /> Använd dock endast svarstypen om det här objektet innehåller värdefältet. Annars ignorerar du det.|String|
|textspråkigtIndex|Indexet för svaret i textEnvar att visa.| Osignerat heltal|
|värde|ID:t som identifierar antingen ett svar som ska visas eller ett objekt med ett svar att visa. Om ID:et identifierar ett svar visar du alla objekt i svaret.|Identifierbar|

### <a name="rankingresponse"></a>RankingSvare  
Definierar var innehållet på sökresultatsidan ska placeras och i vilken ordning.  
  
|Namn|Värde|  
|----------|-----------|  
|<a name="ranking-mainline" />Mainline|Sökresultaten som ska visas i huvudlinjen.|  
|<a name="ranking-pole" />Pole|De sökresultat som bör ges den mest synliga behandlingen (till exempel visas ovanför huvudlinjen och sidofältet).|  
|<a name="ranking-sidebar" />Sidofältet|Sökresultaten som ska visas i sidofältet.| 

### <a name="searchresponse"></a>SearchResponse  
Definierar det objekt på den översta nivån som svaret innehåller när begäran lyckas.  
  
Observera att om tjänsten misstänker en denial of service-attack, kommer begäran att lyckas (HTTP-statuskoden är 200 OK); Svarets brödtext kommer dock att vara tom.  
  
|Namn|Värde|Typ|  
|----------|-----------|----------|  
|_type|Typtips, som är inställt på SearchResponse.|String|  
|Platser|En lista över entiteter som är relevanta för sökfrågan.|JSON-objekt|  
|frågaKontext|Ett objekt som innehåller frågesträngen som Bing använde för begäran.<br /><br /> Det här objektet innehåller frågesträngen som anges av användaren. Den kan också innehålla en ändrad frågesträng som Bing använde för frågan om frågesträngen innehöll ett stavfel.|[QueryContext](#querycontext)|  


## <a name="error-codes"></a>Felkoder

Följande är möjliga HTTP-statuskoder som en begäran returnerar.  
  
|Statuskod|Beskrivning|  
|-----------------|-----------------|  
|200|Lyckades.|  
|400|En av frågeparametrarna saknas eller är ogiltig.|  
|401|Prenumerationsnyckeln saknas eller är ogiltig.|  
|403|Användaren autentiseras (de använde till exempel en giltig prenumerationsnyckel) men de har inte behörighet till den begärda resursen.<br /><br /> Bing kan också returnera den här statusen om anroparen har överskridit sina frågor per månad kvot.|  
|410|Begäran använde HTTP i stället för HTTPS-protokollet. HTTPS är det enda protokoll som stöds.|  
|429|Anroparen överskred sina frågor per sekund kvot.|  
|500|Oväntat serverfel.|

Om begäran misslyckas innehåller svaret ett [ErrorResponse-objekt](#errorresponse) som innehåller en lista över [felobjekt](#error) som beskriver vad som orsakade fel. Om felet är relaterat till `parameter` en parameter identifierar fältet den parameter som är problemet. Och om felet är relaterat till `value` ett parametervärde identifierar fältet det värde som inte är giltigt.

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

Följande är möjliga felkod och underfelskodvärden.

|Kod|Underkod|Beskrivning
|-|-|-
|ServerError (ServerError)|Oväntatare<br/>ResourceError (ResourceError)<br/>Har inte implementerats|HTTP-statuskoden är 500.
|Ogiltigt begärs|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. En obligatorisk parameter saknas till exempel eller så är ett parametervärde ogiltigt.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue är HTTP-statuskoden 400.<br/><br/>Om du använder HTTP-protokollet i stället för HTTPS returnerar Bing HttpNotAllowed och HTTP-statuskoden är 410.
|RateLimitExceed|Inga underkoder|Bing returnerar RateLimitExceed när du överskrider dina frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Om du överskrider QPS returnerar Bing HTTP-statuskod 429, och om du överskrider QPM returnerar Bing 403.
|Ogiltiga myndigheter|Tillståndstagande<br/>TillståndSbrott|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. `Ocp-Apim-Subscription-Key` Huvudet saknas till exempel eller så är prenumerationsnyckeln ogiltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization är HTTP-statuskoden 401.
|Otillräcklig auktorisering|AuktoriseringDisabled<br/>Auktoriseringexpirerad|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization är HTTP-statuskoden 403.

## <a name="next-steps"></a>Nästa steg
- [Snabbstart för lokal företagssökning](quickstarts/local-quickstart.md)
- [Snabbstart för Java-sökning för lokal företagssökning](quickstarts/local-search-java-quickstart.md)
- [Snabbstart för söknod för lokalt företag](quickstarts/local-search-node-quickstart.md)
- [Snabbstart för Lokal företagssökning i Python](quickstarts/local-search-python-quickstart.md)
