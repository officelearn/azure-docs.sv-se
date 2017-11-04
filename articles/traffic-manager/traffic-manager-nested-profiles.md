---
title: Kapslade Traffic Manager-profiler | Microsoft Docs
description: "Den här artikeln beskriver funktionen kapslade profiler för Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 1ac4ec2775ca9f690f5adf4f939908f8cee3f715
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="nested-traffic-manager-profiles"></a>Kapslade Traffic Manager-profiler

Traffic Manager innehåller ett antal metoder för routning av nätverkstrafik som kan du styra hur Traffic Manager väljer vilka slutpunkten ska ta emot trafik från varje slutanvändare. Mer information finns i [Traffic Manager routning av nätverkstrafik metoder](traffic-manager-routing-methods.md).

Varje Traffic Manager-profil anger en enda metod för routning av nätverkstrafik. Det finns emellertid scenarier som kräver mer avancerad routning av nätverkstrafik än routning tillhandahålls som en enskild Traffic Manager-profil. Du kan kapsla Traffic Manager-profiler för att kombinera fördelarna med fler än en metod för routning av nätverkstrafik. Kapslade profiler kan du åsidosätta standardbeteendet för Traffic Manager att stödja större och mer komplexa programdistributioner.

I följande exempel visas hur du använder kapslade Traffic Manager-profiler i olika scenarier.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exempel 1: Kombinera 'Prestanda' och 'Viktat' routning av nätverkstrafik

Anta att du har distribuerat ett program i följande Azure-regioner: västra USA, västra Europa och Östasien. Du kan använda Traffic Manager-prestanda' routning av nätverkstrafik metoden för att distribuera trafik till regionen som är närmast användaren.

![Enskild Traffic Manager-profilen][4]

Anta att du vill testa en uppdatering för din tjänst innan den distribueras mer brett. Du vill använda metoden 'viktat' routning av nätverkstrafik för att dirigera en liten procentandel av trafiken till testdistributionen. Du ställa in testdistributionen tillsammans med den befintliga produktionsdistributionen i västra Europa.

Du kan inte kombinera båda viktat och ' prestanda-routning av nätverkstrafik i en enda profil. För att stödja det här scenariot måste skapa du en Traffic Manager-profil med hjälp av de två slutpunkterna västra Europa och metoden 'Viktat' routning av nätverkstrafik. Sedan lägger du till den här profilen 'child' som en slutpunkt för 'överordnad'-profilen. Den överordnade profilen fortfarande använder metoden routning av nätverkstrafik prestanda och innehåller andra globala distributioner som slutpunkter.

Följande diagram illustrerar det här exemplet:

![Kapslade Traffic Manager-profiler][2]

I den här konfigurationen distribuerar trafik dirigeras via överordnade profil trafik över regioner normalt. Inom västra Europa distribuerar kapslad profil trafik till produktions- och slutpunkter enligt vikterna tilldelad.

