---
title: PowerShell-modul för Azure Lab Services | Microsoft-dokument
description: Den här artikeln innehåller information om en PowerShell-modul som hjälper till att hantera artefakter i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609403"
---
# <a name="azlabservices-powershell-module-preview"></a>PowerShell-modulen Az.LabServices (förhandsversion)
Az.LabServices är en PowerShell-modul som förenklar hanteringen av Azure Lab-tjänster. Den innehåller komposterbara funktioner för att skapa, fråga, uppdatera och ta bort labbkonton, labb, virtuella datorer och bilder. Mer information om den här modulen finns [på Az.LabServices hemsida på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Den här modulen är i **förhandsgranskning**. 

## <a name="example-command"></a>Exempelkommando
Här är ett exempel på hur du använder ett PowerShell-kommando för att stoppa alla virtuella datorer som körs i alla labb.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Komma igång
1. Installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) om det inte finns på din dator. 
2. Ladda ner [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) till din maskin.
3. Importera modulen:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Kör följande kommando för att lista alla labb i din prenumeration.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Nästa steg
Se [Az.LabServices hemsida på GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
