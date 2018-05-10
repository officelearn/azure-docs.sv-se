---
title: Azure Traffic Manager – vanliga frågor och svar | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor och svar om Traffic Manager
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: kumud
ms.openlocfilehash: 718a7eb1e6457c669456d88e5c6e80157b28066c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager vanliga frågor (FAQ)

## <a name="traffic-manager-basics"></a>Traffic Manager-grunderna

### <a name="what-ip-address-does-traffic-manager-use"></a>Vilken IP-adress använder Traffic Manager?

Enligt beskrivningen i [så Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fungerar på DNS-nivå. Skickar den DNS-svar för att dirigera klienterna till lämplig tjänstslutpunkten. Klienter ansluter sedan till tjänstslutpunkten direkt, inte via Trafikhanterarprofilen.

Traffic Manager ger därför inte en slutpunkt eller IP-adress för klienter att ansluta till. Om du vill statiska IP-adressen för din tjänst som måste konfigureras på tjänsten, inte i Trafikhanterarprofilen.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Vilka typer av trafik kan vara routning med Traffic Manager?
Enligt beskrivningen i [så Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), en Traffic Manager-slutpunkt kan vara en internetuppkopplad tjänst som finns i eller utanför Azure. Traffic Manager kan därför kan dirigera trafik från det offentliga internet till en uppsättning slutpunkter som är också internet facing. Om du har slutpunkter som är i ett privat nätverk (till exempel en intern version av [Azure belastningsutjämnare](../load-balancer/load-balancer-overview.md#internalloadbalancer)) eller användare som DNS-förfrågningar från sådana interna nätverk Traffic Manager inte kan användas för dessa trafik.


### <a name="does-traffic-manager-support-sticky-sessions"></a>Stöder Traffic Manager-Fäst' sessioner?

Enligt beskrivningen i [så Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fungerar på DNS-nivå. DNS-svar används för att dirigera klienterna till lämplig tjänstslutpunkten. Klienter som ansluter till tjänstslutpunkten direkt, inte via Trafikhanterarprofilen. Traffic Manager finns därför inte HTTP-trafik mellan klienten och servern.

Dessutom tillhör käll-IP-adressen för DNS-fråga tas emot av Traffic Manager rekursiv DNS-tjänsten inte klienten. Därför Traffic Manager har inget sätt att spåra enskilda klienter och kan inte implementera 'Fäst-sessioner. Den här begränsningen är gemensamma för alla system för DNS-baserad trafik och är inte specifik för Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Varför ser jag ett HTTP-fel när du använder Traffic Manager?

Enligt beskrivningen i [så Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fungerar på DNS-nivå. DNS-svar används för att dirigera klienterna till lämplig tjänstslutpunkten. Klienter ansluter sedan till tjänstslutpunkten direkt, inte via Trafikhanterarprofilen. Traffic Manager stöder inte se HTTP-trafik mellan klienten och servern. Alla HTTP-felet som du ser måste därför kommer från ditt program. Alla DNS-matchning steg har slutförts för klienten att ansluta till programmet. Som innehåller någon interaktion med Traffic Manager på trafikflöde program.

Ytterligare undersökning bör därför fokusera på programmet.

Värdadressen HTTP skickas från klientens webbläsare är de vanligaste källan till problemet. Kontrollera att programmet är konfigurerad för att acceptera rätt värdadressen för det domännamn som du använder. Slutpunkter som använder Azure App Service finns [konfigurera ett anpassat domännamn för en webbapp i Azure App Service med Traffic Manager](../app-service/web-sites-traffic-manager-custom-domain-name.md).

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Vad är prestandapåverkan med Traffic Manager?

Enligt beskrivningen i [så Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fungerar på DNS-nivå. Eftersom klienter ansluta direkt till dina Tjänsteslutpunkter, påverkas inte prestanda som uppstår när du använder Traffic Manager när anslutningen har upprättats.

Eftersom Traffic Manager integrerar med program på DNS-nivå, kräver den en ytterligare DNS-sökning som ska infogas i DNS-matchning av kedjan. Effekten av Traffic Manager på DNS-matchningstid är minimal. Traffic Manager använder ett globalt nätverk av namnservrar och använder [anycast](https://en.wikipedia.org/wiki/Anycast) nätverk för att säkerställa DNS frågor alltid dirigeras till närmaste tillgängliga namnservern. Dessutom innebär cachelagring av DNS-svar att ytterligare DNS-latensen med Traffic Manager gäller endast en del av sessioner.

Metoden prestanda dirigerar trafik till närmaste tillgängliga slutpunkten. Resultatet är att prestandapåverkan som är associerade med den här metoden bör vara minimal. En ökning i DNS-svarstid bör att förskjutas av lägre nätverksfördröjning till slutpunkten.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Vilka programprotokoll kan du använda med Traffic Manager?

Enligt beskrivningen i [så Traffic Manager fungerar](../traffic-manager/traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager fungerar på DNS-nivå. När DNS-sökning är klar kan ansluter klienterna till slutpunkten programmet direkt, inte via Trafikhanterarprofilen. Anslutningen kan därför använda alla protokoll. Om du väljer TCP som det övervakning-protokollet, Traffic Manager endpoint hälsoövervakning kan göras utan att använda alla protokoll på programnivå. Om du vill ha hälsotillståndet verifieras med ett programprotokoll måste slutpunkten kunna svara på begäranden för HTTP eller HTTPS hämta.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan jag använda Traffic Manager med ett 'asbestgaller' domännamn?

Nej. DNS-standarden tillåter inte att skapa CNAME-poster samexisterar med andra DNS-poster med samma namn. En DNS-zon apex (eller rot) innehåller alltid två befintlig DNS-poster. SOA- och auktoritativa NS-poster. Detta innebär en CNAME-post inte kan skapas på zonens apex utan brott mot DNS-standarden.

Traffic Manager kräver en DNS CNAME-post för att mappa alternativa DNS-namn. Exempelvis kan du mappa www.contoso.com till Traffic Manager profil DNS-namnet contoso.trafficmanager.net. Dessutom returnerar trafikhanterarprofilen ett andra DNS CNAME för att ange vilken slutpunkt som klienten ska ansluta till.

Om du vill undvika det här problemet bör du använda en HTTP-omdirigering till trafiken från asbestgaller domännamnet till en annan URL, som sedan kan använda Traffic Manager. Asbestgaller domänen contoso.com kan till exempel omdirigera användare till CNAME ”www.contoso.com” som pekar på Traffic Manager-DNS-namn.

Fullständigt stöd för asbestgaller domäner i Traffic Manager spåras i vår funktionen eftersläpning. Du kan registrera ditt stöd för den här funktionsbegäran av [röstning för den på vår community feedbackwebbplats](https://feedback.azure.com/forums/217313-networking/suggestions/5485350-support-apex-naked-domains-more-seamlessly).

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Traffic Manager betraktar undernätsadress klienten vid hantering av DNS-frågor? 
Ja, utöver käll-IP-adressen för DNS-fråga tas emot (som vanligtvis är IP-adressen för DNS-matchning), när du utför sökningar för routningsmetoder för geografiska och prestanda, traffic manager också anser undernätsadress klienten om det är ingår i frågan genom att matcharen gör förfrågan för slutanvändaren.  
Mer specifikt [RFC 7871 – klientnät i DNS-frågor](https://tools.ietf.org/html/rfc7871) som ger en [tillägget mekanism för DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) som kan överföra på klienten undernätsadress från matchare som stöder den.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Vad är DNS TTL och hur den påverkar Mina användare?

När en DNS-fråga hamnar på Traffic Manager, anger ett värde i svaret kallas time to live (TTL). Det här värdet, vars enheten är i sekunder, anger hur länge till DNS-matchare nedströms på att cachelagra svaret. När DNS-matchare inte är säkert att cachelagra resultatet, gör cachelagringen det enkelt att besvara efterföljande frågor av cacheminnet i stället för att gå till Traffic Manager-DNS-servrar. Detta påverkar svaren på följande sätt:
- en högre TTL minskar antalet frågor som hamna på Traffic Manager-DNS-servrar som kan minska kostnaden för en kund eftersom antalet frågor som hanteras är en fakturerbar användning.
- ett högre TTL-värde kan minska den tid det tar för att göra en DNS-sökning.
- ett högre TTL-värde innebär också att dina data inte återspeglar den senaste hälsoinformation Traffic Manager har erhållits via dess avsöknings agenter.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hur högt eller lågt anger jag TTL för Traffic Manager svar?

Du kan ange på en per profil nivå, DNS TTL vara så lågt som 0 sekunder och lika hög som 2 147 483 647 sekunder (största intervall som är kompatibla med [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt )). TTL-värdet 0 innebär att den underordnade DNS-matchare cachelagrar inte svar på frågor och alla frågor som förväntas nå Traffic Manager-DNS-servrar för namnmatchning.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager geografiska trafikroutningsmetod

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Vilka är några användningsområden där geografiska routning är användbar? 
Geografisk routning kan användas i scenarier där en Azure-kund behöver skilja användarna baserat på geografiska områden. Till exempel kan använder geografiska trafikroutningsmetod, du ge användare från vissa regioner en annan användarupplevelse än de från andra regioner. Ett annat exempel uppfyller lokala data suveränitet uppdrag som kräver att användare från en viss region hanteras endast med slutpunkter i den regionen.

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Vilka är de regioner som stöds av Traffic Manager för geografisk Routning? 
Det land/region som används av Traffic Manager kan hittas [här](traffic-manager-geographic-regions.md). Medan den här sidan hålls uppdaterade med ändringar, kan du också programmässigt hämta samma information med hjälp av den [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hur traffic manager kontrollerar där en användare efterfrågar från? 
Traffic Manager tittar på käll-IP för frågan (det troligen är en lokal DNS-matchare gör frågor för användarens räkning) och använder en intern IP-adress till region karta för att avgöra var. Den här kartan uppdateras kontinuerligt för ändringar i internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Det garanteras att Traffic Manager korrekt kan bestämma den exakta geografiska platsen för användaren i samtliga fall?
Nej, Traffic Manager kan inte garantera att den geografiska region som vi härleda från käll-IP-adressen för en DNS-fråga alltid motsvarar användarens plats på grund av följande skäl: 

- Först enligt beskrivningen i föregående vanliga frågor och svar är källans IP-adress som visas en DNS-matchare gör sökning för användarens räkning. Den geografiska platsen för DNS-matchning är en bra proxy för den geografiska platsen för användaren, kan det också vara olika beroende på storleken på tjänsten DNS-matchare och specifika DNS-matchare tjänsten en kund har valt att använda. Exempelvis kan en kund i Malaysia sin enhet inställningarna används ange ett DNS-lösartjänsten vars DNS-servern i Singapore kan få ut för att hantera frågan lösningar för att användarenhet. I så fall kan Traffic Manager bara se den matcharen IP-adress som motsvarar Singapore platsen. Se även tidigare vanliga frågor om stöd för klienter undernät adress på den här sidan.

- Traffic Manager använder dessutom en intern mappning för att utföra geografiska region översättning IP-adressen. Den här kartan verifieras och uppdateras kontinuerligt att öka noggrannheten för dess och kontot för den växande strukturen på internet, men det finns fortfarande möjlighet att vår information inte är en exakt kopia av den geografiska platsen där alla IP-Adressen adresser.


###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Behöver en slutpunkt fysiskt finnas i samma region som den är konfigurerad med för geografisk Routning? 
Nej, platsen för slutpunkten medför inga begränsningar som regioner kan mappas till den. Exempelvis kan en slutpunkt i USA, Central Azure-region har alla användare från Indien dirigeras till den.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan jag tilldelar geografiska områden slutpunkter i en profil som inte är konfigurerad för att göra geografiska Routning? 

Ja, om routningsmetod för en profil inte geografiska, du kan använda den [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) tilldela geografiska områden till slutpunkterna i den här profilen. Icke-geografiska routning typen profiler kan ignoreras den här konfigurationen. Om du ändrar en sådan profil till geografiska routning vid ett senare tillfälle kan Traffic Manager använda mappningarna.


### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Varför får ett fel när jag försöker ändra routningsmetod för en befintlig profil till geografiska?

Alla slutpunkter under en profil med geografiska routning måste ha minst en region som mappats till den. Om du vill konvertera en befintlig profil till geografiska routning, måste du först associera geografiska områden till dess slutpunkter som använder den [Azure Traffic Manager REST API](https://docs.microsoft.com/rest/api/trafficmanager/) innan du ändrar typen routning till geografiska. Om du använder portalen först tar bort slutpunkterna, ändra routningsmetod för profilen till geografiska och sedan lägga till slutpunkter tillsammans med deras geografiska region-mappning. 


###  <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Varför det rekommenderas starkt att kunderna skapa kapslade profiler i stället för slutpunkter med en profil med geografiska routning aktiverad? 

En region kan tilldelas till endast en slutpunkt i en profil för om dess med geografiska routning. Om den slutpunkten inte är en kapslad typ med en underordnad profil som är kopplat till den, om den slutpunkt som ska feltillstånd, trafik Manager fortsätter att skicka trafik till den sedan med alternativet att inte skicka all trafik som inte är alla bättre. Traffic Manager har inte växling till en annan slutpunkt, även om den region som tilldelats är ”överordnad” regionen tilldelas den slutpunkt som gått feltillstånd (till exempel om en slutpunkt som har region Spanien blir ohälsosamt vi gör inte växling till en annan slutpunkt som har den region som Europa tilldelade). Detta görs för att säkerställa att Traffic Manager respekterar geografisk gränser att en kund har installationen i profilen. För att få förmånen vid fel till en annan slutpunkt när en slutpunkt är inte felfri, rekommenderas det att geografiska områden ska tilldelas kapslade profiler med flera slutpunkter inom det i stället för enskilda slutpunkter. På så sätt kan trafik växling till en annan slutpunkt i samma kapslad underordnad profil om en slutpunkt i profilen för kapslad underordnad misslyckas.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Finns det några begränsningar på API-version som stöder den här Routning?

Ja, endast API-versionen 2017-03-01 och senare stöder geografiska routning skriver. Alla äldre API-versioner kan inte användas för att skapade profiler för geografisk routning eller tilldela slutpunkter geografiska områden. Om ett äldre API-versionen används för att hämta profiler från en Azure-prenumeration, returneras inte någon profil geografiska routning. Dessutom när du använder äldre API-versioner, returnerade en profil som har slutpunkter med en geografisk region tilldelning, har inte dess tilldelning för geografiska region som visas.

## <a name="real-user-measurements"></a>Faktisk slutanvändarmätning

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Vilka är fördelarna med att använda den verkliga användaren mått?
När du använder routningsmetoden för prestanda, Traffic Manager hämtar den bästa Azure-regionen för användaren att ansluta till genom att kontrollera käll-IP och EDNS klientnät (om det skickade) och kontrollera mot nätverket latens intelligence tjänsten upprätthåller. Verklig användare mätningar förbättrar detta för grundläggande användaren genom att låta sina erfarenheter bidra till den här tabellen förutom att säkerställa svarstid som tabellen omfattar rätt slutanvändare nätverk från där användarna ansluter till Azure. Detta leder till en bättre noggrannhet i routning för dina slutanvändare.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan jag använda den verkliga användaren mått med Azure-regioner?
Verklig användare mätningar mäter och rapporter om bara svarstiderna att nå Azure-regioner. Om du använder prestandabaserad routning med slutpunkter finns i Azure-regioner, kan du fortfarande dra från den här funktionen genom att ha ökat svarstid information om representativt Azure-regionen som du har valt att kopplas till den här slutpunkten.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Vilka routningsmetoden fördelar från verkliga användaren mått?
Ytterligare information från verkliga användaren mätningar gäller endast för profiler som använder routningsmetoden för prestanda. Observera att länken verkliga användaren mått är tillgänglig från alla profiler när du visar den via Azure-portalen.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Behöver jag aktivera verkliga användaren mätningar varje profil separat?
Nej, du behöver bara aktivera en gång per prenumeration och alla latensinformation mäts och rapporterade är tillgängliga för alla profiler.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hur inaktiverar jag verkliga användaren mått för min prenumeration?
Du kan stoppa uppstår avgifter till verkliga mått som användare när du slutar samla in och skicka tillbaka latens mätningar från klientprogrammet. Exempelvis när mätning JavaScript inbäddade i webbsidor, kan du sluta använda den här funktionen genom att ta bort JavaScript eller stänga av sitt anrop när sidan renderas.

Du kan också inaktivera verkliga mått för användare genom att ta bort din nyckel. När du tar bort nyckeln ignoreras alla mätningar som skickats till Traffic Manager med nyckeln.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan jag använda den verkliga användaren mått med klientprogram än webbsidor?
Ja, verkliga användaren mått är avsedd att mata in data som samlas in via olika typer av klienter för slutanvändaren. Dessa vanliga frågor kommer att uppdateras när nya typer av klientprogram hämta stöds.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hur många mätningarna görs varje gång min verkliga användaren mätningar aktiverad webbsida återges?
När verkliga användaren mätningar används med mätning JavaScript tillhandahålls resulterar varje sidåtergivningen i sex mätningarna. Dessa rapporteras sedan tillbaka till Traffic Manager-tjänsten. Observera att du debiteras för den här funktionen baserat på antalet mått som rapporteras till Traffic Manager-tjänsten. Till exempel om användaren navigerar bort från din webbsida medan mätningarna utförs men innan det rapporterades, anses dessa mätningar inte för fakturering.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Finns det en fördröjning innan verkliga användaren mätningar skriptet körs i min webbsida?
Nej, det finns ingen programmerade fördröjning innan anropas skriptet.

### <a name="can-i-use-configure-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan jag använda Konfigurera verkliga mått för användare med endast Azure regioner som jag vill mäta?
Ingen, varje gång den anropas mäter skriptet verkliga användaren mätningar en uppsättning sex Azure-regioner som definieras av tjänsten. Det här värdet ändringar mellan olika anrop när ett stort antal sådana anrop uppstår mätning täckning sträcker sig över olika Azure-regioner.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan jag begränsa antalet mätningar som gjorts för ett visst tal?
Måttet JavaScript är inbäddad i din webbsida, och du är i fullständig kontroll över till start och stopp som använder den. Så länge Traffic Manager-tjänsten tar emot en begäran om en lista över Azure-regioner som ska mätas, returneras en uppsättning regioner.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan jag se mätningar som utförs av client-program som en del av den verkliga användaren mått?
Eftersom mätning-logik körs i ditt klientprogram, är du full kontroll om vad som händer inklusive ser latens mått. Traffic Manager rapporterar inte en aggregerad vy över de mått som togs emot under nyckeln länkad till din prenumeration.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan jag ändra mätning skriptet av Traffic Manager?
När du har kontroll över vad som är inbäddad på din webbsida, avråder vi du gör några ändringar i skriptet mätning så att den mäter och rapporterar latens korrekt.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Det är möjligt att andra kan se den nyckel som jag använder med den verkliga användaren mått?
När du bäddar in mätning skriptet till en webbsida ska det vara möjligt att andra kan se skriptet och verkliga användaren mått (ROM)-nyckel. Men det är viktigt att du vet att den här nyckeln skiljer sig från ditt prenumerations-id och genereras av Traffic Manager som ska användas endast för detta ändamål. Känna till din ROM nyckel kommer inte äventyra säkerheten för din Azure-konto.

### <a name="can-others-abuse-my-rum-key"></a>Kan andra missbruk min ROM nyckel?
Även om det är möjligt att andra kan skicka felaktig information till Azure med hjälp av din nyckel Lägg märke till att några fel mått inte ändras routning eftersom det beaktas tillsammans med andra mått vi får. Om du behöver ändra dina nycklar kan du generera nyckeln då den gamla nyckeln blir ignoreras.

###  <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Behöver jag placera mätning JavaScript i alla webbplatser?
Verklig användare mätningar ger dig mer som antalet mätningar ökning. Ändå är det ditt beslut om om du ska placera den i alla webbsidor eller en väljer få. Vår rekommendation är att starta genom att placera den i dina mest besökta sida där en användare förväntas vara på sidan fem sekunder eller mer.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Information om min slutanvändare framgår av Traffic Manager om jag använder verkliga användaren mått?
När den angivna mätningen JavaScript används har Traffic Manager insyn i klientens IP-adress av användaren och källans IP-adress för den lokala DNS-matcharen används. Traffic Manager använder klientens IP-adress först efter att den trunkerats så att inte kunna identifiera specifika användaren som skickade måtten. 

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Stöder webbsidan mätning verkliga mätvärden för användaren behöver använda Traffic Manager för Routning?
Det behöver inte Nej, Använd Traffic Manager. Routning på klientsidan för Traffic Manager fungerar separat från de verkliga användaren mätning del och men det är en bra idé att ha båda i samma webb-egenskap, de behöver inte vara.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Behöver jag värd för alla tjänster på Azure-regioner för användning med den verkliga användaren mått?
Nej, behöver du inte värd för några server-sida-komponent på Azure för den verkliga användaren mått ska fungera. Bildpunkt avbildningen hämtas av måttet JavaScript och tjänsten som körs i olika regioner som Azure på och hanteras av Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Ökar min Azure bandbreddsanvändning när jag använder verkliga användaren mått?
Som nämnts tidigare svar komponenter för serversidan verkliga användaren mätningar ägs och hanteras av Azure. Det innebär att du inte kommer att öka bandbreddsanvändningen Azure eftersom du använder verkliga användaren mått. Observera att detta inte innehåller några bandbreddsanvändning utanför vilka Azure avgifter. Vi minimera bandbredden som används av hämtar endast bildpunkt bilden till mått fördröjning till en Azure-region. 

## <a name="traffic-view"></a>Trafikvy

### <a name="what-does-traffic-view-do"></a>Vad är trafik vyn?
Visa trafik är en funktion i Traffic Manager som hjälper dig att förstå mer om din användare och hur de är. Den använder frågor som tagits emot av Traffic Manager och nätverket latens intelligence tabeller som underhåller för att ge dig följande:
- Regionerna där användarna ansluter till dina slutpunkter i Azure.
- Volymen för användare som ansluter från dessa regioner.
- De Azure-regioner som de komma att vidarebefordras till.
- Deras svarstid-upplevelse för Azure-regioner.

Denna information är tillgänglig för dig att använda via geografisk karta överlägget och tabellvyer i portalen förutom att vara tillgängliga som rådata för hämtning.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hur kan dra nytta av trafik i vyn?

Visa trafik ger övergripande vy av trafiken får Traffic Manager-profiler. I synnerhet användas det för att förstå där användarbasen ansluter från och lika allt sina erfarenheter genomsnittlig svarstid är. Du kan sedan använda den här informationen för att hitta områden där du behöver fokusera, till exempel genom att expandera din Azure storleken till ett område som kan hantera användare med kortare svarstid. En annan information som du kan härledas från vyn trafik är att se mönster av trafik till olika regioner som i sin tur kan hjälpa dig att fatta beslut om öka eller minska inventering i dessa regioner.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hur skiljer sig vyn trafik från mätvärdena som är tillgängliga via Azure-monitor Traffic Manager?

Azure Övervakare kan användas för att förstå den trafik som tas emot av profilen och dess slutpunkter på sammanställd nivå. Du kan också spåra hälsostatus slutpunkter genom att exponera hälsa kontrollera resultaten. Trafik vyn kan användas för att uppnå som när du behöver utöver dessa och förstå din slutanvändarens upplevelse som ansluter till Azure på regional nivå.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Använder trafik visa information om EDNS klienten undernät?

DNS-frågor som hanteras av Azure Traffic Manager Överväg ECS information för att öka noggrannheten för routning. Men när du skapar den datamängd som visar om användarna ansluter från vyn trafik använder endast IP-adressen för DNS-matchning.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hur många dagars data använder trafik vyn?

Trafik visa skapar utdata genom att behandla data från sju dagar före dagen före när det visas av dig. Detta är ett rörligt fönster och den senaste informationen kommer att användas varje gång du besöker.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hur hanterar trafik Visa externa slutpunkter?

När du använder externa slutpunkter som finns utanför Azure-regioner i en Traffic Manager-profil kan du välja att har mappats till en Azure-region som är en proxy för egenskaper svarstid (i praktiken krävs om du använder routningsmetoden för prestanda). Om den har Azure-regionmappningen används den Azure-region latens mått när du skapar trafik visa utdata. Om ingen Azure-region anges vara latensinformation tom i data för dessa externa slutpunkter.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Behöver jag aktivera trafik vy för varje profil i min prenumeration?

Under förhandsversionen kan har trafik vyn aktiverats på en prenumerationsnivå. Som en del av förbättringar som vi gjort innan allmän tillgänglighet, kan du nu aktivera trafik visa på en profil-nivå, så att du har mer detaljerad aktiveringen av den här funktionen. Som standard inaktiveras trafik vyn för en profil.

>[!NOTE]
>Om du har aktiverat trafik visa på en prenumerationsnivå under tid som förhandsgranskning behöver du nu aktivera det igen för varje profil under den prenumerationen.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hur kan jag inaktivera trafik vyn? 
Du kan stänga av trafik vyn för alla profiler som använder portalen eller REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hur fungerar trafiken visa fakturering?

Trafik visa priser baseras på antalet datapunkter som används för att skapa utdata. För närvarande endast datatyp stöds är de frågor som tar emot din profil. Dessutom kan endast debiteras du för bearbetning som gjordes när du har trafik visningen aktiverad. Det innebär att om du aktivera trafik vy för vissa tidsperiod i en månad och inaktivera under andra tider endast datapunkter bearbetas medan du hade funktionen aktiverad antal mot fakturan.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-slutpunkter

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan jag använda Traffic Manager med slutpunkter från flera prenumerationer?

Det går inte att använda slutpunkter från flera prenumerationer med Azure Web Apps. Azure Web Apps kräver att alla anpassade domännamn som används med Web Apps används endast inom en enda prenumeration. Det går inte att använda Web Apps från flera prenumerationer med samma domännamn.

För andra typer av slutpunkter är det möjligt att använda Traffic Manager med slutpunkter från mer än en prenumeration. I Resource Manager från någon prenumeration kan att lägga till slutpunkter i Traffic Manager så länge den person som konfigurerar Traffic Manager-profilen har läsbehörighet till slutpunkten. Dessa behörigheter kan beviljas med [Azure Resource Manager rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/role-assignments-portal.md).


### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan jag använda Traffic Manager med Molntjänsten ”Staging” platser?

Ja. Molntjänsten mellanlagring distributionsplatser kan konfigureras i Traffic Manager som externa slutpunkter. Hälsokontroller debiteras fortfarande i Azure-slutpunkter takt. Eftersom den externa slutpunkten används, fångas ändringar i underliggande inte upp automatiskt. Med externa slutpunkter kan inte Traffic Manager identifiera när Molntjänsten har stoppats eller tagits bort. Traffic Manager fortsätter därför fakturering hälsokontroller tills slutpunkten är inaktiverad eller borttagen.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Traffic Manager har stöd för IPv6-slutpunkter?

Traffic Manager ger inte någon IPv6-addressible namnservrar för närvarande. Traffic Manager kan dock fortfarande användas av IPv6-klienter som ansluter till IPv6-slutpunkter. En klient gör inte DNS-begäranden direkt till Trafikhanterarprofilen. I stället använder klienten en rekursiv DNS-tjänsten. En endast-IPv6-klient skickar begäranden till den rekursiva DNS-tjänsten via IPv6. Sedan ska rekursiv tjänsten kunna kontakta Traffic Manager-namnservrar med IPv4.

Traffic Manager svarar med DNS-namnet på slutpunkten. En DNS AAAA-post som pekar på DNS-namnet på slutpunkten på IPv6-adressen måste finnas för att stödja en IPv6-slutpunkt. Traffic Manager-hälsokontroller stöder endast IPv4-adresser. Tjänsten måste exponera en IPv4-slutpunkt på samma DNS-namn.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan jag använda Traffic Manager för mer än en Webbapp i samma region?

Traffic Manager används vanligtvis för att dirigera trafik till program som distribueras i olika regioner. Men kan det också användas om ett program har mer än en distribution i samma region. Azure Traffic Manager-slutpunkter tillåter inte mer än en slutpunkt för webb-App från samma Azure-region som ska läggas till samma Traffic Manager-profilen.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group"></a>Hur flytta mina Traffic Manager-profilen Azure-slutpunkter till en annan resursgrupp?

Azure-slutpunkter som är associerade med en trafikhanterarprofil spåras med hjälp av sina resurs-ID. När en Azure-resurs som används som en slutpunkt (t.ex, offentliga IP-klassiska Cloud Service, WebApp eller en annan Traffic Manager-profilen som används i en kapslad sätt) flyttas till en annan resursgrupp dess resurs-ID ändringar. I det här scenariot för närvarande måste du uppdatera Traffic Manager-profilen genom att först ta bort och sedan lägga till tillbaka slutpunkter i profilen. 

##  <a name="traffic-manager-endpoint-monitoring"></a>Slutpunktsövervakning av Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Är Traffic Manager motståndskraftiga mot Azure-region fel?

Traffic Manager är en viktig del av leverans av hög tillgänglighet program i Azure.
För att ge hög tillgänglighet måste Traffic Manager har en ovanligt hög tillgänglighet och vara motståndskraftiga mot regionala fel.

Traffic Manager-komponenter är motståndskraftiga mot ett fullständigt fel av någon Azure-region avsiktligt. Den här återhämtning gäller alla Traffic Manager-komponenter: DNS-namn-servrar, API: et, lagringsskiktet och slutpunkten övervakningstjänsten.

I osannolika ett avbrott i en hela Azure-region förväntas Traffic Manager fungerar normalt. Program som distribueras i flera Azure-regioner kan förlita sig på Traffic Manager att dirigera trafik till en tillgänglig instans av sina program.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Hur påverkar valet av resursgruppens plats Traffic Manager?

Traffic Manager är en global tjänst. Det är inte regional. Valet av resursgruppens plats spelar ingen roll till Traffic Manager-profiler som distribuerats i resursgruppen.

Azure Resource Manager kräver att alla resursgrupper att ange en plats, som anger standardplatsen för resurser har distribuerats i resursgruppen. När du skapar en Traffic Manager-profil skapas den i en resursgrupp. Alla Traffic Manager-profiler som använder **globala** som deras plats, åsidosätta standardvärdet resurs grupp.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hur tar jag reda på det aktuella hälsotillståndet för varje slutpunkt?

Den aktuella övervakning statusen för varje slutpunkt, utöver den övergripande profilen visas i Azure-portalen. Den här informationen är också tillgängligt via trafik övervakaren [REST API](https://msdn.microsoft.com/library/azure/mt163667.aspx), [PowerShell-cmdlets](https://msdn.microsoft.com/library/mt125941.aspx), och [plattformsoberoende Azure CLI](../cli-install-nodejs.md).

Du kan också använda Azure övervakaren spårar hälsotillståndet för dina slutpunkter och finns en bild av dem. Mer information om hur du använder Azure-Monitor finns i [Azure-övervakning dokumentationen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

### <a name="can-i-monitor-https-endpoints"></a>Övervakar jag HTTPS-slutpunkter

Ja. Traffic Manager har stöd för sökning via HTTPS. Konfigurera **HTTPS** som protokoll i konfigurationen av övervakningen.

Traffic manager kan inte ange några certifikatvalidering inklusive:

* Serversidan certifikat verifieras inte
* SNI serversidan certifikat stöds inte
* Klientcertifikat stöds inte

### <a name="i-stopped-an-azure-cloud-service--web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Jag har stoppats en Azure-molntjänst / webb-programmet slutpunkt i Traffic Manager-profil men jag kan inte ta emot trafik även när det startas om. Hur kan jag åtgärda detta?

När en Azure cloud service / webb-programslutpunkt är stoppad Traffic Manager stoppar kontrollerar hälsotillståndet och startar om hälsokontrollerna endast när det upptäcker att slutpunkten har startats om. Om du vill förhindra att den här fördröjningen, inaktivera och återaktivera sedan denna slutpunkt i Traffic Manager-profilen när du startar om slutpunkten.   

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan jag använda Traffic Manager även om mitt program inte har stöd för HTTP eller HTTPS?

Ja. Du kan ange TCP som protokoll övervakning och Traffic Manager kan initiera en TCP-anslutning och vänta på svar från slutpunkten. Om slutpunkten svarar anslutningsbegäran med ett svar för att upprätta en anslutning inom den angivna tiden markeras den slutpunkten som felfri.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Vilka specifika svar krävs slutpunkten när du använder TCP övervakning?

När TCP-övervakning används startar Traffic Manager en trevägs TCP handskakning genom att skicka en begäran om SYN till slutpunkt på den angivna porten. Det väntar sedan en viss tidsperiod (som anges i timeout-inställningar) på ett svar från slutpunkten. Om slutpunkten svarar på SYN begäran med ett SYN ACK-svar inom tidsgränsen som anges i inställningarna för övervakning, sedan anses denna slutpunkt felfritt. Om SYN ACK-svar tas emot, återställer anslutningen genom att svara igen med en RST Traffic Manager.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hur snabbt flytta mina användare från en ohälsosamt slutpunkt i Traffic Manager?

Traffic Manager innehåller flera inställningar som kan hjälpa dig att styra redundans Traffic Manager-profilen på följande sätt:
- Du kan ange att Traffic Manager-avsökningar slutpunkterna oftare genom att ställa in intervall för sökning efter 10 sekunder. Detta säkerställer att alla slutpunkten ska feltillstånd kan identifieras så snart som möjligt. 
- Du kan ange hur länge ska vänta innan ett hälsotillstånd kolla begäran gånger (lägsta timeout-värdet är 5 sek).
- Du kan ange hur många fel kan uppstå innan slutpunkten markeras som ohälsosam. Det här värdet kan vara låg som 0, där fallet slutpunkten är markerat feltillstånd när den första hälsokontrollen misslyckas. Med det lägsta värdet 0 för tillåten antalet fel kan dock leda till slutpunkter som ska tas bort från rotationen på grund av tillfälliga problem som kan uppstå vid tidpunkten för sökning.
- Du kan ange den time to live (TTL) för DNS-svaret ska vara så lågt som 0. Om du gör det innebär att DNS-matchare kan inte cachelagrar svaret, och varje ny fråga hämtar ett svar som innehåller den allra senaste informationen för hälsotillstånd med Traffic Manager.

Med hjälp av de här inställningarna ge Traffic Manager redundans under 10 sekunder efter att en slutpunkt blir ohälsosamt och en DNS-fråga görs mot motsvarande profil.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hur kan jag ange olika övervakningsinställningarna för olika slutpunkter i en profil?

Traffic Manager övervakningsinställningarna finns på en per profil nivå. Om du behöver använda en annan övervakning inställning för endast en slutpunkt kan också göra det genom att låta den slutpunkten som en [kapslad profil](traffic-manager-nested-profiles.md) vars övervakningsinställningarna skiljer sig från den överordnade profilen.

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Vilka värden huvud gör endpoint kontroll används?

Traffic Manager använder värdhuvuden hälsokontroller för HTTP och HTTPS. Värdadressen används av Traffic Manager är namnet på slutpunkten mål som konfigurerats i profilen. Det värde som används i värdhuvudet kan inte anges separat från egenskapen target.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Vilka är de IP-adresser som kontrollerar hälsotillståndet kommer?

Klicka på [här](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json) att visa JSON-fil som listar IP-adresser från vilka Traffic Manager hälsokontroller kan kommer. Granska IP-adresser som anges i JSON-filen så att inkommande anslutningar från dessa IP-adresser tillåts på slutpunkterna kontrollera dess hälsostatus.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hur många hälsokontroller till min slutpunkten ges från Traffic Manager?

Antalet Traffic Manager hälsa kontrollerar nå slutpunkten beror på följande:
- Det värde som du har angett för Övervakningsintervall (mindre intervall innebär fler begäranden hamnar på din slutpunkt i en given tidsperiod).
- antalet platser från där hälsotillståndet kontrollerar kommer (IP-adresser från där du kan förvänta dig kontrollerna visas i föregående FAQ).

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager kapslade profiler

### <a name="how-do-i-configure-nested-profiles"></a>Hur konfigurerar kapslade profiler?

Kapslade Traffic Manager-profiler kan konfigureras med både Azure Resource Manager och den klassiska Azure REST API: er, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI-kommandona. De stöds också via den nya Azure-portalen.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Stöd för hur många lager med kapsling har Traffic Manager?

Du kan kapsla profiler upp till 10 nivåer. 'Slinga' tillåts inte.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan jag blanda andra typer av slutpunkter med kapslad underordnad profiler i samma Traffic Manager-profilen?

Ja. Det finns inga begränsningar för hur du kombinerar slutpunkter av olika typer i en profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hur gäller vilken faktureringsmodell som tillämpas för kapslade profiler?

Det är inte negativt priser effekten av att använda kapslade profiler.

Traffic Manager fakturering består av två komponenter: slutpunkt hälsokontroller och miljontals DNS-frågor

* Slutpunkten hälsokontroller: är gratis för en underordnad profil när konfigurerad som en slutpunkt i en överordnad-profil. Övervakning av slutpunkter i profilen för underordnade faktureras som vanligt.
* DNS-frågor: varje fråga räknas endast en gång. En fråga till en överordnad-profil som returnerar en slutpunkt från en underordnad profil är räknas av mot den överordnade profilen.

Fullständig information finns i [Traffic Manager sida med priser](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Finns det en prestandapåverkan för kapslade profiler?

Nej. Det finns ingen inverkan på prestanda när med hjälp av kapslade profiler.

Traffic Manager-namnservrar passerar internt profil hierarkin vid bearbetning av varje DNS-fråga. En DNS-fråga till en överordnad-profil kan ta emot ett DNS-svar till en slutpunkt från en underordnad profil. En CNAME-post används om du använder en enskild profil eller kapslade profiler. Det finns inget behov av att skapa en CNAME-post för varje profil i hierarkin.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hur beräkna hälsotillståndet för en kapslad slutpunkt i en överordnad profil i Traffic Manager?

Den överordnade profilen utföra inte hälsokontroller på underordnade direkt. Hälsotillståndet för den underordnade profilen slutpunkter används i stället för att beräkna den övergripande hälsan för den underordnade profilen. Den här informationen sprids uppåt i hierarkin för kapslade profiler fastställa hälsotillståndet för den kapslade slutpunkten. Överordnad profilen använder denna sammanställda hälsa för att fastställa om trafiken dirigeras till underordnat.

I följande tabell beskrivs beteendet för Traffic Manager hälsa söker efter en kapslad slutpunkt.

| Status för underordnade profilen Övervakare | Överordnad Övervakare för slutpunkten status | Anteckningar |
| --- | --- | --- |
| Inaktiverad. Den underordnade profilen har inaktiverats. |Stoppad |Det överordnade slutpunktstillståndet stoppas inte har inaktiverats. Inaktiverat tillstånd är reserverat för som anger att du har inaktiverat slutpunkten i profilen för överordnade. |
| Försämrad. Minst en underordnad-profilens slutpunkt har statusen degraderad. |Online: antalet Online slutpunkter i profilen för underordnade är minst värdet för MinChildEndpoints.<BR>CheckingEndpoint: antalet Online plus CheckingEndpoint slutpunkter i profilen för underordnade är minst värdet för MinChildEndpoints.<BR>Försämrad: annars. |Trafik dirigeras till en slutpunkt av status CheckingEndpoint. Om MinChildEndpoints är för högt kan försämras alltid slutpunkten. |
| Online. Minst en underordnad-profilens slutpunkt är ett onlinetillstånd. Ingen slutpunkt har statusen degraderad. |Se ovan. | |
| CheckingEndpoints. Minst en underordnad-profilens slutpunkt är 'CheckingEndpoint'. Inga slutpunkter är 'Online' eller 'Försämrad' |Samma som ovan. | |
| Inaktiva. Alla underordnade profilen slutpunkterna är inaktiverad eller stoppad eller den här profilen har inga slutpunkter. |Stoppad | |

## <a name="next-steps"></a>Nästa steg:
- Lär dig mer om Traffic Manager [endpoint övervaknings- och automatisk redundans](../traffic-manager/traffic-manager-monitoring.md).
- Lär dig mer om Traffic Manager [trafikroutningsmetoder](../traffic-manager/traffic-manager-routing-methods.md).
