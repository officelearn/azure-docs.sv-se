---
title: Azure Firewall SNAT privata IP-adressintervall
description: Du kan konfigurera IP-adressens privata intervall så att brand väggen inte översätter trafik till dessa IP-adresser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/20/2020
ms.author: victorh
ms.openlocfilehash: ed8cef00b7de67458c607373c724a3717f14a7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064815"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT privata IP-adressintervall

Azure-brandväggen är inte SNAT med nätverks regler när mål-IP-adressen är i ett privat IP-adressintervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Program regler tillämpas alltid med en [transparent proxy](https://wikipedia.org/wiki/Proxy_server#Transparent_proxy) oavsett mål-IP-adress.

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk SNATs trafiken till någon av brand väggens privata IP-adresser i AzureFirewallSubnet med Azure-brandväggen. Du kan dock konfigurera Azure-brandväggen så att den **inte** bevarar ditt offentliga IP-adressintervall.

## <a name="configure-snat-private-ip-address-ranges"></a>Konfigurera privata IP-adressintervall för SNAT

Du kan använda Azure PowerShell för att ange ett IP-adressintervall som brand väggen inte är SNAT.

### <a name="new-firewall"></a>Ny brand vägg

För en ny brand vägg är Azure PowerShell kommandot:

`New-AzFirewall -Name $GatewayName -ResourceGroupName $RG -Location $Location -VirtualNetworkName $vnet.Name -PublicIpName $LBPip.Name -PrivateRange @("IANAPrivateRanges","IPRange1", "IPRange2")`

> [!NOTE]
> IANAPrivateRanges utökas till aktuella standardinställningar i Azure-brandväggen medan de andra intervallen läggs till i den.

Mer information finns i [New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall?view=azps-3.3.0).

### <a name="existing-firewall"></a>Befintlig brand vägg

Om du vill konfigurera en befintlig brand vägg använder du följande Azure PowerShell-kommandon:

```azurepowershell
$azfw = Get-AzFirewall -ResourceGroupName "Firewall Resource Group name"
$azfw.PrivateRange = @("IANAPrivateRanges","IPRange1", "IPRange2")
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Mallar

Du kan lägga till följande i `additionalProperties` avsnittet:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).