När den överordnade profilen använder metoden 'Prestanda' routning av nätverkstrafik, måste varje slutpunkt tilldelas en plats. Platsen är tilldelad när du konfigurerar slutpunkten. Välj den Azure-regionen närmast din distribution. Azure-regioner är plats-värden som stöds av tabellen Internet svarstid. Mer information finns i [Traffic Manager-prestanda' routning av nätverkstrafik metoden](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exempel 2: Slutpunktsövervakning i kapslade profiler

Traffic Manager övervakar aktivt hälsotillståndet för varje tjänstslutpunkten. Om en slutpunkt är i feltillstånd, dirigerar Traffic Manager användare till alternativa slutpunkter för att bevara tillgängligheten för din tjänst. Den här slutpunkten övervakning och växling vid fel gäller för alla metoder för routning av nätverkstrafik. Mer information finns i [Traffic Manager-slutpunkten övervakning](traffic-manager-monitoring.md). Slutpunktsövervakning fungerar på olika sätt för kapslade profiler. Med kapslade profiler utföra överordnade profilen inte hälsokontroller på underordnade direkt. Hälsotillståndet för den underordnade profilen slutpunkter används i stället för att beräkna den övergripande hälsan för den underordnade profilen. Den här hälsoinformation sprids uppåt i hierarkin för kapslade profiler. Överordnad profilen använder denna sammanställda hälsa för att avgöra om du vill dirigera trafik till den underordnade profilen. Finns det [vanliga frågor och svar](traffic-manager-FAQs.md#traffic-manager-nested-profiles) mer information om hälsoövervakning av kapslade profiler.

Tillbaka till föregående exempel kan anta Produktionsdistribution i västra Europa misslyckas. Som standard leder 'child' profilen all trafik till testdistributionen. Om testdistributionen av också misslyckas avgör den överordnade-profilen att den underordnade profilen inte bör ta emot trafik eftersom alla underordnade slutpunkter är felfria. Sedan distribuerar överordnade profilen trafik till andra regioner.

![Kapslad profil med växling vid fel (standardinställning)][3]

Du kanske nöjd med den här ordningen. Eller kanske berörda all trafik för Västeuropa kommer nu att testa distributionen i stället för en begränsad delmängd trafik. Oavsett hälsan för testdistributionen av som du vill växla över till andra regioner när Produktionsdistribution i västra Europa misslyckas. Du kan ange parametern 'MinChildEndpoints' när du konfigurerar den underordnade profilen som en slutpunkt i den överordnade profilen om du vill aktivera den här redundansen. Parametern anger det minsta antalet tillgängliga slutpunkterna i den underordnade profilen. Standardvärdet är '1'. Det här scenariot kan ange du MinChildEndpoints värdet till 2. Under tröskelvärdet överordnade profilen anser hela underordnade profilen som ska vara tillgängliga och dirigerar trafik till de andra slutpunkterna.

Följande bild visar den här konfigurationen:

![Kapslad profil redundans med 'MinChildEndpoints' = 2][4]

> [!NOTE]
> Metoden 'Priority' routning av nätverkstrafik distribuerar all trafik till en enda slutpunkt. Därför är det lite syfte i inställningen MinChildEndpoints än '1' för en underordnad profil.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exempel 3: Prioriteras redundans regioner i 'prestanda-trafikroutning

Standardbeteendet för metoden 'Prestanda' routning av nätverkstrafik är utformat för att undvika över inläsning nästa närmast slutpunkt och orsakar sammanhängande flera fel. När en slutpunkt inte är all trafik som skulle ha varit till denna slutpunkt jämnt fördelad till andra slutpunkterna över alla regioner.

!['Prestanda-trafik routning med standard-redundans][5]

Dock kanske du föredrar trafikredundans Västeuropa västra USA och dirigera trafik till andra regioner endast när båda slutpunkterna är inte tillgänglig. Du kan skapa den här lösningen använder en profil för underordnade med metoden 'Priority' routning av nätverkstrafik.

!['Prestanda-trafik routning med förmånliga redundans][6]

Eftersom Västeuropa slutpunkten har högre prioritet än västra USA slutpunkten, som alla trafik skickas till slutpunkten Västeuropa när båda slutpunkterna är online. Om det inte går att Västeuropa, dirigeras dess trafik till västra USA. Med den kapslade profilen omdirigeras trafik till Östasien endast när både västra Europa och USA, västra misslyckas.

Du kan upprepa det här mönstret för alla regioner. Ersätt alla tre slutpunkter i profilen för överordnade med tre underordnade profiler, var och en prioriterad failover-sekvens.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exempel 4: Kontrollera 'prestanda-trafikroutning mellan flera slutpunkter i samma region

Anta att prestanda-routning av nätverkstrafik metoden används i en profil som har mer än en slutpunkt i en viss region. Som standard är trafik som riktas mot den regionen fördelas jämnt över alla tillgängliga slutpunkterna i den regionen.

!['Prestanda-trafik routning i region trafikfördelning (standardinställning)][7]

I stället för att lägga till flera slutpunkter i Västeuropa innesluts dessa slutpunkter i en separat underordnade profil. Den underordnade profilen läggs till överordnat som endast slutpunkt i västra Europa. Inställningarna på den underordnade profilen kan styra trafikfördelning med Västeuropa genom att aktivera prioritetsbaserad eller viktat trafikroutning i detta område.

!['Prestanda-trafik routning med anpassade trafikfördelning i region][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exempel 5: Per slutpunkt övervakningsinställningarna

Anta att du använder Traffic Manager för att smidigt flytta trafik från en äldre lokalt webbplats till en ny molnbaserade version finns i Azure. För den gamla platsen som du vill använda startsidan URI för att övervaka hälsa för platsen. Men för den nya versionen molnbaserade du implementerar en anpassad övervakning sida (sökvägen ' / monitor.aspx') som innehåller ytterligare kontroller.

![Traffic Manager-slutpunkt övervakning (standardinställning)][9]

Övervakningsinställningarna i Traffic Manager-profilen gäller för alla slutpunkter inom en enskild profil. Med kapslade profiler använder du en annan underordnad profil per plats för att definiera olika övervakningsinställningarna för.

![Övervakning med per slutpunktsinställningar Traffic Manager-slutpunkt][10]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Traffic Manager-profiler](traffic-manager-overview.md)

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
