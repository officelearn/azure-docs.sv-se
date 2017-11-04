---
title: Skapa en Traffic Manager-profilen i Azure | Microsoft Docs
description: "Den här artikeln beskriver hur du skapar en Traffic Manager-profil"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.openlocfilehash: e9ff7947e7801a9f352a7a947b09893b8f615d88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Den här artikeln beskrivs hur en profil med **prioritet** routning kan skapas för vägen användarna att två slutpunkter i Azure Web Apps. Med hjälp av den **prioritet** routning typen all trafik dirigeras till den första slutpunkten medan andra sparas som en säkerhetskopia. Därför kan användarna dirigeras till den andra slutpunkten om den första slutpunkten blir ohälsosamt.

Två tidigare skapade Azure Web App slutpunkter är associerade till den nya Traffic Manager-profilen i den här artikeln. Mer information om hur du skapar Azure Web App slutpunkter finns på [dokumentationssidan för Azure Web Apps](https://docs.microsoft.com/azure/app-service/). Du kan lägga till någon slutpunkt som har ett DNS-namn och kan nås via det offentliga internet och att vi använder Azure Web Apps slutpunkter som ett exempel.

### <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
1. Logga in på [Azure Portal](http://portal.azure.com) från en webbläsare. Om du inte redan har ett konto, kan du registrera en [kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/free/). 
2. På menyn **Hub** (Hubb) klickar du på **New** (Ny) > **Nätverk** > **See all** (Visa alla), klicka på **Traffic Manager**-profilen för att öppna bladet **Skapa Traffic Manager-profil** och sedan på **Skapa**.
3. På bladet **Skapa Traffic Manager-profil** gör du följande:
    1. Ge profilen ett namn i **Namn**. Det här namnet måste vara unikt inom zonen trafficmanager.net och resulterar i DNS-namnet <name>, trafficmanager.net som används för att komma åt Traffic Manager-profilen.
    2. I **Routningsmetod** väljer du routningsmetoden **Priority** (Prioritet).
    3. I **Prenumeration** väljer du den prenumeration du vill skapa profilen under
    4. I **Resursgrupp** skapar du en ny resursgrupp att placera profilen under.
    5. I **Resursgruppsplats** väljer du plats för resursgruppen. Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.
    6. Klicka på **Skapa**.
    7. När den globala distribueringen av din Traffic Manager-profil är klar listas den i respektive resursgrupp som en av resurserna.

    ![Skapa en Traffic Manager-profil](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till slutpunkter för Traffic Manager

1. I portalens sökfältet, söker du efter den **trafikhanterarprofil** namn som du skapade i föregående avsnitt och klickar på traffic manager-profil i resultatet som de visade.
2. Klicka på **Slutpunkter** på bladet **Traffic Manager-profil** i avsnittet **Inställningar**.
3. Klicka på **Lägg till** på bladet **Slutpunkter** som visas.
4. Ange enligt anvisningarna på bladet **Lägg till slutpunkt**:
    1. Klicka på **Azure-slutpunkt** för **Typ**.
    2. Ange ett **Namn** som du vill använda för att identifiera den här slutpunkten.
    3. För **mål resurstypen**, klickar du på **Apptjänst**.
    4. För **mål resurs**, klickar du på **väljer du en apptjänst** att visa en lista över Webbappar under samma prenumeration. I den **resurs** bladet som visas, Välj App service som du vill lägga till som första slutpunkt.
    5. För **Prioritet**väljer du **1**. Detta resulterar i all trafik går igenom den här slutpunkten, förutsatt att den är felfri.
    6. Behåll **Lägg till som inaktiverad** som avmarkerat.
    7. Klicka på **OK**
5.  Upprepa steg 3 och 4 för nästa Azure Web Apps slutpunkt. Se till att lägga till den med dess **Prioritet**-värde angivet som **2**.
6.  När båda slutpunkterna har lagts till visas de på bladet **Traffic Manager-profil** tillsammans med dess övervakningsstatus som är **Online**.

    ![Lägg till en Traffic Manager-slutpunkt](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>Använd Traffic Manager-profilen
1.  I portalens sökfältet, söker du efter den **trafikhanterarprofil** namn som du skapade i föregående avsnitt. I de resultat som visas, klickar du på trafikhanterarprofilen.
2. I den **trafikhanterarprofil** bladet, klickar du på **översikt**.
3. Den **trafikhanterarprofil** bladet visar DNS-namnet för nyskapade Traffic Manager-profilen. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) att hämta dirigeras till rätt slutpunkten som bestäms av typen routning. I det här fallet alla begäranden dirigeras till den första slutpunkten och om en Traffic Manager identifierar den felaktiga, trafiken växlar automatiskt till nästa slutpunkt.

## <a name="delete-the-traffic-manager-profile"></a>Ta bort Traffic Manager-profilen
Ta bort resursgruppen och Traffic Manager-profilen som du har skapat när de inte längre behövs. Om du vill göra det, välja en resursgrupp från den **trafikhanterarprofil** bladet och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [typer av routing](traffic-manager-routing-methods.md).
- Mer information om typer av slutpunkter [slutpunktstyper](traffic-manager-endpoint-types.md).
- Lär dig mer om [slutpunktsövervakning](traffic-manager-monitoring.md).



