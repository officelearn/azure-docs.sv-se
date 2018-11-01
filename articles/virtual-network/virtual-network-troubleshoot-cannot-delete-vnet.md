---
title: Det går inte att ta bort ett virtuellt nätverk i Azure | Microsoft Docs
description: Lär dig mer om att felsöka problemet som du inte kan ta bort ett virtuellt nätverk i Azure.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4bd143b37e4403d039108b4349b27604b6503e0e
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415227"
---
# <a name="troubleshooting-failed-to-delete-a-virtual-network-in-azure"></a>Felsökning: Det gick inte att ta bort ett virtuellt nätverk i Azure

Du kan få felmeddelanden när du försöker ta bort ett virtuellt nätverk i Microsoft Azure. Den här artikeln innehåller felsökningssteg för att hjälpa dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrollera om en virtuell nätverksgateway körs i det virtuella nätverket](#check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network).
2. [Kontrollera om en application gateway körs i det virtuella nätverket](#check-whether-an-application-gateway-is-running-in-the-virtual-network).
3. [Kontrollera om Azure Active Directory Domain Service har aktiverats i det virtuella nätverket](#check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network).
4. [Kontrollera om det virtuella nätverket är ansluten till en annan resurs](#check-whether-the-virtual-network-is-connected-to-other-resource).
5. [Kontrollera om en virtuell dator körs fortfarande i det virtuella nätverket](#check-whether-a-virtual-machine-is-still-running-in-the-virtual-network).
6. [Kontrollera om det virtuella nätverket har fastnat i migrering](#check-whether-the-virtual-network-is-stuck-in-migration).

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

### <a name="check-whether-a-virtual-network-gateway-is-running-in-the-virtual-network"></a>Kontrollera om en virtuell nätverksgateway körs i virtuella nätverk

Om du vill ta bort det virtuella nätverket, måste du först ta bort den virtuella nätverksgatewayen.

För klassiska virtuella nätverk, går du till den **översikt** sida av det klassiska virtuella nätverket i Azure-portalen. I den **VPN-anslutningar** avsnittet, om gatewayen körs i det virtuella nätverket, så ser du IP-adressen för gatewayen. 

![Kontrollera om gatewayen körs](media/virtual-network-troubleshoot-cannot-delete-vnet/classic-gateway.png)

För virtuella nätverk går du till den **översikt** sidan för det virtuella nätverket. Kontrollera **anslutna enheter** för den virtuella nätverksgatewayen.

![Kontrollera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/vnet-gateway.png)

Innan du kan ta bort gatewayen först ta bort någon **anslutning** objekt i gatewayen. 

### <a name="check-whether-an-application-gateway-is-running-in-the-virtual-network"></a>Kontrollera om en application gateway körs i virtuella nätverk

Gå till den **översikt** sidan för det virtuella nätverket. Kontrollera den **anslutna enheter** för application gateway.

![Kontrollera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/app-gateway.png)

Om det finns en application gateway, måste du ta bort det innan du kan ta bort det virtuella nätverket.

### <a name="check-whether-azure-active-directory-domain-service-is-enabled-in-the-virtual-network"></a>Kontrollera om Azure Active Directory Domain Service har aktiverats i det virtuella nätverket

Om Active Directory Domain Service är aktiverat och anslutet till det virtuella nätverket, kan du inte ta bort det här virtuella nätverket. 

![Kontrollera den anslutna enheten](media/virtual-network-troubleshoot-cannot-delete-vnet/enable-domain-services.png)

Om du vill inaktivera tjänsten, se [inaktivera Azure Active Directory Domain Services med Azure portal](../active-directory-domain-services/active-directory-ds-disable-aadds.md).

### <a name="check-whether-the-virtual-network-is-connected-to-other-resource"></a>Kontrollera om det virtuella nätverket är ansluten till en annan resurs

Sök efter Kretslänkar, anslutningar och virtuella nätverk. Något av detta kan orsaka ett virtuellt nätverk borttagningen misslyckas. 

Den rekommendera borttagning ordningen är följande:

1. gateway-anslutningar
2. Gateways
3. IP-adresser
4. Virtuella nätverk
5. App Service-miljö (ASE)

### <a name="check-whether-a-virtual-machine-is-still-running-in-the-virtual-network"></a>Kontrollera om en virtuell dator körs fortfarande i det virtuella nätverket

Se till att ingen virtuell dator är i det virtuella nätverket.

### <a name="check-whether-the-virtual-network-is-stuck-in-migration"></a>Kontrollera om det virtuella nätverket har fastnat i migrering

Om det virtuella nätverket har fastnat i en migreringstillståndet, kan inte tas bort. Kör följande kommando för att avbryta migreringen och sedan ta bort det virtuella nätverket.

    Move-AzureVirtualNetwork -VirtualNetworkName "Name" -Abort

## <a name="next-steps"></a>Nästa steg

- [Azure Virtual Network](virtual-networks-overview.md)
- [Vanliga frågor och svar (FAQ) om Azure Virtual Network](virtual-networks-faq.md)
