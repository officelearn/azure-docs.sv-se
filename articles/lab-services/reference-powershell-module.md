---
title: PowerShell-modul för Azure Lab Services | Microsoft Docs
description: Den här artikeln innehåller information om en PowerShell-modul som hjälper till med hantering av artefakter i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d9c184f3917be378eb77d1bf4096bfebb5ee1884
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445601"
---
# <a name="azlabservices-powershell-module-preview"></a>PowerShell-modulen Az.LabServices (förhandsversion)
AZ. LabServices är en PowerShell-modul som fören klar hanteringen av Azure Lab-tjänster. Den tillhandahåller sammanställnings bara funktioner för att skapa, fråga, uppdatera och ta bort labb konton, labb, virtuella datorer och avbildningar. Mer information om den här modulen finns på [Start sidan för AZ. LabServices på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Den här modulen är i för **hands version**. 

## <a name="example-command"></a>Exempelkommando
Här är ett exempel på hur du använder ett PowerShell-kommando för att stoppa alla virtuella datorer som körs i alla labb.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Kom igång
1. Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) om den inte finns på din dator. 
2. Ladda ned [AZ. LabServices. psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) till din dator.
3. Importera modulen:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Kör följande kommando för att visa en lista över alla labb i din prenumeration.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Nästa steg
Se [Start sidan för AZ. LabServices på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
