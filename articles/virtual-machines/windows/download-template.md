---
title: Ladda ned mallen för en virtuell Azure-dator
description: Ladda ned mallarna för en virtuell dator för att hjälpa till med att automatisera distributioner i Resurshanterarens distributionsmodell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c73026515f0d7fde4e2f82838696700b1bb17c77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033548"
---
# <a name="download-the-template-for-a-vm"></a>Ladda ned mallen för en virtuell dator
När du skapar en virtuell dator i Azure med hjälp av portalen eller PowerShell skapas en Resource Manager-mall automatiskt åt dig. Du kan använda den här mallen för att snabbt duplicera en distribution. Mallen innehåller information om alla resurser i en resursgrupp. För en virtuell dator innebär det att mallen innehåller allt som skapas till stöd för den virtuella datorn i den resursgruppen, inklusive nätverksresurserna.

## <a name="download-the-template-using-the-portal"></a>Ladda ned mallen med hjälp av portalen
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtual Machines**en till vänstermenyn.
3. Välj den virtuella datorn i listan.
4. Välj **exportera mall**.
5. Välj **Hämta** högst upp på menyn och spara ZIP-filen på den lokala datorn.
6. Öppna ZIP-filen och extrahera filerna till en mapp. Zip-filen innehåller:
   
   * parameters.json
   * mall.json

Filen template.json är mallen.

## <a name="download-the-template-using-powershell"></a>Hämta mallen med PowerShell
Du kan också hämta mallfilen .json med cmdleten [Export-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) Du kan `-path` använda parametern för att ange filnamn och sökväg för .json-filen. I det här exemplet visas hur du hämtar mallen för resursgruppen **myResourceGroup** till mappen **C:\users\public\downloads** på den lokala datorn.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du distribuerar resurser med hjälp av mallar finns i [Resource Manager-mallgenomgången](../../azure-resource-manager/resource-manager-template-walkthrough.md).

