---
title: Ladda ned mallen för en virtuell Azure-dator
description: Hämta mallen för en virtuell dator med portalen eller PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085390"
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

