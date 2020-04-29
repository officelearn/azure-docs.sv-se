---
title: Självstudie – konfigurera geografisk trafik routning med Azure Traffic Manager
description: I den här självstudien beskrivs hur du konfigurerar metoden för geografisk trafik cirkulation med hjälp av Azure Traffic Manager
services: traffic-manager
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 3eb3f354d51833e55f405ed35679f1a5882c057a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "76938796"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Självstudie: Konfigurera den geografiska Traffic routing-metoden med hjälp av Traffic Manager

Med metoden för geografisk trafik cirkulation kan du dirigera trafik till specifika slut punkter baserat på den geografiska plats där begär Anden kommer. Den här självstudien visar hur du skapar en Traffic Manager-profil med den här routningsmetod och konfigurerar slut punkterna för att ta emot trafik från vissa geografiska områden.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/).
2. Klicka på **skapa en resurs** > **nätverk** > **Traffic Manager profil** > **skapa**.
4. I **skapa Traffic Manager-profilen**:
    1. Ange ett namn för din profil. Det här namnet måste vara unikt inom trafficmanager.net-zonen. För att få åtkomst till din Traffic Manager-profil använder du `<profilename>.trafficmanager.net`DNS-namnet.
    2. Välj **geografisk** routningsmetod.
    3. Välj den prenumeration som du vill skapa profilen under.
    4. Använd en befintlig resurs grupp eller skapa en ny resurs grupp för att placera profilen under. Om du väljer att skapa en ny resurs grupp använder du List rutan **resurs grupps plats** för att ange platsen för resurs gruppen. Den här inställningen refererar till platsen för resurs gruppen och har ingen inverkan på den Traffic Manager profil som distribueras globalt.
    5. När du har klickat på **skapa**skapas din Traffic Manager-profil och distribueras globalt.

![Skapa en Traffic Manager-profil](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Lägg till slut punkter

1. Sök efter namnet på Traffic Manager profilen som du skapade i portalens Sök fält och klicka på resultatet när det visas.
2. Navigera till **Inställningar** -> **slut punkter** i Traffic Manager.
3. Klicka på **Lägg till** för att visa **Lägg till slut punkt**.
3. Klicka på **Lägg till** och i **Lägg till slut punkt** som visas, Slutför på följande sätt:
4. Välj **typ** beroende på vilken typ av slut punkt du lägger till. För geografiska routnings profiler som används i produktion rekommenderar vi att du använder kapslade slut punkts typer som innehåller en underordnad profil med fler än en slut punkt. Mer information finns i [vanliga frågor och svar om metoder för geografisk trafik cirkulation](traffic-manager-FAQs.md).
5. Ange ett **Namn** som du vill använda för att identifiera den här slutpunkten.
6. Vissa fält på den här sidan beror på vilken typ av slut punkt du lägger till:
    1. Om du lägger till en Azure-slutpunkt väljer du **mål resurs typ** och **mål** baserat på den resurs som du vill dirigera trafiken till
    2. Om du lägger till en **extern** slut punkt anger du slut punktens **fullständigt kvalificerade domän namn (FQDN)** .
    3. Om du lägger till en **kapslad slut punkt**väljer du den **mål resurs** som motsvarar den underordnade profil som du vill använda och anger **antalet lägsta underordnade slut punkter**.
7. I avsnittet geo-mappning använder du List rutan för att lägga till de regioner där du vill att trafiken ska skickas till den här slut punkten. Du måste lägga till minst en region och du kan ha flera mappade regioner.
8. Upprepa detta för alla slut punkter som du vill lägga till under den här profilen

![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Använd Traffic Manager profilen
1.  I portalens sökfält söker du efter namnet på **Traffic Manager profilen** som du skapade i föregående avsnitt och klickar på Traffic Manager-profilen i resultaten som visas.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av typen av routning.  Om det gäller geografisk routning Traffic Manager titta på käll-IP: en för den inkommande begäran och bestämmer den region från vilken den har sitt ursprung. Om regionen mappas till en slut punkt dirigeras trafiken till där. Om den här regionen inte är mappad till en slut punkt returnerar Traffic Manager svar på en nodata-fråga.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetod för geografisk trafik](traffic-manager-routing-methods.md#geographic).
- Lär dig hur du [testar Traffic Manager inställningar](traffic-manager-testing-settings.md).
