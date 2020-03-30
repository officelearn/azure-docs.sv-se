---
title: Kapslade trafikhanterare profiler i Azure
titleSuffix: Azure Traffic Manager
description: I den här artikeln beskrivs funktionen Kapslade profiler i Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: rohink
ms.openlocfilehash: 282099cb274c1ea872a0df9c2753a939ef31421f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938576"
---
# <a name="nested-traffic-manager-profiles"></a>Kapslade Traffic Manager-profiler

Traffic Manager innehåller en rad trafikroutningsmetoder som gör att du kan styra hur Traffic Manager väljer vilken slutpunkt som ska ta emot trafik från varje slutanvändare. Mer information finns i [Trafikhanterarens trafikroutningsmetoder](traffic-manager-routing-methods.md).

Varje Traffic Manager-profil anger en enda trafikroutningsmetod. Det finns dock scenarier som kräver mer sofistikerad trafikroutning än den routning som tillhandahålls av en enda Traffic Manager-profil. Du kan kapsla Traffic Manager-profiler för att kombinera fördelarna med mer än en trafikroutningsmetod. Kapslade profiler kan du åsidosätta standard traffic manager-beteendet för att stödja större och mer komplexa programdistributioner.

Följande exempel illustrerar hur du använder kapslade Traffic Manager-profiler i olika scenarier.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exempel 1: Kombinera trafikroutning av "prestanda" och "viktad"

Anta att du har distribuerat ett program i följande Azure-regioner: västra USA, Västeuropa och Östasien. Du använder Traffic Manager's "Performance" trafik-routing metod för att distribuera trafik till den region som är närmast användaren.

![Profil för Single Traffic Manager][4]

Anta nu att du vill testa en uppdatering av din tjänst innan du rullar ut den i större utsträckning. Du vill använda den "viktade" trafikroutningsmetoden för att dirigera en liten andel trafik till testdistributionen. Du ställer in testdistributionen tillsammans med den befintliga produktionsdistributionen i Västeuropa.

Du kan inte kombinera både Viktad och Prestandatrafik-routning i en enda profil. För att stödja det här scenariot skapar du en Traffic Manager-profil med hjälp av de två slutpunkterna i Västeuropa och trafikroutningsmetoden "Viktad". Därefter lägger du till den här underordnade profilen som en slutpunkt i den överordnade profilen. Den överordnade profilen använder fortfarande metoden För trafikroutning av prestanda och innehåller de andra globala distributionerna som slutpunkter.

Följande diagram illustrerar det här exemplet:

![Kapslade Traffic Manager-profiler][2]

I den här konfigurationen distribuerar trafik som dirigeras via den överordnade profilen trafik mellan regioner normalt. Inom Västeuropa distribuerar den kapslade profilen trafik till produktions- och testslutpunkterna enligt de tilldelade vikterna.

