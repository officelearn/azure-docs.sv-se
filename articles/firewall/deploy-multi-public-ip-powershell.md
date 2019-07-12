---
title: Distribuera Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703989"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuera en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell

> [!IMPORTANT]
> Azure-brandväggen med flera offentliga IP-adresser är tillgängligt via Azure PowerShell, Azure CLI, REST och mallar. Användargränssnittet för portalen läggs till regioner stegvis och är tillgänglig i alla regioner när distributionen är klar.

Du kan distribuera en Azure-brandvägg med upp till 100 offentliga IP-adresser.

Den här funktionen gör det möjligt för följande scenarier:

- **DNAT** – du kan översätta flera instanser av standard-port till backend-servrar. Du kan till exempel översätta TCP-port 3389 (RDP) för båda IP-adresser om du har två offentliga IP-adresser.
- **SNAT** -ytterligare portar som är tillgängliga för utgående SNAT-anslutningar, minskar risken för SNAT portöverbelastning. För tillfället väljer Azure brandvägg slumpmässigt offentliga IP-källadressen för en anslutning. Om du har någon underordnad filtrering i nätverket, måste du tillåta alla offentliga IP-adresser som är associerade med din brandvägg.

I följande Azure PowerShell-exempel visas hur du kan konfigurera, lägga till och ta bort offentliga IP-adresser för Azure-brandväggen.

> [!NOTE]
> Du kan inte ta bort den första ipConfiguration från Azure-brandvägg offentliga IP-adress konfigurationssidan. Om du vill ändra IP-adressen kan du använda Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Skapa en brandväggsregel med två eller flera offentliga IP-adresser

Det här exemplet skapar en brandvägg som är kopplade till det virtuella nätverket *vnet* med två offentliga IP-adresser.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Lägga till en offentlig IP-adress till en befintlig brandvägg

I det här exemplet, offentliga IP-adress *azFwPublicIp1* är kopplad till brandväggen.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Ta bort en offentlig IP-adress från en befintlig brandvägg

I det här exemplet, offentliga IP-adress *azFwPublicIp1* har kopplats från brandväggen.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Nästa steg

* [Självstudier: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)
