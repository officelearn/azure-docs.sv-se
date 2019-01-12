---
title: Kapslade Traffic Manager-profiler i Azure
titlesuffix: Azure Traffic Manager
description: Den här artikeln förklarar kapslade profiler-funktionen i Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: kumud
ms.openlocfilehash: 6fb6b3e4476efec87b15d175d354afab777e6830
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54228834"
---
# <a name="nested-traffic-manager-profiles"></a>Kapslade Traffic Manager-profiler

Traffic Manager erbjuder en mängd trafikroutningsmetoder så att du kan styra hur Traffic Manager väljer vilken slutpunkt som ska ta emot trafik från varje slutanvändare. Mer information finns i [Traffic Manager trafikroutningsmetoder](traffic-manager-routing-methods.md).

Varje Traffic Manager-profil anger en enda metod för routning av nätverkstrafik. Det finns dock scenarier som kräver mer sofistikerade routning av nätverkstrafik än routning tillhandahålls som en enda Traffic Manager-profil. Du kan kapsla Traffic Manager-profiler om du vill kombinera fördelarna med fler än en metod för routning av nätverkstrafik. Kapslade profiler kan du åsidosätta standardbeteendet för Traffic Manager stöd för större och mer komplexa distributioner av program.

I följande exempel visas hur du använder kapslade Traffic Manager-profiler i olika scenarier.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exempel 1: Kombinera ”Performance” och ”viktat' routning av nätverkstrafik

Anta att du distribuerat ett program i följande Azure-regioner: Västra USA, västra Europa och Asien. Du kan använda Traffic Manager ”Performance” trafikdirigeringsmetoden för att distribuera trafik till regionen som är närmast användaren.

![Enskild Traffic Manager-profil][4]

Anta nu att du vill testa en uppdatering av din tjänst innan den distribueras mer brett. Du vill använda metoden 'viktad' routning av nätverkstrafik för att dirigera en liten andel av trafik till test-distribution. Du har konfigurerat testdistributionen tillsammans med den befintliga produktionsdistributionen i Västeuropa.

Du kan inte kombinera båda viktat och ' prestanda-routning av nätverkstrafik i en enda profil. För det här scenariot måste skapa du en Traffic Manager-profil med hjälp av två Västeuropa slutpunkter och metoden 'Viktat' routning av nätverkstrafik. Sedan lägger du till den här profilen ”underordnad” som en slutpunkt för ”överordnad”-profilen. Den överordnade profilen fortfarande använder trafikdirigeringsmetoden prestanda och innehåller andra globala distributioner som slutpunkter.

Följande diagram illustrerar det här exemplet:

![Kapslade Traffic Manager-profiler][2]

I den här konfigurationen distribuerar trafik dirigeras via den överordnade profilen trafik mellan regioner normalt. Västra Europa distribuerar kapslad profil trafiken slutpunkter i produktion och testning enligt vikterna tilldelad.

