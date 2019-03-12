---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555312"
---
Om det uppstår enhetsproblem med kan skapa du ett supportpaket från systemloggar. Microsoft Support använder det här paketet för felsökning av problem. Följ stegen nedan för att skapa ett supportpaket:

1. [Ansluta till PowerShell-gränssnittet på enheten](#connect-to-the-powershell-interface).
2. Använd den `Get-HcsNodeSupportPackage` kommando för att skapa ett supportpaket. Användningen av cmdlet: en är följande:

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

    Cmdleten samlar in loggar från din enhet och kopierar dessa loggar på en angiven nätverk eller en lokal resurs.

    De parametrar som används är följande:

    - `-Path` -Ange nätverket eller den lokala sökvägen till kopiera supportpaket till. (krävs)
    - `-Credential` -Ange autentiseringsuppgifter för att komma åt den skyddade sökvägen.
    - `-Zip` -Ange om du vill generera en zip-fil.
    - `-Include` -Ange om du vill inkludera komponenterna som ska ingå i supportpaketet. Om inte anges `Default` antas.
    - `-IncludeArchived` -Ange om du vill inkludera arkiverade loggar i supportpaketet.
    - `-IncludePeriodicStats` -Ange om du vill inkludera periodiska stat loggar i supportpaketet.

    
