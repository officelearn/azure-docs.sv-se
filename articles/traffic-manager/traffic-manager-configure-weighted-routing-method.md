---
title: Självstudie – konfigurera viktad trafik för resursallokering med Azure Traffic Manager
description: I den här självstudien lär du dig att belastningsutjämna trafik med en Round-Robin-metod i Traffic Manager
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: allensu
ms.openlocfilehash: 06bb86e635b7b3377e1f313ef3aa3487e1c215bc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422765"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Självstudie: Konfigurera den viktade routningsmetoden för trafik i Traffic Manager

Ett gemensamt metod mönster för trafik cirkulation är att tillhandahålla en uppsättning identiska slut punkter, bland annat moln tjänster och webbplatser, och skicka trafik till var och en. Följande steg beskriver hur du konfigurerar den här typen av metod för trafik dirigering.

> [!NOTE]
> Azure Web App tillhandahåller redan Round Robin-funktioner för belastnings utjämning för webbplatser i en Azure-region (som kan bestå av flera data Center). Med Traffic Manager kan du distribuera trafik mellan webbplatser i olika data Center.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Så här konfigurerar du den viktade routningsmetoden för trafik

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfält söker du efter **Traffic Manager profiler** och klickar sedan på det profil namn som du vill konfigurera routningsmetod för.
3. På bladet **Traffic Manager profil** kontrollerar du att både de moln tjänster och webbplatser som du vill inkludera i konfigurationen finns.
4. I avsnittet **Inställningar** klickar du på **konfiguration**och slutför i **konfigurations** bladet på följande sätt:
    1. För **Inställningar för trafikroutnings metod**kontrollerar du att metoden för trafikroutning **viktas**. Om så inte är fallet klickar du på **viktad** från List rutan.
    2. Ange **Inställningar för slut punkts övervakaren** identisk för alla slut punkter i den här profilen enligt följande:
        1. Välj lämpligt **protokoll**och ange **port** numret. 
        2. För **sökväg** anger du ett snedstreck */* . Om du vill övervaka slut punkter måste du ange en sökväg och ett fil namn. Ett snedstreck "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rot katalogen (standard).
        3. Klicka på **Spara**längst upp på sidan.
5. Testa ändringarna i konfigurationen på följande sätt:
    1.  Sök efter namnet på Traffic Manager profilen i portalens sökfält och klicka på Traffic Manager profilen i resultaten som visas.
    2.  I bladet **Traffic Manager** profil klickar du på **Översikt**.
    3.  Bladet **Traffic Manager profil** visar DNS-namnet för din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av typen av routning. I det här fallet dirigeras alla begär anden till varje slut punkt i en Round-Robin-miljö.
6. När din Traffic Manager-profil fungerar redigerar du DNS-posten på den auktoritativa DNS-servern för att peka ditt företags domän namn till Traffic Manager domän namnet.

![Konfigurera metoden för att dirigera en viktad trafik med hjälp av Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för prioritets trafik](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om [routningsmetod för prestanda trafik](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testar Traffic Manager inställningar](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
