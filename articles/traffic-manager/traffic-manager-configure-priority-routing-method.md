---
title: Självstudiekurs - Konfigurera prioriterad trafikroutning med Azure Traffic Manager
description: I den här självstudien beskrivs hur du konfigurerar metoden för prioriterad trafikroutning i Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: ca223a19ff7ddeae95878f941f3cf295664e62b4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938734"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Självstudiekurs: Konfigurera prioritetstrafikroutningsmetod i Traffic Manager

Oavsett webbplatsläge tillhandahåller Azure-webbplatser redan redundansfunktioner för webbplatser i ett datacenter (kallas även en region). Traffic Manager tillhandahåller redundans för webbplatser i olika datacenter.

Ett vanligt mönster för växling av tjänsten är att skicka trafik till en primär tjänst och tillhandahålla en uppsättning identiska säkerhetskopieringstjänster för redundans. I följande steg beskrivs hur du konfigurerar den här prioriterade redundansen med Azure-molntjänster och webbplatser:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Så här konfigurerar du metoden för prioritetstrafikroutning

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. Sök efter **Traffic Manager-profilerna** i portalens sökfält och klicka sedan på det profilnamn som du vill konfigurera routningsmetoden för.
3. I **profilbladet Traffic Manager** kontrollerar du att både molntjänsterna och webbplatserna som du vill inkludera i konfigurationen finns.
4. Klicka på **Konfiguration**i avsnittet **Inställningar** och slutför följande i **bladet Konfiguration:**
    1. För **inställningar för trafikroutningsmetod**kontrollerar du att trafikroutningsmetoden är **Prioritet**. Om den inte är det klickar du på **Prioritet** i listrutan.
    2. Ange **slutpunktsövervakarens inställningar** identiska för alla slutpunkter i den här profilen enligt följande:
        1. Välj lämpligt **protokoll**och ange **portnumret.** 
        2. För **Bana** skriv */* ett snedstreck . Om du vill övervaka slutpunkter måste du ange en sökväg och ett filnamn. Ett snedstreck för framåt "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rotkatalogen (standard).
        3. Klicka på **Spara**högst upp på sidan .
5. Klicka på **Slutpunkter**i avsnittet **Inställningar** .
6. Granska prioritetsordningen för slutpunkter i bladet **Slutpunkter.** När du väljer metoden **Prioriterad** trafikroutning spelar ordningen på de valda slutpunkterna roll. Verifiera slutpunkternas prioritetsordning.  Den primära slutpunkten är överst. Dubbelkolla på den ordning den visas. alla begäranden dirigeras till den första slutpunkten och om Traffic Manager upptäcker att det är felfritt, växlar trafiken automatiskt över till nästa slutpunkt. 
7. Om du vill ändra slutpunktsprioriteten klickar du på slutpunkten och klickar **Priority** på **Redigera** i **slutpunktsbladet** som visas. 
8. Klicka på **Spara** om du vill spara ändring av slutpunktsinställningarna.
9. När du har slutfört konfigurationsändringarna klickar du på **Spara** längst ned på sidan.
10. Testa ändringarna i konfigurationen på följande sätt:
    1.  Sök efter Profilnamnet Traffic Manager i portalens sökfält och klicka på Traffic Manager-profilen i de resultat som visas.
    2.  Klicka på **Översikt**i profilbladet **Traffic Manager** .
    3.  Profilbladet **Traffic Manager** visar DNS-namnet på din nyskapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till rätt slutpunkt som bestäms av routningstypen. I det här fallet dirigeras alla begäranden till den första slutpunkten och om Traffic Manager upptäcker att det är fel, växlar trafiken automatiskt över till nästa slutpunkt.
11. När traffic manager-profilen har fungerat redigerar du DNS-posten på den auktoritära DNS-servern för att peka företagets domännamn på Traffic Manager-domännamnet.

![Konfigurera prioritetstrafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg


- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Läs mer om [prestandaroutningsmetod](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Läs om hur du [testar Traffic Manager-inställningar](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png