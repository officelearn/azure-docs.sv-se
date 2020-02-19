---
title: Azure Firewall SNAT privata IP-adressintervall
description: Du kan konfigurera IP-adressens privata intervall så att brand väggen inte översätter trafik till dessa IP-adresser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/09/2020
ms.author: victorh
ms.openlocfilehash: b190d07ceadea43ca572f5eb5be3eeeafa616971
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444464"
---
# <a name="azure-firewall-snat-private-ip-address-ranges"></a>Azure Firewall SNAT privata IP-adressintervall

Azure-brandväggen är inte SNAT när mål-IP-adressen är i ett privat IP-adressintervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk, kommer Azure-brandväggen att besvara trafiken till någon av de privata IP-adresserna för brand väggen i AzureFirewallSubnet. Du kan dock konfigurera Azure-brandväggen så att den **inte** bevarar ditt offentliga IP-adressintervall.

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
$azfw.PrivateRange = @(“IANAPrivateRanges”,“IPRange1”, “IPRange2”)
Set-AzFirewall -AzureFirewall $azfw
```

### <a name="templates"></a>Mallar

Du kan lägga till följande i avsnittet `additionalProperties`:

```
"additionalProperties": {
                    "Network.SNAT.PrivateRanges": "IANAPrivateRanges , IPRange1, IPRange2"
                },
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).