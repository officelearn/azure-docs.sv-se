---
title: Ladda ned mallen för en Azure-dator | Microsoft Docs
description: Ladda ned templatefor en virtuell dator för att automatisera distributioner i Resource Manager-distributionsmodellen
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 4ef0c09d2d2ea2ed06708342ab45abcaf149c23e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903122"
---
# <a name="download-the-template-for-a-vm"></a>Ladda ned mallen för en virtuell dator
När du skapar en virtuell dator i Azure med hjälp av portalen eller PowerShell skapas automatiskt en Resource Manager-mall för dig. Du kan använda den här mallen för att snabbt kopiera en distribution. Mallen innehåller information om alla resurser i en resursgrupp. För en virtuell dator innebär detta att mallen innehåller allt som har skapats som stöd för den virtuella datorn i den resursgruppen, inklusive nätverksresurser.

## <a name="download-the-template-using-the-portal"></a>Ladda ned mallen med hjälp av portalen
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. En den vänstra menyn, Välj **virtuella datorer**.
3. Välj den virtuella datorn i listan.
4. Välj **exportmallen**.
5. Välj **hämta** från menyn längst upp och spara ZIP-filen till din lokala dator.
6. Öppna ZIP-filen och extrahera filerna till en mapp. ZIP-filen innehåller:
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

Filen template.json är mallen.

## <a name="download-the-template-using-powershell"></a>Ladda ned mallen med hjälp av PowerShell
Du kan också hämta .json mall filen med den [Export AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) cmdlet. Du kan använda den `-path` parametern för att ange filnamnet och sökvägen för JSON-fil. Det här exemplet visar hur du laddar ned mallen för resursgruppen med namnet **myResourceGroup** till den **C:\users\public\downloads** mapp på din lokala dator.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Nästa steg
Läs mer om att distribuera resurser med hjälp av mallar i [genomgång av Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