När den överordnade profilen använder trafikdirigeringsmetoden ”Performance”, måste varje slutpunkt tilldelas en plats. Platsen tilldelas när du konfigurerar slutpunkten. Välj Azure-regionen närmast din distribution. Azure-regioner är plats-värden som stöds av tabellen Internet svarstid. Mer information finns i [Traffic Manager ”Performance” trafikdirigeringsmetoden](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exempel 2: Slutpunktsövervakning i kapslade profiler

Traffic Manager övervakar aktivt hälsan hos varje serviceslutpunkt. Om en slutpunkt är felaktiga, dirigerar Traffic Manager användare till alternativa slutpunkter att bevara tillgängligheten för din tjänst. Den här slutpunkten av och redundansväxling för beteende gäller för alla metoder för routning av nätverkstrafik. Mer information finns i [Traffic Manager Endpoint Monitoring](traffic-manager-monitoring.md). Slutpunktsövervakning fungerar på olika sätt för kapslade profiler. Med kapslade profiler utföra överordnade profilen inte hälsokontroller av slutpunkter på underordnat direkt. Hälsotillståndet för den underordnade profilen slutpunkter används i stället för att beräkna den övergripande hälsan för den underordnade profilen. Den här hälsoinformation sprids upp kapslad profilhierarki. Den överordnade profilen använder denna sammanställda hälsa för att avgöra om du vill dirigera trafik till den underordnade profilen. Se den [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-nested-profiles) för fullständig information om hälsoövervakning av kapslade profiler.

Tillbaka till föregående exempel kan anta att produktionsdistributionen i Västeuropa misslyckas. Som standard dirigeras all trafik till test-distribution i profilen ”underordnad”. Om det misslyckas också test-distribution, anger den överordnade profilen att den underordnade profilen inte bör ta emot trafik eftersom alla underordnade slutpunkter är skadade. Den överordnade profilen distribuerar sedan trafiken till andra regioner.

![Kapslad profil redundans (standardinställning)][3]

Du kan bli nöjd med den här ordningen. Eller kanske är du orolig att all trafik för Västeuropa kommer nu att test-distribution i stället för en begränsad delmängd-trafik. Oavsett hälsotillståndet för test-distribution som du vill växla över till andra regioner när produktionsdistributionen i Västeuropa misslyckas. Du kan ange parametern 'MinChildEndpoints ”när du konfigurerar den underordnade profilen som en slutpunkt i den överordnade profilen om du vill aktivera den här redundansen. Parametern anger det minsta antalet tillgängliga slutpunkterna i den underordnade profilen. Standardvärdet är '1'. Det här scenariot kan ange du MinChildEndpoints värdet till 2. Den här tröskeln överordnade profilen tar hänsyn till hela underordnade profilen inte var tillgänglig och dirigeras trafiken till de andra slutpunkterna.

Följande bild visar den här konfigurationen:

![Kapslade profil redundans med 'MinChildEndpoints' = 2][4]

> [!NOTE]
> ”Prioritet” trafikdirigeringsmetoden distribuerar all trafik till en enda slutpunkt. Därför finns det lite syfte i en MinChildEndpoints inställning än ”1” för en underordnad-profil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exempel 3: Prioriterad redundans regioner i trafikroutningsmetoden ”Performance”-trafik

Standardbeteendet för trafikdirigeringsmetoden ”Performance” är när du har slutpunkter på olika geografiska platser som slutanvändarna dirigeras till ”närmaste” slutpunkten när det gäller den lägsta Nätverksfördröjningen.

Men du kanske föredrar trafikredundans för Europa, västra USA, västra, och endast dirigera trafik till andra regioner när båda slutpunkterna är inte tillgänglig. Du kan skapa den här lösningen med hjälp av en underordnad-profil med metoden ”prioritet” routning av nätverkstrafik.

![”Performance” trafiken dirigeras med en växling vid fel][6]

Eftersom Västeuropa slutpunkten har högre prioritet än västra USA-slutpunkten, skickas all trafik till slutpunkten Västeuropa när båda slutpunkterna är online. Om det inte går att Västeuropa, dirigeras dess trafik till USA, västra. Med profilen för en kapslad dirigeras trafiken till Östasien endast när misslyckas både västra Europa och västra USA.

Du kan upprepa det här mönstret för alla regioner. Ersätt alla tre slutpunkter i profilen för överordnade med tre underordnade profiler, var och en har en prioriterad redundans-sekvens.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exempel 4: Kontrollera ”Performance” trafikroutning mellan flera slutpunkter i samma region

Anta att prestanda-trafikdirigeringsmetoden används i en profil som har mer än en slutpunkt i en viss region. Som standard är trafik dirigeras till den regionen fördelas jämnt över alla tillgängliga slutpunkterna i den regionen.

![”Performance” trafiken dirigeras regional trafikfördelning (standardinställning)][7]

I stället för att lägga till flera slutpunkter i Europa, västra, är dessa slutpunkter inom en separat underordnad-profil. Den underordnade profilen läggs till överordnat som endast slutpunkten i Västeuropa. Inställningarna på den underordnade profilen kan styra trafikfördelning med Västeuropa genom att aktivera prioritetsbaserad eller viktad trafikdirigering i den regionen.

![”Performance” trafiken dirigeras med anpassade regional trafikfördelning][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exempel 5: Inställningarna för övervakning av per slutpunkt

Anta att du använder Traffic Manager för att smidigt migrera trafik från en äldre lokala webbplats till en ny molnbaserade version i Azure. För äldre platsen, som du vill använda startsidan URI för att övervaka hälsa för platsen. Men för den nya versionen molnbaserade du implementerar en anpassad övervakning sida (sökvägen ”/ monitor.aspx”) som innehåller ytterligare kontroller.

![Traffic Manager endpoint monitoring (standardinställning)][9]

Inställningar för programövervakning i en Traffic Manager-profil gäller för alla slutpunkter i en enda profil. Kapslade profiler använder en annan underordnad-profil per plats för att definiera olika inställningar för programövervakning.

![Övervakning med per slutpunktsinställningarna Traffic Manager-slutpunkt][10]

## <a name="next-steps"></a>Nästa steg

Läs mer om [Traffic Manager-profiler](traffic-manager-overview.md)

Lär dig hur du [skapa en Traffic Manager-profil](traffic-manager-create-profile.md)

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