När den överordnade profilen använder trafikroutningsmetoden "Prestanda" måste varje slutpunkt tilldelas en plats. Platsen tilldelas när du konfigurerar slutpunkten. Välj den Azure-region som är närmast distributionen. Azure-regionerna är de platsvärden som stöds av tabellen för internetfördröjning. Mer information finns i [Traffic Manager 'Prestanda'-trafikroutningsmetod](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exempel 2: Slutpunktsövervakning i kapslade profiler

Traffic Manager övervakar aktivt hälsotillståndet för varje tjänstslutpunkt. Om en slutpunkt är fel, dirigerar Traffic Manager användare till alternativa slutpunkter för att bevara tillgängligheten för din tjänst. Den här slutpunktsövervakningen och redundansfunktionen gäller för alla trafikdirigeringsmetoder. Mer information finns i [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). Slutpunktsövervakning fungerar annorlunda för kapslade profiler. Med kapslade profiler utför den överordnade profilen inte hälsokontroller på barnet direkt. I stället används hälsotillståndet för den underordnade profilens slutpunkter för att beräkna den allmänna hälsan för den underordnade profilen. Den här hälsoinformationen sprids upp i den kapslade profilhierarkin. Den överordnade profilen använder den här aggregerade hälsotillståndet för att avgöra om trafik ska dirigeras till den underordnade profilen. Se [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-nested-profiles) om hur du övervakar kapslade profiler.

Om du återgår till föregående exempel antar du att produktionsdistributionen i Västeuropa misslyckas. Som standard dirigerar den "underordnade" profilen all trafik till testdistributionen. Om testdistributionen också misslyckas avgör den överordnade profilen att den underordnade profilen inte ska ta emot trafik eftersom alla underordnade slutpunkter är felaktiga. Sedan distribuerar den överordnade profilen trafik till de andra regionerna.

![Kapslad profil redundans (standardbeteende)][3]

Du kanske är nöjd med det här arrangemanget. Eller så kanske du är orolig för att all trafik till Västeuropa nu går till testutplaceringen i stället för en begränsad delmängdstrafik. Oavsett hälsotillståndet för testdistributionen vill du växla över till de andra regionerna när produktionsdistributionen i Västeuropa misslyckas. Om du vill aktivera den här redundansen kan du ange parametern "MinChildEndpoints" när du konfigurerar den underordnade profilen som en slutpunkt i den överordnade profilen. Parametern bestämmer det minsta antalet tillgängliga slutpunkter i den underordnade profilen. Standardvärdet är "1". I det här fallet anger du värdet MinChildEndpoints till 2. Under det här tröskelvärdet anser den överordnade profilen att hela den underordnade profilen inte är tillgänglig och dirigerar trafik till de andra slutpunkterna.

Följande bild illustrerar den här konfigurationen:

![Kapslad profil redundans med "MinChildEndpoints" = 2][4]

> [!NOTE]
> Trafikroutningsmetoden "Prioritet" distribuerar all trafik till en enda slutpunkt. Det finns alltså inget syfte i en MinChildEndpoints-inställning än "1" för en underordnad profil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exempel 3: Prioriterade redundansregioner i trafikroutning av prestanda

Standardbeteendet för trafikroutningsmetoden "Prestanda" är när du har slutpunkter på olika geografiska platser som slutanvändarna dirigeras till slutpunkten "närmaste" när det gäller den lägsta nätverksfördröjningen.

Anta dock att du föredrar trafikundans västeuropa till västra USA och bara dirigerar trafik till andra regioner när båda slutpunkterna inte är tillgängliga. Du kan skapa den här lösningen med hjälp av en underordnad profil med trafikroutningsmetoden Prioritet.

![Trafikdragning för prestanda med förmånlig redundans][6]

Eftersom slutpunkten för Västeuropa har högre prioritet än slutpunkten för västra USA skickas all trafik till slutpunkten för Västeuropa när båda slutpunkterna är online. Om Västeuropa misslyckas dirigeras dess trafik till västra USA. Med den kapslade profilen dirigeras trafiken endast till Östasien när både Västeuropa och Västra USA misslyckas.

Du kan upprepa det här mönstret för alla regioner. Ersätt alla tre slutpunkterna i den överordnade profilen med tre underordnade profiler, var och en som ger en prioriterad redundanssekvens.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exempel 4: Styra trafikroutning av prestanda mellan flera slutpunkter i samma region

Anta att trafikroutningsmetoden "Prestanda" används i en profil som har mer än en slutpunkt i en viss region. Som standard fördelas trafik som dirigeras till den regionen jämnt mellan alla tillgängliga slutpunkter i den regionen.

!["Prestanda" trafik routning i regionen trafikdistribution (standardbeteende)][7]

I stället för att lägga till flera slutpunkter i Västeuropa omges dessa slutpunkter i en separat underordnad profil. Den underordnade profilen läggs till i den överordnade som den enda slutpunkten i Västeuropa. Inställningarna för den underordnade profilen kan styra trafikdistributionen med Västeuropa genom att aktivera prioriterad eller viktad trafikroutning inom den regionen.

![Trafikroutning av prestanda med anpassad trafikdistribution i regionen][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exempel 5: Övervakningsinställningar per slutpunkt

Anta att du använder Traffic Manager för att smidigt migrera trafik från en äldre lokal webbplats till en ny molnbaserad version som finns i Azure. För den äldre webbplatsen vill du använda startsidan URI för att övervaka webbplatsens hälsa. Men för den nya molnbaserade versionen implementerar du en anpassad övervakningssida (sökväg /monitor.aspx) som innehåller ytterligare kontroller.

![Övervakning av Traffic Manager-slutpunkter (standardbeteende)][9]

Övervakningsinställningarna i en Traffic Manager-profil gäller för alla slutpunkter i en enda profil. Med kapslade profiler använder du en annan underordnad profil per plats för att definiera olika övervakningsinställningar.

![Slutpunktsövervakning för Traffic Manager med inställningar per slutpunkt][10]

## <a name="faqs"></a>Vanliga frågor och svar

* [Hur konfigurerar jag kapslade profiler?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Hur många lager häckar stöder Traffic Manger?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Kan jag blanda andra slutpunktstyper med kapslade underordnade profiler i samma Traffic Manager-profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Hur gäller faktureringsmodellen för Kapslade profiler?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Finns det en prestandapåverkan för kapslade profiler?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [Hur beräknar Traffic Manager hälsotillståndet för en kapslad slutpunkt i en överordnad profil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Nästa steg

Läs mer om [Traffic Manager-profiler](traffic-manager-overview.md)

Läs om hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
