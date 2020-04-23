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
ms.openlocfilehash: 6ed0d743f9b9cdc136b8f52f4d9d02583fc63eb9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870185"
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
> När du skapar ett virtuellt Azure-nätverk som ska peeras med ett labbkonto är det viktigt att förstå hur det virtuella nätverkets region påverkar var klassrumslabb skapas.  Mer information finns i avsnittet om administratörsguiden om [regioner\platser](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurera vid tidpunkten för skapandet av labbkonto

När det nya [labbkontot skapas](tutorial-setup-lab-account.md)kan du välja ett befintligt virtuellt nätverk som visas i listrutan **Peer-virtuellt nätverk** på fliken **Avancerat.**  Listan visar bara virtuella nätverk i samma region som labbkontot. Det valda virtuella nätverket är anslutet (peered) till labb som skapats under labbkontot.  Alla virtuella datorer i labb som skapas efter att den här ändringen har åtkomst till resurserna i det peer-indelade virtuella nätverket.

![Välj VNet till peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Adressintervall

Det finns också ett alternativ för att ange **adressintervall** för virtuella datorer för labben.  Egenskapen **Adressintervall** gäller endast om **peer-virtuellt nätverk** är aktiverat för labbet.  Om adressintervallet anges skapas alla virtuella datorer i labben under labbkontot i det adressintervallet. Adressintervallet bör vara i CIDR-notation (t.ex. 10.20.0.0/20) och inte överlappa med några befintliga adressintervall.  När du anger ett adressintervall är det viktigt att tänka på antalet labb som ska *skapas* och tillhandahålla ett adressintervall för att hantera det. Lab Services förutsätter högst 512 virtuella datorer per labb.  Ett IP-intervall med '/23' kan till exempel bara skapa ett labb.  Ett intervall med en "/21" gör det möjligt att skapa fyra laboratorier.

Om **adressintervallet** inte har angetts använder Lab Services det standardadressintervall som azure ger det när det virtuella nätverket skapas som ska peer-data med ditt virtuella nätverk.  Intervallet är ofta något i stil med 10.x.0.0/16.  Detta kan leda till överlappning av IP-intervall, så se till att antingen ange och adressintervall i labbinställningarna eller kontrollera adressintervallet för det virtuella nätverket som peer-data.

## <a name="configure-after-the-lab-is-created"></a>Konfigurera när labbet har skapats

Samma egenskap kan aktiveras från **fliken Labs-konfiguration** på sidan **Labbkonto** om du inte har konfigurerat ett peer-nätverk när labbkontot skapades. Ändringen av den här inställningen gäller endast för de övningar som skapas efter ändringen. Som du kan se i bilden kan du aktivera eller inaktivera **virtuella peer-nätverk** för labb i labbkontot.

![Aktivera eller inaktivera VNet-peering när labbet har skapats](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

När du väljer ett virtuellt nätverk för fältet **Virtuellt peer-nätverk** inaktiveras alternativet **Tillåt labbskapare att välja labbplats.** Det beror på att labben i labbkontot måste vara i samma region som labbkontot för att de ska kunna ansluta till resurser i det virtuella peer-nätverket.

> [!IMPORTANT]
> Den peered virtuella nätverksinställningen gäller endast för labb som skapas efter ändringen, inte för de befintliga labben.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

- [Tillåt labbets skapare att välja plats för labbet](allow-lab-creator-pick-lab-location.md)
- [Bifoga ett delat bildgalleri i ett labb](how-to-attach-detach-shared-image-gallery.md)
- [Lägga till en användare som labbägare](how-to-add-user-lab-owner.md)
- [Visa brandväggsinställningar för ett labb](how-to-configure-firewall-settings.md)
- [Konfigurera andra inställningar för ett labb](how-to-configure-lab-accounts.md)
