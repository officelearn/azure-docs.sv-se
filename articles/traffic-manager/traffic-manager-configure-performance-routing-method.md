---
title: Konfigurera prestanda trafikroutningsmetod med Azure Traffic Manager | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar Traffic Manager kan dirigera trafik till slutpunkten med kortast svarstid
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
ms.openlocfilehash: c378043a9e10a0aed5344ac3182af6163d217c7b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140476"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurera routningsmetod för prestanda-trafik

Routningsmetod för prestanda-trafik kan du dirigera trafik till slutpunkten med kortast svarstid från klientens nätverk. Datacenter med kortast svarstid är vanligtvis den närmaste i geografiska avstånd. Den här trafikroutningsmetod kan inte hänsyn till i realtid ändringar i nätverkskonfigurationen eller läsa in.

##  <a name="to-configure-performance-routing-method"></a>Konfigurera routningsmetod för prestanda

1. Logga in på [Azure Portal](http://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfältet söker du efter den **Traffic Manager-profiler** och klicka sedan på namnet på profilen som du vill konfigurera routningsmetod för.
3. I den **Traffic Manager-profil** bladet, kontrollera att både molntjänster och webbplatser som du vill ska ingå i din konfiguration finns.
4. I den **inställningar** klickar du på **Configuration**, och i den **Configuration** bladet gör du följande:
    1. För **trafik routning metodinställningar**, för **routningsmetod** Välj **prestanda**.
    2. Ange den **inställningar för Slutpunktsövervakning** identiska för alla alla slutpunkter i den här profilen på följande sätt:
        1. Välj lämpliga **protokollet**, och ange den **Port** tal. 
        2. För **sökväg** skriver ett snedstreck */*. Du måste ange en sökväg och filnamn för att övervaka slutpunkter. Ett snedstreck ”/” är en giltig post för den relativa sökvägen och innebär att filen är i rotkatalogen (standard).
        3. Överst på sidan klickar du på **spara**.
5.  Testa ändringarna i konfigurationen på följande sätt:
    1.  I portalens sökfältet, Sök efter namnet på Traffic Manager-profilen och klickar på Traffic Manager-profilen i resultaten som visas.
    2.  I den **Traffic Manager** profilera bladet, klickar du på **översikt**.
    3.  Den **Traffic Manager-profil** visar bladet DNS-namnet på din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att dirigeras till rätt slutpunkten som bestäms av routningstyp. I det här fallet alla begäranden dirigeras till slutpunkten med kortast svarstid från klientens nätverk.
6. När Traffic Manager-profilen fungerar kan du redigera DNS-posten på auktoritativa DNS-servern att peka företagets domännamn till domännamnet för Traffic Manager.

![Konfigurera prestanda trafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png