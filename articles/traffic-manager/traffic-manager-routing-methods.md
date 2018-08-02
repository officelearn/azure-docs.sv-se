---
title: Azure Traffic Manager - trafikroutningsmetoder | Microsoft Docs
description: Det här artiklarna hjälper dig att förstå de olika routningsmetoder som används av Traffic Manager
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: timlt
editor: ''
ms.assetid: db1efbf6-6762-4c7a-ac99-675d4eeb54d0
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2017
ms.author: kumud
ms.openlocfilehash: 03f1cc3a34fa8a472dcab9654b65cc97b8473993
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398625"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager-dirigeringsmetoder

Azure Traffic Manager stöder fyra trafikroutningsmetoder information om hur du dirigerar nätverkstrafik till olika Tjänsteslutpunkter. Traffic Manager gäller metoden routning av nätverkstrafik för varje DNS-fråga som den tar emot. Metoden routning av nätverkstrafik anger vilken slutpunkt som returneras i DNS-svaret.

Det finns fyra metoder för trafikroutning som är tillgängliga i Traffic Manager:

* **[Prioritet](#priority):** Välj **prioritet** när du vill använda en primär slutpunkt för all trafik och ge säkerhetskopiering i fall primärt eller backup-slutpunkter är otillgängliga.
* **[Viktat](#weighted):** Välj **viktat** när du vill distribuera trafik mellan en uppsättning slutpunkter, antingen jämnt eller enligt vikterna som du definierar.
* **[Prestanda](#performance):** Välj **prestanda** när du har slutpunkter på olika geografiska platser och du vill att slutanvändarna ska använda ”närmaste” slutpunkten när det gäller den lägsta Nätverksfördröjningen.
* **[Geografisk](#geographic):** Välj **geografiska** så att användare dirigeras till specifika slutpunkter (Azure, externa eller kapslade) baserat på vilka geografiska plats deras DNS-fråga som samlas in från. Detta gör det möjligt för Traffic Manager-kunder att aktivera scenarier där vetskapen om att en användares geografiska region och skicka dem baserat på detta är viktigt. Exempel: uppfyller data landsbaserad placering måste ett demonterat, lokalisering av innehåll och användardata upplevelse och mäta trafik från olika regioner.

Alla Traffic Manager-profiler innehåller övervakning av slutpunktshälsa och automatisk endpoint redundans. Mer information finns i [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). En enda Traffic Manager-profil kan använda endast en trafikroutningsmetod. Du kan välja en annan metod för trafikroutning för din profil när som helst. Ändringarna tillämpas inom en minut och utan avbrott uppstår. Trafikroutningsmetoder kan kombineras med hjälp av kapslade Traffic Manager-profiler. Kapsling möjliggör avancerade och flexibel routning av nätverkstrafik konfigurationer som uppfyller behoven hos större, komplexa program. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Prioritet trafikdirigeringsmetoden

Ofta vill en organisation att tillhandahålla tillförlitlighet för sina tjänster genom att distribuera en eller flera säkerhetskopierade tjänster om deras primära tjänst slutar fungera. ”Prioritet” trafikdirigeringsmetoden kan Azure-kunder enkelt kan implementera det här mönstret för redundans.

![Azure Traffic Manager ”prioritet” trafikdirigeringsmetoden][1]

Traffic Manager-profilen innehåller en prioriterad lista med Tjänsteslutpunkter. Traffic Manager skickar all trafik till den primära slutpunkten (högsta prioritet) som standard. Om den primära slutpunkten inte är tillgänglig, dirigerar Traffic Manager trafiken till den andra slutpunkten. Om både de primära och sekundära slutpunkterna inte är tillgängliga går trafiken till den tredje och så vidare. Tillgängligheten för slutpunkten som baseras på den konfigurerade status (aktiverad eller inaktiverad) och pågående slutpunktsövervakning.

### <a name="configuring-endpoints"></a>Konfigurera slutpunkter

Med Azure Resource Manager kan du konfigurera slutpunktsprioritet uttryckligen med hjälp av egenskapen ”prioritet” för varje slutpunkt. Den här egenskapen är ett värde mellan 1 och 1000. Lägre värden motsvarar högre prioritet. Slutpunkter kan inte dela prioritetsvärdet. Egenskapen är valfri. När det utelämnas används en standardprioritet baserat på den slutpunkt för.

##<a name = "weighted"></a>Viktad trafikdirigeringsmetoden
Metoden 'Viktad' routning av nätverkstrafik kan du vill distribuera trafiken jämnt eller använda en fördefinierad värde.

![Azure Traffic Manager-viktad' trafikdirigeringsmetoden][2]

I viktad routning av nätverkstrafik metoden tilldelar du en vikt till varje slutpunkt i Traffic Manager profilkonfigurationen. Vikten som är ett heltal mellan 1 och 1000. Den här parametern är valfri. Om det utelämnas används en standard-vikt på ”1” i Traffic Manager-hanterare. Högre vikt, desto högre prioritet.

För varje DNS-fråga som tog emot väljer slumpmässigt en tillgänglig slutpunkt i Traffic Manager. Sannolikheten för att välja en slutpunkt är baserad på vikterna som tilldelats till alla tillgängliga slutpunkter. Med hjälp av samma vikt för alla slutpunkter resultat i en jämn trafikfördelning. Med högre eller lägre vikterna på specifika slutpunkter gör de slutpunkterna som ska returneras oftare eller mer sällan i DNS-svar.

Metoden viktad aktiverar vissa användbara scenarier:

* Gradvis Programuppgradering: tilldela en procentandel av trafik för dirigering till en ny slutpunkt och gradvis öka trafiken över tid till 100%.
* Program-migreringen till Azure: skapa en profil med både Azure och externa slutpunkter. Justera vikten för slutpunkter som föredrar de nya slutpunkterna.
* ”Cloud bursting” för ytterligare kapacitet: snabbt utöka en lokal distribution till molnet genom att placera det bakom en Traffic Manager-profil. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera flera slutpunkter och anger vilken del av trafiken går till varje slutpunkt.

Azure Resource Manager-portalen stöder konfiguration av Routning av viktad nätverkstrafik.  Du kan konfigurera vikterna med hjälp av Resource Manager-versioner av Azure PowerShell, CLI och REST-API: er.

Det är viktigt att förstå att DNS-svar cachelagras av klienter och de rekursiva DNS-servrar som klienterna använder för att matcha DNS-namn. Den här typen av cachelagring kan påverka på viktad trafik-distributioner. När antalet klienter och rekursiva DNS-servrar är stor, fungerar fördelning av trafik som förväntat. Dock när antalet klienter eller rekursiva DNS-servrar är liten kan cachelagring avsevärt förskjuta fördelning av trafik.

Vanliga användningsområden är:

* Utvecklings- och testmiljöer
* Program kommunikation
* Program som riktar sig till en smal-användarbas som delar en gemensam rekursiv DNS-infrastruktur (till exempel anställda på företag som ansluter via en proxyserver)

Dessa DNS-cachelagring effekter är gemensamma för alla DNS-baserad trafik routning system, inte bara Azure Traffic Manager. I vissa fall kan tillhandahåller uttryckligen rensa cacheminnet för DNS en lösning. I andra fall kan det vara mer effektivt att ange en alternativ metod för routning av nätverkstrafik.

## <a name = "performance"></a>Trafikdirigeringsmetoden för prestanda

Distribuera slutpunkter i två eller flera platser i hela världen kan förbättra svarstiden för många program genom att dirigera trafiken till den plats som är närmast dig. Trafikdirigeringsmetoden ”Performance” innehåller den här funktionen.

![Azure Traffic Manager ”Performance” trafikdirigeringsmetoden][3]

”Närmaste' slutpunkt är inte nödvändigtvis närmaste enligt geografisk avstånd. I stället anger trafikdirigeringsmetoden ”Performance” närmaste slutpunkten genom att mäta svarstiden i nätverket. Traffic Manager har en tabell med Internet svarstid för att spåra mäts tiden mellan IP-adressintervall och varje Azure-datacenter.

Traffic Manager söker upp källans IP-adress för DNS-inkommande begäran i tabellen Internet svarstid. Traffic Manager väljer en tillgänglig slutpunkt i Azure-datacentret som har den lägsta svarstiden för det IP-adressintervallet, så returnerar den slutpunkten i DNS-svaret.

Enligt beskrivningen i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md), Traffic Manager tar inte emot DNS-frågor direkt från klienter. I stället DNS-frågor kommer från rekursiv DNS-tjänsten att klienterna är konfigurerade för att använda. Därför IP-adressen används för att fastställa 'närmaste' slutpunkt är inte klientens IP-adress, men det är IP-adressen för rekursiv DNS-tjänst. I praktiken är bra proxy för klienten i den här IP-adress.


Traffic Manager uppdaterar regelbundet tabellen Internet svarstid för ändringar i globala Internet och nya Azure-regioner. Programmets prestanda varierar dock beroende på i realtid variationer i belastningen över Internet. Prestanda-trafikroutning övervakar inte belastningen på en viss slutpunkt. Men om en slutpunkt blir otillgänglig, ingår Traffic Manager inte i DNS-svar på frågor.


Saker att Observera:

* Om din profil innehåller flera slutpunkter i samma Azure-region, sedan distribuerar Traffic Manager trafiken jämnt mellan de tillgängliga slutpunkterna i den regionen. Om du föredrar en annan trafikfördelning inom en region, kan du använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* Om alla aktiverade slutpunkter i den närmaste Azure-regionen är försämrade flyttar Traffic Manager trafik till slutpunkterna i nästa närmsta Azure-region. Om du vill definiera en önskad redundans-sekvens, Använd [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* När du använder trafikroutningsmetod för prestanda med externa slutpunkter eller kapslade slutpunkter kan behöva du ange platsen för dessa slutpunkter. Välj Azure-regionen närmast din distribution. Dessa platser är de värden som stöds av tabellen Internet svarstid.
* Den algoritm som väljer slutpunkten är deterministisk. Upprepad DNS-frågor från samma klient dirigeras till samma slutpunkt. Klienter använder vanligtvis olika rekursiva DNS-servrar resan. Klienten kan dirigeras till en annan slutpunkt. Routning kan också påverkas av uppdateringar till tabellen Internet svarstid. Därför garanterar inte prestanda trafikdirigeringsmetoden att en klient alltid dirigeras till samma slutpunkt.
* När Internet svarstid tabellen ändras, kanske du märker att vissa klienter dirigeras till en annan slutpunkt. Den här routningsändring är mer exakta baserat på aktuella svarstidsdata. De här uppdateringarna är nödvändiga för att behålla det arbete du utfört prestanda-trafikroutning Internet ständigt utvecklas.

## <a name = "geographic"></a>Geografisk trafikdirigeringsmetoden

Traffic Manager-profiler kan konfigureras för att använda geografisk routningsmetod så att användare dirigeras till specifika slutpunkter (Azure, externa och kapslade) baserat på vilka geografiska plats deras DNS-fråga som samlas in från. Detta gör det möjligt för Traffic Manager-kunder att aktivera scenarier där vetskapen om att en användares geografiska region och skicka dem baserat på detta är viktigt. Exempel: uppfyller data landsbaserad placering måste ett demonterat, lokalisering av innehåll och användardata upplevelse och mäta trafik från olika regioner.
När en profil har konfigurerats för geografisk routning för varje slutpunkt som associeras med att profilen måste ha en uppsättning geografiska områden som är tilldelade till den. Ett geografiskt område kan vara i följande detaljnivåer 
- Världen – valfri region
- Regional gruppering – till exempel Afrika, Mellanöstern, Australien/Pacific osv. 
- Land/Region – till exempel Irland, Peru, Hongkong SAR osv. 
- Stat/provins – till exempel USA Kalifornien, Australien-Queensland, Kanada Alberta etc. (Obs: den här filnivå stöds endast för tillstånd / regioner i Australien, Kanada, Storbritannien och USA).

När en region eller en uppsättning regioner tilldelas till en slutpunkt, dirigeras förfrågningar från dessa regioner endast till denna slutpunkt. Traffic Manager använder källans IP-adress för DNS-fråga för att bestämma en region som en användare frågor från – detta är vanligtvis IP-adressen för den lokala DNS-matcharen göra frågan användarens räkning.  

![Azure Traffic Manager, geografisk' trafikdirigeringsmetoden](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager läser källans IP-adress för DNS-frågan och avgör vilka geografiska region som den kommer från. Den söker sedan för att se om det finns en slutpunkt som har den här geografiska region som mappats till den. Den här sökningen startas på den lägsta Granulariteten (stat/provins där den stöds, annars på nivån Land/Region) och börjar ända upp till den högsta nivån, vilket är **världen**. Första matchning hittades med den här edge traversal är utsedd till slutpunkten för att returnera i svaret på frågan. När matchning med en slutpunkt för kapslade typ, en slutpunkt i den underordnade profilen returneras, baserat på dess routningsmetod. Följande punkter gäller för det här beteendet:

- Ett geografiskt område kan endast mappas till en slutpunkt i en Traffic Manager-profil när routningstyp som är geografisk routning. Detta säkerställer att routning av användare är deterministisk och kunder kan aktivera scenarier som kräver entydiga geografiska gränser.
- Om en användares region kommer under två olika slutpunkter geografiska mappning, Traffic Manager markerar slutpunkten med den lägsta Granulariteten och beaktar inte dirigering av begäranden från den regionen till den andra slutpunkten. Anta exempelvis att en geografisk routning typ-profil med två slutpunkter - slutpunkt 1 och Endpoint2. Slutpunkt 1 är konfigurerad för att ta emot trafik från Irland och Endpoint2 är konfigurerad för att ta emot trafik från Europa. Om en begäran kommer från Irland, är det alltid dirigeras till slutpunkt 1.
- Eftersom en region kan endast mappas till en slutpunkt, returnerar den i Traffic Manager oavsett om slutpunkten är felfria eller inte.

    >[!IMPORTANT]
    >Vi rekommenderar starkt att kunder som använder geografisk routningsmetod associera det med kapslade typen slutpunkter som har underordnade profiler som innehåller minst två slutpunkter inom varje.
- Om en slutpunkt-matchning hittas och slutpunkten är i den **stoppad** tillstånd, Traffic Manager returnerar svaret inga data. I det här fallet inga ytterligare sökningar görs högre upp i hierarkin geografiskt område. Detta gäller även för kapslade slutpunktstyper när den underordnade profilen finns i den **stoppad** eller **inaktiverad** tillstånd.
- En slutpunkt som visas en **inaktiverad** status, inte tas med i den region som matchar processen. Detta gäller även för kapslade slutpunktstyper när slutpunkten används den **inaktiverad** tillstånd.
- Om en fråga kommer från en geografisk region som har ingen mappning i profilen, returnerar Traffic Manager svaret inga data. Därför rekommenderas starkt som kunder använder geografisk routning till en slutpunkt och det bästa av typen kapslade med minst två slutpunkter inom den underordnade profilen, med regionen **världen** tilldelade till den. Detta säkerställer också att alla IP-adresser som inte mappas till en region hanteras.

Enligt beskrivningen i [hur Traffic Manager fungerar](traffic-manager-how-it-works.md), Traffic Manager tar inte emot DNS-frågor direkt från klienter. I stället DNS-frågor kommer från rekursiv DNS-tjänsten att klienterna är konfigurerade för att använda. Därför IP-adressen används för att avgöra regionen är inte klientens IP-adress, men det är IP-adressen för rekursiv DNS-tjänst. I praktiken är bra proxy för klienten i den här IP-adress.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar program med hög tillgänglighet med hjälp av [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)

Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



