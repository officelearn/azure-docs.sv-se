---
title: Konfigurera flera värden trafikroutningsmetod i Azure Traffic Manager | Microsoft Docs
description: Den här artikeln förklarar hur du konfigurerar Traffic Manager kan dirigera trafik till A/AAAA-slutpunkter.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: 1fae1147c941ed28d23a478a0e809b2dffba7a35
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131366"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurera routningsmetoden för flera värden i Traffic Manager

Den här artikeln beskriver hur du konfigurerar flera värden trafikdirigeringsmetoden. Den **Flervärden är** trafik routningsmetod gör att du kan returnera flera felfria slutpunkter och ökar tillförlitligheten för ditt program eftersom klienter har fler alternativ och försök igen utan att behöva göra en annan DNS-sökning. Flera värden routning är bara aktiverat för profiler som har alla slutpunkter som anges med IPv4 eller IPv6-adresser. När en fråga tas emot för den här profilen, returneras alla felfria slutpunkter baserat på konfigurerbara maximala returnerade antal som angetts. 

>[!NOTE]
> Just nu lägga till slutpunkter med hjälp av IPv4 eller IPv6-adresser stöds endast för slutpunkter av typen **externa** och därmed flera värden routning också stöds endast för dessa slutpunkter.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.
## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp för Traffic Manager-profilen.
1. I det vänstra fönstret i Azure Portal, Välj **resursgrupper**.
2. I **resursgrupper**, överst på sidan Välj **Lägg till**.
3. I **Resursgruppsnamn**, anger du ett namn *myResourceGroupTM1*. För **resursgruppsplats**väljer **USA, östra**, och välj sedan **OK**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil som dirigerar användartrafik som genom att skicka dem till slutpunkten med kortast svarstid.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Traffic Manager-profil** > **Skapa**.
2. I **skapa Traffic Manager-profil**, ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **skapa**:
    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Det här namnet måste vara unikt inom trafficmanager.net-zonen och resultat i DNS-namn, trafficmanager.net som används för att få åtkomst till Traffic Manager-profilen.                                   |
    | Routningsmetod          | Välj den **Flervärden är** routningsmetod.                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj *myResourceGroupTM1*. |
    | Plats                | Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.                              |
    |
  
    ![Skapa en Traffic Manager-profil](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till två IP-adresser som externa slutpunkter till Flervärden är Traffic Manager-profilen som du skapade i föregående steg.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Extern slutpunkt                                   |
    | Namn           | myEndpoint1                                        |
    | Fullständigt kvalificerat domännamn (FQDN) eller IP           | Ange den offentliga IP-adressen för den slutpunkt som du vill lägga till den här Traffic Manager-profil                         |
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myEndpoint2*, för **fullständigt kvalificerade domännamnet (FQDN) eller IP-**, ange den offentliga IP-adressen för den andra slutpunkten.
5.  När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

    ![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [viktad trafikroutningsmetod](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioriterad routningsmetod](traffic-manager-configure-priority-routing-method.md).
- Läs mer om [routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md)
- Lär dig mer om [geografisk routningsmetod](traffic-manager-configure-geographic-routing-method.md).


