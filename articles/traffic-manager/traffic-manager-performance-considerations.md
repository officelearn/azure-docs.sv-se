---
title: "Prestandaöverväganden för Azure Traffic Manager | Microsoft Docs"
description: "Förstå prestanda på Traffic Manager och hur du testar prestanda för din webbplats när du använder Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

Den här sidan förklarar prestandaöverväganden med Traffic Manager. Studera följande scenario:

Du har instanser av din webbplats i WestUS och EastAsia regioner. Någon av instanserna misslyckas hälsokontrollen för traffic manager avsökningen. Programmet trafik dirigeras till Felfri region. Den här redundansen förväntas men prestanda kan vara ett problem utifrån svarstiden för trafik nu till en avlägsna region.

## <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

Endast prestandapåverkan Traffic Manager kan ha på din webbplats är den första DNS-sökningen. En DNS-begäran för namnet på Traffic Manager-profilen hanteras av Microsoft DNS-rotservern som är värd för zonen trafficmanager.net. Traffic Manager fylls och uppdaterar regelbundet, Microsofts DNS rotservrar baserat på Traffic Manager-principen och avsökning resultat. Så även under den första DNS-sökningen skickas ingen DNS-frågor i Traffic Manager.

Traffic Manager består av flera komponenter: DNS-namn-servrar, en API-tjänsten, lagringsskiktet och en slutpunkt övervakningstjänsten. Om en Traffic Manager-tjänstkomponent misslyckas, finns det ingen effekt på DNS-namnet som associeras med Traffic Manager-profilen. Posterna i Microsoft DNS-servrar ändras inte. Dock slutpunktsövervakning och DNS-uppdatering inte sker. Traffic Manager är därför inte kan uppdatera DNS så att den pekar på webbplatsen växling vid fel när din primära plats kraschar.

DNS-namnmatchningen är snabb och resultat cachelagras. Hastigheten på den första DNS-sökningen beror på DNS-servrar som klienten använder för namnmatchning. En klient kan normalt slutföras en DNS-sökning i ~ 50 ms. Resultat av sökningen cachelagras under hela DNS-Time-to-live (TTL). Standard-TTL för Traffic Manager är 300 sekunder.

Trafiken flödar inte via Traffic Manager. När DNS-sökning har slutförts, har klienten en IP-adress för en instans av webbplatsen. Klienten ansluter direkt till den adressen och klarar inte via Traffic Manager. Traffic Manager-princip som du väljer påverkas inte DNS-prestanda. Dock kan en prestanda routning-metod påverka upplevelsen program. Om din princip dirigerar trafik från Nordamerika till en instans som finns i Asien, kan Nätverksfördröjningen för dessa sessioner vara ett prestandaproblem.

## <a name="measuring-traffic-manager-performance"></a>Mäter Traffic Manager-prestanda

Det finns flera webbplatser som du kan använda för att förstå prestanda och beteendet för en Traffic Manager-profilen. Många av dessa platser är gratis men kan ha begränsningar. Vissa webbplatser erbjuder förbättrad övervakning och rapportering för en avgift.

Verktygen på dessa platser mått DNS svarstiderna och visa matcha IP-adresser för klientplatser över hela världen. De flesta av dessa verktyg cachelagrar inte DNS-resultat. Därför visa verktygen fullständig DNS-sökning varje gång ett test körs. När du testar din egen klient uppstår endast fullständig DNS-sökning prestanda för en gång under TTL-värde.

## <a name="sample-tools-to-measure-dns-performance"></a>Exempel verktyg för att mäta DNS-prestanda

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS erbjuder många prestandaverktyg. Jämförelse av DNS-verktyget kan du se hur lång tid det tar för att matcha DNS-namn och hur som jämför till andra DNS-leverantörer.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    En av de enklaste verktyg är WebSitePulse. Ange URL: en för att se DNS-matchningstid, första byten, sista byten och andra prestandastatistik. Du kan välja mellan tre olika test-platser. I det här exemplet ser du att den första körningen visar att DNS-sökning tar 0.204 sek.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Eftersom resultatet cachelagras andra test för samma Traffic Manager-slutpunkten DNS-sökning tar 0.002 sek.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Kanada App syntetiska Övervakare](https://asm.ca.com/en/checkit.php)

    Kallades tidigare för verktyget Watchmouse Kontrollera webbplats, platsen visar DNS-matchningstiden från flera geografiska områden samtidigt. Ange URL: en för att se DNS-matchningstid, tid och hastighet från flera geografiska platser. Använd det här testet om du vill se vilka värdtjänsten returneras för olika platser i världen.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Det här verktyget innehåller prestandastatistik för varje element i en webbsida. Fliken sidan analys visar procentandelen tid som krävs för DNS-sökning.

* [Vad är min DNS?](http://www.whatsmydns.net/)

    Den här platsen har en DNS-sökning från 20 olika platser och visar resultaten på en karta.

* [Gräva webbgränssnitt](http://www.digwebinterface.com)

    Den här platsen visar mer detaljerad DNS-information, inklusive skapa CNAME-poster och A-poster. Kontrollera att du kontrollera 'Färga utdata' och 'statistik, under Alternativ och välj 'All' under Nameservers.

## <a name="next-steps"></a>Nästa steg

[Om Traffic Manager-trafikroutningsmetoder](traffic-manager-routing-methods.md)

[Testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md)

[Åtgärder för Traffic Manager (REST API-referens)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-Cmdlets](http://go.microsoft.com/fwlink/p/?LinkId=400769)

