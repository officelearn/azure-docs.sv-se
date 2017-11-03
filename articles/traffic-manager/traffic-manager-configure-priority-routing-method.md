---
title: "Konfigurera prioritet trafikroutningsmetod med hjälp av Azure Traffic Manager | Microsoft Docs"
description: "Den här artikeln förklarar hur du konfigurerar prioritet trafikroutningsmetod i Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 0db83cde6facc89b8b8aa72e6419129ec868235c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Konfigurera prioritet trafikroutningsmetod i Traffic Manager

Oavsett webbplatsläge innehåller Azure Websites redan failover-funktioner för webbplatser inom ett datacenter (även kallat en region). Traffic Manager ger redundans för webbplatser i olika datacenter.

Ett vanligt mönster för-redundans är att skicka trafik till en primär tjänst och ger en uppsättning av identiska Säkerhetskopieringstjänster för redundans. Följande steg förklarar hur du konfigurerar den här prioriterad redundansen med Azure-molntjänster och webbplatser:

## <a name="to-configure-the-priority-traffic-routing-method"></a>Så här konfigurerar du prioritet trafikroutningsmetod

1. Logga in på [Azure Portal](http://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfältet, söker du efter den **Traffic Manager-profiler** och klicka sedan på namnet på profilen som du vill konfigurera routningsmetoden för.
3. I den **trafikhanterarprofil** bladet, kontrollera att både molntjänster och webbplatser som du vill ska ingå i konfigurationen finns kvar.
4. I den **inställningar** klickar du på **Configuration**, och i den **Configuration** bladet slutförts enligt följande:
    1. För **trafik routning inställningar**, kontrollera att trafikroutningsmetod **prioritet**. Om det inte är det, klickar du på **prioritet** från den nedrullningsbara listan.
    2. Ange den **övervakaren slutpunktsinställningar** identiska för alla alla slutpunkter i den här profilen på följande sätt:
        1. Välj lämpliga **protokollet**, och ange den **Port** nummer. 
        2. För **sökväg** skriver ett snedstreck  */* . Du måste ange en sökväg och filnamn för att övervaka slutpunkter. Ett snedstreck ”/” är ett giltigt för den relativa sökvägen och innebär att filen är i rotkatalogen (standard).
        3. Överst på sidan, klickar du på **spara**.
5. I den **inställningar** klickar du på **slutpunkter**.
6. I den **slutpunkter** bladet granska prioritetsordning för dina slutpunkter. När du väljer den **prioritet** trafikroutningsmetod, ordningen på de valda slutpunkter frågor. Kontrollera prioritetsordning slutpunkter.  Primär slutpunkt är längst upp. Kontrollera i den ordning som den visas. alla begäranden som kommer att dirigeras till den första slutpunkten och om en Traffic Manager identifierar den feltillstånd, trafiken växlar automatiskt till nästa slutpunkt. 
7. Om du vill ändra prioritetsordningen endpoint klickar du på slutpunkten, och i den **Endpoint** bladet som visas, klickar du på **redigera** och ändra den **prioritet** värdet vid behov. 
8. Klicka på **spara** spara ändra inställningarna för slutpunkten.
9. När du har slutfört konfigurationsändringarna klickar du på **spara** längst ned på sidan.
10. Testa ändringar i konfigurationen på följande sätt:
    1.  I portalens sökfältet, söka efter namnet på Traffic Manager-profilen och klickar på Traffic Manager-profilen i resultatet som de visas.
    2.  I den **Traffic Manager** profilen bladet, klickar du på **översikt**.
    3.  Den **trafikhanterarprofil** bladet visar DNS-namnet för nyskapade Traffic Manager-profilen. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att hämta dirigeras till rätt slutpunkten som bestäms av typen routning. I det här fallet alla begäranden som dirigeras till den första slutpunkten och om Traffic Manager identifierar den vara feltillstånd, trafiken växlar automatiskt till nästa slutpunkt.
11. När Traffic Manager-profilen fungerar redigera DNS-posten på auktoritära DNS-servern att peka företagets domännamn till domännamnet för Traffic Manager.

![Konfigurera prioritet trafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg


- Lär dig mer om [viktas trafikroutningsmetod](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [routningsmetoden för prestanda](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png