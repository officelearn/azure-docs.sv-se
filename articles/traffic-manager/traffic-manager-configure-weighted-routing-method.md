---
title: Självstudiekurs - Konfigurera viktad routning av round-robin-trafik med Azure Traffic Manager
description: I den här självstudien beskrivs hur du läser in trafik med hjälp av en round-robin-metod i Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: 8bdc710b36cae70d29d32333f431b8a9dda154cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938713"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Självstudiekurs: Konfigurera den viktade trafikroutningsmetoden i Traffic Manager

Ett vanligt trafikroutningsmetodmönster är att tillhandahålla en uppsättning identiska slutpunkter, som inkluderar molntjänster och webbplatser, och skicka trafik till var och en lika. I följande steg beskrivs hur du konfigurerar den här typen av trafikroutningsmetod.

> [!NOTE]
> Azure Web App tillhandahåller redan funktioner för belastningsutjämning av round-robin för webbplatser inom en Azure-region (som kan bestå av flera datacenter). Med Traffic Manager kan du distribuera trafik mellan webbplatser i olika datacenter.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Så här konfigurerar du den viktade trafikroutningsmetoden

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. Sök efter **Traffic Manager-profilerna** i portalens sökfält och klicka sedan på det profilnamn som du vill konfigurera routningsmetoden för.
3. I **profilbladet Traffic Manager** kontrollerar du att både molntjänsterna och webbplatserna som du vill inkludera i konfigurationen finns.
4. Klicka på **Konfiguration**i avsnittet **Inställningar** och slutför följande i **bladet Konfiguration:**
    1. För **inställningar för trafikroutningsmetod**kontrollerar du att trafikroutningsmetoden är **Viktad**. Om den inte är det klickar du på **Viktad** från listrutan.
    2. Ange **slutpunktsövervakarens inställningar** identiska för alla slutpunkter i den här profilen enligt följande:
        1. Välj lämpligt **protokoll**och ange **portnumret.** 
        2. För **Bana** skriv */* ett snedstreck . Om du vill övervaka slutpunkter måste du ange en sökväg och ett filnamn. Ett snedstreck för framåt "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rotkatalogen (standard).
        3. Klicka på **Spara**högst upp på sidan .
5. Testa ändringarna i konfigurationen på följande sätt:
    1.  Sök efter Profilnamnet Traffic Manager i portalens sökfält och klicka på Traffic Manager-profilen i de resultat som visas.
    2.  Klicka på **Översikt**i profilbladet **Traffic Manager** .
    3.  Profilbladet **Traffic Manager** visar DNS-namnet på din nyskapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till rätt slutpunkt som bestäms av routningstypen. I det här fallet dirigeras alla begäranden varje slutpunkt på ett round-robin-sätt.
6. När traffic manager-profilen har fungerat redigerar du DNS-posten på den auktoritära DNS-servern för att peka företagets domännamn på Traffic Manager-domännamnet.

![Konfigurera viktad trafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [prioritetstrafikroutningsmetod](traffic-manager-configure-priority-routing-method.md).
- Läs mer om [routningsmetod för prestandatrafik](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Läs om hur du [testar Traffic Manager-inställningar](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
