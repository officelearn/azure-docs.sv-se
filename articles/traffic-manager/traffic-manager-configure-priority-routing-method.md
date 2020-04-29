---
title: Självstudie – konfigurera prioriterad trafik routning med Azure Traffic Manager
description: I den här självstudien beskrivs hur du konfigurerar routningsmetoden för prioritets trafik i Traffic Manager
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76938734"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Självstudie: Konfigurera prioritet för trafik cirkulation i Traffic Manager

Oberoende av webbplats läget innehåller Azure Websites redan funktioner för redundans för webbplatser i ett Data Center (kallas även region). Traffic Manager tillhandahåller redundans för webbplatser i olika data Center.

Ett vanligt mönster för redundansväxling av tjänster är att skicka trafik till en primär tjänst och tillhandahålla en uppsättning identiska säkerhets kopierings tjänster för redundans. Följande steg beskriver hur du konfigurerar prioriterad redundans med Azure Cloud Services och Websites:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Så här konfigurerar du metoden för prioritets väg för trafik

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfält söker du efter **Traffic Manager profiler** och klickar sedan på det profil namn som du vill konfigurera routningsmetod för.
3. På bladet **Traffic Manager profil** kontrollerar du att både de moln tjänster och webbplatser som du vill inkludera i konfigurationen finns.
4. I avsnittet **Inställningar** klickar du på **konfiguration**och slutför i **konfigurations** bladet på följande sätt:
    1. För **Inställningar för trafikroutnings metod**kontrollerar du att metoden för trafikroutning är **prioritet**. Om så inte är fallet klickar du på **prioritet** i list rutan.
    2. Ange **Inställningar för slut punkts övervakaren** identisk för alla slut punkter i den här profilen enligt följande:
        1. Välj lämpligt **protokoll**och ange **port** numret. 
        2. För **sökväg** anger du ett snedstreck */*. Om du vill övervaka slut punkter måste du ange en sökväg och ett fil namn. Ett snedstreck "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rot katalogen (standard).
        3. Klicka på **Spara**längst upp på sidan.
5. I avsnittet **Inställningar** klickar du på **slut punkter**.
6. På bladet **slut punkter** granskar du prioritets ordningen för dina slut punkter. När du väljer metoden **prioriterad** trafikroutning är ordningen för de valda slut punkterna. Kontrol lera prioritetsordningen för slut punkter.  Den primära slut punkten är överst. Dubbel kontroll av den ordning som den visas. alla begär Anden dirigeras till den första slut punkten och om Traffic Manager identifierar att den inte är felfri växlar trafiken automatiskt över till nästa slut punkt. 
7. Om du vill ändra prioritets ordning för slut punkten klickar du på slut punkten och på bladet **slut punkt** som visas klickar du på **Redigera** och ändrar **prioritet** svärdet efter behov. 
8. Klicka på **Spara** för att spara ändringar av slut punkts inställningarna.
9. När du har slutfört konfigurations ändringarna klickar du på **Spara** längst ned på sidan.
10. Testa ändringarna i konfigurationen på följande sätt:
    1.  Sök efter namnet på Traffic Manager profilen i portalens sökfält och klicka på Traffic Manager profilen i resultaten som visas.
    2.  I bladet **Traffic Manager** profil klickar du på **Översikt**.
    3.  Bladet **Traffic Manager profil** visar DNS-namnet för din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av typen av routning. I det här fallet dirigeras alla begär anden till den första slut punkten och om Traffic Manager identifierar att den inte är felfri växlar trafiken automatiskt över till nästa slut punkt.
11. När din Traffic Manager-profil fungerar redigerar du DNS-posten på den auktoritativa DNS-servern för att peka ditt företags domän namn till Traffic Manager domän namnet.

![Konfigurera Traffic routing-metoden med hjälp av Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg


- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testar Traffic Manager inställningar](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png