---
title: Azure Traffic Manager - Vanliga frågor och frågor
description: Den här artikeln innehåller svar på vanliga frågor om Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: rohink
ms.openlocfilehash: acdac6e3eafc5251ebd31a34bcb9a4db34f0ebbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254370"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Vanliga frågor och svar om traffic manager (Vanliga frågor och svar)

## <a name="traffic-manager-basics"></a>Grunderna i Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Vilken IP-adress använder Traffic Manager?

Som förklaras i [Hur Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md)fungerar Traffic Manager på DNS-nivå. Den skickar DNS-svar till direktklienter till lämplig tjänstslutpunkt. Klienter ansluter sedan till tjänstslutpunkten direkt, inte via Traffic Manager.

Traffic Manager tillhandahåller därför ingen slutpunkt eller IP-adress som klienter kan ansluta till. Om du vill ha statisk IP-adress för tjänsten måste den konfigureras på tjänsten, inte i Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Vilka typer av trafik kan dirigeras med Traffic Manager?
Som förklaras i [Hur Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md)kan en Traffic Manager-slutpunkt vara vilken internetinriktad tjänst som helst i eller utanför Azure. Därför kan Traffic Manager dirigera trafik som kommer från det offentliga internet till en uppsättning slutpunkter som också är internet inför. Om du har slutpunkter som finns i ett privat nätverk (till exempel en intern version av [Azure Load Balancer)](../load-balancer/concepts-limitations.md#internalloadbalancer)eller har användare som gör DNS-begäranden från sådana interna nätverk, kan du inte använda Traffic Manager för att dirigera den här trafiken.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Stöder Traffic Manager "klibbiga" sessioner?

Som förklaras i [Hur Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md)fungerar Traffic Manager på DNS-nivå. Den använder DNS-svar för att dirigera klienter till rätt tjänstslutpunkt. Klienter ansluter till tjänstslutpunkten direkt, inte via Traffic Manager. Traffic Manager ser därför inte HTTP-trafiken mellan klienten och servern.

Dessutom tillhör käll-IP-adressen för DNS-frågan som tas emot av Traffic Manager den rekursiva DNS-tjänsten, inte klienten. Traffic Manager har därför inget sätt att spåra enskilda klienter och kan inte implementera "klibbiga" sessioner. Den här begränsningen är gemensam för alla DNS-baserade trafikhanteringssystem och är inte specifik för Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Varför visas ett HTTP-fel när jag använder Traffic Manager?

Som förklaras i [Hur Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md)fungerar Traffic Manager på DNS-nivå. Den använder DNS-svar för att dirigera klienter till rätt tjänstslutpunkt. Klienter ansluter sedan till tjänstslutpunkten direkt, inte via Traffic Manager. Traffic Manager ser inte HTTP-trafik mellan klient och server. Därför måste alla HTTP-fel som visas komma från ditt program. För att klienten ska kunna ansluta till programmet är alla DNS-lösningssteg slutförs. Det inkluderar all interaktion som Traffic Manager har på programmets trafikflöde.

Ytterligare undersökningar bör därför inriktas på ansökan.

HTTP-värdhuvudet som skickas från klientens webbläsare är den vanligaste problemkällan. Kontrollera att programmet är konfigurerat för att acceptera rätt värdhuvud för det domännamn du använder. Slutpunkter med Azure App Service finns [i konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager](../app-service/configure-domain-traffic-manager.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Vad har prestandapåverkan med Traffic Manager?

Som förklaras i [Hur Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md)fungerar Traffic Manager på DNS-nivå. Eftersom klienter ansluter till dina tjänstslutpunkter direkt uppstår ingen prestandapåverkan när trafikhanteraren när anslutningen har upprättats.

Eftersom Traffic Manager integreras med program på DNS-nivå krävs en ytterligare DNS-sökning som ska infogas i DNS-lösningskedjan. Traffic Manager inverkan på DNS-matchningstiden är minimal. Traffic Manager använder ett globalt nätverk av namnservrar och använder [anycast-nätverk](https://en.wikipedia.org/wiki/Anycast) för att säkerställa att DNS-frågor alltid dirigeras till den närmaste tillgängliga namnservern. Dessutom innebär cachelagring av DNS-svar att den ytterligare DNS-svarstid som uppstår genom att använda Traffic Manager endast gäller för en bråkdel av sessionerna.

Metoden Prestanda dirigerar trafik till närmaste tillgängliga slutpunkt. Nettoresultatet är att den totala prestandapåverkan som är associerad med den här metoden ska vara minimal. Varje ökning av DNS-svarstiden bör kompenseras av lägre nätverksfördröjning till slutpunkten.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Vilka programprotokoll kan jag använda med Traffic Manager?

Som förklaras i [Hur Traffic Manager Works](../traffic-manager/traffic-manager-how-it-works.md)fungerar Traffic Manager på DNS-nivå. När DNS-sökningen är klar ansluter klienter till programmets slutpunkt direkt, inte via Traffic Manager. Därför kan anslutningen använda alla programprotokoll. Om du väljer TCP som övervakningsprotokoll kan Trafikhanterarens hälsoövervakning för slutpunkt utföras utan att använda några programprotokoll. Om du väljer att verifiera hälsotillståndet med hjälp av ett programprotokoll måste slutpunkten kunna svara på http- eller HTTPS GET-begäranden.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan jag använda Traffic Manager med ett "naket" domännamn?

Ja. Mer information om hur du skapar en aliaspost för ditt domännamn apex för att referera till en Azure Traffic Manager-profil finns i [Konfigurera en aliaspost för att stödja apex-domännamn med Traffic Manager](../dns/tutorial-alias-tm.md).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Tar Traffic Manager hänsyn till klientundernätsadressen när dns-frågor hanteras? 

Ja, förutom käll-IP-adressen för den DNS-fråga som tas emot (som vanligtvis är IP-adressen för DNS-matcharen), när du utför uppslag efter metoder för geografisk, prestanda och undernätsdirigering, tar trafikhanteraren också hänsyn till klientundernätsadressen om Den ingår i frågan av matcharen som gör begäran för slutanvändarens räkning.  
Rfc [7871 – klientundernät i DNS-frågor](https://tools.ietf.org/html/rfc7871) som tillhandahåller en [tilläggsmekanism för DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) som kan vidarebefordra klientundernätsadressen från resolvers som stöder det.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Vad är DNS TTL och hur påverkar det mina användare?

När en DNS-fråga hamnar på Traffic Manager anger den ett värde i svaret som kallas time-to-live (TTL). Det här värdet, vars enhet är i sekunder, anger för DNS-resolvers nedströms hur länge det här svaret ska cachelagras. DNS-resolvers garanteras inte cachelagra det här resultatet, men cachelagring gör det möjligt för dem att svara på eventuella efterföljande frågor från cacheminnet i stället för att gå till Traffic Manager DNS-servrar. Detta påverkar svaren på följande sätt:

- en högre TTL minskar antalet frågor som landar på Traffic Manager DNS-servrar, vilket kan minska kostnaden för en kund eftersom antalet frågor som visas är en fakturerbar användning.
- en högre TTL kan potentiellt minska den tid det tar att göra en DNS-sökning.
- en högre TTL innebär också att dina data inte återspeglar den senaste hälsoinformation som Traffic Manager har fått genom sina sonderingsagenter.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hur högt eller lågt kan jag ställa in TTL för Traffic Manager-svar?

Du kan ställa in DNS TTL på en nivå per profil så att den är så låg som 0 sekunder och så hög som 2 147 483 647 sekunder (det maximala intervallet som uppfyller [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). En TTL på 0 innebär att nedströms DNS-resolvers inte cachelagrar frågesvar och att alla frågor förväntas nå Traffic Manager DNS-servrar för lösning.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hur kan jag förstå mängden frågor som kommer till min profil? 

Ett av måtten som tillhandahålls av Traffic Manager är antalet frågor som besvaras av en profil. Du kan hämta den här informationen på en aggregering på profilnivå eller så kan du dela upp den ytterligare för att se volymen av frågor där specifika slutpunkter returnerades. Dessutom kan du ställa in aviseringar för att meddela dig om frågesvarsvolymen korsar de villkor som du har angett. För mer information, [Traffic Manager-mått och aviseringar](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Trafikhanterare Geografisk trafikroutningsmetod

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Vilka är några användningsfall där geografisk routning är användbar?

Geografisk routningstyp kan användas i alla scenarier där en Azure-kund behöver skilja sina användare baserat på geografiska regioner. Med hjälp av metoden Geografisk trafikroutning kan du till exempel ge användare från specifika regioner en annan användarupplevelse än de som kommer från andra regioner. Ett annat exempel är att följa lokala datasuveränitetsmandat som kräver att användare från en viss region endast betjänas av slutpunkter i den regionen.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hur avgör jag om jag ska använda prestandaroutningsmetod eller geografisk routningsmetod?

Den viktigaste skillnaden mellan dessa två populära routningsmetoder är att i prestandaroutningsmetod är ditt primära mål att skicka trafik till slutpunkten som kan ge den lägsta svarstiden till anroparen, medan det primära målet i Geografisk routning är att framtvinga en geo för dina uppringare så att du medvetet kan dirigera dem till en viss slutpunkt. Överlappningen sker eftersom det finns ett samband mellan geografisk närhet och lägre latens, även om detta inte alltid är sant. Det kan finnas en slutpunkt i en annan geografi som kan ge den anropare en bättre latensupplevelse och i så fall skickar prestandaroutning användaren till den slutpunkten, men geografisk routning skickar dem alltid till den slutpunkt som du har mappat för deras geografiska regionen. För att ytterligare klargöra det, överväga följande exempel - med geografisk routning kan du göra ovanliga mappningar som att skicka all trafik från Asien till slutpunkter i USA och all amerikansk trafik till slutpunkter i Asien. I så fall kommer geografisk routning medvetet göra exakt vad du har konfigurerat den att göra och prestandaoptimering är inte en faktor. 
>[!NOTE]
>Det kan finnas scenarier där du kan behöva både prestanda och geografiska routningsfunktioner, för dessa scenarier kan kapslade profiler vara bra val. Du kan till exempel ställa in en överordnad profil med geografisk routning där du skickar all trafik från Nordamerika till en kapslad profil som har slutpunkter i USA och använda prestandaroutning för att skicka den trafiken till den bästa slutpunkten i den uppsättningen. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Vilka regioner stöds av Traffic Manager för geografisk routning?

Lands-/regionhierarkin som används av Traffic Manager finns [här](traffic-manager-geographic-regions.md). Även om den här sidan hålls uppdaterad med eventuella ändringar, kan du också programmässigt hämta samma information med hjälp av [AZURE Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hur avgör Traffic Manager var en användare frågar ifrån?

Traffic Manager tittar på källan IP för frågan (detta är troligen en lokal DNS-resolver gör frågan på uppdrag av användaren) och använder en intern IP till region karta för att bestämma platsen. Denna karta uppdateras löpande för att ta hänsyn till förändringar i Internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Är det garanterat att Traffic Manager korrekt kan bestämma den exakta geografiska platsen för användaren i varje enskilt fall?

Nej, Traffic Manager kan inte garantera att den geografiska region som vi härleder från käll-IP-adressen för en DNS-fråga alltid motsvarar användarens plats på grund av följande orsaker:

- Först, som beskrivs i föregående FAQ, källan IP-adress vi ser är att en DNS-resolver gör sökningen på uppdrag av användaren. Medan dns-matcharens geografiska plats är en bra proxy för användarens geografiska plats, kan den också vara olika beroende på DNS-matchningstjänstens fotavtryck och den specifika DNS-matchningstjänst som kunden har valt att använda. Ett exempel kan en kund i Malaysia ange i enhetens inställningar använda en DNS-matchningstjänst vars DNS-server i Singapore kan väljas för att hantera frågematchningarna för den användaren/enheten. I så fall kan Traffic Manager bara se matcharens IP-adress som motsvarar Singapore-platsen. Se även tidigare vanliga frågor om support för klientundernätsadresser på den här sidan.

- För det andra använder Traffic Manager en intern karta för att göra IP-adressen till geografisk regionöversättning. Även om denna karta valideras och uppdateras kontinuerligt för att öka dess riktighet och redogöra för internets föränderliga karaktär, finns det fortfarande en möjlighet att vår information inte är en exakt representation av den geografiska placeringen av hela IP Adresser.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Måste en slutpunkt vara fysiskt placerad i samma region som den som konfigureras med för geografisk routning?

Nej, slutpunktens plats medför inga begränsningar för vilka regioner som kan mappas till den. En slutpunkt i REGIONEN US-Central Azure kan till exempel ha alla användare från Indien riktade till den.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan jag tilldela geografiska regioner till slutpunkter i en profil som inte är konfigurerad för geografisk routning?

Ja, om routningsmetoden för en profil inte är geografisk kan du använda [REST-API:et](https://docs.microsoft.com/rest/api/trafficmanager/) för Azure Traffic Manager för att tilldela geografiska regioner till slutpunkter i den profilen. När det gäller icke-geografiska routningstypprofiler ignoreras den här konfigurationen. Om du ändrar en sådan profil till geografisk routningstyp vid ett senare tillfälle kan Traffic Manager använda dessa mappningar.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Varför får jag ett felmeddelande när jag försöker ändra routningsmetoden för en befintlig profil till Geografisk?

Alla slutpunkter under en profil med geografisk routning måste ha minst en region mappad till den. Om du vill konvertera en befintlig profil till geografisk routningstyp måste du först associera geografiska regioner till alla dess slutpunkter med hjälp av [AZURE Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) innan du ändrar routningstypen till geografisk. Om du använder portalen tar du först bort slutpunkterna, ändrar flödesmetoden för profilen till geografisk och lägger sedan till slutpunkterna tillsammans med mappningen av geografiska områden.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Varför rekommenderas det starkt att kunder skapar kapslade profiler i stället för slutpunkter under en profil med geografisk routning aktiverad?

En region kan tilldelas endast en slutpunkt i en profil om den använder den geografiska routningsmetoden. Om slutpunkten inte är en kapslad typ med en underordnad profil kopplad till den, om slutpunkten går felfritt, fortsätter Traffic Manager att skicka trafik till den eftersom alternativet att inte skicka någon trafik inte är bättre. Traffic Manager redundas inte till en annan slutpunkt, även om den tilldelade regionen är en "överordnad" region som tilldelats slutpunkten som gick felfritt (till exempel om en slutpunkt som har region Spanien går ohälsosamt, redundas vi inte till en annan slutpunkt som har regionen Europa tilldelats den). Detta görs för att säkerställa att Traffic Manager respekterar de geografiska gränser som en kund har ställt in i sin profil. För att få fördelen av att misslyckas över till en annan slutpunkt när en slutpunkt går felfritt, rekommenderas att geografiska regioner tilldelas kapslade profiler med flera slutpunkter i stället för enskilda slutpunkter. På så sätt, om en slutpunkt i den kapslade underordnade profilen misslyckas, kan trafiken växla över till en annan slutpunkt inuti samma kapslade underordnade profil.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Finns det några begränsningar för API-versionen som stöder den här routningstypen?

Ja, endast API-version 2017-03-01 och nyare stöder den geografiska routningstypen. Alla äldre API-versioner kan inte användas för att skapa profiler av geografisk routningstyp eller tilldela geografiska regioner till slutpunkter. Om en äldre API-version används för att hämta profiler från en Azure-prenumeration returneras ingen profil för geografisk routningstyp. Dessutom, när du använder äldre API-versioner, någon profil som returneras som har slutpunkter med en geografisk region tilldelning, inte har sin geografiska region tilldelning visas.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Routningsmetod för trafikhanterarens undernätstrafik

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Vilka är några användningsfall där undernätsdirigering är användbar?

Med undernätsdirigering kan du skilja den upplevelse du levererar för specifika uppsättningar användare som identifieras av käll-IP-adressen för deras IP-adress för DNS-begäranden. Ett exempel skulle vara att visa olika innehåll om användare ansluter till en webbplats från företagets huvudkontor. En annan skulle vara att begränsa användare från vissa Internet-adresser till att endast komma åt slutpunkter som endast stöder IPv4-anslutningar om dessa Internetleverantörer har underparprestanda när IPv6 används.
En annan orsak till att använda undernätsroutningsmetoden är tillsammans med andra profiler i en kapslad profiluppsättning. Om du till exempel vill använda geografisk routningsmetod för geo-stängsel för användarna, men för en viss Internet-leverantören som du vill göra en annan routningsmetod, kan du ha en routningsmetod för undernät som den överordnade profilen och åsidosätta internetleverantören för att använda ett visst underordnat profilen och har standardgeinderad profil för alla andra.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hur känner Traffic Manager till slutanvändarens IP-adress?

Slutanvändarens enheter använder vanligtvis en DNS-matchning för att utföra DNS-sökningen för deras räkning. Den utgående IP för sådana resolvers är vad Traffic Manager ser som källan IP. Dessutom ser undernätsdirigeringsmetoden också ut för att se om det finns ECS-information (Extended Client Subnet) som skickades med begäran. Om ECS-information finns, det är den adress som används för att bestämma operationsföljden. I avsaknad av ECS-information används frågans käll-IP för routning.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hur kan jag ange IP-adresser när jag använder undernätsdirigering?

IP-adresser som ska associeras med en slutpunkt kan anges på två sätt. Först kan du använda den quad prickade decimaloktettnotation med en start- och slutadress för att ange intervallet (till exempel 1.2.3.4-5.6.7.8 eller 3.4.5.6-3.4.5.6). För det andra kan du använda CIDR-notationen för att ange intervallet (till exempel 1.2.3.0/24). Du kan ange flera intervall och kan använda båda notationstyperna i en intervalluppsättning. Några begränsningar gäller.

-    Du kan inte ha överlappning av adressintervall eftersom varje IP behöver mappas till endast en enda slutpunkt
-    Startadressen får inte vara mer än slutadressen
-    När det gäller CIDR-notationen bör IP-adressen före "/" vara startadressen för det intervallet (till exempel är 1.2.3.0/24 giltig men 1.2.3.4.4/24 är INTE giltig)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hur kan jag ange en återgångsslutpunkt när jag använder undernätsroutning?

Om du har en slutpunkt utan undernät som är mappad till den i en profil med undernätsdirigering dirigeras alla begäranden som inte matchar med andra slutpunkter här. Vi rekommenderar starkt att du har en sådan återgångsslutpunkt i din profil eftersom Traffic Manager returnerar ett NXDOMAIN-svar om en begäran kommer in och den inte mappas till några slutpunkter eller om den mappas till en slutpunkt men slutpunkten är felaktig.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Vad händer om en slutpunkt inaktiveras i en profil för undernätsroutningstyp?

I en profil med undernätsdirigering, om du har en slutpunkt med den inaktiverade, fungerar Traffic Manager som om slutpunkten och undernätsmappningarna som den har inte finns. Om en fråga som skulle ha matchats med dess IP-adressmappning tas emot och slutpunkten är inaktiverad, returnerar Traffic Manager en återgångsslutpunkt (en utan mappningar) eller om en sådan slutpunkt inte finns, returnerar ett NXDOMAIN-svar.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Trafikhanterare MultiValue trafik routing metod

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Vilka är några användningsfall där MultiValue-routning är användbar?

MultiValue-routning returnerar flera felfria slutpunkter i ett enda frågesvar. Den största fördelen med detta är att om en slutpunkt är felfritt, har klienten fler alternativ för att försöka igen utan att göra ett annat DNS-anrop (som kan returnera samma värde från en uppströmscache). Detta gäller för tillgänglighetskänsliga program som vill minimera stilleståndstiden.
En annan användning för MultiValue-routningsmetod är om en slutpunkt är "dual-homed" till både IPv4- och IPv6-adresser och du vill ge den som ringer båda alternativen att välja mellan när den initierar en anslutning till slutpunkten.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hur många slutpunkter returneras när MultiValue-routning används?

Du kan ange det maximala antalet slutpunkter som ska returneras och MultiValue returnerar inte mer än så många felfria slutpunkter när en fråga tas emot. Det högsta möjliga värdet för den här konfigurationen är 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Kommer jag att få samma uppsättning slutpunkter när MultiValue-routning används?

Vi kan inte garantera att samma uppsättning slutpunkter returneras i varje fråga. Detta påverkas också av det faktum att vissa slutpunkter kan gå ohälsosamma vid vilken tidpunkt de inte kommer att ingå i svaret

## <a name="real-user-measurements"></a>Faktisk slutanvändarmätning

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Vilka är fördelarna med att använda riktiga användarmätningar?

När du använder prestandaroutningsmetod väljer Traffic Manager den bästa Azure-regionen som slutanvändaren kan ansluta till genom att inspektera käll-IP- och EDNS-klientundernätet (om det skickas in) och kontrollerar den mot den nätverksfördröjning som tjänsten underhåller. Real User Measurements förbättrar detta för din slutanvändarbas genom att deras erfarenhet bidrar till den här svarstidstabellen förutom att se till att den här tabellen på ett tillfredsställande sätt sträcker sig över slutanvändarnätverken där dina slutanvändare ansluter till Azure. Detta leder till en ökad noggrannhet i routningen av slutanvändaren.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan jag använda riktiga användarmätningar med regioner som inte är Azure?

Verkliga användarmätningar mäter och rapporterar om endast svarstiden för att nå Azure-regioner. Om du använder prestandabaserad routning med slutpunkter som finns i regioner som inte är Azure kan du fortfarande dra nytta av den här funktionen genom att ha ökad svarstidsinformation om den representativa Azure-regionen som du hade valt att associeras med den här slutpunkten.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Vilken routningsmetod drar nytta av verkliga användarmätningar?

Den ytterligare information som erhålls genom verkliga användarmätningar gäller endast för profiler som använder prestandaroutningsmetoden. Länken Verkliga användarmätningar är tillgänglig från alla profiler när du visar den via Azure-portalen.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Måste jag aktivera Real User Measurements varje profil separat?

Nej, du behöver bara aktivera den en gång per prenumeration och all svarstidsinformation som mäts och rapporteras är tillgängliga för alla profiler.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hur inaktiverar jag Riktiga användarmätningar för min prenumeration?

Du kan sluta samla in avgifter relaterade till verkliga användarmätningar när du slutar samla in och skicka tillbaka svarstidsmätningar från klientprogrammet. När du till exempel mäter JavaScript inbäddat på webbsidor kan du sluta använda den här funktionen genom att ta bort JavaScript eller genom att stänga av dess anrop när sidan återges.

Du kan också stänga av riktiga användarmått genom att ta bort nyckeln. När du har tagit bort nyckeln ignoreras alla mätningar som skickas till Traffic Manager med den nyckeln.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan jag använda realanvändarmätningar med andra klientprogram än webbsidor?

Ja, Real User Measurements är utformat för att få in data som samlas in via olika typer av slutanvändare. Den här vanliga frågor och svar kommer att uppdateras när nya typer av klientprogram får stöd.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hur många mätningar görs varje gång min webbsida med riktiga användarmätningar återges?

När Real User Measurements används med mätningen JavaScript tillhandahålls, resulterar varje sidrendering i sex mätningar som görs. Dessa rapporteras sedan tillbaka till trafikhanterarens tjänst. Du debiteras för den här funktionen baserat på antalet mätningar som rapporteras till Traffic Manager-tjänsten. Om användaren till exempel navigerar bort från webbsidan medan mätningarna görs men innan de rapporterades, beaktas inte dessa mätningar för fakturering.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Finns det en fördröjning innan skriptet Real User Measurements körs på min webbsida?

Nej, det finns ingen programmerad fördröjning innan skriptet anropas.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan jag använda riktiga användarmätningar med endast de Azure-regioner som jag vill mäta?

Nej, varje gång det anropas mäter skriptet Verkliga användarmätningar en uppsättning av sex Azure-regioner som bestäms av tjänsten. Den här uppsättningen ändras mellan olika anrop och när ett stort antal sådana anrop inträffar sträcker sig mättäckningen över olika Azure-regioner.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan jag begränsa antalet mätningar som gjorts till ett visst antal?

Mätningen JavaScript är inbäddad i din webbsida och du har full kontroll över när du ska börja och sluta använda den. Så länge Traffic Manager-tjänsten tar emot en begäran om att en lista över Azure-regioner ska mätas returneras en uppsättning regioner.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan jag se mätningarna som gjorts av mitt klientprogram som en del av Real User Measurements?

Eftersom mätlogiken körs från klientprogrammet har du full kontroll över vad som händer, inklusive att se svarstidsmätningarna. Traffic Manager rapporterar inte en samlad vy över de mätningar som tagits emot under nyckeln som är kopplad till din prenumeration.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan jag ändra måttskriptet från Traffic Manager?

Medan du har kontroll över vad som är inbäddat på din webbsida, avråder vi dig starkt från att göra några ändringar i mätskriptet för att säkerställa att det mäter och rapporterar svaren korrekt.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Kommer det att vara möjligt för andra att se nyckeln jag använder med riktiga användarmätningar?

När du bäddar in måttskriptet på en webbsida är det möjligt för andra att se skriptet och din RUM-nyckel (Real User Measurements). Men det är viktigt att veta att den här nyckeln skiljer sig från ditt prenumerations-ID och genereras av Traffic Manager som endast ska användas för detta ändamål. Att känna till din RUM-nyckel äventyrar inte säkerheten för ditt Azure-konto.

### <a name="can-others-abuse-my-rum-key"></a>Kan andra missbruka min RUM-nyckel?

Även om det är möjligt för andra att använda din nyckel för att skicka fel information till Azure, kommer några felaktiga mätningar inte att ändra routningen eftersom den beaktas tillsammans med alla andra mätningar vi får. Om du behöver ändra dina nycklar kan du återskapa nyckeln då den gamla nyckeln ignoreras.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Måste jag lägga mätningen JavaScript i alla mina webbsidor?

Real User Measurements ger mer värde i takt med att antalet mätningar ökar. Med detta sagt, det är ditt beslut om huruvida du behöver lägga den i alla dina webbsidor eller ett fåtal utvalda. Vår rekommendation är att börja med att placera den på din mest besökta sida där en användare förväntas stanna på den sidan fem sekunder eller mer.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Kan information om mina slutanvändare identifieras av Traffic Manager om jag använder riktiga användarmätningar?

När det medföljande måttet JavaScript används kommer Traffic Manager att ha insyn i slutanvändarens klient-IP-adress och källans IP-adress för den lokala DNS-matcharen som de använder. Traffic Manager använder klientens IP-adress först efter att ha fått den trunkerad för att inte kunna identifiera den specifika slutanvändaren som skickade mätningarna.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Måste webbsidan som mäter verkliga användarmätningar använda Traffic Manager för routning?

Nej, den behöver inte använda Traffic Manager. Routing sidan av Traffic Manager fungerar separat från Real User Measurement del och även om det är en bra idé att ha dem båda i samma webbegendom, behöver de inte vara.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Måste jag vara värd för någon tjänst i Azure-regioner som ska användas med riktiga användarmätningar?

Nej, du behöver inte vara värd för någon komponent på serversidan på Azure for Real User Measurements för att fungera. Den enda pixelavbildning som hämtas av mätningen JavaScript och tjänsten som kör den i olika Azure-regioner är värd och hanteras av Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Kommer min Azure-bandbreddsanvändning att öka när jag använder riktiga användarmätningar?

Som nämnts i föregående svar ägs och hanteras komponenterna på serversidan i verkliga användarmätningar av Azure. Det innebär att din Azure-bandbreddsanvändning inte ökar eftersom du använder riktiga användarmätningar. Detta inkluderar inte någon bandbreddsanvändning utanför vad Azure debiterar. Vi minimerar bandbredden som används genom att bara hämta en bild av en pixel för att mäta svarstiden till en Azure-region. 

## <a name="traffic-view"></a>Trafikvy

### <a name="what-does-traffic-view-do"></a>Vad gör Traffic View?

Traffic View är en funktion i Traffic Manager som hjälper dig att förstå mer om dina användare och hur deras upplevelse är. Den använder de frågor som tas emot av Traffic Manager och nätverksfördröjningsinformationstabeller som tjänsten underhåller för att ge dig följande:

- De regioner där användarna ansluter till dina slutpunkter i Azure.
- Volymen användare som ansluter från dessa regioner.
- De Azure-regioner som de dirigeras till.
- Deras svarstidsupplevelse till dessa Azure-regioner.

Den här informationen är tillgänglig för dig att använda via geografiska kartöverlägg och tabellvyer i portalen förutom att vara tillgänglig som rådata för dig att ladda ner.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hur kan jag dra nytta av att använda Traffic View?

Traffic View ger dig den övergripande vyn för den trafik som dina Traffic Manager-profiler får. I synnerhet kan den användas för att förstå var din användarbas ansluter från och lika viktigt vad deras genomsnittliga latens erfarenhet är. Du kan sedan använda den här informationen för att hitta områden där du måste fokusera, till exempel genom att utöka ditt Azure-fotavtryck till en region som kan betjäna dessa användare med lägre svarstid. En annan insikt som du kan härleda från att använda Trafikvyn är att se trafikmönstren till olika regioner som i sin tur kan hjälpa dig att fatta beslut om att öka eller minska uppfinna i dessa regioner.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hur skiljer sig Traffic View från Traffic Manager-måtten tillgängliga via Azure-övervakaren?

Azure Monitor kan användas för att förstå på en aggregerad nivå vilken trafik som tas emot av din profil och dess slutpunkter. Du kan också spåra slutpunkternas hälsostatus genom att exponera hälsokontrollresultaten. När du behöver gå längre än dessa och förstå slutanvändarens upplevelse av att ansluta till Azure på regional nivå kan Traffic View användas för att uppnå detta.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Använder Traffic View EDNS-klientundernätsinformation?

DNS-frågor som betjänas av Azure Traffic Manager anser ECS-information för att öka noggrannheten i routningen. Men när du skapar datauppsättningen som visar var användarna ansluter från, använder Traffic View endast IP-adressen för DNS-matcharen.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hur många dagar med data använder Traffic View?

Traffic View skapar utdata genom att bearbeta data från de sju dagar som föregår dagen innan när de visas av dig. Detta är ett rörligt fönster och de senaste uppgifterna kommer att användas varje gång du besöker.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hur hanterar Traffic View externa slutpunkter?

När du använder externa slutpunkter som finns utanför Azure-regioner i en Traffic Manager-profil kan du välja att den ska mappas till en Azure-region som är en proxy för dess svarstidsegenskaper (detta är i själva verket nödvändigt om du använder prestandaroutningsmetod). Om den här Azure-regionmappningen har används den Azure-regionens svarstidsmått när du skapar Traffic View-utdata. Om ingen Azure-region anges kommer svarstidsinformationen att vara tom i data för dessa externa slutpunkter.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Måste jag aktivera Traffic View för varje profil i min prenumeration?

Under förhandsgranskningsperioden aktiverades Traffic View på prenumerationsnivå. Som en del av de förbättringar vi gjorde före den allmänna tillgängligheten kan du nu aktivera Traffic View på profilnivå, så att du kan få mer detaljerad aktivering av den här funktionen. Som standard inaktiveras Traffic View för en profil.

>[!NOTE]
>Om du har aktiverat Traffic View på prenumerationsnivå under förhandsgranskningstiden måste du nu återaktivera den för var och en av profilen under den prenumerationen.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hur stänger jag av Traffic View?

Du kan inaktivera Trafikvyn för alla profiler som använder portal- eller REST-API:et. 

### <a name="how-does-traffic-view-billing-work"></a>Hur fungerar Traffic View-fakturering?

Traffic View-prissättning baseras på antalet datapunkter som används för att skapa utdata. För närvarande är den enda datatyp som stöds de frågor som din profil tar emot. Dessutom debiteras du bara för den bearbetning som gjordes när du har aktiverat Traffic View. Det innebär att om du aktiverar Traffic View under en viss tidsperiod i en månad och inaktiverar den under andra tider, räknas bara de datapunkter som bearbetas medan du hade funktionen aktiverad mot din faktura.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan jag använda Traffic Manager med slutpunkter från flera prenumerationer?

Det går inte att använda slutpunkter från flera prenumerationer med Azure Web Apps. Azure Web Apps kräver att alla anpassade domännamn som används med Web Apps endast används i en enda prenumeration. Det går inte att använda Webbappar från flera prenumerationer med samma domännamn.

För andra slutpunktstyper är det möjligt att använda Traffic Manager med slutpunkter från mer än en prenumeration. I Resource Manager kan slutpunkter från valfri prenumeration läggas till i Traffic Manager, så länge som den person som konfigurerar Traffic Manager-profilen har läsbehörighet till slutpunkten. Dessa behörigheter kan beviljas med hjälp av [Azure Resource Manager rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan jag använda Traffic Manager med Cloud Service 'Staging'-platser?

Ja. Cloud Service 'staging' slots kan konfigureras i Traffic Manager som externa slutpunkter. Hälsokontroller debiteras fortfarande med Azure-slutpunkter.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Stöder Traffic Manager IPv6-slutpunkter?

Traffic Manager tillhandahåller för närvarande inte IPv6-adresserbara namnservrar. Traffic Manager kan dock fortfarande användas av IPv6-klienter som ansluter till IPv6-slutpunkter. En klient gör inte DNS-begäranden direkt till Traffic Manager. I stället använder klienten en rekursiv DNS-tjänst. En IPv6-klient skickar begäranden till den rekursiva DNS-tjänsten via IPv6. Då bör den rekursiva tjänsten kunna kontakta Traffic Manager-namnservrarna med IPv4.

Traffic Manager svarar med slutpunktens DNS-namn eller IP-adress. För att stödja en IPv6-slutpunkt finns det två alternativ. Du kan lägga till slutpunkten som ett DNS-namn som har en associerad AAAA-post och Traffic Manager kommer att hälsokontrollera slutpunkten och returnera den som en CNAME-posttyp i frågesvaret. Du kan också lägga till slutpunkten direkt med IPv6-adressen och Traffic Manager returnerar en AAAA-typpost i frågesvaret.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan jag använda Traffic Manager med mer än en webbapp i samma region?

Vanligtvis används Traffic Manager för att dirigera trafik till program som distribueras i olika regioner. Det kan dock också användas där ett program har mer än en distribution i samma region. Traffic Manager Azure-slutpunkter tillåter inte att mer än en Web App-slutpunkt från samma Azure-region läggs till i samma Traffic Manager-profil.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hur flyttar jag min Traffic Manager-profils Azure-slutpunkter till en annan resursgrupp eller prenumeration?

Azure-slutpunkter som är associerade med en Traffic Manager-profil spåras med hjälp av sina resurs-ID:er. När en Azure-resurs som används som en slutpunkt (till exempel Offentlig IP, Klassisk molntjänst, WebApp eller en annan Traffic Manager-profil som används på ett kapslat sätt) flyttas till en annan resursgrupp eller prenumeration, ändras dess resurs-ID. I det här fallet måste du för närvarande uppdatera Traffic Manager-profilen genom att först ta bort och sedan lägga tillbaka slutpunkterna i profilen.

## <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning för Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Är Traffic Manager motståndskraftigt mot Azure-regionfel?

Traffic Manager är en viktig komponent i leveransen av program med högtillgänglig information i Azure.
För att ge hög tillgänglighet måste Traffic Manager ha en exceptionellt hög tillgänglighetsnivå och vara motståndskraftig mot regionala fel.

Som design är Traffic Manager-komponenter motståndskraftiga mot ett fullständigt fel i alla Azure-regioner. Den här återhämtningen gäller för alla Traffic Manager-komponenter: DNS-namnservrarna, API:et, lagringslagret och slutpunktsövervakningstjänsten.

I den osannolika händelsen av ett avbrott i en hel Azure-region förväntas Traffic Manager fortsätta att fungera normalt. Program som distribueras i flera Azure-regioner kan förlita sig på Traffic Manager för att dirigera trafik till en tillgänglig instans av deras program.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hur påverkar valet av resursgruppsplats Traffic Manager?

Traffic Manager är en enda global tjänst. Det är inte regionalt. Valet av resursgruppsplats gör ingen skillnad för Traffic Manager-profiler som distribueras i den resursgruppen.

Azure Resource Manager kräver att alla resursgrupper anger en plats som bestämmer standardplatsen för resurser som distribueras i resursgruppen. När du skapar en Traffic Manager-profil skapas den i en resursgrupp. Alla Traffic Manager-profiler använder **globalt** som sin plats och åsidosätter resursgruppens standardinställning.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hur avgör jag den aktuella hälsan för varje slutpunkt?

Den aktuella övervakningsstatusen för varje slutpunkt, förutom den övergripande profilen, visas i Azure-portalen. Den här informationen är också tillgänglig via REST [API](https://msdn.microsoft.com/library/azure/mt163667.aspx)FÖR trafikövervakaren, [PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)och [Azure CLI över flera plattformar.](../cli-install-nodejs.md)

Du kan också använda Azure Monitor för att spåra hälsotillståndet för dina slutpunkter och se en visuell representation av dem. Mer information om hur du använder Azure Monitor finns i [Azure Monitoring-dokumentationen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Kan jag övervaka HTTPS-slutpunkter?

Ja. Traffic Manager stöder sondering via HTTPS. Konfigurera **HTTPS** som protokoll i övervakningskonfigurationen.

Traffic manager kan inte tillhandahålla någon certifikatvalidering, inklusive:

* Certifikat på serversidan har inte validerats
* SNI-certifikat på serversidan har inte validerats
* Klientcertifikat stöds inte

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Använder jag en IP-adress eller ett DNS-namn när jag lägger till en slutpunkt?

Traffic Manager stöder att lägga till slutpunkter på tre sätt att referera dem – som ett DNS-namn, som en IPv4-adress och som en IPv6-adress. Om slutpunkten läggs till som en IPv4- eller IPv6-adress kommer frågesvaret att vara av posttyp A respektive AAAA. Om slutpunkten lades till som ett DNS-namn kommer frågesvaret att vara av posttyp CNAME. Det är bara tillåtet att lägga till slutpunkter som IPv4- eller IPv6-adress om slutpunkten är av typen **Extern**.
Alla routningsmetoder och övervakningsinställningar stöds av de tre slutpunktsadresseringstyperna.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Vilka typer av IP-adresser kan jag använda när jag lägger till en slutpunkt?

Med Traffic Manager kan du använda IPv4- eller IPv6-adresser för att ange slutpunkter. Det finns några begränsningar som anges nedan:

- Adresser som motsvarar reserverade privata IP-adressutrymmen är inte tillåtna. Dessa adresser inkluderar de som kallas ut i RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 och RFC 5771
- Adressen får inte innehålla några portnummer (du kan ange vilka portar som ska användas i profilkonfigurationsinställningarna)
- Inga två slutpunkter i samma profil kan ha samma mål-IP-adress

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan jag använda olika slutpunktsadresseringstyper i en enda profil?

Nej, Traffic Manager tillåter inte att du blandar slutpunktsadresseringstyper i en profil, förutom när det gäller en profil med multivalue-routningstyp där du kan blanda IPv4- och IPv6-adresstyper

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Vad händer när en inkommande frågas posttyp skiljer sig från den posttyp som är associerad med slutpunkternas adresstyp?

När en fråga tas emot mot en profil hittar Traffic Manager först slutpunkten som måste returneras enligt den angivna routningsmetoden och slutpunkternas hälsostatus. Den tittar sedan på den posttyp som begärs i den inkommande frågan och den posttyp som är associerad med slutpunkten innan du returnerar ett svar baserat på tabellen nedan.

För profiler med annan routningsmetod än MultiValue:

|Begäran om inkommande fråga|     Typ av slutpunkt|     Svar tillhandahålls|
|--|--|--|
|Någon |    A / AAAA / CNAME |    Slutpunkt för mål| 
|A |    A / CNAME |    Slutpunkt för mål|
|A |    AAAA |    NODATA (NODATA) |
|AAAA |    AAAA / CNAME |    Slutpunkt för mål|
|AAAA |    A |    NODATA (NODATA) |
|CNAME |    CNAME |    Slutpunkt för mål|
|CNAME     |A / AAAA |    NODATA (NODATA) |
|

För profiler med routningsmetod inställd på MultiValue:

|Begäran om inkommande fråga|     Typ av slutpunkt |    Svar tillhandahålls|
|--|--|--|
|Någon |    Blandning av A och AAAA |    Målslutpunkter|
|A |    Blandning av A och AAAA |    Endast målslutpunkter av typ A|
|AAAA    |Blandning av A och AAAA|     Endast målslutpunkter av typen AAAA|
|CNAME |    Blandning av A och AAAA |    NODATA (NODATA) |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan jag använda en profil med IPv4 / IPv6-adresserade slutpunkter i en kapslad profil?

Ja, du kan med undantag för att en profil av typen MultiValue inte kan vara en överordnad profil i en kapslad profiluppsättning.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Jag stoppade en slutpunkt för webbprogram i min Traffic Manager-profil men jag får ingen trafik även efter att jag startat om den. Hur kan jag åtgärda detta?

När en slutpunkt för Azure-webbprogram stoppas slutar Traffic Manager kontrollera dess hälsa och startar om hälsokontrollerna först efter att den upptäcker att slutpunkten har startats om. Om du vill förhindra den här fördröjningen inaktiverar och kan du ändra slutpunkten i Traffic Manager-profilen när du har startat om slutpunkten.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan jag använda Traffic Manager även om mitt program inte har stöd för HTTP eller HTTPS?

Ja. Du kan ange TCP som övervakningsprotokoll och Traffic Manager kan initiera en TCP-anslutning och vänta på ett svar från slutpunkten. Om slutpunkten svarar på anslutningsbegäran med ett svar för att upprätta anslutningen, inom tidsgränsen, markeras slutpunkten som felfri.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Vilka specifika svar krävs från slutpunkten när du använder TCP-övervakning?

När TCP-övervakning används startar Traffic Manager ett trevägs TCP-handslag genom att skicka en SYN-begäran till slutpunkten vid den angivna porten. Den väntar sedan på ett SYN-ACK-svar från slutpunkten under en tidsperiod (anges i timeout-inställningarna).

- Om ett SYN-ACK-svar tas emot inom den timeout-period som anges i övervakningsinställningarna, anses slutpunkten vara felfri. En FIN eller FIN-ACK är det förväntade svaret från Trafikhanteraren när den regelbundet avslutar en socket.
- Om ett SYN-ACK-svar tas emot efter den angivna timeouten svarar Trafikhanteraren med en RST för att återställa anslutningen.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hur snabbt flyttar Traffic Manager mina användare bort från en felaktig slutpunkt?

Traffic Manager innehåller flera inställningar som kan hjälpa dig att styra redundansbeteendet för traffic manager-profilen på följande sätt:

- Du kan ange att Traffic Manager avsöker slutpunkterna oftare genom att ställa in avsökningsintervallet med 10 sekunder. Detta säkerställer att alla slutpunkter som går fel kan identifieras så snart som möjligt. 
- Du kan ange hur länge du ska vänta innan en hälsokontrollbegäran har utfärdats (minsta time out-värde är 5 sek).
- Du kan ange hur många fel som kan uppstå innan slutpunkten markeras som fel. Det här värdet kan vara lågt som 0, i vilket fall slutpunkten markeras som fel så snart den misslyckas den första hälsokontrollen. Om du använder det lägsta värdet 0 för det tolererade antalet fel kan det dock leda till att slutpunkter tas ur rotation på grund av eventuella tillfälliga problem som kan uppstå vid tidpunkten för avböd.
- Du kan ange tid att leva (TTL) för DNS-svaret ska vara så lågt som 0. Detta innebär att DNS-resolvers inte kan cachelagra svaret och varje ny fråga får ett svar som innehåller den senaste hälsoinformationen som Traffic Manager har.

Genom att använda dessa inställningar kan Traffic Manager tillhandahålla redundans under 10 sekunder efter att en slutpunkt blir felaktig och en DNS-fråga görs mot motsvarande profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hur kan jag ange olika övervakningsinställningar för olika slutpunkter i en profil?

Trafikhanterarens övervakningsinställningar ligger på en nivå per profil. Om du behöver använda en annan övervakningsinställning för endast en slutpunkt kan den göras genom att ha slutpunkten som en [kapslad profil](traffic-manager-nested-profiles.md) vars övervakningsinställningar skiljer sig från den överordnade profilen.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hur tilldelar jag HTTP-huvuden till Hälsokontrollerna i Traffic Manager till slutpunkterna?

Med Traffic Manager kan du ange anpassade huvuden i hälsokontrollerna HTTP(S) som initieras till slutpunkterna. Om du vill ange ett anpassat huvud kan du göra det på profilnivå (gäller för alla slutpunkter) eller ange det på slutpunktsnivå. Om ett huvud definieras på båda nivåerna åsidosätter det som anges på slutpunktsnivå profilnivån ett.
Ett vanligt användningsfall för detta är att ange värdhuvuden så att Traffic Manager-begäranden kan dirigeras korrekt till en slutpunkt som finns i en miljö med flera innehavare. Ett annat användningsfall av detta är att identifiera Traffic Manager-begäranden från en slutpunkts HTTP-begäran om begäran loggar

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Vad används hälsokontroller av slutpunkter?

Om ingen anpassad värdhuvudinställning anges är värdhuvudet som används av Traffic Manager DNS-namnet på slutpunktsmålet som konfigurerats i profilen, om det är tillgängligt.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Vilka IP-adresser kommer hälsokontrollerna från?

Klicka [här](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) om du vill visa JSON-filen som visar de IP-adresser som Traffic Manager-hälsokontroller kan komma från. Granska IP-adresserna som anges i JSON-filen för att säkerställa att inkommande anslutningar från dessa IP-adresser tillåts vid slutpunkterna för att kontrollera dess hälsostatus.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hur många hälsokontroller till min slutpunkt kan jag förvänta mig av Traffic Manager?

Antalet hälsokontroller av Traffic Manager som når slutpunkten beror på följande:

- det värde som du har angett för övervakningsintervallet (mindre intervall innebär att fler begäranden landar på slutpunkten under en viss tidsperiod).
- Antalet platser där hälsokontrollerna kommer (IP-adresserna där du kan förvänta dig att dessa kontroller visas i föregående vanliga frågor och svar).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hur får jag ett meddelande om en av mina slutpunkter går ner?

Ett av måtten som tillhandahålls av Traffic Manager är hälsostatus för slutpunkter i en profil. Du kan se detta som en mängd av alla slutpunkter i en profil (till exempel är 75 % av slutpunkterna felfria) eller på en slutpunktsnivå. Traffic Manager-mått exponeras via Azure Monitor och du kan använda dess [aviseringsfunktioner](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) för att få meddelanden när slutpunktens hälsostatus ändras. Mer information finns i [Traffic Manager-mått och aviseringar](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager kapslade profiler

### <a name="how-do-i-configure-nested-profiles"></a>Hur konfigurerar jag kapslade profiler?

Kapslade Traffic Manager-profiler kan konfigureras med både Azure Resource Manager och de klassiska Azure REST-API:erna, Azure PowerShell-cmdlets och Azure CLI-kommandon över flera plattformar. De stöds också via den nya Azure-portalen.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hur många lager häckar stöder Traffic Manger?

Du kan kapsla profiler upp till 10 nivåer djup. "Loopar" är inte tillåtna.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan jag blanda andra slutpunktstyper med kapslade underordnade profiler i samma Traffic Manager-profil?

Ja. Det finns inga begränsningar för hur du kombinerar slutpunkter av olika typer i en profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hur gäller faktureringsmodellen för Kapslade profiler?

Det finns ingen negativ prispåverkan av att använda kapslade profiler.

Traffic Manager-fakturering har två komponenter: hälsokontroller av slutpunkter och miljontals DNS-frågor

* Hälsokontroller av slutpunkter: Det finns ingen avgift för en underordnad profil när den konfigureras som en slutpunkt i en överordnad profil. Övervakning av slutpunkterna i den underordnade profilen faktureras på vanligt sätt.
* DNS-frågor: Varje fråga räknas bara en gång. En fråga mot en överordnad profil som returnerar en slutpunkt från en underordnad profil räknas endast mot den överordnade profilen.

Fullständig information finns på [prissidan för Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Finns det en prestandapåverkan för kapslade profiler?

Nej. Det finns ingen prestandapåverkan när du använder kapslade profiler.

Traffic Manager-namnservrarna går igenom profilhierarkin internt när varje DNS-fråga bearbetas. En DNS-fråga till en överordnad profil kan ta emot ett DNS-svar med en slutpunkt från en underordnad profil. En enda CNAME-post används oavsett om du använder en enskild profil eller kapslade profiler. Du behöver inte skapa en CNAME-post för varje profil i hierarkin.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hur beräknar Traffic Manager hälsotillståndet för en kapslad slutpunkt i en överordnad profil?

Den överordnade profilen utför inte hälsokontroller på barnet direkt. I stället används hälsotillståndet för den underordnade profilens slutpunkter för att beräkna den allmänna hälsan för den underordnade profilen. Den här informationen sprids upp den kapslade profilhierarkin för att fastställa hälsotillståndet för den kapslade slutpunkten. Den överordnade profilen använder den här aggregerade hälsotillståndet för att avgöra om trafiken kan dirigeras till den underordnade.

I följande tabell beskrivs beteendet för Hälsokontroller av Traffic Manager för en kapslad slutpunkt.

| Status för övervakare för underordnad profil | Status för överordnad slutpunktsövervakare | Anteckningar |
| --- | --- | --- |
| Inaktiverat Den underordnade profilen har inaktiverats. |Stoppad |Det överordnade slutpunktstillståndet är stoppat, inte inaktiverat. Tillståndet Inaktiverat är reserverat för att ange att du har inaktiverat slutpunkten i den överordnade profilen. |
| Försämras. Minst en underordnad profilslutpunkt är i ett försämrat tillstånd. |Online: Antalet onlineslutpunkter i den underordnade profilen är minst värdet för MinChildEndpoints.<BR>KontrollEndpoint: Antalet slutpunkter online plus CheckingEndpoint i den underordnade profilen är minst värdet för MinChildEndpoints.<BR>Degraderad: annars. |Trafiken dirigeras till en slutpunkt för status CheckingEndpoint. Om MinChildEndpoints är för högt försämras slutpunkten alltid. |
| Online. Minst en underordnad profilslutpunkt är ett onlinetillstånd. Ingen slutpunkt är i tillståndet Försämrad. |Se ovan. | |
| CheckEndpoints. Minst en underordnad profilslutpunkt är "CheckingEndpoint". Inga slutpunkter är "Online" eller "Degraderad" |Samma som ovan. | |
| Inaktiva. Alla underordnade profilslutpunkter är inaktiverade eller stoppade, eller så har den här profilen inga slutpunkter. |Stoppad | |

## <a name="next-steps"></a>Nästa steg:

- Läs mer om [slutpunktsövervakning i](../traffic-manager/traffic-manager-monitoring.md)Traffic Manager och automatisk redundans .
- Läs mer om [trafikstyrningsmetoder i](../traffic-manager/traffic-manager-routing-methods.md)Traffic Manager .
