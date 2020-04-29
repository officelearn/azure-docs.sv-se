---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128873"
---
Om du vill återställa enheten måste du rensa alla data på data disken och start disken på enheten på ett säkert sätt. 

Använd `Reset-HcsAppliance` cmdleten för att rensa både data diskarna och start disken eller bara data diskarna. Med `ClearData` växlarna och `BootDisk` kan du rensa data diskarna och start disken.

`BootDisk` Växeln rensar start disken och gör enheten oanvändbar. Den bör endast användas när enheten behöver returneras till Microsoft. Mer information finns i [returnera enheten till Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Om du använder enhets återställning i det lokala webb gränssnittet rensas bara data diskarna på ett säkert sätt, men start disken hålls intakt. Start disken innehåller enhets konfigurationen.

1. [Anslut till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Skriv följande i kommandotolken:

    `Reset-HcsAppliance -ClearData -BootDisk`

    I följande exempel visas hur du använder den här cmdleten:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
