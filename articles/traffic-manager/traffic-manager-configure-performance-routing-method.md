---
title: Konfigurera routningsmetod för prestandatrafik med Hjälp av Azure Traffic Manager | Microsoft-dokument
description: I den här artikeln beskrivs hur du konfigurerar Traffic Manager för att dirigera trafik till slutpunkten med lägsta svarstid
services: traffic-manager
manager: twooley
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: rohink
ms.openlocfilehash: f5e9b7690c28793a35c692a6125a6b11c7a140a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938759"
---
# <a name="configure-the-performance-traffic-routing-method"></a>Konfigurera routningsmetoden för prestandatrafik

Med metoden För trafikroutning av prestanda kan du dirigera trafik till slutpunkten med den lägsta svarstiden från klientens nätverk. Vanligtvis är datacenter med den lägsta svarstiden närmast geografiskt avstånd. Den här trafikroutningsmetoden kan inte ta hänsyn till ändringar i realtid i nätverkskonfiguration eller belastning.

##  <a name="to-configure-performance-routing-method"></a>Så här konfigurerar du prestandaroutningsmetod

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. Sök efter **Traffic Manager-profilerna** i portalens sökfält och klicka sedan på det profilnamn som du vill konfigurera routningsmetoden för.
3. I **profilbladet Traffic Manager** kontrollerar du att både molntjänsterna och webbplatserna som du vill inkludera i konfigurationen finns.
4. Klicka på **Konfiguration**i avsnittet **Inställningar** och slutför följande i **bladet Konfiguration:**
    1. För **inställningar för trafikroutningsmetod**väljer du **Prestanda**för **routningsmetod** .
    2. Ange **slutpunktsövervakarens inställningar** identiska för alla slutpunkter i den här profilen enligt följande:
        1. Välj lämpligt **protokoll**och ange **portnumret.** 
        2. För **Bana** skriv */* ett snedstreck . Om du vill övervaka slutpunkter måste du ange en sökväg och ett filnamn. Ett snedstreck för framåt "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rotkatalogen (standard).
        3. Klicka på **Spara**högst upp på sidan .
5.  Testa ändringarna i konfigurationen på följande sätt:
    1.  Sök efter Profilnamnet Traffic Manager i portalens sökfält och klicka på Traffic Manager-profilen i de resultat som visas.
    2.  Klicka på **Översikt**i profilbladet **Traffic Manager** .
    3.  Profilbladet **Traffic Manager** visar DNS-namnet på din nyskapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till rätt slutpunkt som bestäms av routningstypen. I det här fallet dirigeras alla begäranden till slutpunkten med den lägsta svarstiden från klientens nätverk.
6. När traffic manager-profilen har fungerat redigerar du DNS-posten på den auktoritära DNS-servern för att peka företagets domännamn på Traffic Manager-domännamnet.

![Konfigurera routningsmetod för prestandatrafik med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Läs om hur du [testar Traffic Manager-inställningar](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png