---
title: "Hämta en mall för en virtuell dator i Azure | Microsoft Docs"
description: "Hämta templatefor en virtuell dator för att automatisera distributioner i Resource Manager-distributionsmodellen"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 93ed84cb146119c877c3a143c5f7af9ca8ba0656
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2017
---
# <a name="download-the-template-for-a-vm"></a>Ladda ned mallen för en virtuell dator
När du skapar en virtuell dator i Azure med hjälp av portalen eller PowerShell skapas automatiskt en Resource Manager-mall för dig. Du kan använda den här mallen för att snabbt duplicera en distribution. Mallen innehåller information om alla resurser i en resursgrupp. För en virtuell dator, innebär detta att mallen innehåller allt som har skapats för den virtuella datorn i den resursgrupp, inklusive nätverksresurser.

## <a name="download-the-template-using-the-portal"></a>Hämta en mall med hjälp av portalen
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. En den vänstra menyn markerar **virtuella datorer**.
3. Välj den virtuella datorn i listan.
4. Välj **automatiseringsskriptet**.
5. Välj **hämta** från menyn överst och spara ZIP-filen till den lokala datorn.
6. Öppna .zip-filen och extrahera filerna till en mapp. ZIP-filen innehåller:
   
   * Deploy.ps1
   * Deploy.SH 
   * deployer.RB
   * DeploymentHelper.cs
   * parameters.JSON
   * Template.JSON

Filen template.json är mallen.

## <a name="download-the-template-using-powershell"></a>Hämta en mall med hjälp av PowerShell
Du kan också hämta JSON mallen filen med den [Export-AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) cmdlet. Du kan använda den `-path` parametern för att ange filnamnet och sökvägen för JSON-fil. Det här exemplet illustrerar hur du hämtar mallen för resursgruppen med namnet **myResourceGroup** till den **C:\users\public\downloads** mapp på den lokala datorn.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du distribuerar resurser med hjälp av mallar finns [genomgång av Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

