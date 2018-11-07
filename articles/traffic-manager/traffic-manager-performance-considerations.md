---
title: Prestandaöverväganden för Azure Traffic Manager | Microsoft Docs
description: Förstå prestanda på Traffic Manager och hur du testar prestanda för din webbplats när du använder Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 1bf2222e09644520bbfc6c5424c7f29d05b3c799
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257705"
---
# <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

Den här sidan förklaras prestandaöverväganden med Traffic Manager. Föreställ dig följande:

Du har instanser av din webbplats i WestUS och Asienöstra. Någon av instanserna misslyckas efter att hälsokontrollen för traffic manager avsökningen. Programtrafik dirigeras till regionen felfritt. Den här redundansen förväntas men prestanda kan vara ett problem utifrån fördröjning av all nu trafik till en avlägsen region.

## <a name="performance-considerations-for-traffic-manager"></a>Prestandaöverväganden för Traffic Manager

Den enda prestandapåverkan Traffic Manager kan ha på din webbplats är den första DNS-sökningen. En DNS-begäran för namnet på Traffic Manager-profilen hanteras av Microsoft DNS-rotserver som är värd för trafficmanager.net-zonen. Traffic Manager fylls och uppdaterar regelbundet, Microsofts DNS rotservrar baserat på Traffic Manager-principen och avsökning resultaten. Så även under den första DNS-sökningen skickas inga DNS-frågor till Traffic Manager.

Traffic Manager består av flera komponenter: DNS-namn-servrar, en API-tjänst, storage-skiktet och en slutpunkt som övervakningstjänsten. Om en tjänstkomponent för Traffic Manager-misslyckas, finns det ingen effekt på DNS-namn som är associerade med Traffic Manager-profilen. Posterna i Microsoft DNS-servrar ändras inte. Slutpunktsövervakning av och uppdatering av DNS inte inträffar emellertid. Traffic Manager är därför inte att uppdatera DNS-server så att den pekar på webbplatsen växling vid fel när din primära plats kraschar.

DNS-namnmatchningen går snabbt och resultatet cachelagras. Hastigheten på den första DNS-sökningen beror på DNS-servrar som klienten använder för namnmatchning. En klient kan normalt att slutföra en DNS-sökning i ~ 50 ms. Resultaten av sökningen cachelagras under hela DNS-Time-to-live (TTL). Standard-TTL för Traffic Manager är 300 sekunder.

Trafiken flödar inte via Traffic Manager. När DNS-sökningen har slutförts kan har klienten en IP-adress för en instans av din webbplats. Klienten ansluter direkt till adressen och skickar inte via Traffic Manager. Traffic Manager-principen som du väljer påverkas inte DNS-prestanda. En prestanda routning-metod kan negativt påverka upplevelsen. Om din princip omdirigerar trafik från Nordamerika till en instans i Asien, till exempel vara svarstiden i nätverk för dessa sessioner prestandaproblem.

## <a name="measuring-traffic-manager-performance"></a>Mäta Traffic Manager-prestanda

Det finns flera webbplatser som du kan använda för att förstå prestanda och beteendet för en Traffic Manager-profil. Många av dessa platser är kostnadsfria, men du kan ha begränsningar. Vissa webbplatser erbjuder förbättrad övervakning och rapportering för en avgift.

Verktygen på dessa platser mått DNS svarstider och visa löst IP-adresser för klientplatser runtom i världen. De flesta av dessa verktyg Cachelagra inte DNS-resultat. Därför visar verktygen fullständigt DNS-sökning varje gång som en testkörning. När du testar från din egen klient uppstår endast fullständiga DNS-sökning prestanda för en gång under hela TTL-värde.

## <a name="sample-tools-to-measure-dns-performance"></a>Exempel-verktyg för att mäta DNS-prestanda

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS erbjuder många prestandaverktyg. Jämförelse av DNS-verktyget kan du se hur lång tid det tar för att matcha DNS-namnet och hur som jämför för andra DNS-tjänster.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    Ett av de enklaste verktygen är WebSitePulse. Ange URL: en om du vill se DNS-matchningstid, första byten, sista byten och andra prestandastatistik. Du kan välja mellan tre olika testplatser. I det här exemplet ser du att den första körningen visar att DNS-sökning tar 0.204 sek.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Eftersom resultatet cachelagras andra test för samma slutpunkt i Traffic Manager DNS-sökning tar 0.002 sek.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Övervakare för CA-App syntetiska](https://asm.ca.com/en/checkit.php)

    Kallades tidigare för verktyget Watchmouse Kontrollera webbplats, den här platsen visar DNS-matchningstid från flera geografiska områden samtidigt. Ange URL: en om du vill se DNS-matchningstid, anslutningstid och hastighet från flera geografiska platser. Använd det här testet om du vill se vilka värdtjänsten returneras för olika platser runt om i världen.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Det här verktyget förser prestandastatistik för varje element i en webbsida. Fliken sidan analys visar procentuella andelen tid som krävs för DNS-sökning.

* [Vad är min DNS?](http://www.whatsmydns.net/)

    Den här platsen har en DNS-sökning från 20 olika platser och visar resultat på en karta.

* [Fördjupa webbgränssnitt](http://www.digwebinterface.com)

    Den här platsen visar mer detaljerad DNS-information, inklusive CNAME-poster och A-poster. Kontrollera att du kontrollerar du 'Färga utdata ”och” statistik ”under Alternativ och Välj” alla ”under namnservrar.

## <a name="next-steps"></a>Nästa steg

[Om Traffic Manager routningsmetoder](traffic-manager-routing-methods.md)

[Testa dina Traffic Manager-inställningar](traffic-manager-testing-settings.md)

[Åtgärder för Traffic Manager (REST API-referens)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-cmdletar](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager)

