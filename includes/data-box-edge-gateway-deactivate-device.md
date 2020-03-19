---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128873"
---
Om du återställa enheten måste du på ett säkert sätt rensa alla data på datadisken och startdisken på enheten. 

Använd cmdleten `Reset-HcsAppliance` till att rensa både datadiskarna och startdisken, eller bara datadiskarna. Med växlarna `ClearData` och `BootDisk` kan du rensa datadiskarna respektive startdisken.

Växeln `BootDisk` rensar startdisken och gör enheten oanvändbar. Den bör bara användas när enheten måste returneras till Microsoft. Mer information finns i artikeln om att [returnera enheten till Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Om du använder enhetsåterställningen i det lokala webbgränssnittet rensas bara datadiskarna på ett säkert sätt, men startdisken hålls intakt. Startdisken innehåller enhetskonfigurationen.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Skriv följande i kommandotolken:

    `Reset-HcsAppliance -ClearData -BootDisk`

    I följande exempel visas hur du använder denna cmdlet:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
