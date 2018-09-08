---
title: Ytterligare krav för SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Ytterligare krav för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164734"
---
# <a name="additional-network-requirements"></a>Ytterligare krav

Ibland kan du ha ytterligare krav som en del av distributionen av stora HANA-instanser. Den här artikeln visar de följande krav:
- [Att lägga till fler IP-adresser eller undernät ](#adding-more-ip-addresses-or-subnets)
- [Att lägga till virtuella vetworks](#adding-vnets)
- [Öka bandbredden för expressroute-krets](#increasing-expressroute-circuit-bandwidth)
- [Att lägga till en ytterligare expressroute-krets](#adding-an-additional-expressroute-circuit)
- [Tar bort ett undernät](#deleting-a-subnet)
- [Tar bort ett virtuellt nätverk](#deleting-a-vnet)
- [Tar bort en uttrycklig router-krets](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Att lägga till fler IP-adresser eller undernät

Använda Azure-portalen, PowerShell eller CLI när att lägga till flera IP-adresser eller undernät.

I det här fallet är rekommendationen att lägga till det nya IP-adressintervallet som nytt intervall i det virtuella nätverkets adressutrymme i stället för att generera ett nytt aggregerade intervall. I båda fallen måste du skicka den här ändringen till Microsoft för att tillåta anslutning av det nya IP-adressintervallet till stora HANA-instansen-enheter i din klient. Du kan öppna en supportförfrågan för Azure att hämta det nya VNet-adressutrymmet som har lagts till. När du fått en bekräftelse, utföra nästa steg.

Om du vill skapa ett ytterligare undernät från Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network), och för att skapa från PowerShell, se [skapa ett virtuellt nätverk med hjälp av PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Att lägga till virtuella nätverk

När du har anslutit först en eller flera virtuella Azure-nätverk, kanske du vill lägga till ytterligare som har åtkomst till SAP HANA på Azure (stora instanser). Först, skicka en supportförfrågan för Azure, i din begäran innehåller både den information som identifierar den specifika Azure-distributionen och de IP-utrymme adressintervall för Azure VNet-adressutrymmet. SAP HANA på Azure Service Management ger sedan nödvändig information du behöver att ansluta ytterligare virtuella nätverk och ExpressRoute. För varje virtuellt nätverk behöver du en unik nyckel för auktorisering att ansluta till ExpressRoute-krets till stora HANA-instanser.

Steg för att lägga till ett nytt Azure VNet:

1. Fullständig det första steget i onboarding-processen finns i den _skapar virtuella Azure-nätverket_ avsnittet.
2. Fullständig det andra steget i onboarding-processen finns i den _skapar gateway-undernät_ avsnittet.
3. För att ansluta dina ytterligare virtuella nätverk till HANA stora instans ExpressRoute-kretsen, öppna en supportförfrågan för Azure med information om det nya VNet och begär en ny nyckel för auktorisering.
4. När ett meddelande om att auktoriseringen har slutförts, användning av Microsoft tillhandahåller autentiseringsinformationen för att slutföra tredje steget i onboarding-processen finns i den _länka virtuella nätverk_ avsnittet.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Öka ExpressRoute-kretsens bandbredd

Konsultera SAP HANA på Azure Service Management. Om du bör öka bandbredden för SAP HANA på Azure (stora instanser) ExpressRoute-krets, skapar du en supportförfrågan för Azure. (Du kan begära en ökning av en enda kretsbandbredden upp till högst 10 Gbit/s). Sedan visas meddelandet när åtgärden har slutförts; Ingen ytterligare åtgärd krävs för att aktivera den här högre hastighet i Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Att lägga till en ytterligare ExpressRoute-krets

Konsultera SAP HANA på Azure Service Management om du är bäst att en ytterligare ExpressRoute-krets krävs, gör en supportbegäran för att skapa en ny ExpressRoute-krets (och att hämta information om tillstånd att ansluta till den) Azure. Adressutrymmet som ska användas på de virtuella nätverken måste definieras innan du gör begäran, i ordning för SAP HANA på Azure Service Management för auktorisering.

När den nya kretsen har skapats och SAP HANA på Azure Service Management-konfigurationen är klar kan ska du ta emot meddelanden med informationen du behöver att fortsätta. Följ stegen ovan för att skapa och ansluta det nya VNet till den här ytterligare kretsen. Det går inte att ansluta virtuella Azure-nätverk till den här ytterligare kretsen om de redan är anslutna till en annan SAP HANA på Azure (stor instans) ExpressRoute-kretsen i samma Azure-Region.

## <a name="deleting-a-subnet"></a>Tar bort ett undernät

Azure portal, PowerShell eller CLI kan användas för att ta bort ett virtuellt nätverk undernät. Om din Azure VNet-IP-adress olika/Azure VNet-adressutrymmet var ett sammansatt adressintervall, finns det inga Följ åt dig med Microsoft. Förutom att det virtuella nätverket fortfarande sprider adressutrymme för BGP-väg innehåller som det borttagna undernätet. Om du har definierat i Azure VNet-IP-adress olika/Azure VNet-adressutrymmet som flera IP-adressintervall som någon har tilldelats till ditt borttagna undernät, bör du ta bort som utanför din VNet-adressutrymmet och därefter meddela SAP HANA på Azure Service Management till ta bort den från de intervall som du kan kommunicera med SAP HANA på Azure (stora instanser).

Om du vill ta bort ett undernät, se [ta bort ett undernät](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) för mer information om hur du skapar undernät.

## <a name="deleting-a-vnet"></a>Tar bort ett virtuellt nätverk

Om du vill ta bort ett virtuellt nätverk, se [ta bort ett virtuellt nätverk](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). SAP HANA på Azure Service Management tar bort de befintliga tillstånd på SAP HANA på Azure (stora instanser) ExpressRoute-krets och ta bort Azure VNet-IP-adress intervall/Azure VNet-adressutrymmet för kommunikation med stora HANA-instanser.

När det virtuella nätverket har tagits bort, kan du öppna en supportförfrågan för Azure att tillhandahålla de IP-utrymme adressintervall som ska tas bort.

Säkerställ allt tas bort genom att ta bort följande objekt:

- ExpressRoute-anslutning, VNet-Gateway, offentlig IP för VNet-Gateway och virtuellt nätverk

## <a name="deleting-an-expressroute-circuit"></a>Tar bort en ExpressRoute-krets

Om du vill ta bort en ytterligare SAP HANA på Azure (stora instanser) ExpressRoute-krets, öppna en supportförfrågan för Azure med SAP HANA på Azure Service Management och begära att kretsen ska tas bort. I Azure-prenumeration kan du ta bort eller behålla det virtuella nätverket efter behov. Dock måste du ta bort anslutningen mellan HANA stora instanser ExpressRoute-kretsen och den länkade VNet-gatewayen.

Om du även vill ta bort ett virtuellt nätverk, följ anvisningarna på tar bort ett virtuellt nätverk i avsnittet ovan.

**Nästa steg**

- Se [installera och konfigurera SAP HANA (stora instanser) på Azure](hana-installation.md).
