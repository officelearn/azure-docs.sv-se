---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555301"
---
Om du vill återställa din enhet, måste du radera alla data på datadisken och startdisken för enheten på ett säkert sätt. 

Använd den `Reset-HcsAppliance` cmdlet för att rensa ut datadiskar och startdisken eller bara datadiskar. Den `ClearData` och `BootDisk` växlar kan du rensa datadiskar och startdisken respektive.

Om du använder enhets-återställning i det lokala webbgränssnittet endast datadiskar raderas på ett säkert sätt men startdisken förblir intakta. Startdisken innehåller enhetskonfigurationen.

1. [Ansluta till PowerShell-gränssnittet](#connect-to-the-powershell-interface).
2. Skriv följande i kommandotolken:

    `Reset-HcsAppliance -ClearData -BootDisk`

    I följande exempel visas hur du använder den här cmdleten:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
