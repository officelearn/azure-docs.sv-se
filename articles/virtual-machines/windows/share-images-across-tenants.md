---
title: Dela galleriavbildningar över klienter i Azure | Microsoft Docs
description: Lär dig mer om att dela VM-avbildningar i Azure-klienter med hjälp av delad Image Galleries.
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 5b3c4e5380c65b2ab6c736e7fabe1813fe32afc2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466490"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Dela galleriet VM-avbildningar i Azure-klienter

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


> [!IMPORTANT]
> Du kan inte använda portalen för att distribuera en virtuell dator från en avbildning i en annan azure-klient. Om du vill skapa en virtuell dator från en avbildning som delas mellan klienter, måste du använda den [Azure CLI](../linux/share-images-across-tenants.md) eller Powershell.

## <a name="create-a-vm-using-powershell"></a>Skapa en virtuell dator med hjälp av PowerShell


Logga in på båda klienter med hjälp av program-ID, hemlighet och klient-ID. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

Skapa den virtuella datorn i resursgruppen som har behörighet på appregistreringen. Ersätt informationen i det här exemplet med dina egna.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>Nästa steg

Du kan också skapa delade bild galleriresurser med hjälp av den [Azure-portalen](shared-images-portal.md).