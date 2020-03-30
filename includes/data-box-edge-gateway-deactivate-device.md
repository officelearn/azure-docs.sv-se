---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128873"
---
Om du vill återställa enheten måste du på ett säkert sätt radera alla data på datadisken och enhetens startdiskett. 

Använd `Reset-HcsAppliance` cmdlet för att utplåna både datadiskarna och startdisketten eller bara datadiskarna. Med `ClearData` `BootDisk` respektive switcharna och växlarna kan du rensa datadiskarna respektive startdisketten.

Växeln `BootDisk` rensar startdisken och gör enheten oanvändbar. Den bör endast användas när enheten behöver returneras till Microsoft. Mer information finns i [Returnera enheten till Microsoft](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Om du använder enhetsåterställningen i det lokala webbgränssnittet är det bara datadiskarna som rensas på ett säkert sätt, men startdisketten hålls intakt. Startdisken innehåller enhetskonfigurationen.

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
