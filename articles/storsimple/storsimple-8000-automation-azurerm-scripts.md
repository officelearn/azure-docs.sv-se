---
title: Använda Azure Resource Manager-skript för att hantera StorSimple-enheter | Microsoft Docs
description: Lär dig hur du använder Azure Resource Manager-skript för att automatisera uppgifter för StorSimple
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 4184d3605c464b5da495de0698155dda1ae4e474
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438719"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Använda Azure Resource Manager SDK-baserade skript för att hantera StorSimple-enheter

Den här artikeln beskriver hur Azure Resource Manager SDK-baserade skript kan användas för att hantera din enhet i StorSimple 8000-serien. Dessutom ingår ett exempelskript vägleder dig genom stegen för att konfigurera din miljö för att köra skripten.

Den här artikeln gäller för enheter i StorSimple 8000-serien som kör i Azure portal.

## <a name="sample-scripts"></a>Exempelskript

Följande exempel på skript är tillgängliga för att automatisera olika StorSimple-jobb.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabell med Azure Resource Manager SDK-baserade exempelskript

| Skriptexempel för Azure Resource Manager                    | Beskrivning                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Authorize-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Det här skriptet kan du ge din StorSimple-enhet för att ändra krypteringsnyckeln för tjänstdata.                                                                                                           |
| [Create-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Det här skriptet skapar en 8010 eller 8020 StorSimple Cloud Appliance. Molninstallationen kan sedan konfigureras och registrerad med StorSimple Data Manager-tjänsten.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Det här skriptet skapar eller ändrar StorSimple-volymer.                                                                                                                                                             |
| [Get-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Det här skriptet visar en lista över alla säkerhetskopior för en enhet som registreras med StorSimple Device Manager-tjänsten.                                                                                                          |
| [Get-DeviceBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Detta skript för alla principer för säkerhetskopiering för StorSimple-enheten.                                                                                                                                                 |
| [Get-DeviceJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Det här skriptet hämtar alla StorSimple-jobb som körs på din StorSimple Device Manager-tjänsten.                                                                                                                     |
| [Get-DeviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Det här skriptet söker igenom uppdateringsservern och får du reda på om uppdateringar är tillgängliga att installera på din StorSimple-enhet.                                                                                          |
| [Install-DeviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Det här skriptet installerar uppdateringar på din StorSimple-enhet.                                                                                                                                           |
| [Manage-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Det här skriptet startar en manuell molnögonblicksbild och tar bort ögonblicksbilder av molnet som är äldre än angivna kvarhållningsdagar.                                                                                                   |
| [Monitor-Backups.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Det här Azure Automation Runbook PowerShell-skriptet rapporterar status för alla säkerhetskopieringsjobb.                                                                                                              |
| [Remove-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Det här skriptet tar bort ett enskilt objekt för säkerhetskopiering.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Det här skriptet startar en manuell säkerhetskopiering på StorSimple-enheten.                                                                                                                                       |
| [Update-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Det här skriptet uppdaterar krypteringsnyckeln för tjänstdata för alla StorSimple 8010/8020-Molninstallationer som registrerats med StorSimple Device Manager-tjänsten.                                     |
| [Verify-BackupScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Det här skriptet visar säkerhetskopiorna som saknas när du har analyserat alla scheman som är associerade med principer för säkerhetskopiering. Verifierar också säkerhetskopieringskatalogen med listan över tillgängliga säkerhetskopior.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurera och köra ett exempelskript

Det här avsnittet tar ett exempelskript och beskriver de olika stegen som krävs för att köra skriptet.

### <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du kontrollera att du har:

*   Azure PowerShell installerad. Installera Azure PowerShell-moduler:
    * I en Windows-miljö, följer du stegen i [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.4.0). Du kan installera Azure PowerShell på Windows Server-värd för din StorSimple om du använder en.
    * I en Linux eller MacOS-miljö, följer du stegen i [installera och konfigurera Azure PowerShell på MacOS eller Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux?view=azurermps-4.4.0).

Mer information om hur du använder Azure PowerShell, går du till [Kom igång med hjälp av Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azurermps-4.4.0).

### <a name="run-azure-powershell-script"></a>Köra Azure PowerShell-skript

Skriptet i det här exemplet visar en lista över alla jobb på en StorSimple-enhet. Detta omfattar de jobb som lyckades, misslyckades eller pågår. Utför följande steg för att ladda ned och kör skriptet.

1. Starta Azure PowerShell. Skapa en ny mapp och ändra katalogen till den nya mappen.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Ladda ned NuGet CLI](http://www.nuget.org/downloads) under mappen skapade i föregående steg. Det finns olika versioner av _nuget.exe_. Välj den version som motsvarar din SDK. Varje länk för hämtning pekar direkt till en _.exe_ fil. Se till att högerklicka på och spara filen på datorn i stället för att köra den från webbläsaren.

    Du kan också köra följande kommando för att hämta och lagra skriptet i samma mapp som du skapade tidigare.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Hämta beroende SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Ladda ned skriptet från GitHub exempelprojektet.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Kör skriptet. När du uppmanas att autentisera, anger du dina autentiseringsuppgifter för Azure. Det här skriptet bör utdata i en filtrerad lista över alla jobb på StorSimple-enheten.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Exempel på utdata

Följande utdata visas när exempelskriptet har körts. Utdata innehåller alla jobb som körts på en registrerad enhet som startat den 25 September 2017 och fyllas i av den 2 oktober 2017.

```
-----------------------------------------
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Scripts\StorSimpleSDKTools> wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1
PS C:\Scripts\StorSimpleSDKTools> .\Get-DeviceJobs.ps1 -SubscriptionId 1234ab5c-678d-910e-9fc4-0accc9c0166e -TenantId 12a345bc-67d8-91ef-01ab-2c7cd123ef45 -DeviceName 8600-ABC1234567D89EF -ResourceGroupName Contoso -ManagerName ContosoDeviceMgr -FilterByStartTime "09/25/2017 08:10:02" -FilterByEndTime "10/02/2017 08:10:02"


Status            : Succeeded
StartTime         : 10/2/2017 10:30:02 PM
EndTime           : 10/2/2017 10:31:36 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : ss-asr-policy1
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-SHG0997877L71FC/jobs/75905c48-b153-4af1-8b21-4b9a2ff9
                    825b
Name              : 75905c48-b153-4af1-8b21-4b9a2ff9825b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000
-------------------------------------------
CUT              CUT  
-------------------------------------------
Status            : Succeeded
StartTime         : 9/26/2017 5:00:02 PM
EndTime           : 9/26/2017 5:01:20 PM
PercentComplete   : 100
Error             :
JobType           : ScheduledBackup
DataStats         : Microsoft.Azure.Management.StorSimple8000Series.Models.DataStatistics
EntityLabel       : 8010 policy
EntityType        : Microsoft.StorSimple/managers/devices/backupPolicies
JobStages         :
DeviceId          : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF
IsCancellable     : True
BackupType        : CloudSnapshot
SourceDeviceId    :
BackupPointInTime : 1/1/0001 12:00:00 AM
Id                : /subscriptions/1234ab5c-678d-910e-9fc4-0accc9c0166e/resourceGroups/Contoso/providers/Microsoft.Stor
                    Simple/managers/ContosoDeviceMgr/devices/8600-ABC1234567D89EF/jobs/3cfd8108-db60-4e9a-a8da-6d8fe457
                    8d2b
Name              : 3cfd8108-db60-4e9a-a8da-6d8fe4578d2b
Type              : Microsoft.StorSimple/managers/devices/jobs
Kind              : Series8000



PS C:\Scripts\StorSimpleSDKTools>
--------------------------------------------

```


## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Device Manager-tjänsten för att hantera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).