---
title: Skapa en skydds-värd med Azure PowerShell | Microsoft Docs
description: I den här artikeln lär du dig hur du skapar en Azure skydds-värd
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: f0e7a66ef7d6947306f1b2ffec54b8e9d12737d7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018601"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Skapa en Azure skydds-värd med hjälp av Azure PowerShell

Den här artikeln visar hur du skapar en Azure skydds-värd med PowerShell. När du har distribuerat skydds kan du ansluta till den virtuella datorn via dess privata IP-adress via webbläsaren med hjälp av Azure Portal. Din virtuella dator behöver ingen offentlig IP-adress, en ytterligare klient eller en särskild program vara. Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator. Den sömlösa RDP/SSH-upplevelsen är tillgänglig för alla virtuella datorer i samma virtuella nätverk.

Dessa anvisningar gäller för PowerShell-distribution. Du kan också skapa en Azure skydds-värd med hjälp av [Azure Portal](tutorial-create-host-portal.md) eller [Azure CLI](create-host-cli.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en skydds-värd

Det här avsnittet hjälper dig att skapa en ny Azure skydds-resurs för ditt virtuella nätverk med hjälp av Azure PowerShell.

1. Skapa ett virtuellt nätverk och ett Azure skydds-undernät. Du måste skapa Azure skydds-undernätet med namnet Value **AzureBastionSubnet**. Med det här värdet kan Azure veta vilket undernät som skydds-resurserna ska distribueras till. Detta skiljer sig från ett Gateway-undernät. Du måste använda ett undernät med minst/27 eller större undernät (/27,/26 osv.). Skapa **AzureBastionSubnet** utan några routningstabeller eller delegeringar. Om du använder nätverks säkerhets grupper på **AzureBastionSubnet**, se artikeln [arbeta med NSG: er](bastion-nsg.md) .

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Skapa en offentlig IP-adress för Azure-skydds. Den offentliga IP-adressen är den offentliga IP-adressen som skydds-resursen som RDP/SSH kommer att få åtkomst till (via port 443). Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Skapa en ny Azure skydds-resurs i AzureBastionSubnet för det virtuella nätverket. Det tar ungefär 5 minuter för skydds-resursen att skapa och distribuera.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Nästa steg

* Anslut till en virtuell dator.
   * [Virtuell Linux-dator](bastion-connect-vm-ssh.md)
   * [Virtuell Windows-dator](bastion-connect-vm-rdp.md)
