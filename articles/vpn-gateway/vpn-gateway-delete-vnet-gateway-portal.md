---
title: 'Ta bort en virtuell nätverksgateway: Azure-portalen: Resource Manager | Microsoft Docs'
description: Ta bort en virtuell nätverksgateway med hjälp av Azure-portalen i Resource Manager-distributionsmodellen.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.openlocfilehash: d0c54a00afd723b68bedc0a305cfb0f16edf4ac9
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816263"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Ta bort en virtuell nätverksgateway med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisk)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Den här artikeln innehåller instruktioner för att ta bort en Azure VPN-gatewayer som distribueras med hjälp av Resource Manager-distributionsmodellen. Det finns ett par olika metoder som du kan använda när du vill ta bort en virtuell nätverksgateway för en VPN-gateway-konfiguration.

- Om du vill ta bort allt innehåll och börja om från början, som i fallet med en testmiljö kan du ta bort resursgruppen. När du tar bort en resursgrupp tas alla resurser i gruppen bort. Den här metoden rekommenderas bara om du inte vill behålla någon av resurserna i resursgruppen. Du kan inte selektivt Radera endast några resurser med hjälp av den här metoden.

- Om du vill behålla några av resurserna i resursgruppen blir tar du bort en virtuell nätverksgateway något mer komplicerad. Innan du kan ta bort den virtuella nätverksgatewayen, måste du först radera alla resurser som är beroende av gatewayen. De steg du följer beror på vilken typ av anslutningar som du skapade och alla beroende resurser för varje anslutning.

> [!IMPORTANT]
> Anvisningarna nedan beskriver hur du tar bort Azure VPN-gatewayer som distribueras med hjälp av Resource Manager-distributionsmodellen. Om du vill ta bort en VPN-gateway distribueras med den klassiska distributionsmodellen, Använd Azure PowerShell enligt beskrivningen [här](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Ta bort en VPN-gateway

Om du vill ta bort en virtuell nätverksgateway måste du ta bort varje resurs som gäller för den virtuella nätverksgatewayen. Resurser måste tas bort i en viss ordning på grund av beroenden.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Då raderas den virtuella nätverksgatewayen. Nästa åtgärderna kan du ta bort alla resurser som används inte längre.

### <a name="to-delete-the-local-network-gateway"></a>Att ta bort den lokala nätverksgatewayen

1. I **alla resurser**, leta upp de lokala nätverksgatewayerna som är kopplade till varje anslutning.
2. På den **översikt** bladet för den lokala nätverksgatewayen, klickar du på **ta bort**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Att ta bort den offentliga IP-adressresursen för gateway

1. I **alla resurser**, leta upp den offentliga IP-adressresursen som är kopplad till gatewayen. Om den virtuella nätverksgatewayen var aktiv-aktiv, visas två offentliga IP-adresser. 
2. På den **översikt** för offentliga IP-adress och klicka på **ta bort**, sedan **Ja** att bekräfta.

### <a name="to-delete-the-gateway-subnet"></a>Att ta bort gateway-undernätet

1. I **alla resurser**, hitta det virtuella nätverket. 
2. På den **undernät** bladet klickar du på den **GatewaySubnet**, klicka sedan på **ta bort**. 
3. Klicka på **Ja** att bekräfta att du vill ta bort gateway-undernätet.

## <a name="deleterg"></a>Ta bort en VPN-gateway genom att ta bort resursgruppen.

Om du inte är orolig att behålla någon av dina resurser i resursgruppen och du bara vill börja om från början, kan du ta bort en hela resursgruppen. Det här är ett snabbt sätt att ta bort allt. Följande steg gäller endast för Resource Manager-distributionsmodellen.

1. I **alla resurser**, leta upp resursgruppen och klicka för att öppna bladet.
2. Klicka på **Ta bort**. Visa berörda resurser på Delete-bladet. Se till att du vill ta bort alla dessa resurser. Om inte, använder du stegen i ta bort en VPN-gateway överst i den här artikeln.
3. Om du vill fortsätta, skriver du namnet på resursgruppen som du vill ta bort och klicka sedan på **ta bort**.
