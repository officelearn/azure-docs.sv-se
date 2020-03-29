---
title: Prestandaöverväganden för Azure Traffic Manager | Microsoft-dokument
description: Förstå prestanda på Traffic Manager och hur du testar webbplatsens prestanda när du använder Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 84367a00643c48e7fe2fb7f907bab64589193b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938545"
---
# <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

På den här sidan beskrivs prestandaöverväganden med Traffic Manager. Föreställ dig följande scenario:

Du har instanser av din webbplats i WestUS och EastAsia regioner. En av instanserna misslyckas hälsokontrollen för trafikhanterarens avsökning. Programtrafik dirigeras till den felfria regionen. Den här redundansen förväntas, men prestanda kan vara ett problem baserat på svarstiden för den trafik som nu reser till en avlägsen region.

## <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

Den enda prestandapåverkan som Traffic Manager kan ha på din webbplats är den första DNS-sökningen. En DNS-begäran om namnet på Traffic Manager-profilen hanteras av Microsoft DNS-rotservern som är värd för zonen trafficmanager.net. Traffic Manager fyller i och uppdaterar regelbundet Microsofts DNS-rotservrar baserat på Traffic Manager-principen och avsökningsresultaten. Så även under den första DNS-sökningen skickas inga DNS-frågor till Traffic Manager.

Traffic Manager består av flera komponenter: DNS-namnservrar, en API-tjänst, lagringslagret och en slutpunktsövervakningstjänst. Om en Traffic Manager-tjänstkomponent misslyckas påverkas inte DNS-namnet som är kopplat till Traffic Manager-profilen. Posterna i Microsoft DNS-servrarna förblir oförändrade. Slutpunktsövervakning och DNS-uppdatering sker dock inte. Traffic Manager kan därför inte uppdatera DNS så att den pekar på redundansplatsen när den primära webbplatsen går ned.

DNS-namnmatchning är snabb och resultaten cachelagras. Hastigheten för den första DNS-sökningen beror på vilka DNS-servrar klienten använder för namnmatchning. Vanligtvis kan en klient slutföra en DNS-sökning inom ~50 ms. Resultatet av sökningen cachelagras under hela DNS Time-to-live (TTL). StandardTL för Traffic Manager är 300 sekunder.

Trafiken flödar INTE genom Traffic Manager. När DNS-sökningen är klar har klienten en IP-adress för en instans av din webbplats. Klienten ansluter direkt till den adressen och passerar inte Traffic Manager. Den Traffic Manager-princip du väljer påverkar inte DNS-prestandan. En prestandaroutningsmetod kan dock påverka programupplevelsen negativt. Om din princip till exempel omdirigerar trafik från Nordamerika till en instans som finns i Asien kan nätverksfördröjningen för dessa sessioner vara ett prestandaproblem.

## <a name="measuring-traffic-manager-performance"></a>Mäta traffic manager-prestanda

Det finns flera webbplatser som du kan använda för att förstå prestanda och beteende för en Traffic Manager-profil. Många av dessa webbplatser är gratis men kan ha begränsningar. Vissa webbplatser erbjuder förbättrad övervakning och rapportering mot en avgift.

Verktygen på dessa platser mäter DNS-svarstider och visar de lösta IP-adresserna för klientplatser runt om i världen. De flesta av dessa verktyg cachelagrar inte DNS-resultaten. Därför visar verktygen den fullständiga DNS-sökningen varje gång ett test körs. När du testar från din egen klient får du bara full DNS-uppslagsprestanda en gång under TTL-varaktigheten.

## <a name="sample-tools-to-measure-dns-performance"></a>Exempelverktyg för att mäta DNS-prestanda

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS erbjuder många prestandaverktyg. Verktyget DNS-jämförelse kan visa hur lång tid det tar att matcha ditt DNS-namn och hur det kan jämföras med andra DNS-tjänstleverantörer.

* [Websitepulse](https://www.websitepulse.com/help/tools.php)

    Ett av de enklaste verktygen är WebSitePulse. Ange URL:en för att se DNS-matchningstid, Första byte, Senaste byte och annan prestandastatistik. Du kan välja mellan tre olika testplatser. I det här exemplet ser du att den första körningen visar att DNS-sökning tar 0,204 sek.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Eftersom resultaten cachelagras tar det andra testet för samma Traffic Manager-slutpunkt som DNS-sökningen tar 0,002 sek.

    ![puls2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Den syntetiska övervakaren för CA-appen](https://asm.ca.com/en/checkit.php)

    Den här webbplatsen, som tidigare kallades verktyget Kontroll av bevakningsmus, visar dns-matchningstiden från flera geografiska regioner samtidigt. Ange URL:en för att se DNS-matchningstid, anslutningstid och hastighet från flera geografiska platser. Använd det här testet för att se vilken värdtjänst som returneras för olika platser runt om i världen.

    ![puls1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Det här verktyget tillhandahåller resultatstatistik för varje element på en webbsida. På fliken Sidanalys visas procentandelen tid som lagts ned på DNS-sökning.

* [Vad är min DNS?](https://www.whatsmydns.net/)

    Den här webbplatsen gör en DNS-sökning från 20 olika platser och visar resultaten på en karta.

* [Dig webbgränssnitt](https://www.digwebinterface.com)

    Den här webbplatsen visar mer detaljerad DNS-information, inklusive CNAMEs och A-poster. Kontrollera att du kontrollerar "Colorize output" och "Stats" under alternativ och välj "Alla" under Nameservers.

## <a name="next-steps"></a>Efterföljande moment

[Om trafikhanterare trafikroutningsmetoder](traffic-manager-routing-methods.md)

[Testa dina Traffic Manager-inställningar](traffic-manager-testing-settings.md)

[Åtgärder i Traffic Manager (REST-API-referens)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager Cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager)

