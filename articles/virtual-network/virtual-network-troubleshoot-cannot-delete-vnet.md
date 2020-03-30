---
title: Det går inte att ta bort ett virtuellt nätverk i Azure | Microsoft-dokument
description: Lär dig hur du felsöker problemet där du inte kan ta bort ett virtuellt nätverk i Azure.
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
ms.openlocfilehash: 2d427a8b40fcb537801ce76aae6bc32fcda3a307
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056932"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Felsökning: Det gick inte att ta bort ett virtuellt nätverk i Azure

Du kan få felmeddelanden när du försöker ta bort ett virtuellt nätverk i Microsoft Azure. Den här artikeln innehåller felsökningssteg som hjälper dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrollera om en virtuell nätverksgateway körs i det virtuella nätverket](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Kontrollera om en programgateway körs i det virtuella nätverket](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Kontrollera om Azure Active Directory Domain Service är aktiverat i det virtuella nätverket](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Kontrollera om det virtuella nätverket är anslutet till andra resurser](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Kontrollera om en virtuell dator fortfarande körs i det virtuella nätverket](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Kontrollera om det virtuella nätverket har fastnat i migreringen](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Kontrollera om en virtuell nätverksgateway körs i det virtuella nätverket

Om du vill ta bort det virtuella nätverket måste du först ta bort den virtuella nätverksgatewayen.

För klassiska virtuella nätverk går du till **översiktssidan för** det klassiska virtuella nätverket i Azure-portalen. I avsnittet **VPN-anslutningar,** om gatewayen körs i det virtuella nätverket, kommer du att se IP-adressen för gatewayen. 

![Kontrollera om gatewayen körs](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

För virtuella nätverk går du till sidan **Översikt** för det virtuella nätverket. Kontrollera **anslutna enheter** för den virtuella nätverksgatewayen.

![Kontrollera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Innan du kan ta bort gatewayen tar du först bort alla **Anslutningsobjekt** i gatewayen. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Kontrollera om en programgateway körs i det virtuella nätverket

Gå till **sidan Översikt** för det virtuella nätverket. Kontrollera **de anslutna enheterna** för programgatewayen.

![Kontrollera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Om det finns en programgateway måste du ta bort den innan du kan ta bort det virtuella nätverket.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Kontrollera om Azure Active Directory Domain Service är aktiverat i det virtuella nätverket

Om Active Directory Domain Service är aktiverad och ansluten till det virtuella nätverket kan du inte ta bort det virtuella nätverket. 

![Kontrollera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Information om hur du inaktiverar tjänsten finns [i Inaktivera Azure Active Directory Domain Services med Azure-portalen](../active-directory-domain-services/delete-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Kontrollera om det virtuella nätverket är anslutet till andra resurser

Sök efter kretslänkar, anslutningar och virtuella nätverks peerings. Något av detta kan orsaka att en virtuell nätverksborttagning misslyckas. 

Den rekommenderade borttagningsordningen är följande:

1. Gatewayanslutningar
2. Gateways
3. Ips
4. Virtuella nätverks peerings
5. App Service Miljö (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Kontrollera om en virtuell dator fortfarande körs i det virtuella nätverket

Kontrollera att ingen virtuell dator finns i det virtuella nätverket.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Kontrollera om det virtuella nätverket har fastnat i migreringen

Om det virtuella nätverket har fastnat i ett migreringstillstånd kan det inte tas bort. Kör följande kommando för att avbryta migreringen och ta sedan bort det virtuella nätverket.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Nästa steg

- [Virtuella Azure-nätverk](virtual-networks-overview.md)
- [Vanliga frågor och svar (FAQ) om Azure Virtual Network](virtual-networks-faq.md)
