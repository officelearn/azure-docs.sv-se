---
title: Med Azure Traffic Manager – vanliga frågor och svar
description: Den här artikeln innehåller svar på vanliga frågor och svar om Traffic Manager
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: kumud
ms.openlocfilehash: a6a8fee942edf4cec98a6d2f46eb2f63b7595c09
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200055"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Vanliga frågor (och svar FAQ) om Traffic Manager

## <a name="traffic-manager-basics"></a>Traffic Manager-grunderna

### <a name="what-ip-address-does-traffic-manager-use"></a>Vilken IP-adress använder Traffic Manager?

Enligt beskrivningen i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. Skickar den DNS-svar för att dirigera klienterna till lämplig tjänsteslutpunkt. Klienterna ansluter därefter till tjänsteslutpunkt direkt, inte via Traffic-Manager.

Traffic Manager tillhandahåller därför inte en slutpunkt eller en IP-adress för klienter att ansluta till. Om du vill statisk IP-adress för din tjänst, som måste konfigureras på tjänsten, inte i Traffic-Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Vilka typer av trafik kan vara dirigeras med Traffic Manager?
Enligt beskrivningen i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), en Traffic Manager-slutpunkt kan vara valfri tjänst som finns i och utanför Azure som riktas mot internet. Därför kan Traffic Manager dirigera trafik från det offentliga internet till en uppsättning slutpunkter som är också internetuppkopplade. Om du har slutpunkter som är i ett privat nätverk (till exempel en intern version av [Azure Load Balancer](../load-balancer/load-balancer-overview.md#internalloadbalancer)) eller har användare som DNS-förfrågningar från sådana interna nätverk, kan du inte använda Traffic Manager för att dirigera trafiken.


### <a name="does-traffic-manager-support-sticky-sessions"></a>Stöder ' fästsessioner ”i Traffic Manager?

Enligt beskrivningen i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. DNS-svar används för att dirigera klienterna till lämplig tjänsteslutpunkt. Klienterna ansluter till tjänsteslutpunkt direkt, inte via Traffic-Manager. Traffic Manager finns därför inte HTTP-trafik mellan klienten och servern.

Dessutom tillhör källans IP-adress för DNS-frågan som tagits emot av Traffic Manager rekursiv DNS-tjänsten, inte klienten. Därför finns inget sätt att spåra enskilda klienter Traffic Manager och inte kan implementera ' fästsessioner '. Den här begränsningen är gemensam för alla DNS-baserad trafik hanteringssystem och är inte specifikt till Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Varför ser jag ett HTTP-fel när du använder Traffic Manager?

Enligt beskrivningen i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. DNS-svar används för att dirigera klienterna till lämplig tjänsteslutpunkt. Klienterna ansluter därefter till tjänsteslutpunkt direkt, inte via Traffic-Manager. Traffic Manager gör inte se HTTP-trafik mellan klienten och servern. Därför måste alla HTTP-fel som du ser kommer från ditt program. För att klienten ska ansluta till programmet, har alla DNS-Lösningssteg slutförts. Det omfattar en interaktion med Traffic Manager på trafikflöde program.

Ytterligare utredning bör därför fokusera på programmet.

HTTP-rubriken skickas från klientens webbläsare är den vanligaste orsaken problem. Se till att programmet är konfigurerad för att acceptera rätt Värdrubriken för domännamnet som du använder. Slutpunkter med Azure App Service, se [konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Vad är prestandapåverkan från återställning av med Traffic Manager?

Enligt beskrivningen i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. Eftersom klienterna ansluter direkt till dina tjänstslutpunkter kan påverkas inte prestanda när med Traffic Manager när anslutningen har upprättats.

Eftersom Traffic Manager kan integreras med program på DNS-nivå, krävs det en ytterligare DNS-sökning som ska infogas i kedjan för DNS-matchning. Effekten av Traffic Manager på DNS-matchningstid är minimal. Traffic Manager använder ett globalt nätverk med namnservrar och använder [anycast](https://en.wikipedia.org/wiki/Anycast) nätverk för att säkerställa DNS frågor alltid dirigeras till närmaste tillgängliga namnservern. Cachelagring av DNS-svar innebär också att ytterligare DNS-latensen med hjälp av Traffic Manager gäller endast en bråkdel av sessioner.

Metoden prestanda dirigerar trafik till närmaste tillgängliga slutpunkten. Resultatet är att den övergripande prestandapåverkan som är associerade med den här metoden bör vara minimal. En ökning i DNS-latens bör avvika med lägre nätverksfördröjning till slutpunkten.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Vilka programprotokoll kan jag använda med Traffic Manager?

Enligt beskrivningen i [hur Traffic Manager fungerar](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager fungerar på DNS-nivå. När DNS-sökningen är klar kan ansluter klienterna till slutpunkten programmet direkt, inte via Traffic-Manager. Anslutningen kan därför använda alla protokoll. Om du väljer TCP som det övervakning-protokollet, Traffic Manager endpoint hälsoövervakning kan göras utan att använda alla programprotokoll. Om du vill ha hälsotillståndet verifieras med ett programprotokoll måste slutpunkten för att kunna svara på begäranden för HTTP eller HTTPS hämta.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan jag använda Traffic Manager med ett ”utan www-domännamn?

Nej. DNS-standarden tillåter inte CNAME-poster att finnas tillsammans med andra DNS-poster med samma namn. En DNS-zon apex (eller rot) innehåller alltid två befintliga DNS-poster; SOA- och auktoritativa NS-poster. Det innebär att inte går att skapa en CNAME-posten i basdomänen utan brott mot DNS-standarden.

Traffic Manager kräver en DNS CNAME-post för att mappa anpassad DNS-namn. Exempelvis kan du mappa `www.contoso.com` till DNS-namnet för Traffic Manager profilen `contoso.trafficmanager.net`. Traffic Manager-profilen returnerar dessutom en andra DNS CNAME för att ange vilken slutpunkt som klienten ska ansluta till.

Om du vill undvika det här problemet bör du använda en HTTP-omdirigering därigenom dirigera trafik från domän utan www-namn till en annan URL, som sedan kan använda Traffic Manager. Domän utan www ”contoso.com” kan till exempel omdirigera användare till CNAME ”www.contoso.com” som pekar på Traffic Manager DNS-namn.

Fullständigt stöd för utan www domäner i Traffic Manager spåras i våra funktionen återstående uppgifter. Du kan registrera upp som stöd för den här funktionsbegäran från [röstat på vår webbplats för community-feedback](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager betraktar undernätsadress klienten vid hantering av DNS-frågor? 
Ja, förutom källans IP-adress för DNS-fråga tas emot (som vanligtvis är IP-adressen för DNS-matchning), när du utför sökningar för routningsmetoder för geografisk, prestanda och undernät, traffic manager också tar hänsyn till undernätet klientadressen om den ingår i frågan genom att matcharen gör begäran åt användaren.  
Mer specifikt [RFC 7871 – klientnät i DNS-frågor](https://tools.ietf.org/html/rfc7871) som ger en [Tilläggsmekanism för DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) som kan överföra på undernätet klientadressen från matchare som stöder den.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Vad är TTL för DNS och hur påverkar det Mina användare?

När en DNS-fråga hamnar i Traffic Manager, anger ett värde i svaret kallas time to live (TTL). Det här värdet, vars enheten är i sekunder, anger hur lång tid att DNS-matchare nedströms på att cachelagra svaret. När DNS-matchare inte garanteras att cachelagra resultatet, gör cachelagra det enkelt att svara på eventuella efterföljande frågor av cacheminnet för istället för att gå till Traffic Manager DNS-servrar. Detta påverkar svaren på följande sätt:
- en högre TTL minskar antalet frågor som hamnar på Traffic Manager DNS-servrar, som kan minska kostnaden för en kund eftersom antalet frågor som hanteras är en fakturerbar förbrukning.
- en högre TTL kan minska den tid det tar för att göra en DNS-sökning.
- en högre TTL innebär också att dina data inte återspeglar den senaste hälsoinformation som Traffic Manager har hämtats via dess avsöknings agenter.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hur hög eller låg kan jag ställa in TTL-värdet för Traffic Manager svar?

Du kan ange vid en per-profilnivå TTL för DNS vara så lågt som 0 sekunder och så mycket som 2 147 483 647 sekunder (maximalt intervall som är kompatibla med [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). TTL-värdet 0 innebär att den underordnade DNS-matchare cachelagrar inte svar på frågor och alla frågor som förväntas att nå Traffic Manager DNS-servrar för matchning.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hur kan jag är införstådd med mängden frågor kommer till min profil? 
En av mått som tillhandahålls av Traffic Manager är antalet frågor som svarade med en profil. Du kan hämta den här informationen en profil på aggregering eller du kan dela upp den ytterligare till Se mängden frågor där slutpunkter som returnerades. Du kan också ställa in aviseringar för att meddela dig om svarsvolym frågan överskrider de villkor som du har angett. Mer information [Traffic Manager-mått och aviseringar](traffic-manager-metrics-alerts.md).

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Metod för Traffic Manager geografisk trafikroutning

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Vilka är några användningsområden där geografisk routning är användbart? 
Geografisk routningstyp kan användas i alla scenarier där Azure-kund måste skilja sina användare baserat på geografiska regioner. Till exempel kan använder geografisk trafikroutningsmetod, du ge användare från specifika regioner en annan användarupplevelse än de från andra regioner. Ett annat exempel uppfyller lokala data datasuveränitet regelverk som kräver att användare från en viss region betjänas av slutpunkterna i den regionen.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hur avgör jag om jag ska använda routningsmetod för prestanda eller geografisk routningsmetod? 
Den stora skillnaden mellan dessa två metoder för populära routning är att i metoden primära målet är att skicka trafik till den slutpunkt som kan ge lägsta möjliga svarstid för anroparen geografisk routning Huvudsyftet är att genomdriva ett geografiskt område Tidsgränsen för dina anropare så att du kan testa appskyddsbegränsningarna vidarebefordra dem till en viss slutpunkt. Överlappande sker eftersom det inte finns en korrelation mellan geografiskt nära och kortare svarstider, även om detta inte stämmer. Det kan finnas en slutpunkt i ett annat geografiskt område som ger en bättre upplevelse för svarstid för anroparen och i så fall prestanda routning skickas användaren till denna slutpunkt men geografisk routning kommer alltid att skicka dem till slutpunkten som du har mappat för sina geografiskt område. För att ytterligare gör det Rensa bör du överväga att följande exempel - med geografisk Se Routning du ovanliga mappningar som skickar all trafik från Asien till slutpunkter i USA och alla amerikanska trafik till slutpunkter i Asien. I så fall geografisk routning avsiktligt gör exakt vad du har konfigurerat den ska och optimera prestanda är inte en faktor. 
>[!NOTE]
>Det kan finnas scenarier där du kan behöva både prestanda och geografiska routningsfunktioner för dessa scenarier kapslade profiler kan vara bra alternativ. Du kan till exempel ställer in en överordnad med geografisk routning där du skickar all trafik från Nordamerika en kapslad profil med slutpunkter i USA och använder prestanda routning för att skicka dessa trafik till den bästa slutpunkten i uppsättningen. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Vilka är de regioner som stöds av Traffic Manager för geografisk routning för? 
Hierarki för land/region som används av Traffic Manager finns [här](traffic-manager-geographic-regions.md). Även om den här sidan hålls uppdaterad med de eventuella ändringar, du kan också hämta samma information med hjälp av den [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hur traffic manager kontrollerar där en användare är frågor från? 
Traffic Manager tittar på käll-IP för frågan (det troligen är en lokal DNS-matchare gör frågor efter användarens räkning) och använder en intern IP-adress till regionskarta för att avgöra var. Den här kartan har uppdaterats med jämna mellanrum att hänsyn till ändringar på internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Är det säkert att Traffic Manager korrekt kan bestämma den exakta geografiska platsen för användaren i alla fall?
Nej, Traffic Manager kan inte garantera att den geografiska region som vi härleda från käll-IP-adressen för en DNS-fråga alltid kommer motsvarar användarens plats på grund av följande orsaker: 

- Först, enligt beskrivningen i föregående vanliga frågor och svar, källans IP-adress som ser vi är som en DNS-matchare gör lookup användarens räkning. Den geografiska platsen för DNS-matchning är en bra proxy för den geografiska platsen för användaren, kan det också vara olika beroende på storleken för DNS-matchare tjänsten och den specifika DNS-matchare tjänsten som en kund har valt att använda. Exempelvis kan en kund i Malaysia i enhetens inställningar att ange en DNS-matchare tjänst vars DNS-servern i Singapore kan få ut för att hantera fråga lösningar för den användare/enheten. Traffic Manager kan i så fall kan bara se det angavs vilken IP-adress som motsvarar den Singapore platsen. Se även tidigare vanliga frågor och svar om klientstöd undernät-adressen på den här sidan.

- Traffic Manager använder dessutom en intern karta göra geografiskt område translation IP-adressen. Den här kartan verifieras och uppdateras kontinuerligt att öka noggrannheten för dess och ta hänsyn till den växande natur internet, men det finns fortfarande möjligheten att vår information inte är en exakt kopia av den geografiska platsen för alla IP-Adressen adresser.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Behöver en slutpunkt fysiskt finnas i samma region som den är konfigurerad med för geografisk routning för? 
Nej, platsen för slutpunkten medför inga begränsningar som regioner kan mappas till den. En slutpunkt i USA, Central Azure-region kan exempelvis ha alla användare från Indien dirigeras till den.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan jag tilldela geografiska områden till slutpunkter i en profil som inte har konfigurerats för att göra geografisk Routning? 

Ja, om det inte geografisk routningsmetod för en profil, du kan använda den [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) tilldela geografiska områden till slutpunkter i profilen. När det gäller icke-geografiska routning typ profiler ignoreras den här konfigurationen. Om du ändrar sådana en profil till geografiska routningstyp vid ett senare tillfälle, kan Traffic Manager använda dessa mappningar.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Varför får jag ett fel när jag försöker ändra routningsmetod för en befintlig profil till geografisk?

Alla slutpunkter i en profil med geografisk routning måste ha minst en region som mappats till den. Om du vill konvertera en befintlig profil till geografisk routning, måste du först associera geografiska områden till dess slutpunkter med hjälp av den [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) innan du ändrar routningstyp till geografiska. Om du använder portalen först tar bort slutpunkterna, ändra routningsmetod för profilen till geografiska och sedan lägga till slutpunkter tillsammans med deras geografiska region-mappning. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Varför det rekommenderas starkt att kunder skapar kapslade profiler i stället för slutpunkter i en profil med geografisk routning aktiverad? 

En region kan tilldelas till endast en slutpunkt i en profil om det använder geografisk routningsmetod. Om slutpunkten inte är en kapslad typ med en underordnad-profil som är kopplat till den, om att slutpunkten kommer skadade, Traffic Manager fortsätter att skicka trafik till den sedan med alternativet att inte skicka all trafik som inte är alla bättre. Traffic Manager tillåter inte att redundansväxla till en annan slutpunkt, även om den region som tilldelats är ”överordnad” regionen tilldelade till den slutpunkt som gått feltillstånd (till exempel om en slutpunkt som har region Spanien går skadade, vi gör inte att redundansväxla till en annan slutpunkt som har den regionen Europa tilldelade). Detta görs för att säkerställa att Traffic Manager respekterar geografiska gränser att en kund har konfigurerat i sina profiler. För att få fördelen med växling till en annan slutpunkt när en slutpunkt blir ohälsosamt kan rekommenderas det att geografiska regioner tilldelas till kapslade profiler med flera slutpunkter i den i stället för enskilda slutpunkter. På så vis kan trafik redundansväxling till en annan slutpunkt i samma profil i kapslade underordnade om en slutpunkt i profilen för kapslade underordnade misslyckas.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Finns det några restriktioner för API-version som stöder den här routningstyp?

Ja, endast API-version 2017-03-01 och senare har stöd för geografisk routning skriver. Alla äldre API-versioner kan inte användas för att skapade profiler av typen för geografisk routning eller tilldela geografiska områden till slutpunkter. Om en äldre API-version används för att hämta profiler från en Azure-prenumeration, returneras inte någon profil för geografisk routningstyp. Dessutom, när du använder äldre API-versioner, returneras alla profiler som har slutpunkter med en tilldelning av geografiskt område, har inte dess tilldelning för geografiska region som visas.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager undernät trafikroutningsmetod

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Vilka är några användningsområden där undernätet routning är användbart?
Undernät routning kan du skilja den upplevelse du levererar för specifika uppsättningar med användare som identifieras av käll-IP för DNS-begäranden IP-adresser. Ett exempel är som visar olika innehåll om användare ansluter till en webbplats från ditt företags HQ. En annan skulle vara att begränsa användare från vissa Internetleverantörer endast åtkomst till slutpunkter som stöder endast IPv4-anslutningar om dessa Internetleverantörer har icke nominellt prestanda när IPv6 används.
Ett annat skäl att använda undernät routningsmetod anges tillsammans med andra profiler i en kapslad profil. Om du vill använda geografisk routningsmetod för geografiska avgränsningar användarna, men du vill göra en annan routningsmetod för en specifik Internet-leverantör, du exempelvis har en profil withy undernät routningsmetod som den överordnade profilen och åsidosätta Internet-Leverantören om du vill använda en viss underordnad pro fil- och ha standard geografiska profil för alla andra.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hur kan IP-adressen för användaren i Traffic Manager?
Slutanvändarenheter använder vanligtvis en DNS-matchare för att göra DNS-sökning för deras räkning. Utgående IP-Adressen för sådana matchare är Traffic Manager ser som käll-IP. Dessutom verkar undernät routningsmetod även om du vill se om det finns information som EDNS0 utökade klienten undernät (ECS) som skickades med begäran. Om ECS finns språkinformation, är den adress som används för att bestämma routningen. Käll-IP för frågan används inte av ECS information är för routningen.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hur kan jag ange IP-adresser när du använder undernätet Routning?
IP-adresser ska associeras med en slutpunkt kan anges på två sätt. Du kan först använda fyra punktavgränsad decimalform oktett-notation med en start- och slut-adresser för att ange intervallet (till exempel 1.2.3.4-5.6.7.8 eller 3.4.5.6-3.4.5.6). Dessutom kan du använda CIDR-notation ange intervallet (till exempel 1.2.3.0/24). Du kan ange flera intervall och kan använda båda typerna av notering i en rad. Några begränsningar gäller.
-   Du kan inte ha överlappande adressintervall eftersom varje IP måste mappas till endast en enda slutpunkt
-   Startadressen får inte innehålla fler än slutadressen
-   När det gäller CIDR-notering, IP-adressen innan den '/' ska vara startadressen av det intervallet (till exempel 1.2.3.0/24 är giltig men 1.2.3.4.4/24 är inte giltig)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hur kan jag ange en återställningsplats slutpunkt när du använder undernätet Routning?
I en profil med undernätet routning, om du har en slutpunkt med inga undernät som mappats till, dirigeras varje begäran som inte överensstämmer med andra slutpunkter till här. Vi rekommenderar starkt att du har en återställningsplats slutpunkt i din profil eftersom Traffic Manager returnerar ett NXDOMAIN svar om en begäran kommer in och det inte är kopplat till alla slutpunkter eller om det är kopplat till en slutpunkt utan att slutpunkten är skadad.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Vad händer om en slutpunkt har inaktiverats i ett undernät routning typ profil?
I en profil med undernätet routning, om du har en slutpunkt med som är inaktiverad, Traffic Manager fungerar som om slutpunkten och undernät-mappningar har inte finns. Om en fråga som skulle har matchade med dess IP-adressmappningen tas emot och slutpunkten är inaktiverad, Traffic Manager kommer att returnera en återställningsplats slutpunkt (en med ingen mappning) eller om en sådan slutpunkt inte finns, returneras ett NXDOMAIN svar.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager Flervärden är trafikroutningsmetod

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Vilka är några användningsområden där flera värden routning är användbart?
Flera värden routning returnerar flera felfria slutpunkter i en enda frågesvaret. Den största fördelen med detta är att, om en slutpunkt är ohälsosamt kan klienten har fler alternativ och försök igen utan att göra ett annat DNS-anrop (som kan returnera samma värde från en överordnad cache). Detta gäller för tillgänglighet känsliga program som vill minimera i driftstopp.
Ett annat användningsområde för flera värden routningsmetod är om en slutpunkt är ”dual-homed” till både IPv4 och IPv6-adresser och du vill ge anroparen båda alternativ att välja mellan när den upprättar en anslutning till slutpunkten.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hur många slutpunkter som returneras när flera värden routing används?
Du kan ange det maximala antalet slutpunkter som ska returneras och Flervärden är returnerar inga fler än så många felfria slutpunkter när en fråga tas emot. Största möjliga värde för den här konfigurationen är 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Jag får samma uppsättning slutpunkter när flera värden routing används?
Vi kan inte garantera att samma uppsättning slutpunkter kommer att returneras i varje fråga. Detta påverkas även av det faktum att några av slutpunkterna försätts feltillstånd då inte kommer de inkluderas i svaret

## <a name="real-user-measurements"></a>Faktisk slutanvändarmätning

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Vilka är fördelarna med att använda Real User Measurements?
När du använder routningsmetod för prestanda, Traffic Manager hämtar den bästa Azure-regionen för användaren att ansluta till genom att kontrollera käll-IP och EDNS klienten undernät (om det har skickats in) och kontrollera den mot nätverket svarstid intelligence tjänsten underhåller. Real User Measurements förbättrar detta för din slutpunkt användarbas genom att låta deras upplevelse som bidrar till den här tabellen utöver att kontrollera hälsotillståndet svarstid som den här tabellen omfattar rätt slutanvändare-nätverk från där användarna ansluta till Azure. Detta leder till en större noggrannhet i Routning av användaren.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan jag använda Real User Measurements med icke-Azure-regioner?
Real User Measurements mäter och rapporterar bara svarstiderna att nå Azure-regioner. Om du använder prestandabaserad routning med slutpunkter som finns i Azure-regioner kan dra du fortfarande nytta av den här funktionen genom att ha ökad latensinformation om representativa Azure-region du har valt som ska associeras med den här slutpunkten.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Vilken routningsmetod dra nytta av Real User Measurements?
Ytterligare information från Real User Measurements gäller endast för profiler som använder routningsmetod för prestanda. Länken Real User Measurements är tillgänglig från alla profiler när du visar den via Azure portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Måste jag aktivera Real User Measurements varje profil separat?
Nej, du behöver bara aktivera en gång per prenumeration och informationen för svarstid mäts och rapporterade är tillgängliga för alla profiler.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hur inaktiverar jag Real User Measurements för min prenumeration?
Du kan stoppa betalar avgifter som rör Real User Measurements när du slutar samla in och skicka tillbaka replikeringssvarstiderna från klientprogrammet. Till exempel om mätning JavaScript inbäddad i webbsidor, kan du stoppa med hjälp av den här funktionen genom att ta bort JavaScript eller genom att stänga av dess anrop när sidan renderas.

Du kan också stänga av Real User Measurements genom att ta bort din nyckel. När du tar bort nyckeln ignoreras alla mätningar som skickas till Traffic Manager med nyckeln.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan jag använda Real User Measurements med klientprogram än webbsidor?
Ja, Real User Measurements har utformats att mata in data som samlas in via en annan typ av slutanvändaren klienter. Den här vanliga frågor och svar kommer att uppdateras när nya typer av klientprogram hämta stöds.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hur många mätningarna görs varje gång som min Real User Measurements aktiverat webbsida återges?
När Real User Measurements används tillsammans med mätning JavaScript tillhandahålls, resulterar varje sidrendering i sex mätningarna. Dessa rapporteras sedan tillbaka till Traffic Manager-tjänsten. Du debiteras för den här funktionen baserat på antalet mått som rapporterats till Traffic Manager-tjänsten. Till exempel om användaren navigerar bort från din webbsida när mätningarna utförs men innan det rapporterades, beaktas dessa mätningar inte för fakturering.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Finns det en fördröjning innan Real User Measurements skriptet körs i min webbsida?
Nej, det finns ingen programmerade fördröjning innan anropas skriptet.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan jag använda Real User Measurements med endast Azure-regionerna jag vill mäta?
Nej, varje gång den anropas mäter skriptet Real User Measurements en uppsättning med sex Azure-regioner som definieras av tjänsten. Det här värdet ändringar mellan olika anrop när ett stort antal sådana anrop uppstår mätning täckning som sträcker sig över olika Azure-regioner.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan jag begränsa antalet mätningarna för att ett visst antal?
Mätning JavaScript är inbäddad i din webbsida och du har full kontroll över när du kan starta och stoppa som använder den. Så länge Traffic Manager-tjänsten tar emot en begäran om en lista över Azure-regioner som ska mätas, returneras en uppsättning regioner.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan jag se mätningar som görs av mitt klientprogram som en del av Real User Measurements?
Eftersom mätning logik körs i ditt klientprogram, har du fullständig kontroll av vad som händer, t.ex svarstid mätning av faktisk användning. Traffic Manager rapporterar inte en aggregerad vy över measurements som tagit emot under nyckeln länkad till din prenumeration.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan jag ändra skriptet mätning som tillhandahålls av Traffic Manager?
När du arbetar med kontroll över vad som är inbäddad på din webbsida avråder vi du inte göra några ändringar i mätning skriptet så att den mäter och rapporterar befinner korrekt.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Ska det vara möjligt att andra kan se den nyckel som jag använda med Real User Measurements?
När du bäddar in mätning skriptet till en webbsida ska det vara möjligt att andra kan se skriptet och din nyckel för Real User Measurements (ROM). Men det är viktigt att veta att den här nyckeln skiljer sig från ditt prenumerations-id och genereras av Traffic Manager som ska användas enbart för detta ändamål. Vetskapen om att köra nyckeln äventyrar inte säkerheten för dina Azure-konto.

### <a name="can-others-abuse-my-rum-key"></a>Kan andra missbruka min köra nyckel?
Det är möjligt att andra kan använda din nyckel för att skicka information om fel till Azure, ändras inte några fel mätningar routningen eftersom det beaktas tillsammans med alla andra mått vi får. Om du vill ändra dina nycklar återskapa du nyckeln för den gamla nyckeln blir då tas bort.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Behöver jag placera mätningen JavaScript i alla webbplatser?
Real User Measurements ger mer värde som antalet mätningar ökning. Ändå är det komma fram till om du ska placera den i dina webbsidor eller en väljer några. Vår rekommendation är att starta genom att placera den i din mest besökta sida där en användare förväntas att stanna kvar på den sidan fem sekunder eller mer.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Information om slutanvändarna framgår av Traffic Manager om jag använder Real User Measurements?
När den angivna mätningen JavaScript används har Traffic Manager insyn i klientens IP-adress för användaren och källans IP-adress för den lokala DNS-matcharen som de använder. Traffic Manager använder klientens IP-adress först efter att den trunkeras om du vill inte att kunna identifiera den specifika användaren som skickade mätningar. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Fungerar på webbsidan mätning Real User Measurements måste du använda Traffic Manager för Routning?
Nej, det behöver inte använda Traffic Manager. Routning sida av Traffic Manager fungerar separat från den verkliga användare mätning-delen och även om det är en bra idé att ha dem båda på samma webb-egenskap, de behöver du inte.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Måste jag vara värd för alla tjänster på Azure-regioner ska användas med Real User Measurements?
Nej, du behöver inte vara värd för någon server-sida-komponent på Azure för Real User Measurements ska fungera. Bildpunkt avbildningen hämtas genom mätning JavaScript och tjänsten körs i olika Azure-regioner som är hyst och hanterad av Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Kommer min Azure bandbreddsanvändning ökar när jag använder Real User Measurements?
Som vi nämnde i föregående svar, serversidan komponenterna i Real User Measurements ägs och hanteras av Azure. Det innebär att din Azure bandbreddsanvändning inte ökar vanligtvis eftersom du använder Real User Measurements. Detta inkluderar inte eventuella bandbreddsanvändning utanför vilka Azure-avgifter. Vi kan minimera den bandbredd som används genom att hämta bara en enda pixel-avbildning till mått svarstiden för en Azure-region. 

## <a name="traffic-view"></a>Trafikvy

### <a name="what-does-traffic-view-do"></a>Vad är Traffic View?
Trafikvy är en funktion av Traffic Manager som hjälper dig att förstå mer om dina användare och hur deras upplevelse. Den använder de frågor som tas emot av Traffic Manager och nätverket svarstid intelligence tabeller som underhåller så att du får följande:
- Regionerna där användarna ansluta till dina slutpunkter i Azure.
- Antal användare som ansluter från dessa regioner.
- Vilka Azure-regioner som de komma dirigeras till.
- Upplevelsen svarstid till dessa Azure-regioner.

Den här informationen är tillgänglig för dig att använda via geografisk karta överlägget och tabellvyer i portalen förutom att vara tillgängliga som rådata för hämtning.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hur kan jag dra nytta av Traffic View?

Traffic View ger dig den övergripande vyn av trafiken som får din Traffic Manager-profiler. I synnerhet användas den för att förstå var användarbasen ansluter från och lika viktiga upplevelse för genomsnittlig svarstid är. Du kan sedan använda den här informationen för att hitta områden där du behöver fokusera, till exempel genom att expandera din Azure-fotavtrycket till en region där du kan dessa användare med kortare svarstider. En annan information som du kan härleda från att använda Traffic View är att se mönster av trafiken till andra regioner som i sin tur kan hjälpa dig att fatta beslut rörande ökar eller minskar göra i dessa regioner.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hur skiljer sig Traffic View från mått som är tillgängliga via Azure monitor Traffic Manager?

Azure Monitor kan användas för att förstå en sammanställd nivå den trafik som tas emot av din profil och dess slutpunkter. Du kan också spåra hälsotillståndet för slutpunkterna genom att exponera hälsotillstånd kontrollera resultaten. Traffic View kan användas för att uppnå som när du behöver gå bortom de och förstå din slutanvändarens upplevelse som ansluter till Azure på en regional nivå.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Använder Traffic View EDNS klientnät information?

DNS-frågor som hanteras av Azure Traffic Manager Överväg ECS information för att öka noggrannheten för routning. Men när du skapar datauppsättningen som visar där användarna ansluter från, Traffic View använder endast IP-adressen för DNS-matchning.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hur många dagars data använder Traffic View?

Traffic View skapar sina utdata genom att bearbeta data från de sju dagarna föregående dagen före när det visas av dig. Detta är ett rörligt fönster och den senaste informationen kommer att användas varje gång du besöker.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hur hanterar Traffic View externa slutpunkter?

När du använder externa slutpunkter som finns utanför Azure-regioner i en Traffic Manager-profil kan du välja att den ska mappas till en Azure-region som är en proxy för dess egenskaper för svarstid (detta är i själva verket behövs om du använder routningsmetod för prestanda). Om den har den här Azure-regionmappningen används den Azure-region mått för datainmatningssvarstider när du skapar Traffic View-utdata. Om inga Azure-region har angetts anges svarstiden information i data för de externa slutpunkterna.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Måste jag aktivera Trafikvyn för varje profil i min prenumeration?

Traffic View har aktiverats på en prenumerationsnivå i förhandsversionen. Som en del av förbättringar som vi gjort före den allmänt tillgängliga, kan du nu aktivera Trafikvy på en profilnivå, vilket gör att du har mer detaljerade aktiveringen av den här funktionen. Traffic View kommer att inaktiveras för en profil som standard.

>[!NOTE]
>Om du har aktiverat Traffic View på prenumerationsnivå under tid som förhandsversion måste du nu aktivera det igen för var och en av profilen under den aktuella prenumerationen.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hur kan jag inaktivera Trafikvyn? 
Du kan inaktivera Trafikvyn för en profil med hjälp av portalen eller REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hur fungerar fakturering för Traffic View?

Traffic View priserna baseras på antalet datapunkter som används för att skapa utdata. Den enda datatyp som stöds är för närvarande de frågor som tar emot din profil. Dessutom kan faktureras du bara för den bearbetning som gjordes när du har Trafikvy aktiveras. Det innebär att om du aktiverar Traffic View för vissa tidsperiod under en månad och stänga av den under andra tider, endast datapunkter bearbetade medan du hade funktionen aktiverad antal mot din faktura.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan jag använda Traffic Manager med slutpunkter från flera prenumerationer?

Det går inte att använda slutpunkter från flera prenumerationer med Azure Web Apps. Azure Web Apps kräver att alla anpassade domännamn som används med Web Apps används endast i en enskild prenumeration. Det går inte att använda Web Apps från flera prenumerationer med samma domännamn.

För andra typer av slutpunkter är det möjligt att använda Traffic Manager med slutpunkter från mer än en prenumeration. I Resource Manager slutpunkter från alla prenumerationer går att lägga till Traffic Manager, så länge som den person som konfigurerar Traffic Manager-profilen har läsbehörighet till slutpunkten. De här behörigheterna kan tilldelas via [Azure Resource Manager rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan jag använda Traffic Manager med Cloud Service ”mellanlagring” platser?

Ja. Molntjänst ”mellanlagring” platser kan konfigureras i Traffic Manager som externa slutpunkter. Hälsokontroller av slutpunkter är fortfarande att debiteras enligt taxan för Azure-slutpunkter. Eftersom den externa slutpunkten används, plockas ändringar i underliggande inte upp automatiskt. Traffic Manager kan inte identifiera när Molntjänsten stoppas eller tas bort med externa slutpunkter. Traffic Manager fortsätter därför fakturering hälsokontroller tills slutpunkten är inaktiverad eller borttagen.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager som har stöd för IPv6-slutpunkter?

Traffic Manager tillhandahåller inte IPv6-addressible namnservrar för närvarande. Traffic Manager kan dock fortfarande användas av IPv6-klienter som ansluter till IPv6-slutpunkter. En klient gör inte DNS-begäranden direkt till Traffic-Manager. I stället använder klienten en rekursiv DNS-tjänst. En endast-IPv6-klient skickar begäranden till den rekursiva DNS-tjänsten via IPv6. Sedan bör tjänsten rekursiv kunna kontakta Traffic Managers namnservrar som använder IPv4.

Traffic Manager svarar med DNS-namn eller IP-adressen för slutpunkten. Det finns två alternativ för att stödja en IPv6-slutpunkt. Du kan lägga till slutpunkten som ett DNS-namn som har en associerad AAAA-post och Traffic Manager kommer hälsokontrollen som slutpunkten och gå sedan tillbaka den som en CNAME-post anger i frågesvaret. Du kan också lägga till slutpunkten direkt med IPv6-adress och Traffic Manager returnerar en typ AAAA-post i frågesvaret. 

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan jag använda Traffic Manager med mer än en Webbapp i samma region?

Traffic Manager används vanligtvis för att dirigera trafik till program som distribueras i olika regioner. Men kan det också användas där ett program har fler än en distribution i samma region. Azure Traffic Manager-slutpunkter tillåter inte mer än en Web App-slutpunkt från samma Azure-region som ska läggas till samma Traffic Manager-profilen.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Hur flyttar jag min Traffic Manager-profil Azure-slutpunkter till en annan resursgrupp?

Azure-slutpunkter som är associerade med en Traffic Manager-profil spåras med hjälp av deras resurs-ID: N. Om en Azure-resurs som används som en slutpunkt (till exempel offentlig IP-adress, klassisk molntjänst, WebApp eller en annan Traffic Manager-profil som används på ett sätt som är kapslade) flyttas till en annan resursgrupp, dess resurs-ID-ändringar. I det här scenariot för närvarande måste du uppdatera Traffic Manager-profilen genom att först har tagits bort och sedan lägga tillbaka slutpunkter i profilen. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager endpoint monitoring

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Är Traffic Manager elastisk vid Azure-region fel?

Traffic Manager är en viktig del av leverans av program med hög tillgänglighet i Azure.
För att ge hög tillgänglighet, Traffic Manager har ovanligt hög tillgänglighet och vara motståndskraftiga mot regionala fel.

Avsiktligt, Traffic Manager-komponenter som är motståndskraftig mot ett fullständigt fel i alla Azure-regioner. Den här återhämtning gäller alla Traffic Manager-komponenter: DNS-namnet servrar, API: et, storage-skiktet och slutpunkten övervakningstjänsten.

Vid händelse av ett avbrott i en hel Azure-region förväntas Traffic Manager fortsätter att fungera normalt. Program som distribueras i flera Azure-regioner kan förlita dig på Traffic Manager dirigera trafik till en tillgänglig instans av programmet.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hur påverkar valet av resursgruppens plats Traffic Manager?

Traffic Manager är en enda, global tjänst. Det är inte regional. Valet av resursgruppens plats spelar ingen roll till Traffic Manager-profiler som distribuerats i resursgruppen.

Azure Resource Manager kräver att alla resursgrupper att ange en plats, vilket avgör standardplatsen för resurser som har distribuerats i resursgruppen. När du skapar en Traffic Manager-profil skapas den i en resursgrupp. Alla Traffic Manager-profiler använder **globala** som sin plats, åsidosätter resource group förvalda.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hur tar jag reda på det aktuella hälsotillståndet för varje slutpunkt?

Den aktuella övervakning statusen för varje slutpunkt, förutom den övergripande profilen visas i Azure-portalen. Den här informationen är också tillgängligt via trafik övervakaren [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager), och [plattformsoberoende Azure CLI](../cli-install-nodejs.md).

Du kan också använda Azure Monitor för att spåra hälsotillståndet för dina slutpunkter och se en visuell representation av dem. Mer information om hur du använder Azure Monitor finns i den [Azure Monitoring dokumentation](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Kan jag övervaka HTTPS-slutpunkter?

Ja. Traffic Manager stöder över HTTPS-avsökning. Konfigurera **HTTPS** som protokoll i konfigurationen av övervakningen.

Traffic manager kan inte ange någon certifikatsverifiering inklusive:

* Inte valideras certifikaten för serversidan
* SNI serversidan certifikat stöds inte
* Klientcertifikat stöds inte

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Kan jag använda en IP-adress eller ett DNS-namn när du lägger till en slutpunkt?
Traffic Manager har stöd för att lägga till slutpunkter med hjälp av tre sätt att hänvisa dem – som en DNS-namn, som en IPv4-adress och en IPv6-adress. Om slutpunkten läggs till som en IPv4- eller IPv6-adress blir frågesvaret posttyp A eller AAAA. Om slutpunkten har lagts till som ett DNS-namn, blir svaret på frågan för CNAME-posttyp. Lägga till slutpunkter som IPv4 eller IPv6-adress tillåts endast om slutpunkten är av typen **externa**.
Alla routningsmetoder och övervakar inställningar stöds av tre slutpunktstyper för adressering.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Vilka typer av IP-adresser kan jag använda när du lägger till en slutpunkt?
Traffic Manager kan du använda IPv4 eller IPv6-adresser för att ange slutpunkter. Det finns några begränsningar som visas nedan:
- Adresser som motsvarar reserverade privata IP-adressutrymmena är inte tillåtna. De här adresserna är de som beskrivs i RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 och RFC 5771
- Adressen får inte innehålla några portnummer (du kan ange portarna som ska användas i inställningarna för profil-konfiguration) 
- Inga två slutpunkter i samma profil kan ha samma mål-IP

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan jag använda olika endpoint adressering typer i en enda profil?
Nej, Traffic Manager kan du inte blanda adressering slutpunktstyper inom en profil, såvida en profil med flera värden routningstyp där du kan blanda IPv4 och IPv6-adressering typer

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Vad händer när ett inkommande frågan posttyp skiljer sig från den typ som är associerade med typen adressering av slutpunkterna?
När en fråga tas emot mot en profil, hittar Traffic Manager först den slutpunkt som ska returneras enligt vilken routningsmetod som angetts och hälsotillståndet för slutpunkterna. Den tittar sedan på posttypen har begärt i den inkommande frågan och den typ som är associerade med slutpunkten innan det returneras ett svar baserat på tabellen nedan.

För profiler med routningmetod än Flervärden är:
|Inkommande query-fråga|    Typ av slutpunkt|  Svar som tillhandahålls|
|--|--|--|
|ALLA |  A / AAAA / CNAME |  Mål-slutpunkt| 
|A |    A / CNAME | Mål-slutpunkt|
|A |    AAAA |  INGA DATA |
|AAAA | AAAA / CNAME |  Mål-slutpunkt|
|AAAA | A | INGA DATA |
|CNAME |    CNAME | Mål-slutpunkt|
|CNAME  |A / AAAA | INGA DATA |
|
För profiler med routningsmetod inställd på Flervärden är:

|Inkommande query-fråga|    Typ av slutpunkt | Svar som tillhandahålls|
|--|--|--|
|ALLA |  Blandning av A och AAAA | Målslutpunkter|
|A |    Blandning av A och AAAA | Endast Målslutpunkter av typ A|
|AAAA   |Blandning av A och AAAA|     Målslutpunkter av typen AAAA|
|CNAME |    Blandning av A och AAAA | INGA DATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan jag använda en profil med IPv4 / IPv6 åtgärdas slutpunkter i en kapslad profil?
Ja, du kan med undantaget att en profil av typen Flervärden är inte får vara en överordnad-profil i en kapslad profil ange.


### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Jag har stoppat en Azure-molntjänst / webb-programslutpunkt i min Traffic Manager-profil men jag tar inte emot trafik även efter att jag startas om. Hur kan jag åtgärda detta?

När en Azure-molntjänst / webb-programslutpunkt är stoppad Traffic Manager stoppar kontrollerar dess hälsa och startar om hälsokontrollerna endast när det upptäcker att slutpunkten har startats om. För att förhindra att den här fördröjningen, inaktivera och sedan återaktivera den slutpunkten i Traffic Manager-profil när du startar om slutpunkten.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan jag använda Traffic Manager även om mitt program inte har stöd för HTTP eller HTTPS?

Ja. Du kan ange TCP som protokoll för övervakning och Traffic Manager kan initiera en TCP-anslutning och vänta tills ett svar från slutpunkten. Om slutpunkten som svar på begäran med ett svar att upprätta anslutningen inom tidsgränsen markeras att slutpunkten som felfritt.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Vilka specifika svar krävs slutpunkten när du använder TCP övervakning?

När TCP-övervakning används startar Traffic Manager en 3-vägs TCP-handskakning genom att skicka en SYN-begäran till slutpunkten på den angivna porten. Den väntar sedan en viss tidsperiod (som anges i inställningarna för timeout) för ett svar från slutpunkten. Om slutpunkten som svarar SYN-begäran med ett SYN ACK-svar inom den angivna tiden som anges i inställningarna för övervakning, är att slutpunkten anses felfritt. Om SYN ACK-svar tas emot, återställs anslutningen genom att svara med en RSTA i Traffic Manager.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hur snabbt flytta mina användare från en skadad slutpunkt i Traffic Manager?

Traffic Manager tillhandahåller flera inställningar som kan hjälpa dig att styra beteendet redundans för Traffic Manager-profilen på följande sätt:
- Du kan ange att Traffic Manager avsökningar slutpunkterna oftare genom att ange intervall för avsökning på 10 sekunder. Detta säkerställer att valfri slutpunkt kommer feltillstånd kan identifieras så snart som möjligt. 
- Du kan ange hur länge innan en hälsokontroll begäran gånger (minsta timeout-värdet är 5 sek).
- Du kan ange hur många fel kan uppstå innan slutpunkten markeras som felaktig. Det här värdet kan vara lite som 0, i vilket fall slutpunkten har ett dåligt när den första hälsokontrollen misslyckas. Med det minsta värdet 0 för tolererat antal fel kan dock leda till slutpunkter som tas bort från roteringen på grund av tillfälliga problem som kan uppstå vid tidpunkten för avsökning.
- Du kan ange time to live (TTL) för DNS-svaret ska vara så lågt som 0. Om du gör det innebär att du kan inte cachelagrar svaret, DNS-matchare och varje ny fråga hämtar ett svar som innehåller den allra senaste informationen för hälsotillstånd med Traffic Manager.

Genom att använda de här inställningarna kan får Traffic Manager redundans under 10 sekunder efter att en slutpunkt går feltillstånd och en DNS-fråga ställs mot motsvarande profilen.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hur kan jag ange olika övervakningsinställningar för olika slutpunkter i en profil?

Traffic Manager övervakningsinställningarna finns på en per-profilnivå. Om du vill använda en annan övervakning inställning för endast en slutpunkt kan du göra genom att låta den slutpunkten som en [kapslad profil](traffic-manager-nested-profiles.md) vars övervakningsinställningarna skiljer sig från den överordnade profilen.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hur kan jag tilldela HTTP-huvuden till Traffic Manager hälsokontroller till min slutpunkter?
Traffic Manager kan du ange anpassade huvuden i den HTTP (S) hälsokontroller initieras till dina slutpunkter. Om du vill ange en anpassad rubrik du göra det på profilnivå (gäller för alla slutpunkter) eller ange på nivån slutpunkt. Om en rubrik har definierats på båda nivåerna kan åsidosätta den som angetts på nivån endpoint profilnivå en.
Ett vanligt användningsfall för detta är att ange värdhuvuden så att Traffic Manager-begäranden kan korrekt dirigeras till en slutpunkt i en miljö med flera innehavare. Ett annat exempel med detta är att identifiera Traffic Manager-förfrågningar från en slutpunkt HTTP (S) begära loggar

## <a name="what-host-header-do-endpoint-health-checks-use"></a>Vilka värden rubrik gör endpoint hälsokontroller användning?
Om ingen rubrik inställning för anpassade värden anges, är du värdhuvudet som används av Traffic Manager DNS-namnet på slutpunkten målet som konfigurerats i profilen, om som är tillgänglig. 


### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Vilka är de IP-adresser som kontrollerar hälsotillståndet kommer?

Klicka på [här](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) att visa JSON-fil som listar IP-adresser från vilken Traffic Manager hälsokontroller kan kommer. Granska IP-adresserna som anges i JSON-filen för att säkerställa att inkommande anslutningar från dessa IP-adresser tillåts på slutpunkterna att kontrollera dess hälsostatus.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hur många hälsokontroller till min slutpunkt kan jag förvänta mig från Traffic Manager?

Antalet Traffic Manager health kontrollerar nå slutpunkten beror på följande:
- Det värde som du har ställt in för Övervakningsintervall (mindre intervall innebär fler begäranden som hamnar på slutpunkten i en viss tidsperiod).
- antalet platser från där hälsokontroller kommer (IP-adresserna där du kan förvänta dig de här kontrollerna visas i föregående vanliga frågor och svar).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hur vet jag om en av Mina slutpunkterna slutar att fungera? 
En av de mått som tillhandahålls av Traffic Manager är hälsostatus för slutpunkter i en profil. Du kan se detta som en aggregering för alla slutpunkter i en profil (till exempel 75% av dina slutpunkter fungerar felfritt), eller på en per slutpunkt nivå. Traffic Manager-mått är tillgängliga via Azure Monitor och du kan använda dess [aviseringsfunktioner](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) att få meddelanden när det finns en ändring i din slutpunkt hälsostatus. Mer information finns i [Traffic Manager-mått och aviseringar](traffic-manager-metrics-alerts.md).  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager kapslade profiler

### <a name="how-do-i-configure-nested-profiles"></a>Hur konfigurerar jag kapslade profiler?

Kapslade Traffic Manager-profiler kan konfigureras med hjälp av både Azure Resource Manager och den klassiska Azure REST API: er, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI-kommandon. De stöds också via den nya Azure-portalen.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Stöd för hur många lager med kapslade gör Traffic Manager?

Du kan kapsla profiler upp till 10 nivåer. 'Loopar' tillåts inte.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan jag blanda andra typer av slutpunkter med kapslade underordnade profiler i samma Traffic Manager-profilen?

Ja. Det finns inga begränsningar för hur du kombinerar slutpunkter för olika typer i en profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hur tillämpas faktureringsmodellen för kapslade profiler?

Det är inte negativt priser effekten av att använda kapslade profiler.

Traffic Manager-fakturering består av två komponenter: hälsokontroller av slutpunkter och miljontals DNS-frågor

* Hälsokontroller av slutpunkter: Det finns ingen kostnad för en underordnad profil när konfigurerad som en slutpunkt i en överordnad-profil. Övervakning av slutpunkter i profilen för underordnade debiteras som vanligt.
* DNS-frågor: Varje fråga räknas endast en gång. En fråga till en överordnad-profil som returnerar en slutpunkt från en underordnad profil räknas mot den överordnade profilen.

Fullständig information finns i [Traffic Manager-sidan med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Finns det en prestandapåverkan för kapslade profiler?

Nej. Påverkas inte prestanda när med hjälp av kapslade profiler.

Traffic Managers namnservrar passerar profilhierarki internt vid bearbetning av varje DNS-fråga. En DNS-fråga till en överordnad-profil kan ta emot ett DNS-svar med en slutpunkt från en underordnad-profil. En CNAME-post används om du använder en enda profil eller kapslade profiler. Det finns inget behov att skapa en CNAME-post för varje profil i hierarkin.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hur beräknar hälsotillståndet för en kapslad slutpunkt i en överordnad-profil i Traffic Manager?

Den överordnade profilen utföra inte hälsokontroller av slutpunkter på underordnat direkt. Hälsotillståndet för den underordnade profilen slutpunkter används i stället för att beräkna den övergripande hälsan för den underordnade profilen. Den här informationen sprids uppåt i hierarkin för kapslade profiler med att fastställa hälsotillståndet för den kapslade slutpunkten. Den överordnade profilen använder denna sammanställda hälsa för att fastställa om trafiken kan dirigeras till underordnat.

I följande tabell beskrivs beteendet för Traffic Manager hälsokontroller för en kapslad slutpunkt.

| Underordnade profilen Monitor-status | Överordnade slutpunkten Monitor-status | Anteckningar |
| --- | --- | --- |
| Inaktiverad. Den underordnade profilen har inaktiverats. |Stoppad |Det överordnade slutpunktstillståndet stoppas inte inaktiverat. Inaktiverat tillstånd är reserverad för som anger att du har inaktiverat slutpunkten i profilen för överordnade. |
| Försämrad. Minst en underordnad slutpunktsprofilen har statusen degraderad. |Online: antalet Online slutpunkter i profilen för underordnade är minst värdet för MinChildEndpoints.<BR>CheckingEndpoint: antalet Online plus CheckingEndpoint slutpunkter i profilen för underordnade är minst värdet för MinChildEndpoints.<BR>Försämrad: annars. |Trafiken dirigeras till en slutpunkt för status CheckingEndpoint. Om MinChildEndpoints är för högt, sjunker alltid slutpunkten. |
| Online. Minst en underordnad profil slutpunkten är ett onlinetillstånd. Ingen slutpunkt har statusen degraderad. |Se ovan. | |
| CheckingEndpoints. Minst en underordnad profil slutpunkten är 'CheckingEndpoint'. Inga slutpunkter är ”Online' eller 'Degraderad' |Samma som ovan. | |
| Inaktiva. Alla underordnade profilen slutpunkterna är inaktiverad eller stoppad eller den här profilen har inga slutpunkter. |Stoppad | |

## <a name="next-steps"></a>Nästa steg:
- Läs mer om Traffic Manager [slutpunkt för övervakning och automatisk redundans](../traffic-manager/traffic-manager-monitoring.md).
- Läs mer om Traffic Manager [trafikroutningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
