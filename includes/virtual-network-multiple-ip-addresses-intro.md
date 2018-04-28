---
title: ta med fil
description: ta med fil
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: f1dc43b6fea4ebfb47439ec64b573cc471614d98
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
> [!div class="op_single_selector"]
> * [Azure Portal](../articles/virtual-network/virtual-network-multiple-ip-addresses-portal.md)
> * [PowerShell](../articles/virtual-network/virtual-network-multiple-ip-addresses-powershell.md)
> * [Azure CLI](../articles/virtual-network/virtual-network-multiple-ip-addresses-cli.md)
>

En Azure Virtual Machine (VM) har ett eller flera nätverksgränssnitt (NIC) kopplade till den. Ett NIC kan vara tilldelat en eller flera statiska eller dynamiska offentliga och privata IP-adresser. Med en virtuell dator som tilldelas flera IP-adresser blir följande möjligt:

* Du kan hantera flera webbplatser eller tjänster med olika IP-adresser och SSL-certifikat på en enda server.
* Du kan konfigurera den som en virtuell nätverksenhet, t.ex. en brandvägg eller belastningsutjämnare.
* Du kan lägga till någon av de privata IP-adresserna för något av nätverkskorten i en Azure Load Balancer-backend-pool. Tidigare gick det bara att lägga till den primära IP-adressen för det primära nätverkskortet i en backend-pool. Mer information om hur du belastningsutjämnar flera IP-konfigurationer finns i artikeln [Load balancing multiple IP configurations](../articles/load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Belastningsutjämna flera IP-konfigurationer).

Varje nätverkskort som är kopplat till en virtuell dator har en eller flera associerade IP-konfigurationer. Varje konfiguration tilldelas en statisk eller dynamisk privat IP-adress. Varje konfiguration kan också ha en associerad offentlig IP-adressresurs. En offentlig IP-adressresurs har antingen en tilldelad dynamisk eller statisk offentlig IP-adress. Mer information om IP-adresser i Azure finns i artikeln om [IP-adresser i Azure](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md). 

Det finns en gräns för hur många privata IP-adresser kan tilldelas till ett NIC. Det finns också en gräns för hur många offentliga IP-adresser som kan användas i en Azure-prenumeration. Mer information finns i artikeln om [Azure-gränser](../articles/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
