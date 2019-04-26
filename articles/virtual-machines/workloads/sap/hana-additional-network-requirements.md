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
ms.openlocfilehash: 2f4c74076f2763addc1f2b971d8ea0ca29ce6fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203983"
---
# <a name="additional-network-requirements-for-large-instances"></a>Ytterligare krav för stora instanser

Du kan ha ytterligare krav som en del av en distribution av stora instanser av SAP HANA på Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Lägga till fler IP-adresser eller undernät

Använd Azure portal, PowerShell eller Azure CLI när du lägger till fler IP-adresser eller undernät.

Lägg till det nya IP-adressintervallet som ett nytt intervall till virtuella nätverkets adressutrymme, i stället för att generera ett nytt aggregerade intervall. Skicka den här ändringen till Microsoft. På så sätt kan du ansluta från det nya IP-adressintervallet till HANA stora instanser enheterna i din klient. Du kan öppna en supportförfrågan för Azure att hämta den nya virtuella nätverkets adressutrymme har lagts till. När du fått en bekräftelse, utföra nästa steg.

Om du vill skapa ett ytterligare undernät från Azure-portalen, [skapa ett virtuellt nätverk med Azure portal](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). Om du vill skapa en från PowerShell [skapa ett virtuellt nätverk med hjälp av PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Lägg till virtuella nätverk

När du först ansluta en eller flera Azure-nätverk, kanske du vill ansluta fler håller på att som har åtkomst till SAP HANA på Azure (stora instanser). Först måste skicka en supportförfrågan för Azure. I din begäran, innehåller den information som identifierar den specifika Azure-distributionen. Även innehålla utrymme för IP-adressintervall eller intervall med Azure virtuella nätverkets adressutrymme. SAP HANA på Microsoft Service Management ger sedan nödvändig information du behöver att ansluta ytterligare virtuella nätverk och Azure ExpressRoute. För alla virtuella nätverk behöver du en unik auktorisering för att ansluta till ExpressRoute-krets till stora HANA-instanser.

## <a name="increase-expressroute-circuit-bandwidth"></a>Öka ExpressRoute-kretsens bandbredd

Konsultera SAP HANA på Microsoft Service Management. Om de rekommenderar att du ökar bandbredden för SAP HANA på Azure (stora instanser) ExpressRoute-krets, skapa en supportförfrågan för Azure. (Du kan begära en ökning av en enda kretsbandbredden upp till högst 10 Gbit/s). Sedan visas meddelandet när åtgärden har slutförts; Du behöver inte göra något annat att aktivera den här högre hastighet i Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Lägg till en ytterligare ExpressRoute-krets

Konsultera SAP HANA på Microsoft Service Management. Om de rekommenderar att du kan lägga till en ytterligare ExpressRoute-krets, skapa en supportförfrågan för Azure (inklusive en begäran att hämta auktoriseringsinformation att ansluta till den nya kretsen). Innan du gör begäran, måste du definiera adressutrymmet som används på de virtuella nätverken. SAP HANA på Microsoft Service Management kan sedan ger behörighet.

När den nya kretsen har skapats och SAP HANA på Microsoft Service Management-konfigurationen är klar kan få du ett meddelande med informationen du behöver att fortsätta. Det går inte att ansluta virtuella Azure-nätverk till den här ytterligare kretsen om de redan är anslutna till en annan SAP HANA på Azure (stor instans) ExpressRoute-kretsen i samma Azure-region.

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Du kan använda Azure portal, PowerShell eller Azure CLI för att ta bort ett virtuellt nätverksundernät. Om ditt Azure-nätverk IP-adressintervall eller en adress adressutrymme var ett sammansatt adressintervall, finns det inga Följ åt dig med Microsoft. (Observera att det virtuella nätverket fortfarande sprids BGP vägen adressutrymmet som innehåller undernätet som har tagits bort.) Du har definierat i Azure virtuella nätverkets adressutrymme adressintervall eller en adress som flera IP-adressintervall, varav en tilldelades till undernätet har tagits bort. Glöm inte att ta bort som från din virtuella nätverkets adressutrymme. Ett meddelande i SAP HANA på Microsoft Service Management ta bort den från de intervall som du kan kommunicera med SAP HANA på Azure (stora instanser).

Mer information finns i [ta bort ett undernät](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Mer information finns i [ta bort ett virtuellt nätverk](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA på Microsoft Service Management tar bort de befintliga tillstånd på SAP HANA på Azure (stora instanser) ExpressRoute-krets. Det tar också bort Azure-nätverk IP-adressintervall eller en adress adressutrymmet för kommunikation med stora HANA-instanser.

När du tar bort det virtuella nätverket, öppnar du en supportförfrågan för Azure att ge utrymme för IP-adressintervall eller intervall som ska tas bort.

Säkerställ att du tar bort allt genom att ta bort ExpressRoute-anslutning, den virtuella nätverksgatewayen, den offentliga IP-gateway för virtuellt nätverk och det virtuella nätverket.

## <a name="delete-an-expressroute-circuit"></a>Ta bort en ExpressRoute-krets

Ta bort en ytterligare SAP HANA på Azure (stora instanser) ExpressRoute-krets, öppna en supportförfrågan för Azure med SAP HANA på Microsoft Service Management. Begäran att kretsen tas bort. I Azure-prenumeration kan du ta bort eller behålla det virtuella nätverket, vid behov. Dock måste du ta bort anslutningen mellan HANA stora instanser ExpressRoute-kretsen och den länkade virtuella nätverksgatewayen.

## <a name="next-steps"></a>Nästa steg

- [Installera och konfigurera SAP HANA (stora instanser) i Azure](hana-installation.md)
