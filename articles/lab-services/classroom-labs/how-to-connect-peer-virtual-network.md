---
title: Ansluta till ett peer-nätverk i Azure Lab Services | Microsoft Docs
description: Lär dig hur du ansluter ditt labb nätverk till ett annat nätverk som en peer. Du kan till exempel ansluta ditt lokala skol-/University-nätverk med Labbets virtuella nätverk i Azure.
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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 9e53b6bdb041bfac5a82ed607b75b25ab0513f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188012"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Anslut ditt labb nätverk till ett peer-virtuellt nätverk i Azure Lab Services

Den här artikeln innehåller information om peering The Labs Network med ett annat nätverk.

## <a name="overview"></a>Översikt

Med peering för virtuella nätverk kan du sömlöst ansluta virtuella Azure-nätverk. När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peer-kopplat virtuella nätverk dirigeras via Microsoft stamnät-infrastrukturen, ungefär som trafik dirigeras mellan virtuella datorer i samma virtuella nätverk, via privata IP-adresser. Mer information finns i [peering för virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md).

Du kan behöva ansluta ditt labbs nätverk till ett peer-virtuellt nätverk i vissa scenarier, inklusive följande:

- De virtuella datorerna i labbet har program vara som ansluter till lokala licens servrar för att erhålla licens.
- De virtuella datorerna i labbet behöver åtkomst till data uppsättningar (eller andra filer) på Universitys nätverks resurser.

Vissa lokala nätverk är anslutna till Azure Virtual Network antingen via [ExpressRoute](../../expressroute/expressroute-introduction.md) eller [Virtual Network Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Dessa tjänster måste konfigureras utanför Azure Lab Services. Mer information om hur du ansluter ett lokalt nätverk till Azure med hjälp av ExpressRoute finns i [Översikt över ExpressRoute](../../expressroute/expressroute-introduction.md). För lokal anslutning med en Virtual Network Gateway måste gatewayen, det angivna virtuella nätverket och labb kontot vara i samma region.

> [!NOTE]
> När du skapar en Azure-Virtual Network som ska peer-kopplas med ett labb konto är det viktigt att du förstår hur det virtuella nätverkets region påverkar hur klass rums labben skapas.  Mer information finns i administratörs guidens avsnitt på [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera när labb kontot skapas

När du skapar ett nytt [labb konto](tutorial-setup-lab-account.md)kan du välja ett befintligt virtuellt nätverk som visas i list rutan **peer Virtual Network** på fliken **Avancerat** .  I listan visas endast virtuella nätverk i samma region som labb kontot. Det valda virtuella nätverket är anslutet (peer-kopplat) till labb som skapats under labb kontot.  Alla virtuella datorer i labb som skapas efter den här ändringen kommer att ha åtkomst till resurserna i det peer-virtuella nätverket.

![Välj VNet till peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Adressintervall

Det finns också ett alternativ för att tillhandahålla **adress intervall** för de virtuella datorerna i labbet.  Egenskapen för **adress intervall** gäller endast om det **virtuella peer-nätverket** är aktiverat för labbet.  Om adress intervallet anges skapas alla virtuella datorer i labben under labb kontot i det adress intervallet. Adress intervallet ska vara i CIDR-format (t. ex. 10.20.0.0/20) och inte överlappa några befintliga adress intervall.  När du tillhandahåller ett adress intervall är det viktigt att tänka på hur många *labb* som ska skapas och tillhandahålla ett adress intervall för att hantera det. Labb tjänster förutsätter högst 512 virtuella datorer per labb.  Ett IP-intervall med "/23" kan till exempel endast skapa ett labb.  Ett intervall med "/21" gör det möjligt att skapa fyra labb.

Om **adress intervallet** inte anges använder Lab-tjänsterna det standard adress intervall som ges av Azure när det virtuella nätverket skapas för peer-kopplas till det virtuella nätverket.  Intervallet är ofta något som 10. x. 0,0/16.  Detta kan leda till överlappande IP-intervall, så se till att antingen ange och adressera intervallet i labb inställningarna eller kontrol lera adress intervallet för det virtuella nätverket som peer-kopplas.

## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats

Du kan aktivera samma egenskap på fliken labb **konfiguration** på sidan **labb konto** om du inte har konfigurerat ett peer-nätverk när du skapade labb kontot. Ändringar som görs i den här inställningen gäller bara för de labb som skapas efter ändringen. Som du kan se i avbildningen kan du aktivera eller inaktivera ett **virtuellt dator nätverk** för labb i labb kontot.

![Aktivera eller inaktivera VNet-peering när labbet har skapats](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

När du väljer ett virtuellt nätverk för det **virtuella peer-nätverket** är alternativet **Tillåt att labb skaparen att välja labb plats** inaktiverat. Det beror på att labb i labb kontot måste finnas i samma region som labb kontot för att de ska kunna ansluta till resurser i det virtuella peer-nätverket.

> [!IMPORTANT]
> Inställningen för peer-kopplat virtuellt nätverk gäller endast labb som skapas när ändringen har gjorts, inte i de befintliga labben.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Koppla ett delat avbildnings galleri till ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägg till en användare som labb ägare](how-to-add-user-lab-owner.md)
- [Visa brand Väggs inställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)
