---
title: Azure Traffic Manager - trafikroutningsmetoder | Microsoft Docs
description: Det här artiklarna hjälper till att du förstår de olika trafikroutningsmetoder som används av Traffic Manager
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
ms.openlocfilehash: c9bd9b4913e38ed5c1f7f4ec8ee7e3210fa3be8f
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30245370"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager-dirigeringsmetoder

Azure Traffic Manager stöder fyra routning av nätverkstrafik för att fastställa hur att dirigera nätverkstrafik till de olika slutpunkterna. Traffic Manager gäller metoden routning av nätverkstrafik för varje DNS-fråga tas emot. Metoden routning av nätverkstrafik avgör vilken slutpunkt som returneras i DNS-svaret.

Det finns fyra trafikroutningsmetoder i Traffic Manager:

* **[Prioritet](#priority):** Välj **prioritet** när du vill använda en primär tjänstslutpunkt för all trafik och ange säkerhetskopieringar om primärt eller säkerhetskopiering slutpunkter är inte tillgänglig.
* **[Viktat](#weighted):** Välj **viktat** när du vill distribuera trafik mellan en uppsättning slutpunkter, antingen jämnt eller baserat på vikt, som du definierar.
* **[Prestanda](#performance):** Välj **prestanda** när du har slutpunkter i olika geografiska platser och du vill att användarna ska använda ”närmaste” slutpunkt vad gäller den lägsta Nätverksfördröjningen.
* **[Geografisk](#geographic):** Välj **geografiska** så att användare dirigeras till specifika slutpunkter (Azure, externt eller kapslade) baserat på vilka geografiska plats deras DNS-fråga kommer från. Detta ger Traffic Manager-kunder att aktivera scenarier där känna till en användares geografisk region och routning dem baserat på som är viktigt. Exempel: uppfyller data suveränitet uppdrag, lokalisering av användaren & innehåll och mäta trafik från olika regioner.

Alla Traffic Manager-profiler innehåller övervakning av hälsotillstånd för slutpunkten och automatisk endpoint växling vid fel. Mer information finns i [Traffic Manager-slutpunkten övervakning](traffic-manager-monitoring.md). En enskild Traffic Manager-profil kan använda en enda trafikroutningsmetod. Du kan välja en annan trafikroutningsmetod för din profil när som helst. Ändringarna tillämpas inom en minut och utan avbrott uppstår. Routning av nätverkstrafik metoder kan kombineras med hjälp av kapslade Traffic Manager-profiler. Kapsling gör avancerade och flexibel routning av nätverkstrafik konfigurationer som uppfyller behoven hos större, komplexa program. Mer information finns i [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).

## <a name = "priority"></a>Prioritet routning av nätverkstrafik metod

Ofta vill organisationen tillförlitlighet för dess tjänster genom att distribuera en eller flera säkerhetskopierade tjänster om deras primära tjänsten kraschar. Metoden 'Priority' routning av nätverkstrafik kan Azure kunder enkelt kan implementera det här mönstret för växling vid fel.

![Azure Traffic Manager 'Priority' routning av nätverkstrafik, metod][1]

Trafikhanterarprofilen innehåller en prioriterad lista med slutpunkter. Som standard skickar Traffic Manager all trafik till den primära (högsta prioritet)-slutpunkten. Om den primära slutpunkten inte är tillgänglig dirigerar Traffic Manager trafik till andra slutpunkten. Om både de primära och sekundära slutpunkterna inte är tillgängliga går trafiken till tredje och så vidare. Tillgängligheten för slutpunkten är baserat på konfigurerad status (aktiverade eller inaktiverade) och pågående slutpunktsövervakning.

### <a name="configuring-endpoints"></a>Konfigurera slutpunkter

Med Azure Resource Manager kan du konfigurera slutpunktsprioritet uttryckligen med hjälp av egenskapen 'priority' för varje slutpunkt. Den här egenskapen är ett värde mellan 1 och 1000. Lägre värden motsvarar högre prioritet. Slutpunkter kan inte dela prioritetsvärdet. Egenskapen är valfri. När utelämnas används en standardprioritet baserat på slutpunkten ordning.

##<a name = "weighted"></a>Viktade routning av nätverkstrafik, metod
Metoden 'Viktat' routning av nätverkstrafik kan du distribuera trafiken jämnt eller använda en fördefinierad vikt.

![Azure Traffic Manager-viktad' routning av nätverkstrafik metod][2]

I viktat routning av nätverkstrafik metoden tilldelar du varje slutpunkt i profilkonfigurationen för Traffic Manager. Vikten är ett heltal från 1 till 1000. Den här parametern är valfri. Om det utelämnas används trafik chefer en standard vikt '1'. Högre vikt, desto högre prioritet.

För varje DNS-fråga tas emot, väljer slumpmässigt en tillgänglig slutpunkt i Traffic Manager. Sannolikheten för att välja en slutpunkt som baseras på viktningar som tilldelats till alla tillgängliga slutpunkterna. Med hjälp av samma vikt mellan alla slutpunkter resultat i en även trafikfördelning. Med högre eller lägre vikterna på specifika slutpunkter gör dessa slutpunkter som ska returneras mer eller mindre ofta i DNS-svar.

Metoden viktat aktiverar vissa användbara scenarier:

* Stegvis Programuppgradering: tilldela en procentandel av trafik dirigeras till en ny slutpunkt och gradvis öka trafiken över tid till 100%.
* Programmet migrering till Azure: skapa en profil med både Azure och externa slutpunkter. Justera vikten av att slutpunkter föredrar nya slutpunkter.
* Moln-burst-överföring för ytterligare kapacitet: snabbt expandera en lokal distribution i molnet genom att lägga till bakom en Traffic Manager-profilen. När du behöver extra kapacitet i molnet kan du lägga till eller aktivera flera slutpunkter och anger vilken del av trafiken som går till varje slutpunkt.

Azure Resource Manager portal stöder konfiguration av Routning av viktat nätverkstrafik.  Du kan konfigurera vikterna med Resource Manager-versioner av Azure PowerShell, CLI och REST-API: er.

Det är viktigt att förstå att DNS-svar cachelagras av klienter och av rekursiva DNS-servrar som klienter använder för att matcha DNS-namn. Den här cachelagring kan påverka viktat trafik distributioner. När antalet klienter och rekursiva DNS-servrar är stor, fungerar trafikfördelning som förväntat. Dock när antalet klienter eller rekursiv DNS-servrar är litet kan cachelagring avsevärt skeva trafik-distribution.

Vanliga användningsområden omfattar:

* Utvecklings- och testmiljöer
* Program kommunikation
* Program syftar till att en smala-användarbas som delar en gemensam rekursiv DNS-infrastruktur (till exempel anställda på företaget som ansluter via en proxyserver)

Dessa DNS-cachelagring effekter är gemensamma för alla DNS-baserad trafik routning system, inte bara Azure Traffic Manager. Explicit rensa DNS-cacheminnet kan i vissa fall kan ge en lösning. I andra fall måste vara en alternativ metod för routning av nätverkstrafik lämpligare.

## <a name = "performance"></a>Prestanda routning av nätverkstrafik metod

Distribuera slutpunkter i två eller flera platser över hela världen kan förbättra svarstiden för många program genom att dirigera trafiken till den plats som ligger närmast dig. Metoden routning av nätverkstrafik, prestanda, ger den här funktionen.

![Azure Traffic Manager-prestanda' routning av nätverkstrafik, metod][3]

'Närmaste' slutpunkt är inte nödvändigtvis närmaste mätt av geografiska avstånd. I stället anger metoden 'Prestanda' routning av nätverkstrafik närmaste slutpunkten genom att mäta Nätverksfördröjningen. Traffic Manager upprätthåller en Internet latens tabell för att spåra fram och åter tiden mellan IP-adressintervall och varje Azure-datacenter.

Traffic Manager letar upp källans IP-adress för denna inkommande begäran DNS i tabellen Internet svarstid. Traffic Manager väljer du en slutpunkt som är tillgängliga i Azure-datacentret som har den lägsta fördröjningen för IP-adressintervall, och returnerar sedan denna slutpunkt i DNS-svaret.

Enligt beskrivningen i [så Traffic Manager fungerar](traffic-manager-overview.md#how-traffic-manager-works), Traffic Manager får inte DNS-frågor direkt från klienter. I stället DNS-frågor kommer från den rekursiva DNS-tjänsten att klienterna är konfigurerade för att använda. Därför IP-adressen används för att avgöra 'närmaste' slutpunkt är inte klientens IP-adress, men det är IP-adressen för rekursiv DNS-tjänsten. I praktiken är IP-adressen en bra proxy för klienten.


Traffic Manager uppdaterar regelbundet tabellen Internet svarstid för ändringar i globala Internet och nya Azure-regioner. Programprestanda varierar, beroende på realtid variationer i belastningen över Internet. Routning av prestanda-trafik övervakar inte belastningen på en viss tjänstslutpunkt. Men om en slutpunkt blir otillgänglig, ingår Traffic Manager inte i DNS-svar på frågor.


Pekar på Observera:

* Om din profil innehåller flera slutpunkter i samma Azure-region, sedan fördelar Traffic Manager trafiken jämnt över de tillgängliga slutpunkterna i den regionen. Om du föredrar en annan trafikfördelning inom en region, kan du använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* Om alla aktiverade slutpunkterna i den närmaste Azure-regionen är försämrade flyttas Traffic Manager trafik till slutpunkter i nästa närmaste Azure-region. Om du vill definiera en önskad failover-sekvens, Använd [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md).
* När med externa slutpunkter eller kapslade slutpunkter routningsmetoden för prestanda-trafik, måste du ange platsen för dessa slutpunkter. Välj den Azure-regionen närmast din distribution. Dessa platser är de värden som stöds av tabellen Internet svarstid.
* Den algoritm som väljer slutpunkten är deterministisk. Upprepad DNS-frågor från samma klient dirigeras till samma slutpunkten. Klienter använder normalt olika rekursiv DNS-servrar när du reser. Klienten kan dirigeras till en annan slutpunkt. Routning kan också påverkas av uppdateringar till tabellen Internet svarstid. Därför garanterar metoden routning av nätverkstrafik prestanda inte att en klient alltid dirigeras till samma slutpunkten.
* Det kan hända att vissa klienter dirigeras till en annan slutpunkt när tabellen Internet latens ändras. Ändringen routning är exaktare baserat på aktuella svarstidsdata. De här uppdateringarna är mycket viktigt att bibehålla riktighet routning av prestanda-trafik som Internet ständigt utvecklas.

## <a name = "geographic"></a>Geografisk routning av nätverkstrafik metod

Traffic Manager-profiler kan konfigureras för att använda den geografiska routningsmetoden så att användare dirigeras till specifika slutpunkter (Azure, externt eller kapslade) baserat på vilka geografiska plats deras DNS-fråga som kommer från. Detta ger Traffic Manager-kunder att aktivera scenarier där känna till en användares geografisk region och routning dem baserat på som är viktigt. Exempel: uppfyller data suveränitet uppdrag, lokalisering av användaren & innehåll och mäta trafik från olika regioner.
När en profil har konfigurerats för geografisk routning varje slutpunkt som är kopplade till att profilen måste ha en uppsättning geografiska områden som är kopplade till den. Ett geografiskt område kan vara på följande detaljnivåer 
- World – en region
- Regional gruppering – till exempel Afrika, Mellanöstern, Australien/Pacific osv. 
- Land/Region – till exempel Irland Peru, Hongkong SAR osv. 
- Region – till exempel USA California, Australien-Queensland Kanada Alberta etc. (Obs: den här filnivå stöds bara för tillstånd / regioner i Australien, Kanada, Storbritannien och USA).

När en region eller en uppsättning regioner tilldelas till en slutpunkt, dirigeras förfrågningar från dessa regioner endast till denna slutpunkt. Traffic Manager använder käll-IP-adressen för DNS-frågan för att avgöra den region där en användare efterfrågar från – vanligen är detta IP-adressen för den lokala DNS-matcharen göra frågan för användarens räkning.  

![Azure Traffic Manager-geografiska' routning av nätverkstrafik metod](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager läser käll-IP-adressen för DNS-fråga och bestämmer vilka geografiska region som det härstammar från. Den söker sedan för att se om det finns en slutpunkt som har den här geografiska region som mappats till den. Den här sökningen startar på filnivå lägsta (region där den stöds, eller på nivån Land/Region) och går till den högsta nivån, vilket är **World**. Den första matchningen hittades med den här traversal utses slutpunkten ska returneras i svaret på frågan. När matchning med en slutpunkt för kapslad typ, en slutpunkt inom den underordnade profilen returneras, baserat på dess routningsmetoden. Följande punkter gäller för det här problemet:

- Ett geografiskt område kan endast mappas till en slutpunkt i Traffic Manager-profil när routning typen är geografiska routning. Detta säkerställer att routning av användare är deterministisk och kunder kan aktivera scenarier som kräver entydigt geografisk gränser.
- Om en användares region kommer under två olika slutpunkter geografiska mappning, väljer slutpunkten med lägsta Granulariteten Traffic Manager och anser inte routning begäranden från den regionen till andra slutpunkten. Anta till exempel att en geografisk routning typen profil med två slutpunkter - slutpunkt 1 och Endpoint2. Slutpunkt 1 är konfigurerad för att ta emot trafik från Irland och Endpoint2 är konfigurerad att ta emot trafik från Europa. Om en begäran kommer från Irland, dirigeras det alltid till slutpunkt 1.
- Eftersom en region kan endast mappas till en slutpunkt, returneras den Traffic Manager oavsett om slutpunkten är felfri eller inte.

    >[!IMPORTANT]
    >Du rekommenderas att kunder som använder den geografiska routningsmetoden associeras med de kapslade typen slutpunkter som har underordnade profiler som innehåller minst två slutpunkter inom varje.
- Om en matchning slutpunkt och denna slutpunkt finns i den **stoppad** tillstånd Traffic Manager returnerar svaret inga data. I det här fallet inga ytterligare sökningar görs högre upp i hierarkin geografiskt område. Detta gäller även för kapslade slutpunktstyper när den underordnade profilen är i den **stoppad** eller **inaktiverad** tillstånd.
- Om en slutpunkt visar en **inaktiverad** status, inte tas med i den region som matchar processen. Detta gäller även för kapslade slutpunktstyper när slutpunkten är i den **inaktiverad** tillstånd.
- Om en fråga kommer från en geografisk region som inte har någon mappning i den här profilen, returnerar Traffic Manager svaret inga data. Därför rekommenderar vi att kunder använder geografiska routning med en slutpunkt som helst av typen kapslade med minst två slutpunkter inom den underordnade profilen, med regionen **World** kopplade till den. Detta säkerställer att alla IP-adresser som inte mappas till en region hanteras.

Enligt beskrivningen i [så Traffic Manager fungerar](traffic-manager-how-traffic-manager-works.md), Traffic Manager får inte DNS-frågor direkt från klienter. I stället DNS-frågor kommer från den rekursiva DNS-tjänsten att klienterna är konfigurerade för att använda. Därför IP-adressen används för att avgöra regionen är inte klientens IP-adress, men det är IP-adressen för rekursiv DNS-tjänsten. I praktiken är IP-adressen en bra proxy för klienten.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du utvecklar program med hög tillgänglighet med hjälp av [slutpunktsövervakning för Traffic Manager](traffic-manager-monitoring.md)

Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png



