---
title: Konfigurera viktad resursallokering trafikroutningsmetod med Azure Traffic Manager | Microsoft Docs
description: Den här artikeln förklarar hur du belastningsutjämna trafik med hjälp av en resursallokering-metod i Traffic Manager
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
ms.openlocfilehash: 132cbecdad8303414f15ff3f18caaccd6d430ded
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154947"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Konfigurera viktad trafikroutningsmetod i Traffic Manager

Ett vanligt trafik routning metoden mönster är att tillhandahålla en uppsättning identiska slutpunkter, bland annat molntjänster och webbplatser, och skicka trafik till varje lika. Följande steg beskriver hur du konfigurerar den här typen av trafikroutningsmetod.

> [!NOTE]
> Med Azure Web Apps tillhandahåller redan belastningsutjämning för resursallokering för webbplatser inom en Azure-Region (som kan innehålla flera datacenter). Traffic Manager kan du distribuera trafik mellan webbplatser på olika datacenter.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Konfigurera viktad metod för trafikroutning

1. Logga in på [Azure Portal](http://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfältet söker du efter den **Traffic Manager-profiler** och klicka sedan på namnet på profilen som du vill konfigurera routningsmetod för.
3. I den **Traffic Manager-profil** bladet, kontrollera att både molntjänster och webbplatser som du vill ska ingå i din konfiguration finns.
4. I den **inställningar** klickar du på **Configuration**, och i den **Configuration** bladet gör du följande:
    1. För **trafik routning metodinställningar**, kontrollera att trafikroutningsmetod är **viktat**. Om det inte är det, klickar du på **viktat** från den nedrullningsbara listan.
    2. Ange den **inställningar för Slutpunktsövervakning** identiska för alla alla slutpunkter i den här profilen på följande sätt:
        1. Välj lämpliga **protokollet**, och ange den **Port** tal. 
        2. För **sökväg** skriver ett snedstreck */*. Du måste ange en sökväg och filnamn för att övervaka slutpunkter. Ett snedstreck ”/” är en giltig post för den relativa sökvägen och innebär att filen är i rotkatalogen (standard).
        3. Överst på sidan klickar du på **spara**.
5. Testa ändringarna i konfigurationen på följande sätt:
    1.  I portalens sökfältet, Sök efter namnet på Traffic Manager-profilen och klickar på Traffic Manager-profilen i resultaten som visas.
    2.  I den **Traffic Manager** profilera bladet, klickar du på **översikt**.
    3.  Den **Traffic Manager-profil** visar bladet DNS-namnet på din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att dirigeras till rätt slutpunkten som bestäms av routningstyp. I det här fallet alla begäranden dirigeras varje slutpunkt i en resursallokering.
6. När Traffic Manager-profilen fungerar kan du redigera DNS-posten på auktoritativa DNS-servern att peka företagets domännamn till domännamnet för Traffic Manager.

![Konfigurera viktad trafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [prioritet prestandatrafik](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om [prestanda prestandatrafik](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
