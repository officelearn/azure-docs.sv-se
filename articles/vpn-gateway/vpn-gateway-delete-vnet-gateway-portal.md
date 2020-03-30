---
title: 'Azure VPN Gateway: Ta bort en gateway: portal'
description: Ta bort en virtuell nätverksgateway med Azure-portalen i Distributionsmodellen för Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863673"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Ta bort en virtuell nätverksgateway med portalen

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Den här artikeln innehåller instruktioner för hur du tar bort en Azure VPN-gateways som distribueras med hjälp av Distributionsmodellen för Resource Manager. Det finns ett par olika metoder som du kan ta när du vill ta bort en virtuell nätverksgateway för en VPN-gatewaykonfiguration.

- Om du vill ta bort allt och börja om, som i fallet med en testmiljö, kan du ta bort resursgruppen. När du tar bort en resursgrupp tas alla resurser i gruppen bort. Den här metoden rekommenderas bara om du inte vill behålla någon av resurserna i resursgruppen. Du kan inte selektivt ta bort bara ett fåtal resurser med den här metoden.

- Om du vill behålla några av resurserna i resursgruppen blir det något mer komplicerat att ta bort en virtuell nätverksgateway. Innan du kan ta bort den virtuella nätverksgatewayen måste du först ta bort alla resurser som är beroende av gatewayen. Vilka steg du följer beror på vilken typ av anslutningar du har skapat och de beroende resurserna för varje anslutning.

> [!IMPORTANT]
> Instruktionerna nedan beskriver hur du tar bort Azure VPN-gateways som distribueras med hjälp av Distributionsmodellen för Resource Manager. Om du vill ta bort en VPN-gateway som distribueras med den klassiska distributionsmodellen använder du Azure PowerShell enligt beskrivningen [här](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Ta bort en VPN-gateway

Om du vill ta bort en virtuell nätverksgateway måste du först ta bort varje resurs som hör till den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Nu tas den virtuella nätverksgatewayen bort. Nästa steg hjälper dig att ta bort alla resurser som inte längre används.

### <a name="to-delete-the-local-network-gateway"></a>Så här tar du bort den lokala nätverksgatewayen

1. Leta reda på de lokala nätverksgatewayer som var associerade med varje anslutning i **Alla resurser.**
2. Klicka på **Ta bort**på **bladet Översikt** för den lokala nätverksgatewayen .

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Så här tar du bort den offentliga IP-adressresursen för gatewayen

1. Leta reda på den offentliga IP-adressresurs som var kopplad till gatewayen i **Alla resurser.** Om den virtuella nätverksgatewayen var aktiv-aktiv visas två offentliga IP-adresser. 
2. Klicka på **Ta bort**på sidan **Översikt** för den offentliga IP-adressen och sedan **Ja** för att bekräfta.

### <a name="to-delete-the-gateway-subnet"></a>Så här tar du bort gateway-undernätet

1. Leta reda på det virtuella nätverket i **Alla resurser.** 
2. Klicka på **GatewaySubnet**på **bladet Undernät** och klicka sedan på **Ta bort**. 
3. Klicka på **Ja** för att bekräfta att du vill ta bort gateway-undernätet.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Ta bort en VPN-gateway genom att ta bort resursgruppen

Om du inte är orolig för att behålla någon av dina resurser i resursgruppen och bara vill börja om från början kan du ta bort en hel resursgrupp. Detta är ett snabbt sätt att ta bort allt. Följande steg gäller endast för resurshanterarens distributionsmodell.

1. Leta reda på resursgruppen i **Alla resurser**och klicka för att öppna bladet.
2. Klicka på **Ta bort**. Visa de berörda resurserna på bladet Ta bort. Kontrollera att du vill ta bort alla dessa resurser. Om inte, använd stegen i Ta bort en VPN-gateway högst upp i den här artikeln.
3. Om du vill fortsätta skriver du namnet på den resursgrupp som du vill ta bort och klickar sedan på **Ta bort**.
