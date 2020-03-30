---
title: Ytterligare nätverkskrav för SAP HANA på Azure (stora instanser) | Microsoft-dokument
description: Ytterligare nätverkskrav för SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 66bbd8b462ac35756be0fae6eba940ba0aba6c4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614571"
---
# <a name="additional-network-requirements-for-large-instances"></a>Ytterligare nätverkskrav för stora instanser

Du kan ha ytterligare nätverkskrav som en del av en distribution av stora instanser av SAP HANA på Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Lägga till fler IP-adresser eller undernät

Använd antingen Azure-portalen, PowerShell eller Azure CLI när du lägger till fler IP-adresser eller undernät.

Lägg till det nya IP-adressintervallet som ett nytt intervall i det virtuella nätverksadressutrymmet i stället för att generera ett nytt aggregerat intervall. Skicka den här ändringen till Microsoft. På så sätt kan du ansluta från det nya IP-adressintervallet till HANA:s stora instansenheter i klienten. Du kan öppna en Azure-supportbegäran för att få det nya virtuella nätverksadressutrymmet tillagt. När du har fått en bekräftelse utför du nästa steg.

Information om hur du skapar ytterligare ett undernät från Azure-portalen finns i [Skapa ett virtuellt nätverk med Azure-portalen](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network). En skapa från PowerShell finns i [Skapa ett virtuellt nätverk med PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Lägga till virtuella nätverk

När du först har anslutit ett eller flera virtuella Azure-nätverk kanske du vill ansluta ytterligare nätverk som har åtkomst till SAP HANA på Azure (stora instanser). Skicka först en Azure-supportbegäran. I den begäran ska du inkludera den specifika informationen som identifierar den specifika Azure-distributionen. Inkludera även IP-adressutrymmesintervallet eller intervallen för Azure-adressutrymmet för virtuella nätverk. SAP HANA på Microsoft Service Management ger sedan nödvändig information som du behöver för att ansluta ytterligare virtuella nätverk och Azure ExpressRoute. För varje virtuellt nätverk behöver du en unik auktoriseringsnyckel för att ansluta till ExpressRoute-kretsen till STORA HANA-instanser.

## <a name="increase-expressroute-circuit-bandwidth"></a>Öka ExpressRoute-kretsbandbredden

Rådgör med SAP HANA om Microsoft Service Management. Om de råder dig att öka bandbredden för SAP HANA på Azure (stora instanser) ExpressRoute-krets, skapa en Azure-supportbegäran. (Du kan begära en ökning för en enda krets bandbredd upp till högst 10 Gbit/s.) Du får sedan ett meddelande när åtgärden är klar. Du behöver inte göra något annat för att aktivera den här högre hastigheten i Azure.

## <a name="add-an-additional-expressroute-circuit"></a>Lägga till ytterligare en ExpressRoute-krets

Rådgör med SAP HANA om Microsoft Service Management. Om de rekommenderar att du lägger till ytterligare en ExpressRoute-krets skapar du en Azure-supportbegäran (inklusive en begäran om att få auktoriseringsinformation för att ansluta till den nya kretsen). Innan du gör begäran måste du definiera adressutrymmet som används i de virtuella nätverken. SAP HANA på Microsoft Service Management kan sedan tillhandahålla auktorisering.

När den nya kretsen skapas och SAP HANA på Microsoft Service Management-konfigurationen är klar får du ett meddelande med den information du behöver för att fortsätta. Du kan inte ansluta virtuella Azure-nätverk till den här ytterligare kretsen om de redan är anslutna till en annan SAP HANA på Azure (stor instans) ExpressRoute-krets i samma Azure-region.

## <a name="delete-a-subnet"></a>Ta bort ett undernät

Om du vill ta bort ett virtuellt nätverksundernät kan du använda Azure-portalen, PowerShell eller Azure CLI. Om ditt AZURE-IP-adressintervall för virtuella nätverk eller adressutrymme var ett aggregerat intervall finns det ingen uppföljning för dig med Microsoft. (Observera dock att det virtuella nätverket fortfarande sprider BGP-vägadressutrymmet som innehåller det borttagna undernätet.) Du kanske har definierat Azures virtuella nätverksadressintervall eller adressutrymme som flera IP-adressintervall, varav ett har tilldelats ditt borttagna undernät. Var noga med att ta bort det från ditt virtuella nätverksadressutrymme. Informera sedan SAP HANA om Microsoft Service Management för att ta bort det från de intervall som SAP HANA på Azure (stora instanser) tillåts kommunicera med.

Mer information finns i [Ta bort ett undernät](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Ta bort ett virtuellt nätverk

Mer information finns i [Ta bort ett virtuellt nätverk](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network).

SAP HANA på Microsoft Service Management tar bort befintliga auktoriseringar på SAP HANA på Azure (stora instanser) ExpressRoute-kretsen. Det tar också bort Azure virtuella nätverk IP-adressintervall eller adressutrymme för kommunikation med HANA stora instanser.

När du har tagit bort det virtuella nätverket öppnar du en Azure-supportbegäran för att ange ip-adressutrymmesintervall eller intervall som ska tas bort.

Ta bort allt genom att ta bort ExpressRoute-anslutningen, den virtuella nätverksgatewayen, den offentliga IP-adressen för den virtuella nätverksgatewayen och det virtuella nätverket.

## <a name="delete-an-expressroute-circuit"></a>Ta bort en ExpressRoute-krets

Om du vill ta bort ytterligare en SAP HANA på Azure (stora instanser) ExpressRoute-krets öppnar du en Azure-supportbegäran med SAP HANA på Microsoft Service Management. Begär att kretsen tas bort. Inom Azure-prenumerationen kan du ta bort eller behålla det virtuella nätverket efter behov. Du måste dock ta bort anslutningen mellan HANA-kretsen för stora instanser ExpressRoute och den länkade virtuella nätverksgatewayen.

## <a name="next-steps"></a>Nästa steg

- [Installera och konfigurera SAP HANA (stora instanser) i Azure](hana-installation.md)
