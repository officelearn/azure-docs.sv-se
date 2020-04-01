---
title: Ansluta till ett peer-nätverk i Azure Lab Services | Microsoft-dokument
description: Lär dig hur du ansluter labbnätverket till ett annat nätverk som peer. Anslut till exempel ditt lokala skol-/universitetsnätverk med Labs virtuella nätverk i Azure.
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
ms.openlocfilehash: 56c31e03eeec0c81207dc402e864eadec2d768bd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474074"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Ansluta labbets nätverk med ett virtuellt peer-nätverk i Azure Lab Services 
Den här artikeln innehåller information om peering ditt labbnätverk med ett annat nätverk. 

## <a name="overview"></a>Översikt
Med virtual network peering kan du sömlöst ansluta virtuella Azure-nätverk. När de virtuella nätverken har peer-kopplats visas de som ett nätverk för anslutningsändamål. Trafiken mellan virtuella datorer i peered virtuella nätverk dirigeras genom Microsofts stamnätsinfrastruktur, ungefär som trafiken dirigeras mellan virtuella datorer i samma virtuella nätverk, endast via privata IP-adresser. Mer information finns i [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md).

Du kan behöva ansluta labbets nätverk till ett virtuellt peer-nätverk i vissa scenarier, inklusive följande:

- De virtuella datorerna i labbet har programvara som ansluter till lokala licensservrar för att förvärva licens
- De virtuella datorerna i labbet behöver åtkomst till datauppsättningar (eller andra filer) på universitetets nätverksresurser. 

Vissa lokala nätverk är anslutna till Azure Virtual Network antingen via [ExpressRoute](../../expressroute/expressroute-introduction.md) eller [Virtual Network Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Dessa tjänster måste ställas in utanför Azure Lab Services. Mer information om hur du ansluter ett lokalt nätverk till Azure med ExpressRoute finns i [Översikt över ExpressRoute](../../expressroute/expressroute-introduction.md). För lokal anslutning med hjälp av en virtuell nätverksgateway måste gatewayen, det angivna virtuella nätverket och labbkontot alla finnas i samma region.

> [!NOTE]
> När du skapar ett virtuellt Azure-nätverk som ska peeras med ett labbkonto är det viktigt att förstå hur det virtuella nätverkets region påverkar var klassrumslabb skapas.  Mer information finns i administratörsguidens avsnitt om [regioner\platser](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera vid tidpunkten för skapandet av labbkonto
När det nya labbkontot skapas kan du välja ett befintligt virtuellt nätverk som visas i listrutan **Peer-virtuellt nätverk** på fliken **Avancerat.** Det valda virtuella nätverket är anslutet (peer-peer) till labb som skapats under labbkontot. Alla virtuella datorer i labb som skapas efter att den här ändringen har åtkomst till resurserna i det peer-indelade virtuella nätverket. 

Det finns också en bestämmelse om att tillhandahålla **adressintervall** för virtuella datorer för labben. Om adressintervallet anges skapas alla virtuella datorer i labben under labbkontot i det adressintervallet. Adressintervallet bör vara i CIDR-notation (t.ex. 10.20.0.0/20) och inte överlappa med några befintliga adressintervall. När du anger ett adressintervall är det viktigt att tänka på antalet virtuella datorer som kommer att skapas i labben och tillhandahålla ett adressintervall för att hantera det. För ett visst intervall visas antalet labb som det kan rymma.

![Välj VNet till peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Detaljerade steg-för-steg-instruktioner för att skapa ett labbkonto finns i [Konfigurera ett labbkonto](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats
Samma egenskap kan aktiveras från **fliken Labs-konfiguration** på sidan **Labbkonto** om du inte har konfigurerat ett peer-nätverk när labbkontot skapades. Ändringen av den här inställningen gäller endast för de övningar som skapas efter ändringen. Som du kan se i bilden kan du aktivera eller inaktivera **virtuella peer-nätverk** för labb i labbkontot. 

![Aktivera eller inaktivera VNet-peering när labbet har skapats](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

När du väljer ett virtuellt nätverk för fältet **Virtuellt peer-nätverk** inaktiveras alternativet **Tillåt labbskapare att välja labbplats.** Det beror på att labben i labbkontot måste vara i samma region som labbkontot för att de ska kunna ansluta med resurser i det virtuella peer-nätverket. 

> [!IMPORTANT]
> Den här inställningsändringen gäller endast för labb som skapas när ändringen har gjorts, inte för de befintliga labben. 


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Bifoga ett delat bildgalleri i ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägga till en användare som labbägare](how-to-add-user-lab-owner.md)
- [Visa brandväggsinställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)
