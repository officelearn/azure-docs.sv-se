---
title: Konfigurera geografisk routningsmetod med Azure Traffic Manager
description: Den här artikeln förklarar hur du konfigurerar geografiska trafikroutningsmetod med Azure Traffic Manager
services: traffic-manager
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 64e3cbd082f2d18668dff87fbec098add73ffda6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010079"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Konfigurera geografisk routningsmetod som med Traffic Manager

Geografisk trafikroutningsmetod kan du dirigera trafik till specifika slutpunkter baserat på den geografiska platsen där förfrågningarna kommer. Den här självstudien visar hur du skapar en Traffic Manager-profil med den här routningsmetod och konfigurera slutpunkter för att ta emot trafik från specifika geografiska områden.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/).
2. Klicka på **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
4. I den **skapa Traffic Manager-profil**:
    1. Ange ett namn för din profil. Det här namnet måste vara unikt inom trafficmanager.net-zonen. Om du vill komma åt din Traffic Manager-profil kan du använda DNS-namnet <profilename>. trafficmanager.net.
    2. Välj den **geografisk** routningsmetod.
    3. Välj den prenumeration som du vill skapa profilen under.
    4. Använd en befintlig resursgrupp eller skapa en ny resursgrupp att placera profilen under. Om du vill skapa en ny resursgrupp använder den **plats för resursgruppen** listrutan och ange platsen för resursgruppen. Den här inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.
    5. När du klickar på **skapa**, Traffic Manager-profilen skapas och distribueras globalt.

![Skapa en Traffic Manager-profil](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Lägga till slutpunkter

1. Sök efter namnet på Traffic Manager-profilen du skapade i portalens sökfältet och klicka på resultatet när det visas.
2. Gå till **inställningar** -> **slutpunkter** i Traffic Manager.
3. Klicka på **Lägg till** att visa den **Lägg till slutpunkt**.
3. Klicka på **Lägg till** och i den **Lägg till slutpunkt** som visas kan slutföras på följande sätt:
4. Välj **typ** beroende på typen av slutpunkt som du lägger till. För geografisk routning profiler som används i produktion, rekommenderar vi starkt med kapslade slutpunktstyper som innehåller en underordnad-profil med mer än en slutpunkt. Mer information finns i [vanliga frågor och svar om geografisk trafikroutningsmetoder](traffic-manager-FAQs.md).
5. Ange ett **Namn** som du vill använda för att identifiera den här slutpunkten.
6. Vissa fält på den här sidan beror på vilken typ av slutpunkt som du lägger till:
    1. Om du lägger till en Azure-slutpunkt, väljer du den **Målresurstyp** och **Target** baserat på den resurs du vill dirigera trafik till
    2. Om du lägger till en **externa** slutpunkt, ge den **fullständigt kvalificerade domännamnet (FQDN)** för din slutpunkt.
    3. Om du lägger till en **kapslade endpoint**väljer den **målresurs** som motsvarar den underordnade profilen som du vill använda och ange den **minsta antal för underordnade slutpunkter**.
7. I avsnittet Geo-mappning, Använd listrutan för att lägga till regionerna som du vill att trafik skickas till den här slutpunkten från. Du måste lägga till minst en region och du kan ha flera regioner som har mappats.
8. Upprepa detta för alla slutpunkter som du vill lägga till under den här profilen

![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Använd Traffic Manager-profil
1.  I portalens sökfältet söker du efter den **Traffic Manager-profil** namn som du skapade i föregående avsnitt och klicka på traffic manager-profilen i resultaten som visas.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att dirigeras till rätt slutpunkten som bestäms av routningstyp.  När det gäller geografisk routning Traffic Manager tittar på käll-IP för inkommande begäran och anger regionen där det med ursprung. Om den regionen mappas till en slutpunkt, dirigeras trafiken till där. Om den här regionen inte är mappad till en slutpunkt, returnerar något svar inga data med Traffic Manager.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [geografisk prestandatrafik](traffic-manager-routing-methods.md#geographic).
- Lär dig hur du [testa inställningarna för Traffic Manager](traffic-manager-testing-settings.md).
