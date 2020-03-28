---
title: Självstudiekurs - Konfigurera geografisk trafikroutning med Azure Traffic Manager
description: I den här självstudien beskrivs hur du konfigurerar den geografiska routningsmetoden med Azure Traffic Manager
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938796"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Självstudiekurs: Konfigurera den geografiska trafikroutningsmetoden med Traffic Manager

Med metoden Geografisk trafikroutning kan du dirigera trafik till specifika slutpunkter baserat på den geografiska plats där begäranden kommer. Den här självstudien visar hur du skapar en Traffic Manager-profil med den här routningsmetoden och konfigurerar slutpunkterna för att ta emot trafik från specifika geografiska områden.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare. Om du inte redan har ett konto kan du [registrera dig för en kostnadsfri utvärderingsmånad](https://azure.microsoft.com/free/).
2. Klicka på **Skapa en resurs** > **Traffic** > **Manager-profil** > **Skapa.**
4. I **profilen Skapa trafikhanterare:**
    1. Ange ett namn för din profil. Det här namnet måste vara unikt inom zonen trafficmanager.net. Om du vill komma åt traffic `<profilename>.trafficmanager.net`manager-profilen använder du DNS-namnet .
    2. Välj metoden **Geografisk** routning.
    3. Välj den prenumeration som du vill skapa den här profilen under.
    4. Använd en befintlig resursgrupp eller skapa en ny resursgrupp som den här profilen ska placeras under. Om du väljer att skapa en ny resursgrupp använder du **listrutan Resursgrupp för** att ange resursgruppens plats. Den här inställningen refererar till resursgruppens plats och påverkar inte Traffic Manager-profilen som distribueras globalt.
    5. När du har klickat på **Skapa**skapas och distribueras traffic manager-profilen globalt.

![Skapa en Traffic Manager-profil](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Lägga till slutpunkter

1. Sök efter traffic manager-profilnamnet som du skapade i portalens sökfält och klicka på resultatet när det visas.
2. Navigera till **Inställningar** -> **slutpunkter** i Traffic Manager.
3. Klicka på **Lägg till** om du vill visa lägg **till slutpunkt**.
3. Klicka på **Lägg till** och i **slutpunkten Lägg till** som visas, slutför enligt följande:
4. Välj **Typ** beroende på vilken typ av slutpunkt du lägger till. För geografiska routningsprofiler som används i produktionen rekommenderar vi starkt att du använder kapslade slutpunktstyper som innehåller en underordnad profil med mer än en slutpunkt. Mer information finns i [Vanliga frågor om geografiska trafikroutningsmetoder](traffic-manager-FAQs.md).
5. Ange ett **Namn** som du vill använda för att identifiera den här slutpunkten.
6. Vissa fält på den här sidan beror på vilken typ av slutpunkt du lägger till:
    1. Om du lägger till en Azure-slutpunkt väljer du **resurstypen Mål** och **målet** baserat på den resurs som du vill dirigera trafik till
    2. Om du lägger till en **extern** slutpunkt anger du det **fullständigt kvalificerade domännamnet (FQDN)** för slutpunkten.
    3. Om du lägger till en **kapslad slutpunkt**väljer du den **målresurs** som motsvarar den underordnade profil som du vill använda och anger **antalet minsta underordnade slutpunkter**.
7. I avsnittet Geo-mappning använder du listrutan för att lägga till de regioner där du vill att trafik ska skickas till den här slutpunkten. Du måste lägga till minst en region och du kan ha flera regioner mappade.
8. Upprepa detta för alla slutpunkter som du vill lägga till under den här profilen

![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Använda traffic manager-profilen
1.  I portalens sökfält söker du efter **profilnamnet Traffic Manager** som du skapade i föregående avsnitt och klickar på trafikhanterarens profil i de resultat som visas.
2. Klicka på **Översikt**.
3. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till rätt slutpunkt som bestäms av routningstypen.  När det gäller geografisk routning tittar Traffic Manager på käll-IP för den inkommande begäran och bestämmer från vilket region den har sitt ursprung. Om den regionen mappas till en slutpunkt dirigeras trafiken dit. Om den här regionen inte mappas till en slutpunkt returnerar Traffic Manager ett NODATA-frågesvar.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [geografisk trafikroutningsmetod](traffic-manager-routing-methods.md#geographic).
- Läs om hur du [testar Traffic Manager-inställningar](traffic-manager-testing-settings.md).
