---
title: Ladda ned mallen för en virtuell Azure-dator | Microsoft Docs
description: Ladda ned templatefor en virtuell dator för att hjälpa till med automatisering av distributioner i distributions modellen för Resource Manager
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
ms.openlocfilehash: 39abe054869bf68a254b9305a680ecc892359300
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898876"
---
# <a name="download-the-template-for-a-vm"></a>Ladda ned mallen för en virtuell dator
När du skapar en virtuell dator i Azure med hjälp av portalen eller PowerShell skapas en Resource Manager-mall automatiskt åt dig. Du kan använda den här mallen för att snabbt duplicera en distribution. Mallen innehåller information om alla resurser i en resurs grupp. För en virtuell dator innebär detta att mallen innehåller allt som skapas i stöd för den virtuella datorn i resurs gruppen, inklusive nätverks resurserna.

## <a name="download-the-template-using-the-portal"></a>Ladda ned mallen med portalen
1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Virtual Machines**på en vänster-meny.
3. Välj den virtuella datorn i listan.
4. Välj **Exportera mall**.
5. Välj **Ladda ned** på menyn längst upp och spara. zip-filen på den lokala datorn.
6. Öppna. zip-filen och extrahera filerna till en mapp. Zip-filen innehåller:
   
   * Parameters. JSON
   * Template. JSON

Filen Template. JSON är mallen.

## <a name="download-the-template-using-powershell"></a>Ladda ned mallen med PowerShell
Du kan också hämta. JSON-mallfilen med cmdleten [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . Du kan använda parametern `-path` för att ange fil namn och sökväg för. JSON-filen. Det här exemplet visar hur du hämtar mallen för resurs gruppen med namnet **myResourceGroup** till mappen **C:\users\public\downloads** på den lokala datorn.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du distribuerar resurser med hjälp av mallar finns i [genom gång av Resource Manager-mall](../../azure-resource-manager/resource-manager-template-walkthrough.md).

