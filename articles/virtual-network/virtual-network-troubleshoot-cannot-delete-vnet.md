---
title: Det går inte att ta bort ett virtuellt nätverk i Azure | Microsoft Docs
description: Lär dig hur du felsöker problemet där det inte går att ta bort ett virtuellt nätverk i Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 8942e9180e87552ec64e0e848751f492778c9993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131656"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Fel sökning: det gick inte att ta bort ett virtuellt nätverk i Azure

Du kan få fel meddelanden när du försöker ta bort ett virtuellt nätverk i Microsoft Azure. Den här artikeln innehåller fel söknings steg som hjälper dig att lösa det här problemet.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrol lera om en virtuell nätverksgateway körs i det virtuella nätverket](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Kontrol lera om en Application Gateway körs i det virtuella nätverket](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Kontrol lera om Azure Active Directory domän tjänsten är aktive rad i det virtuella nätverket](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Kontrol lera om det virtuella nätverket är anslutet till en annan resurs](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Kontrol lera om en virtuell dator fortfarande körs i det virtuella nätverket](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Kontrol lera om det virtuella nätverket fastnar i migreringen](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Kontrol lera om en virtuell nätverksgateway körs i det virtuella nätverket

Om du vill ta bort det virtuella nätverket måste du först ta bort den virtuella Nätverksgatewayen.

För klassiska virtuella nätverk går du till **översikts** sidan för det klassiska virtuella nätverket i Azure Portal. I avsnittet **VPN-anslutningar** , om gatewayen körs i det virtuella nätverket, visas IP-adressen för gatewayen. 

![Kontrol lera om gatewayen körs](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

För virtuella nätverk går du till sidan **Översikt** i det virtuella nätverket. Kontrol lera **anslutna enheter** för den virtuella Nätverksgatewayen.

![Kontrol lera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Innan du kan ta bort gatewayen tar du först bort alla **anslutnings** objekt i gatewayen. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Kontrol lera om en Application Gateway körs i det virtuella nätverket

Gå till **översikts** sidan för det virtuella nätverket. Kontrol lera de **anslutna enheterna** för Application Gateway.

![Kontrol lera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Om det finns en Application Gateway måste du ta bort den innan du kan ta bort det virtuella nätverket.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Kontrol lera om Azure Active Directory domän tjänsten är aktive rad i det virtuella nätverket

Om tjänsten Active Directory-domän är aktive rad och ansluten till det virtuella nätverket kan du inte ta bort det här virtuella nätverket. 

![Kontrol lera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Information om hur du inaktiverar tjänsten finns i [inaktivera Azure Active Directory Domain Services att använda Azure Portal](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Kontrol lera om det virtuella nätverket är anslutet till en annan resurs

Sök efter krets länkar, anslutningar och virtuella nätverks peering. Något av dessa kan orsaka att det inte går att ta bort ett virtuellt nätverk. 

Den rekommenderade borttagnings ordningen är följande:

1. Gatewayanslutningar
2. Gateways
3. Period
4. Peering för virtuella nätverk
5. App Service-miljön (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Kontrol lera om en virtuell dator fortfarande körs i det virtuella nätverket

Kontrol lera att det inte finns någon virtuell dator i det virtuella nätverket.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Kontrol lera om det virtuella nätverket fastnar i migreringen

Om det virtuella nätverket fastnar i ett migreringsarkiv kan det inte tas bort. Kör följande kommando för att avbryta migreringen och ta sedan bort det virtuella nätverket.

```azurepowershell
Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort
```

## <a name="next-steps"></a>Nästa steg

- [Azure Virtual Network](virtual-networks-overview.md)
- [Vanliga frågor och svar (FAQ) om Azure Virtual Network](virtual-networks-faq.md)
