---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187802"
---
Om du upplever några enhetsproblem kan du skapa ett supportpaket från systemloggarna. Microsoft Support använder det här paketet för att felsöka problemen. Så här skapar du ett supportpaket:

1. [Anslut till Enhetens PowerShell-gränssnitt](#connect-to-the-powershell-interface).
2. Använd `Get-HcsNodeSupportPackage` kommandot för att skapa ett supportpaket. Användningen av cmdlet är följande:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Cmdlet samlar in loggar från enheten och kopierar loggarna till ett angivet nätverk eller lokal resurs.

    De parametrar som används är följande:

    - `-Path`- Ange nätverket eller den lokala sökvägen som supportpaketet ska kopieras till. (krävs)
    - `-Credential`- Ange autentiseringsuppgifter för att komma åt den skyddade sökvägen.
    - `-Zip`- Ange att generera en zip-fil.
    - `-Include`- Ange att de komponenter som ska ingå i supportpaketet ska ingå. Om inte anges, `Default` antas.
    - `-IncludeArchived`- Ange att inkludera arkiverade loggar i supportpaketet.
    - `-IncludePeriodicStats`- Ange att inkludera periodiska stat loggar i supportpaketet.

    
