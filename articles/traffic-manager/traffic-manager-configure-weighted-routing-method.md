---
title: "Konfigurera routningsmetoden för viktad resursallokering trafik med hjälp av Azure Traffic Manager | Microsoft Docs"
description: "Den här artikeln förklarar hur du läser in Utjämna trafiken genom att använda en resursallokering-metoden i Traffic Manager"
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
ms.openlocfilehash: 7aa4c9120d44ff1b3e59a57090ea04e3f8021fc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Konfigurera routningsmetoden för viktat trafik i Traffic Manager

En vanliga trafik routning metoden mönstret är att tillhandahålla en uppsättning identiska slutpunkter, bland annat molntjänster och webbplatser, och skicka trafik till varje resursallokering överskrids. Följande steg beskriver hur du konfigurerar den här typen av trafikroutningsmetod.

> [!NOTE]
> Azure webbplatser tillhandahåller redan resursallokering för webbplatser inom ett datacenter (även kallat en region) för belastningsutjämning. Traffic Manager kan du ange routningsmetoden för resursallokering för webbplatser i olika datacenter.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>Så här konfigurerar du viktad trafikroutningsmetod

1. Logga in på [Azure Portal](http://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/). 
2. I portalens sökfältet, söker du efter den **Traffic Manager-profiler** och klicka sedan på namnet på profilen som du vill konfigurera routningsmetoden för.
3. I den **trafikhanterarprofil** bladet, kontrollera att både molntjänster och webbplatser som du vill ska ingå i konfigurationen finns kvar.
4. I den **inställningar** klickar du på **Configuration**, och i den **Configuration** bladet slutförts enligt följande:
    1. För **trafik routning inställningar**, kontrollera att trafikroutningsmetod **viktat**. Om det inte är det, klickar du på **viktat** från den nedrullningsbara listan.
    2. Ange den **övervakaren slutpunktsinställningar** identiska för alla alla slutpunkter i den här profilen på följande sätt:
        1. Välj lämpliga **protokollet**, och ange den **Port** nummer. 
        2. För **sökväg** skriver ett snedstreck  */* . Du måste ange en sökväg och filnamn för att övervaka slutpunkter. Ett snedstreck ”/” är ett giltigt för den relativa sökvägen och innebär att filen är i rotkatalogen (standard).
        3. Överst på sidan, klickar du på **spara**.
5. Testa ändringar i konfigurationen på följande sätt:
    1.  I portalens sökfältet, söka efter namnet på Traffic Manager-profilen och klickar på Traffic Manager-profilen i resultatet som de visas.
    2.  I den **Traffic Manager** profilen bladet, klickar du på **översikt**.
    3.  Den **trafikhanterarprofil** bladet visar DNS-namnet för nyskapade Traffic Manager-profilen. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att hämta dirigeras till rätt slutpunkten som bestäms av typen routning. I det här fallet alla begäranden dirigeras varje slutpunkt på ett sätt för resursallokering.
6. När Traffic Manager-profilen fungerar redigera DNS-posten på auktoritära DNS-servern att peka företagets domännamn till domännamnet för Traffic Manager.

![Konfigurera viktat trafikroutningsmetod med Traffic Manager][1]

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [prioritet trafikroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Lär dig mer om [prestanda trafikroutningsmetoden](traffic-manager-configure-performance-routing-method.md).
- Lär dig mer om [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).
- Lär dig hur du [testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md).

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
