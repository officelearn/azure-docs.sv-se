---
title: Skapa en Bastion-värd med Azure CLI | Azure Bastion
description: I den här artikeln kan du lära dig hur du skapar och tar bort en Bastion-värd
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: cherylmc
ms.openlocfilehash: 9e216bf2bb8e2e605723256f0511b9bdb63083b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337576"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Skapa en Azure Bastion-värd med Azure CLI

Den här artikeln visar hur du skapar en Azure Bastion-värd med Azure CLI. När du har etablerat Azure Bastion-tjänsten i ditt virtuella nätverk är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Azure Bastion-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan också skapa en Azure Bastion-värd med hjälp av [Azure-portalen](bastion-create-host-portal.md)eller använda [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har en Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en Bastion-värd

Det här avsnittet hjälper dig att skapa en ny Azure Bastion-resurs med Azure CLI.

1. Skapa ett virtuellt nätverk och ett Azure Bastion-undernät. Du måste skapa undernätet Azure Bastion med namnvärdet **AzureBastionSubnet**. Det här värdet gör att Azure vet vilket undernät som bastionresurserna ska distribueras till. Detta skiljer sig från ett gateway-undernät. Du måste använda ett undernät på minst /27 eller större undernät (/27, /26 och så vidare). Skapa **AzureBastionSubnet** utan några vägtabeller eller delegeringar. Om du använder nätverkssäkerhetsgrupper på **AzureBastionSubnet**läser du artikeln [Arbeta med NSG:er.](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Skapa en offentlig IP-adress för Azure Bastion. Den offentliga IP:n är den offentliga IP-adressen som den Bastion-resurs som RDP/SSH kommer att nås på (över port 443). Den offentliga IP-adressen måste finnas i samma region som den Bastion-resurs som du skapar.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Skapa en ny Azure Bastion-resurs i AzureBastionSubnet i ditt virtuella nätverk. Det tar cirka 5 minuter för Bastion-resursen att skapa och distribuera.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor om Bastion](bastion-faq.md) för ytterligare information.

* Information om hur du använder nätverkssäkerhetsgrupper med undernätet Azure Bastion finns i [Arbeta med NSG.To](bastion-nsg.md)use Network Security Groups with the Azure Bastion subnet, see Work with NSGs .
