---
title: Tillåt labbskapare att välja plats i Azure Lab Services
description: I den här artikeln beskrivs hur en labbkontoadministratör kan tillåta labbskapare att välja platser för sina labb.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444374"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Tillåt labbskapare att välja plats för labbet i Azure Lab Services
I Azure Lab Services kan en labbkontoägare tillåta labbskapare (lärare) att välja en plats för labbet som de skapar. Den här platsen kan skilja sig från platsen för labbkontot. En plats är en grupp Azure-regioner. Usa:s plats är till exempel en grupp regioner som östra USA, västra USA och så vidare. 

Du som labbkontoägare kan välja alternativet **Tillåt labbskapare att välja labbplats** när du skapar ett labbkonto och när du har skapat labbkontot (eller ett befintligt labbkonto). 

## <a name="at-the-time-of-lab-account-creation"></a>Vid tidpunkten för skapandet av labbkonto
När du skapar ett labbkonto visas det här alternativet på den första skärmen ( fliken**Grunderna).** 

![Aktivera alternativet när labbet skapas](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Det här alternativet är inaktiverat om du väljer ett virtuellt peer-nätverk för ditt labbkonto på fliken **Avancerat.**  

![När peer virtuellt nätverk är aktiverat](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>När labbkontot har skapats
När du har skapat labbkontot kan du aktivera eller inaktivera det här alternativet genom att följa dessa steg: 

1. På sidan **Labbkonto** väljer du **Labbinställningar** på den vänstra menyn.
2. Välj alternativet **Tillåt labbskapare att välja labbplats** om du vill tillåta labbskaparen att välja en plats för labbet. Om den är inaktiverad skapas labben automatiskt på samma plats där labbkontot finns. 
    
    Det här fältet är inaktiverat när du väljer ett virtuellt nätverk för fältet **Peer-virtuellt nätverk.** Det beror på att labb i labbkontot måste vara i samma region som labbkontot för att de ska komma åt resurser i det virtuella peer-nätverket. 
1. Välj **Spara** i verktygsfältet. 

    ![Labbinställningar](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Ingen virtuell nätverks- och platsval är inte tillåten
I det här fallet har du inte aktiverat alternativet **Tillåt labbskapare att välja labbplats.** 

![Ingen labbplats](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Sedan ser labbskapare (lärare) inte ett alternativ för att välja en plats för labbet. De kommer att se priset per timme för varje storlek alternativ som är tillgängliga för dem. När de skapar ett labb skapas det i en Azure-region som finns på samma plats som Azure-regionen som deras labbkonto finns i. Om labbkontot till exempel finns i **västra USA**kan labbet skapas i södra **centrala USA** men inte skapas i Östra **Kanada**. Vi garanterar inte något om den region vi väljer bortsett från att det är på plats. Om en storlek för närvarande är begränsad ser labbskaparen en kryssruta där de kan se de storlekar som vi normalt stöder men som för närvarande inte är tillgängliga. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>I virtuellt nätverk och platsval är inte tillåtet
I det här fallet är **alternativet Tillåt labbskapare att välja labbplatsalternativ** inaktiverat eftersom du har valt ett virtuellt peer-nätverk för labbkontot. Sedan ser labbskapare samma skärm som med det tidigare alternativet. Eftersom alla virtuella datorer måste vara i samma Azure-region som det virtuella nätverket, kommer labbet att skapas i samma Azure-region som det virtuella nätverket finns i. Om den aktuella regionen är begränsad för en storlek visas storleken som otillgänglig. 

## <a name="location-selection-is-enabled"></a>Platsval är aktiverat
När du väljer **Tillåt labbskapare att välja labbplats**ser labbskapare (lärare) ett alternativ för att välja en plats när du skapar ett labb. 

![Välj en labbplats](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Labbskapare ser prisintervallet för alla platser som storleken finns på och kan välja en plats. Labbet skapas i alla Azure-regioner som mappar till den platsen.

Om en plats är begränsad visas den inte i listan som standard. Expandera listrutan och välj **Visa ej tillgängliga platser för den här storleken**. 

![Visa platser som inte är tillgängliga](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Kostnad
Tidigare baserades prissättningen på den virtuella datorns storlek som du väljer för labbet. Nu baseras priset på kombinationen av operativsystem (OS), Storlek och plats. 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Ansluta labbets nätverk med ett virtuellt peer-nätverk](how-to-connect-peer-virtual-network.md)
- [Bifoga ett delat bildgalleri i ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägga till en användare som labbägare](how-to-add-user-lab-owner.md)
- [Visa brandväggsinställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)