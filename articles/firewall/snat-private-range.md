---
title: Privata IP-adressintervall för Azure Firewall SNAT
description: Du kan konfigurera privata IP-adressintervall så att brandväggen inte SNAT-trafik till dessa IP-adresser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064815"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Privata IP-adressintervall för Azure Firewall SNAT

Azure-brandväggen fungerar inte med nätverksregler när mål-IP-adressen finns i ett privat IP-adressintervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Programregler tillämpas alltid med hjälp av en [transparent proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) oavsett mål-IP-adress.

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk, Azure Firewall SNATs trafiken till en av brandväggen privata IP-adresser i AzureFirewallSubnet. Du kan dock konfigurera Azure-brandväggen så att **den inte** är SNAT-adressintervallet för din offentliga IP.However, you can configure Azure Firewall to not SNAT your public IP address range.

## <a name="configure-snat-private-ip-address-ranges"></a>Konfigurera SNAT-privata IP-adressintervall

Du kan använda Azure PowerShell för att ange ett IP-adressintervall som brandväggen inte kommer att SNAT.

### <a name="new-firewall"></a>Ny brandvägg

För en ny brandvägg är Kommandot Azure PowerShell:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges utökas till de aktuella standardinställningarna på Azure-brandväggen medan de andra intervallen läggs till i den.

Mer information finns i [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Befintlig brandvägg

Om du vill konfigurera en befintlig brandvägg använder du följande Azure PowerShell-kommandon:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Mallar

Du kan lägga till `additionalProperties` följande i avsnittet:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).