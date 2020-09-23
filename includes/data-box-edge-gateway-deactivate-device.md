---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "79128873"
---
Om du återställa enheten måste du på ett säkert sätt rensa alla data på datadisken och startdisken på enheten. 

Använd `Reset-HcsAppliance` cmdleten för att rensa både data diskarna och start disken eller bara data diskarna. Med `ClearData` `BootDisk` växlarna och kan du rensa data diskarna och start disken.

`BootDisk`Växeln rensar start disken och gör enheten oanvändbar. Den bör bara användas när enheten måste returneras till Microsoft. Mer information finns i [returnera enheten till Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Om du använder enhetsåterställningen i det lokala webbgränssnittet rensas bara datadiskarna på ett säkert sätt, men startdisken hålls intakt. Startdisken innehåller enhetskonfigurationen.

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
