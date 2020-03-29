---
title: Konfigurera trafikroutning med flera värden – Azure Traffic Manager
description: I den här artikeln beskrivs hur du konfigurerar Traffic Manager för att dirigera trafik till A/AAAA-slutpunkter.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938770"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurera routningsmetod för flera värden i Traffic Manager

I den här artikeln beskrivs hur du konfigurerar trafikroutningsmetoden MultiValue. **Med metoden För** trafikroutning med flera värden för flera värden för flera värden och öka tillförlitligheten i ditt program, eftersom klienterna har fler alternativ för att försöka igen utan att behöva göra en annan DNS-sökning. MultiValue-routning är endast aktiverad för profiler som har alla sina slutpunkter angivna med IPv4- eller IPv6-adresser. När en fråga tas emot för den här profilen returneras alla felfria slutpunkter baserat på det konfigurerbara maximala avkastningsantalet som angetts. 

>[!NOTE]
> För närvarande stöds lägger till slutpunkter med IPv4- eller IPv6-adresser endast för slutpunkter av typen **Extern** och därför stöds multivalue-routning också endast för sådana slutpunkter.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.
## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp för Traffic Manager-profilen.
1. Välj **Resursgrupper**i den vänstra rutan i Azure-portalen .
2. Välj **Lägg till**högst upp på sidan i **resursgrupper**.
3. Skriv ett namn *myResourceGroupTM1*i **resursgruppnamn**. För **Resursgruppsplats**väljer du **Östra USA**och väljer sedan **OK**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager-profil som dirigerar användartrafik genom att skicka dem till slutpunkten med lägsta svarstid.

1. Välj **Skapa en resurs** > **Traffic** > **Manager-profil** > **Skapa**på den övre vänstra sidan av skärmen .
2. I **Profilen Skapa Trafikhanteraren**anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Skapa:**
    
    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetod för **flera värden.**                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj *myResourceGroupTM1*. |
    | Location                | Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.                              |
   |        |           | 
  
   ![Skapa en Traffic Manager-profil](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till två IP-adresser som externa slutpunkter i multivalue Traffic Manager-profilen som du skapade i föregående steg.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange, eller välj, följande information, acceptera standardinställningarna för de återstående inställningarna och välj sedan **OK:**

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Extern slutpunkt                                   |
    | Namn           | myEndpoint1                                        |
    | Fullständigt kvalificerat domännamn (FQDN) eller IP           | Ange den offentliga IP-adressen för den slutpunkt som du vill lägga till i den här Traffic Manager-profilen                         |
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slutpunkt med namnet *myEndpoint2*, för **fullständigt kvalificerat domännamn (FQDN) eller IP**, ange den offentliga IP-adressen för den andra slutpunkten.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

   ![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Läs mer om [prestandaroutningsmetod](traffic-manager-configure-performance-routing-method.md)
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).


