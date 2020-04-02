---
title: Skapa en Bastion-värd med Azure Powershell | Microsoft-dokument
description: I den här artikeln kan du lära dig hur du skapar en Azure Bastion-värd
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 43d834f0c834696cd4a836466c9663fe7c31a392
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520501"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Skapa en Azure Bastion-värd med Azure PowerShell

Den här artikeln visar hur du skapar en Azure Bastion-värd med PowerShell. När du har etablerat Azure Bastion-tjänsten i ditt virtuella nätverk är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Azure Bastion-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan också skapa en Azure Bastion-värd med hjälp av [Azure-portalen](bastion-create-host-portal.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en Bastion-värd

Det här avsnittet hjälper dig att skapa en ny Azure Bastion-resurs med Azure PowerShell.

1. Skapa ett virtuellt nätverk och ett Azure Bastion-undernät. Du måste skapa undernätet Azure Bastion med namnvärdet **AzureBastionSubnet**. Det här värdet gör att Azure vet vilket undernät som bastionresurserna ska distribueras till. Detta skiljer sig från ett gateway-undernät. Du måste använda ett undernät på minst /27 eller större undernät (/27, /26 och så vidare). Skapa **AzureBastionSubnet** utan några vägtabeller eller delegeringar. Om du använder nätverkssäkerhetsgrupper på **AzureBastionSubnet**läser du artikeln [Arbeta med NSG:er.](bastion-nsg.md)

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Skapa en offentlig IP-adress för Azure Bastion. Den offentliga IP:n är den offentliga IP-adressen som den Bastion-resurs som RDP/SSH kommer att nås på (över port 443). Den offentliga IP-adressen måste finnas i samma region som den Bastion-resurs som du skapar.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Skapa en ny Azure Bastion-resurs i AzureBastionSubnet i ditt virtuella nätverk. Det tar cirka 5 minuter för Bastion-resursen att skapa och distribuera.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor om Bastion](bastion-faq.md) för ytterligare information.

* Information om hur du använder nätverkssäkerhetsgrupper med undernätet Azure Bastion finns i [Arbeta med NSG.To](bastion-nsg.md)use Network Security Groups with the Azure Bastion subnet, see Work with NSGs .
