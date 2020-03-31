---
title: Azure Traffic Manager - trafikroutningsmetoder
description: Den här artikeln hjälper dig att förstå de olika trafikroutningsmetoder som används av Traffic Manager
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 4a035506943eeffa2c3fc4fec27c47da4136683b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250912"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager-dirigeringsmetoder

Azure Traffic Manager stöder sex trafikdirigeringsmetoder för att avgöra hur nätverkstrafik ska dirigeras till de olika tjänstslutpunkterna. För alla profiler tillämpar Traffic Manager den trafikroutningsmetod som är kopplad till den för varje DNS-fråga som tas emot. Trafikroutningsmetoden avgör vilken slutpunkt som returneras i DNS-svaret.

Följande trafikroutningsmetoder är tillgängliga i Traffic Manager:

* **[Prioritet:](#priority-traffic-routing-method)** Välj **Prioritet** när du vill använda en primär tjänstslutpunkt för all trafik och tillhandahålla säkerhetskopior om de primära slutpunkterna för säkerhetskopiering eller säkerhetskopiering inte är tillgängliga.
* **[Viktad:](#weighted)** Välj **Viktad** när du vill distribuera trafik över en uppsättning slutpunkter, antingen jämnt eller enligt vikter, som du definierar.
* **[Prestanda:](#performance)** Välj **prestanda** när du har slutpunkter på olika geografiska platser och du vill att slutanvändarna ska använda slutpunkten "närmaste" när det gäller den lägsta nätverksfördröjningen.
* **[Geografisk](#geographic):** Välj **Geografiskt** så att användarna dirigeras till specifika slutpunkter (Azure, Extern eller Kapslad) baserat på vilken geografisk plats deras DNS-fråga kommer från. Detta ger Traffic Manager-kunder möjlighet att aktivera scenarier där det är viktigt att känna till en användares geografiska region och dirigera dem baserat på det. Exempel på detta är att följa datasuveränitetsmandat, lokalisering av innehåll & användarupplevelsen och mätning av trafik från olika regioner.
* **[Multivärde:](#multivalue)** Välj **MultiValue** för Traffic Manager-profiler som bara kan ha IPv4/IPv6-adresser som slutpunkter. När en fråga tas emot för den här profilen returneras alla felfria slutpunkter.
* **[Undernät](#subnet):** Välj Trafikroutningsmetod **i undernät** om du vill mappa uppsättningar med IP-adressintervall för slutanvändare till en viss slutpunkt i en Traffic Manager-profil. När en begäran tas emot är slutpunkten som returneras den som mappas för begärans källa IP-adress. 


Alla Traffic Manager-profiler omfattar övervakning av slutpunktshälsa och automatisk redundans för slutpunkter. Mer information finns i [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). En enda Traffic Manager-profil kan bara använda en trafikroutningsmetod. Du kan välja en annan trafikroutningsmetod för din profil när som helst. Ändringar tillämpas inom en minut och inga driftstopp uppstår. Trafikdirigeringsmetoder kan kombineras med hjälp av kapslade Traffic Manager-profiler. Kapsling möjliggör sofistikerade och flexibla trafikdirigeringskonfigurationer som uppfyller behoven hos större, komplexa program. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Metod för prioriterad trafikdirigering

Ofta vill en organisation tillhandahålla tillförlitlighet för sina tjänster genom att distribuera en eller flera säkerhetskopieringstjänster om deras primära tjänst går ner. Med trafikroutningsmetoden Prioritet kan Azure-kunder enkelt implementera det här redundansmönstret.

![Azure Traffic Manager 'Prioritet' trafik-routning metod](media/traffic-manager-routing-methods/priority.png)

Traffic Manager-profilen innehåller en prioriterad lista över tjänstslutpunkter. Som standard skickar Traffic Manager all trafik till den primära slutpunkten (högsta prioritet). Om den primära slutpunkten inte är tillgänglig dirigerar Traffic Manager trafiken till den andra slutpunkten. Om både den primära och den sekundära slutpunkter är otillgängliga går trafiken till den tredje och så vidare. Tillgängligheten för slutpunkten baseras på den konfigurerade statusen (aktiverad eller inaktiverad) samt pågående slutpunktsövervakning.

### <a name="configuring-endpoints"></a>Konfigurera slutpunkter

Med Azure Resource Manager konfigurerar du slutpunktsprioriteten uttryckligen med egenskapen "prioritet" för varje slutpunkt. Den här egenskapen är ett värde mellan 1 och 1000. Lägre värden representerar en högre prioritet. Slutpunkter kan inte dela prioritetsvärden. Det är valfritt att ange egenskapen. När den utelämnas används en standardprioritet som baseras på slutpunktsordningen.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metod för viktad trafikdirigering
Med trafikroutningsmetoden "Viktad" kan du fördela trafiken jämnt eller använda en fördefinierad viktning.

![Azure Traffic Manager 'Viktad" trafik-routning metod](media/traffic-manager-routing-methods/weighted.png)

I metoden Viktad trafikdirigering tilldelar du en vikt till varje slutpunkt i Traffic Manager-profilkonfigurationen. Vikten är ett heltal mellan 1 och 1 000. Den här parametern är valfri. Om det utelämnas använder trafikansvariga en standardvikt på "1". Ju högre vikt, desto högre prioritet.

För varje DNS-fråga som tas emot väljer Traffic Manager slumpmässigt en tillgänglig slutpunkt. Sannolikheten för att välja en slutpunkt baseras på de vikter som tilldelats till alla tillgängliga slutpunkter. Om du använder samma vikt för alla slutpunkter resulterar det i en jämn trafikfördelning. Om du använder högre eller lägre vikter på specifika slutpunkter returneras dessa slutpunkter mer eller mindre ofta i DNS-svaren.

Den viktade metoden möjliggör några användbara scenarier:

* Gradvis programuppgradering: Allokera en procentandel av trafiken för att dirigera till en ny slutpunkt och gradvis öka trafiken över tid till 100 %.
* Programmigrering till Azure: Skapa en profil med både Azure och externa slutpunkter. Justera slutpunkternas vikt så att de vill de nya slutpunkterna.
* Cloud-bursting för ytterligare kapacitet: Expandera snabbt en lokal distribution i molnet genom att lägga den bakom en Traffic Manager-profil. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera fler slutpunkter och ange vilken del av trafiken som går till varje slutpunkt.

Förutom att använda Azure-portalen kan du konfigurera vikter med Azure PowerShell, CLI och REST API:er.

Det är viktigt att förstå att DNS-svar cachelagras av klienter och av de rekursiva DNS-servrar som klienterna använder för att matcha DNS-namn. Den här cachelagringen kan påverka viktade trafikdistributioner. När antalet klienter och rekursiva DNS-servrar är stort fungerar trafikdistributionen som förväntat. Men när antalet klienter eller rekursiva DNS-servrar är liten, kan cachelagring avsevärt skeva trafikdistributionen.

Vanliga användningsfall är:

* Utvecklings- och testmiljöer
* Kommunikation mellan program och program
* Program som riktar sig till en smal användarbas som delar en gemensam rekursiv DNS-infrastruktur (till exempel anställda i företag som ansluter via en proxy)

Dessa DNS-cachelagringseffekter är gemensamma för alla DNS-baserade trafikroutningssystem, inte bara Azure Traffic Manager. I vissa fall kan det vara en lösning att uttryckligen rensa DNS-cachen. I andra fall kan en alternativ trafikroutningsmetod vara lämpligare.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Prestanda trafik-routing metod

Distribuera slutpunkter på två eller flera platser över hela världen kan förbättra svarstiden för många program genom att dirigera trafik till den plats som är "närmast" till dig. Trafikroutningsmetoden "Prestanda" ger den här funktionen.

![Trafikroutningsmetoden för Azure Traffic Manager](media/traffic-manager-routing-methods/performance.png)

Slutpunkten "närmaste" är inte nödvändigtvis närmast mätt med geografiskt avstånd. I stället bestämmer trafikdirigeringsmetoden "Prestanda" den närmaste slutpunkten genom att mäta nätverksfördröjningen. Traffic Manager underhåller en Internet Latency Tabell för att spåra rundresa tid mellan IP-adressintervall och varje Azure datacenter.

Traffic Manager slår upp käll-IP-adressen för den inkommande DNS-begäran i tabellen Internet-svarstid. Traffic Manager väljer sedan en tillgänglig slutpunkt i Azure-datacentret som har den lägsta svarstiden för det IP-adressintervallet och returnerar slutpunkten i DNS-svaret.

Som förklaras i [Hur Traffic Manager Works](traffic-manager-how-it-works.md)får Traffic Manager inte DNS-frågor direkt från klienter. Dns-frågor kommer snarare från den rekursiva DNS-tjänst som klienterna är konfigurerade att använda. Därför är IP-adressen som används för att fastställa den "närmaste" slutpunkten inte klientens IP-adress, men det är IP-adressen för den rekursiva DNS-tjänsten. I praktiken är den här IP-adressen en bra proxy för klienten.


Traffic Manager uppdaterar regelbundet internetfördröjningstabellen för att ta hänsyn till ändringar i det globala Internet och nya Azure-regioner. Programprestanda varierar dock beroende på variationer i realtid i belastningen över Internet. Prestandatrafikdirigering övervakar inte belastningen på en viss tjänstslutpunkt. Men om en slutpunkt blir otillgänglig, inkluderar Traffic Manager inte den i DNS-frågesvar.


Punkter att notera:

* Om din profil innehåller flera slutpunkter i samma Azure-region distribuerar Traffic Manager trafiken jämnt över tillgängliga slutpunkter i den regionen. Om du föredrar en annan trafikfördelning inom en region kan du använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* Om alla aktiverade slutpunkter i närmaste Azure-region försämras flyttar Traffic Manager trafik till slutpunkterna i nästa närmaste Azure-region. Om du vill definiera en önskad redundanssekvens använder du [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* När du använder routningsmetoden Prestanda trafik med externa slutpunkter eller kapslade slutpunkter måste du ange platsen för dessa slutpunkter. Välj den Azure-region som är närmast distributionen. Dessa platser är de värden som stöds av internetfördröjningstabellen.
* Algoritmen som väljer slutpunkten är deterministisk. Upprepade DNS-frågor från samma klient dirigeras till samma slutpunkt. Vanligtvis använder klienter olika rekursiva DNS-servrar när de reser. Klienten kan dirigeras till en annan slutpunkt. Routning kan också påverkas av uppdateringar av tabellen för internetfördröjning. Därför garanterar metoden För trafikdirigering av prestanda inte att en klient alltid dirigeras till samma slutpunkt.
* När Internet-svarstidstabellen ändras kanske du märker att vissa klienter dirigeras till en annan slutpunkt. Den här routningsändringen är mer exakt baserat på aktuella svarstidsdata. Dessa uppdateringar är nödvändiga för att upprätthålla noggrannheten i trafikroutning av prestanda när Internet ständigt utvecklas.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Geografisk trafikdirigeringsmetod

Traffic Manager-profiler kan konfigureras för att använda den geografiska routningsmetoden så att användare dirigeras till specifika slutpunkter (Azure, extern eller kapslad) baserat på vilken geografisk plats deras DNS-fråga kommer från. Detta ger Traffic Manager-kunder möjlighet att aktivera scenarier där det är viktigt att känna till en användares geografiska region och dirigera dem baserat på det. Exempel på detta är att följa datasuveränitetsmandat, lokalisering av innehåll & användarupplevelsen och mätning av trafik från olika regioner.
När en profil har konfigurerats för geografisk routning måste varje slutpunkt som är associerad med den profilen ha en uppsättning geografiska regioner tilldelade till den. Ett geografiskt område kan ligga på följande nivåer av granularitet 
- Världen – vilken region som helst
- Regional gruppering – till exempel Afrika, Mellanöstern, Australien/Stillahavsområdet etc. 
- Land/region – till exempel Irland, Peru, Hongkong sar etc. 
- Stat / provins - till exempel USA-Kalifornien, Australien-Queensland, Kanada-Alberta etc. (obs: denna granularitet nivå stöds endast för stater / provinser i Australien, Kanada och USA).

När en region eller en uppsättning regioner tilldelas en slutpunkt dirigeras alla begäranden från dessa regioner endast till den slutpunkten. Traffic Manager använder källan IP-adressen för DNS-frågan för att avgöra vilken region som en användare frågar från - vanligtvis är detta IP-adressen för den lokala DNS-matcharen som gör frågan för användarens räkning.  

![Azure Traffic Manager 'Geografisk" trafik-routning metod](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager läser källans IP-adress för DNS-frågan och bestämmer vilken geografisk region den kommer från. Den ser sedan ut att se om det finns en slutpunkt som har den här geografiska regionen mappad till den. Denna sökning börjar på den lägsta granularitetsnivå (stat / provins där det stöds, annars på land / region nivå) och går hela vägen upp till den högsta nivån, som är **världen**. Den första matchningen som hittades med den här traversalen anges som slutpunkt för att returnera i frågesvaret. När du matchar med en kapslad typslutpunkt returneras en slutpunkt i den underordnade profilen, baserat på dess routningsmetod. Följande punkter är tillämpliga på detta:

- En geografisk region kan bara mappas till en slutpunkt i en Traffic Manager-profil när routningstypen är geografisk routning. Detta säkerställer att routning av användare är deterministisk och kunder kan aktivera scenarier som kräver entydiga geografiska gränser.
- Om en användares region hamnar under två olika slutpunkters geografiska mappning väljer Traffic Manager slutpunkten med den lägsta granulariteten och överväger inte att dirigera begäranden från den regionen till den andra slutpunkten. Tänk dig till exempel en geografisk routningstypprofil med två slutpunkter - Slutpunkt1 och Slutpunkt2. Endpoint1 är konfigurerad för att ta emot trafik från Irland och Endpoint2 är konfigurerad för att ta emot trafik från Europa. Om en begäran kommer från Irland dirigeras den alltid till Endpoint1.
- Eftersom en region bara kan mappas till en slutpunkt returnerar Traffic Manager den oavsett om slutpunkten är felfri eller inte.

    >[!IMPORTANT]
    >Vi rekommenderar starkt att kunder som använder den geografiska routningsmetoden associerar den med slutpunkterna För kapslad typ som har underordnade profiler som innehåller minst två slutpunkter inom varje.
- Om en slutpunktsmatchning hittas och slutpunkten är i **tillståndet Stoppad** returnerar Traffic Manager ett NODATA-svar. I det här fallet görs inga ytterligare uppslag högre upp i den geografiska regionhierarkin. Det här beteendet gäller även för kapslade slutpunktstyper när den underordnade profilen är i tillståndet **Stoppad** eller **inaktiverad.**
- Om en slutpunkt visar en **inaktiverad** status inkluderas den inte i regionmatchningsprocessen. Detta gäller även för kapslade slutpunktstyper när slutpunkten är i **inaktiverat** tillstånd.
- Om en fråga kommer från ett geografiskt område som inte har någon mappning i den profilen returnerar Traffic Manager ett NODATA-svar. Därför rekommenderas starkt att kunder använder geografisk routning med en slutpunkt, helst av typen Kapslad med minst två slutpunkter i den underordnade profilen, med regionen **World** tilldelad den. Detta säkerställer också att alla IP-adresser som inte mappas till en region hanteras.

Som förklaras i [Hur Traffic Manager Works](traffic-manager-how-it-works.md)får Traffic Manager inte DNS-frågor direkt från klienter. Dns-frågor kommer snarare från den rekursiva DNS-tjänst som klienterna är konfigurerade att använda. Därför är IP-adressen som används för att bestämma regionen inte klientens IP-adress, men det är IP-adressen för den rekursiva DNS-tjänsten. I praktiken är den här IP-adressen en bra proxy för klienten.

### <a name="faqs"></a>Vanliga frågor och svar

* [Vilka är några användningsfall där geografisk routning är användbar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hur avgör jag om jag ska använda prestandaroutningsmetod eller geografisk routningsmetod?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Vilka regioner stöds av Traffic Manager för geografisk routning?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hur avgör Traffic Manager var en användare frågar ifrån?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Är det garanterat att Traffic Manager korrekt kan bestämma den exakta geografiska platsen för användaren i varje enskilt fall?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Måste en slutpunkt vara fysiskt placerad i samma region som den som konfigureras med för geografisk routning?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Kan jag tilldela geografiska regioner till slutpunkter i en profil som inte är konfigurerad för geografisk routning?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Varför får jag ett felmeddelande när jag försöker ändra routningsmetoden för en befintlig profil till Geografisk?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Varför rekommenderas det starkt att kunder skapar kapslade profiler i stället för slutpunkter under en profil med geografisk routning aktiverad?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Finns det några begränsningar för API-versionen som stöder den här routningstypen?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Flervärdestrafikroutningsmetod
Med metoden För trafikroutning med **flera** värden kan du få flera felfria slutpunkter i ett enda DNS-frågesvar. Detta gör det möjligt för anroparen att göra återförsök på klientsidan med andra slutpunkter i händelse av att en returnerad slutpunkt inte svarar. Det här mönstret kan öka tillgängligheten för en tjänst och minska den svarstid som är associerad med en ny DNS-fråga för att hämta en felfri slutpunkt. Routningsmetod för multivalue fungerar bara om alla slutpunkter av typen "Extern" och anges som IPv4- eller IPv6-adresser. När en fråga tas emot för den här profilen returneras alla felfria slutpunkter och är föremål för ett konfigurerbart maximalt avkastningsantal.

### <a name="faqs"></a>Vanliga frågor och svar

* [Vilka är några användningsfall där MultiValue-routning är användbar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hur många slutpunkter returneras när MultiValue-routning används?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Kommer jag att få samma uppsättning slutpunkter när MultiValue-routning används?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Trafikroutningsmetod för undernät
Med trafikroutningsmetoden **undernät** kan du mappa en uppsättning IP-adressintervall för slutanvändare till specifika slutpunkter i en profil. Om Traffic Manager tar emot en DNS-fråga för den profilen, kommer den att granska källans IP-adress för den begäran (i de flesta fall kommer detta att vara den utgående IP-adressen för DNS-matcharen som används av anroparen), avgöra vilken slutpunkt den är mappad till och returnerar slutpunkten i frågesvaret. 

Den IP-adress som ska mappas till en slutpunkt kan anges som CIDR-intervall (t.ex. 1.2.3.0/24) eller som ett adressintervall (t.ex. 1.2.3.4-5.6.7.8). IP-intervallen som är associerade med en slutpunkt måste vara unika i den profilen och kan inte ha en överlappning med IP-adressuppsättningen för en annan slutpunkt i samma profil.
Om du definierar en slutpunkt utan adressintervall fungerar den som en reserv och tar trafik från eventuella återstående undernät. Om ingen återgångsslutpunkt ingår skickar Traffic Manager ett NODATA-svar för odefinierade områden. Vi rekommenderar därför starkt att du antingen definierar en återgångsslutpunkt eller kontrollerar att alla möjliga IP-intervall anges över slutpunkterna.

Undernätsroutning kan användas för att ge en annan upplevelse för användare som ansluter från ett visst IP-utrymme. Med undernätsdirigering kan en kund till exempel göra så att alla begäranden från företagskontoret dirigeras till en annan slutpunkt där de kanske testar en internt exklusiv version av företages app. Ett annat scenario är om du vill ge användare som ansluter från en specifik ISP en annan upplevelse (till exempel blockera användare från en viss ISP).

### <a name="faqs"></a>Vanliga frågor och svar

* [Vilka är några användningsfall där undernätsdirigering är användbar?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Hur känner Traffic Manager till slutanvändarens IP-adress?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hur kan jag ange IP-adresser när jag använder undernätsdirigering?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hur kan jag ange en återgångsslutpunkt när jag använder undernätsroutning?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Vad händer om en slutpunkt inaktiveras i en profil för undernätsroutningstyp?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar program med hög tillgänglighet med hjälp av [Traffic Manager-slutpunktsövervakning](traffic-manager-monitoring.md)




