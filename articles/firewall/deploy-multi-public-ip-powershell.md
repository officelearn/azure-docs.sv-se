---
title: Distribuera Azure-brandväggen med flera offentliga IP-adresser med PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med flera offentliga IP-adresser med Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 21f645e64c9944ed102f538710ea6facc26c7e83
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314036"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuera en Azure Firewall med flera offentliga IP-adresser med hjälp av Azure PowerShell

Med den här funktionen kan du aktivera följande scenarier:

- **DNAT** - Du kan översätta flera standardportinstanser till dina serverdservrar. Om du till exempel har två offentliga IP-adresser kan du översätta TCP-port 3389 (RDP) för båda IP-adresserna.
- **SNAT** - Ytterligare portar är tillgängliga för utgående SNAT-anslutningar, vilket minskar risken för SNAT-portutmattning. För närvarande väljer Azure Firewall slumpmässigt den offentliga käll-IP-adress som ska användas för en anslutning. Om du har någon nedströmsfiltrering i nätverket måste du tillåta alla offentliga IP-adresser som är associerade med brandväggen.
 
Azure-brandvägg med flera offentliga IP-adresser är tillgänglig via Azure-portalen, Azure PowerShell, Azure CLI, REST och mallar. Du kan distribuera en Azure-brandvägg med upp till 100 offentliga IP-adresser.

Följande Azure PowerShell-exempel visar hur du kan konfigurera, lägga till och ta bort offentliga IP-adresser för Azure-brandväggen.

> [!NOTE]
> Du kan inte ta bort den första ipConfiguration från konfigurationssidan för offentlig IP-adress för Azure-brandväggen. Om du vill ändra IP-adressen kan du använda Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Skapa en brandvägg med två eller flera offentliga IP-adresser

I det här exemplet skapas en brandvägg som är ansluten till virtuellt *nätverksvnät* med två offentliga IP-adresser.

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

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Lägga till en offentlig IP-adress i en befintlig brandvägg

I det här exemplet är den offentliga IP-adressen *azFwPublicIp1* kopplad till brandväggen.

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

I det här exemplet är den offentliga IP-adressen *azFwPublicIp1* fristående från brandväggen.

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

* [Snabbstart: Skapa en Azure-brandvägg med flera offentliga IP-adresser - Resource Manager-mall](quick-create-multiple-ip-template.md)
