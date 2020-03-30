---
title: Använda AzureRM PowerShell-skript för att hantera StorSimple-enheter
description: Lär dig hur du använder Azure Resource Manager-skript för att automatisera StorSimple-jobb
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 10/03/2017
ms.author: alkohli
ms.openlocfilehash: 03a5ef49b2d58d351d882b30b5d11e4a5ba90264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471966"
---
# <a name="use-azure-resource-manager-sdk-based-scripts-to-manage-storsimple-devices"></a>Använda SDK-baserade Azure Resource Manager-skript för att hantera StorSimple-enheter

I den här artikeln beskrivs hur SDK-baserade Skript i Azure Resource Manager kan användas för att hantera din StorSimple 8000-serieenhet. Ett exempelskript ingår också för att gå igenom stegen för att konfigurera din miljö för att köra dessa skript.

Den här artikeln gäller endast StorSimple 8000-seriens enheter som körs i Azure-portalen.

## <a name="sample-scripts"></a>Exempelskript

Följande exempelskript finns tillgängliga för att automatisera olika StorSimple-jobb.

#### <a name="table-of-azure-resource-manager-sdk-based-sample-scripts"></a>Tabell över SDK-baserade exempelskript för Azure Resource Manager

| Skript för Azure Resource Manager                    | Beskrivning                                                                                                                                                                                                       |
|--------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Auktorisera-ServiceEncryptionRollover.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Authorize-ServiceEncryptionRollover.ps1)          | Med det här skriptet kan du auktorisera din StorSimple-enhet för att ändra krypteringsnyckeln för tjänstdata.                                                                                                           |
| [Skapa-StorSimpleCloudAppliance.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Create-StorSimpleCloudAppliance.ps1)              | Det här skriptet skapar en 8010 eller en 8020 StorSimple Cloud Appliance. Molninstallationen kan sedan konfigureras och registreras med tjänsten StorSimple Data Manager.                                                       |
| [CreateOrUpdate-Volume.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/CreateOrUpdate-Volume.ps1)                        | Det här skriptet skapar eller ändrar StorSimple-volymer.                                                                                                                                                             |
| [Hämta-enhetBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackup.ps1)                             | Det här skriptet visar alla säkerhetskopior för en enhet som är registrerad med tjänsten StorSimple Device Manager.                                                                                                          |
| [Hämta-enhetBackupPolicy.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceBackupPolicy.ps1)                       | Det här skriptet alla principer för säkerhetskopiering för din StorSimple-enhet.                                                                                                                                                 |
| [Hämta-enhetJobs.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1)                               | Det här skriptet får alla StorSimple-jobb som körs på tjänsten StorSimple Device Manager.                                                                                                                     |
| [Hämta-deviceUpdateAvailability.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceUpdateAvailability.ps1)                 | Det här skriptet söker igenom uppdateringsservern och visar om det finns uppdateringar att installera på Din StorSimple-enhet.                                                                                          |
| [Installera-deviceUpdate.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Install-DeviceUpdate.ps1)                         | Det här skriptet installerar tillgängliga uppdateringar på din StorSimple-enhet.                                                                                                                                           |
| [Hantera-CloudSnapshots.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Manage-CloudSnapshots.ps1)                        | Det här skriptet startar en manuell ögonblicksbild av molnet och tar bort ögonblicksbilder av molnet som är äldre än angivna kvarhållningsdagar.                                                                                                   |
| [Bildskärm-säkerhetskopior.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1)                              | Det här Azure Automation Runbook PowerShell-skriptet rapporterar status för alla säkerhetskopieringsjobb.                                                                                                              |
| [Ta bort-DeviceBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Remove-DeviceBackup.ps1)                          | Det här skriptet tar bort ett enda säkerhetskopieringsobjekt.                                                                                                                                                           |
| [Start-DeviceBackupJob.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Start-DeviceBackupJob.ps1)                        | Det här skriptet startar en manuell säkerhetskopiering på Din StorSimple-enhet.                                                                                                                                       |
| [Uppdatering-CloudApplianceServiceEncryptionKey.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Update-CloudApplianceServiceEncryptionKey.ps1)    | Det här skriptet uppdaterar krypteringsnyckeln för tjänstdata för alla 8010/8020 StorSimple Cloud Appliances som är registrerade hos tjänsten StorSimple Device Manager.                                     |
| [Verifiera-säkerhetskopieringScheduleAndBackup.ps1](https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Verify-BackupScheduleAndBackup.ps1)               | Det här skriptet belyser de saknade säkerhetskopiorna efter att ha analyserat alla scheman som är associerade med principer för säkerhetskopiering. Den verifierar också säkerhetskopieringskatalogen med listan över tillgängliga säkerhetskopior.             |




## <a name="configure-and-run-a-sample-script"></a>Konfigurera och köra ett exempelskript

Det här avsnittet tar ett exempel skript och detaljer de olika steg som krävs för att köra skriptet.

### <a name="prerequisites"></a>Krav

Innan du börjar, se till att du har:

*   Azure PowerShell installerat. Så här installerar du Azure PowerShell-moduler:
    * I en Windows-miljö följer du stegen i [Installera och konfigurerar Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Du kan installera Azure PowerShell på din Windows Server-värd för din StorSimple om du använder en.
    * I en Linux- eller MacOS-miljö följer du stegen i [Installera och konfigurera Azure PowerShell på MacOS eller Linux](https://docs.microsoft.com/powershell/azure/azurerm/install-azurermps-maclinux).

Mer information om hur du använder Azure PowerShell finns i [Komma igång med att använda Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="run-azure-powershell-script"></a>Kör Azure PowerShell-skript

Skriptet som används i det här exemplet visar alla jobb på en StorSimple-enhet. Detta inkluderar de jobb som lyckades, misslyckades eller pågår. Utför följande steg för att hämta och köra skriptet.

1. Starta Azure PowerShell. Skapa en ny mapp och ändra katalogen till den nya mappen.

    ```
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```    
2. [Ladda ner NuGet CLI](https://www.nuget.org/downloads) under mappen som skapades i föregående steg. Det finns olika versioner av _nuget.exe_. Välj den version som motsvarar din SDK. Varje nedladdningslänk pekar direkt på en _EXE-fil._ Var noga med att högerklicka och spara filen på datorn i stället för att köra den från webbläsaren.

    Du kan också köra följande kommando för att hämta och lagra skriptet i samma mapp som du skapade tidigare.
    
    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```
3. Ladda ner den beroende SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```    
4. Hämta skriptet från GitHub-projektet.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Get-DeviceJobs.ps1 -Out Get-DeviceJobs.ps1

    ```

5. Kör skriptet. Ange dina Azure-autentiseringsuppgifter när du uppmanas att autentisera. Det här skriptet ska mata ut en filtrerad lista över alla jobb på StorSimple-enheten.
           
    ```           
        .\Get-StorSimpleJob.ps1 -SubscriptionId [subid] -TenantId [tenant id] -DeviceName [name of device] -ResourceGroupName [name of resource group] -ManagerName[name of device manager] -FilterByStatus [Filter for job status] -FilterByJobType [Filter for job type] -FilterByStartTime [Filter for start date time] -FilterByEndTime [Filter for end date time]

    ```

### <a name="sample-output"></a>Exempel på utdata

Följande utdata visas när exempelskriptet körs. Utdata innehåller alla jobb som kördes på en registrerad enhet som startade den 25 september 2017 och avslutades den 2 oktober 2017.

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
