---
title: Distribuera Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell
description: I den här artikeln får du lära dig hur du distribuerar en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/21/2019
ms.author: victorh
ms.openlocfilehash: 9ec37197376c815c4fb9072164520a707b02be2b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312727"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuera en Azure-brandvägg med flera offentliga IP-adresser med hjälp av Azure PowerShell

> [!IMPORTANT]
> Azure-brandväggen med flera offentliga IP-adresser är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan distribuera en Azure-brandvägg med upp till 600 offentliga IP-adresser.

Den här funktionen gör det möjligt för följande scenarier:

- **DNAT** – du kan översätta flera instanser av standard-port till backend-servrar. Du kan till exempel översätta TCP-port 3389 (RDP) för båda IP-adresser om du har två offentliga IP-adresser.
- **SNAT** -ytterligare portar som är tillgängliga för utgående SNAT-anslutningar, minskar risken för SNAT portöverbelastning. För tillfället väljer Azure brandvägg slumpmässigt offentliga IP-källadressen för en anslutning. Om du har någon underordnad filtrering i nätverket, måste du tillåta alla offentliga IP-adresser som är associerade med din brandvägg.

I följande Azure PowerShell-exempel visas hur du kan lägga till, ta bort och konfigurera offentliga IP-adresser för Azure-brandväggen.

> [!NOTE]
> Den offentliga förhandsversionen, om du lägger till eller ta bort en offentlig IP-adress till en aktiv brandvägg kanske befintliga inkommande anslutningar med hjälp av DNAT regler inte fungerar i 40-120 sekunder. Du kan inte ta bort den första offentliga IP-adress som har tilldelats brandväggen, såvida inte brandväggen har frigjorts eller tagits bort.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Lägga till en offentlig IP-adress till en befintlig brandvägg

I det här exemplet, offentliga IP-adress *azFwPublicIp1* som återfinns i brandväggen.

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

I det här exemplet, offentliga IP-adress *azFwPublicIp1* som är oberoende av från brandväggen.

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

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)
