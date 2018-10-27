---
title: Konfigurera prioritet trafikroutningsmetod med Azure Traffic Manager | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar prioritet trafikroutningsmetod i Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 52bfc73ad586bb3852e30a850d4ba50a113183d3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140425"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Konfigurera prioriterad routningsmetod för trafik i Traffic Manager

Oavsett webbplatsläge ger Azure Websites redan redundansfunktionen för webbplatser inom ett datacenter (även kallat en region). Traffic Manager ger redundans för webbplatser på olika datacenter.

Ett vanligt mönster för-redundans är att skicka trafik till en primär tjänst och tillhandahåller en uppsättning identiska Säkerhetskopieringstjänster för redundans. Följande steg beskriver hur du konfigurerar den här prioriterade redundansen med Azure molntjänster och webbplatser:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Så här konfigurerar du prioritet trafikroutningsmetod

1. Logga in på [Azure Portal](http://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfältet söker du efter den **Traffic Manager-profiler** och klicka sedan på namnet på profilen som du vill konfigurera routningsmetod för.
3. I den **Traffic Manager-profil** bladet, kontrollera att både molntjänster och webbplatser som du vill ska ingå i din konfiguration finns.
4. I den **inställningar** klickar du på **Configuration**, och i den **Configuration** bladet gör du följande:
    1. För **trafik routning metodinställningar**, kontrollera att trafikroutningsmetod är **prioritet**. Om det inte är det, klickar du på **prioritet** från den nedrullningsbara listan.
    2. Ange den **inställningar för Slutpunktsövervakning** identiska för alla alla slutpunkter i den här profilen på följande sätt:
        1. Välj lämpliga **protokollet**, och ange den **Port** tal. 
        2. För **sökväg** skriver ett snedstreck */*. Du måste ange en sökväg och filnamn för att övervaka slutpunkter. Ett snedstreck ”/” är en giltig post för den relativa sökvägen och innebär att filen är i rotkatalogen (standard).
        3. Överst på sidan klickar du på **spara**.
5. I den **inställningar** klickar du på **slutpunkter**.
6. I den **slutpunkter** bladet granska prioritetsordning för dina slutpunkter. När du väljer den **prioritet** trafikroutningsmetod, ordningen på de valda slutpunkter frågor. Kontrollera prioritetsordningen för slutpunkter.  Den primära slutpunkten är längst upp. Kontrollera i den ordning som visas. alla begäranden som kommer att dirigeras till den första slutpunkten och om Traffic Manager identifierar den vara felaktig, trafiken växlar automatiskt över till nästa slutpunkten. 
7. Om du vill ändra prioritetsordningen slutpunkten klickar du på slutpunkten, och i den **Endpoint** bladet som visas, klickar du på **redigera** och ändra den **prioritet** värde efter behov. 
8. Klicka på **spara** spara ändra slutpunktsinställningarna för.
9. När du har slutfört konfigurationsändringarna klickar du på **spara** längst ned på sidan.
10. Testa ändringarna i konfigurationen på följande sätt:
    1.  I portalens sökfältet, Sök efter namnet på Traffic Manager-profilen och klickar på Traffic Manager-profilen i resultaten som visas.
    2.  I den **Traffic Manager** profilera bladet, klickar du på **översikt**.
    3.  Den **Traffic Manager-profil** visar bladet DNS-namnet på din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att dirigeras till rätt slutpunkten som bestäms av routningstyp. I det här fallet alla begäranden dirigeras till den första slutpunkten och om Traffic Manager identifierar den vara felaktig, trafiken växlar automatiskt över till nästa slutpunkten.
11. När Traffic Manager-profilen fungerar kan du redigera DNS-posten på auktoritativa DNS-servern att peka företagets domännamn till domännamnet för Traffic Manager.

![Konfigurera prioritet trafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg


- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png