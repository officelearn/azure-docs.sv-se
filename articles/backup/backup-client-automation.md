---
title: Säkerhetskopiera Windows Server till Azure med hjälp av PowerShell
description: Lär dig hur du distribuerar och hanterar Azure Backup med hjälp av PowerShell
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/24/2018
ms.author: pvrk
ms.openlocfilehash: c2f6d8262d47a537667ef7b25333a3beff425bbe
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58878697"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Distribuera och hantera säkerhetskopiering till Azure för Windows Server/Windows-klient med hjälp av PowerShell

Den här artikeln visar hur du använder PowerShell för att konfigurera Azure Backup i Windows Server eller en Windows-klient och hantera säkerhetskopiering och återställning.

## <a name="install-azure-powershell"></a>Installera Azure PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kommer igång [installera den senaste versionen av PowerShell](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Följande steg vägleder dig genom att skapa ett Recovery Services-valv. Recovery Services-valvet skiljer sig från ett säkerhetskopieringsvalv.

1. Om du använder Azure Backup för första gången, måste du använda den **registrera AzResourceProvider** cmdlet för att registrera Azure Recovery Services-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Recovery Services-valvet är en ARM-resurs, så du måste placera den i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en ny. När du skapar en ny resursgrupp måste du ange namn och plats för resursgruppen.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Använd den **New AzRecoveryServicesVault** cmdlet för att skapa det nya valvet. Glöm inte att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Ange vilken typ av lagringsredundans ska användas. Du kan använda [lokalt Redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [geografiskt Redundant lagring (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet - BackupStorageRedundancy för testVault anges till GeoRedundant.

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valv i en prenumeration

Använd **Get-AzRecoveryServicesVault** att visa en lista över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrollera att ett nytt valv har skapats eller för att se vilka valv är tillgängliga i prenumerationen.

Kör kommando, **Get-AzRecoveryServicesVault**, och alla valv i prenumerationen visas.

```powershell
Get-AzRecoveryServicesVault
```

```Output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```


[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="installing-the-azure-backup-agent"></a>Installera Azure Backup-agenten

Innan du installerar Azure Backup-agenten som du behöver ha installationsprogrammet hämtade och finns på Windows Server. Du kan hämta den senaste versionen av installationsprogrammet från den [Microsoft Download Center](https://aka.ms/azurebackup_agent) eller från Recovery Services-valvet instrumentpanelssidan. Spara installationsprogrammet i en lättillgänglig plats som * C:\Downloads\*.

Du kan också använda PowerShell för att hämta installationshämtaren:
 
 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Om du vill installera agenten, kör du följande kommando i en upphöjd PowerShell-konsol:

```powershell
MARSAgentInstaller.exe /q
```

Detta installerar agent med alla standardalternativ. Installationen tar några minuter i bakgrunden. Om du inte anger den */nu* alternativet kommer **Windows Update** fönster öppnas i slutet av installationen att söka efter uppdateringar. När du har installerat visas agenten i listan över installerade program.

Om du vill se listan över installerade program, gå till **Kontrollpanelen** > **program** > **program och funktioner**.

![Agenten har installerats](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsalternativ

Om du vill se alla alternativ som är tillgängliga via kommandoraden använder du följande kommando:

```powershell
MARSAgentInstaller.exe /?
```

De tillgängliga alternativen är:

| Alternativ | Information | Standard |
| --- | --- | --- |
| /q |Tyst installation |- |
| / p: ”plats” |Sökvägen till installationsmappen för Azure Backup-agenten. |C:\Program Files\Microsoft Azure Recovery Services-agenten |
| / s: ”plats” |Sökväg till cachemappen för Azure Backup-agenten. |C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Delta i Microsoft Update |- |
| /nu |Inte söka efter uppdateringar när installationen är klar |- |
| /d |Avinstallerar Microsoft Azure Recovery Services-agenten |- |
| /pH |Proxyadress för värd |- |
| /po |Portnummer för proxyservern värd |- |
| /Pu |Proxyanvändarnamnet för värd |- |
| /PW |Lösenord för proxy |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrera Windows Server eller Windows-klientdator till ett Recovery Services-valv

När du har skapat Recovery Services-valvet kan ladda ned den senaste agenten och autentiseringsuppgifterna för valvet och lagra den på en lämplig plats som C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

Kör på Windows Server eller Windows-klientdator, den [Start OBRegistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) cmdlet för att registrera datorn med valvet.
Det här och andra cmdletar som används för säkerhetskopiering, är från MSONLINE-modulen som Mars AgentInstaller har lagts till som en del av installationen. 

Installationsprogrammet för agenten inte uppdatera $Env: PSModulePath variabeln. Det innebär att modulen automatiskt, laddning misslyckas. För att lösa problemet kan du göra följande:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Du kan också du kan manuellt läsa in modulen i skriptet på följande sätt:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

När du läser in Online Backup-cmdletar kan du registrera autentiseringsuppgifterna för valvet:

```powershell
Start-OBRegistration -VaultCredentials $CredsFilename.FilePath -Confirm:$false
```

```Output
CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : testvault
Region              : WestUS
Machine registration succeeded.
```

> [!IMPORTANT]
> Använd inte relativa sökvägar för att ange valvautentiseringsfilen. Du måste ange en absolut sökväg som indata till cmdleten.
>
>

## <a name="networking-settings"></a>Nätverksinställningar

När anslutningen för Windows-dator till internet via en proxyserver kan proxyinställningarna också anges till agenten. I det här exemplet finns ingen proxyserver så att vi uttryckligen Rensa alla proxy-relaterad information.

Användning av nätverksbandbredd kan även kontrolleras med alternativ för `work hour bandwidth` och `non-work hour bandwidth` för en given uppsättning dagar i veckan.

Inställningsinformation för proxy och bandbredd görs med hjälp av den [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) cmdlet:

```powershell
Set-OBMachineSetting -NoProxy
```

```Output
Server properties updated successfully.
```

```powershell
Set-OBMachineSetting -NoThrottle
```

```Output
Server properties updated successfully.
```

## <a name="encryption-settings"></a>Krypteringsinställningar

Säkerhetskopierade data skickas till Azure Backup krypteras för att skydda sekretessen för data. Krypteringslösenfrasen är ”password” att dekryptera data vid tidpunkten för återställningen.

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Spara lösenfrasen informationen säker när den har angetts. Du kan inte återställa data från Azure utan lösenfrasen.
>
>

## <a name="back-up-files-and-folders"></a>Säkerhetskopiera filer och mappar

Alla säkerhetskopior från Windows-servrar och klienter till Azure Backup regleras av en princip. Principen består av tre delar:

1. En **Säkerhetskopieringsschemat** som anger när säkerhetskopieringar ska tas och synkroniserat med tjänsten.
2. En **bevarandeschema** som anger hur lång tid att behålla återställningspunkter i Azure.
3. En **filen inkludering/exkludering specifikationen** som avgör vad ska säkerhetskopieras.

I det här dokumentet eftersom vi automatisera säkerhetskopiering, vi antar ingenting har konfigurerats. Vi börjar med att skapa en ny princip för säkerhetskopiering med den [New OBPolicy](https://technet.microsoft.com/library/hh770416.aspx) cmdlet.

```powershell
$NewPolicy = New-OBPolicy
```

Just nu principen är tom och andra cmdletar är behövs för att definiera vilka objekt som kommer att inkluderade eller exkluderade, när säkerhetskopieringar ska köras, och där säkerhetskopiorna lagras.

### <a name="configuring-the-backup-schedule"></a>Konfigurera schemat för säkerhetskopiering

Först av 3 delar av en princip är schemat för säkerhetskopiering, som skapas med hjälp av den [New OBSchedule](https://technet.microsoft.com/library/hh770401) cmdlet. Schemat för säkerhetskopiering definierar när säkerhetskopieringar ska vidtas. När du skapar ett schema måste du ange 2 indataparametrar:

* **Dagar i veckan** som säkerhetskopieringen ska köras. Du kan köra säkerhetskopieringsjobb på bara en dag eller varje dag i veckan eller en kombination däremellan.
* **Tider på dagen** när säkerhetskopieringen ska köras. Du kan definiera upp till 3 olika tidpunkter på dagen när säkerhetskopieringen utlöses.

Du kan till exempel konfigurera en princip för säkerhetskopiering som körs klockan 4 varje lördag och söndag.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Schemat för säkerhetskopiering måste vara associerad med en princip och detta kan uppnås med hjälp av den [Set-OBSchedule](https://technet.microsoft.com/library/hh770407) cmdlet.

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### <a name="configuring-a-retention-policy"></a>Konfigurera en bevarandeprincip

Bevarandeprincipen anger hur länge återställningspunkter som skapats från säkerhetskopieringsjobb ska bevaras. När du skapar en ny bevarandeprincip genom den [New OBRetentionPolicy](https://technet.microsoft.com/library/hh770425) kan du ange hur många dagar som återställningspunkter för säkerhetskopiering måste sparas med Azure Backup. Exemplet nedan anger en bevarandeprincip på 7 dagar.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Bevarandeprincipen måste associeras med den huvudsakliga principen med hjälp av cmdleten [Set-OBRetentionPolicy](https://technet.microsoft.com/library/hh770405):

```powershell
Set-OBRetentionPolicy -Policy $NewPolicy -RetentionPolicy $RetentionPolicy
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### <a name="including-and-excluding-files-to-be-backed-up"></a>Inkludera och exkludera filer som ska säkerhetskopieras

En `OBFileSpec` objektet definierar filerna som ska inkluderas och exkluderas i en säkerhetskopia. Det här är en uppsättning regler som omfång för skyddade filer och mappar på en dator. Du kan använda så många filen inkluderingsgrupp eller undantagsgrupp regler som krävs och koppla dem till en princip. När du skapar ett nytt OBFileSpec-objekt, kan du:

* Ange vilka filer och mappar som ska tas med
* Ange vilka filer och mappar som ska undantas
* Ange rekursiv säkerhetskopiering av data i en mapp (eller) om endast de översta filerna i den angivna mappen bör säkerhetskopieras upp.

Det senare uppnås med hjälp av flaggan - rekursiv i kommandot New-OBFileSpec.

I exemplet nedan är vi säkerhetskopiera volym C: och D: och undanta OS-binärfiler i Windows-mappen och alla temporära mappar. Du vill göra det vi ska skapa två filen anvisningar med hjälp av den [New OBFileSpec](https://technet.microsoft.com/library/hh770408) cmdleten: en för inkludering och en för undantag. När filen specifikationer har skapats, de är associerade med en princip med hjälp av den [Lägg till OBFileSpec](https://technet.microsoft.com/library/hh770424) cmdlet.

```powershell
$Inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")
$Exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Inclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

```powershell
Add-OBFileSpec -Policy $NewPolicy -FileSpec $Exclusions
```

```Output
BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### <a name="applying-the-policy"></a>Tillämpa principen

Nu principobjektet är klar och har en associerad Säkerhetskopieringsschemat, bevarandeprincip och en lista för inkludering/exkludering av filer. Den här principen kan nu bekräftas för Azure Backup ska använda. Innan du installerar den nya principen se till att det finns inga befintliga säkerhetskopieringsprinciper som är kopplad till servern med hjälp av den [Remove-OBPolicy](https://technet.microsoft.com/library/hh770415) cmdlet. Ta bort principen uppmanas bekräfta. Hoppa över bekräftelse-användning i `-Confirm:$false` flaggan med cmdlet: en.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Genomför principobjektet görs med hjälp av den [Set-OBPolicy](https://technet.microsoft.com/library/hh770421) cmdlet. Detta kommer också ombeds bekräfta. Hoppa över bekräftelse-användning i `-Confirm:$false` flaggan med cmdlet: en.

```powershell
Set-OBPolicy -Policy $NewPolicy
```

```Output
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

Du kan visa information om en befintlig princip för säkerhetskopiering med hjälp av den [Get-OBPolicy](https://technet.microsoft.com/library/hh770406) cmdlet. Du kan öka detaljnivån ytterligare med den [Get-OBSchedule](https://technet.microsoft.com/library/hh770423) för schemat för säkerhetskopiering och [Get-OBRetentionPolicy](https://technet.microsoft.com/library/hh770427) cmdlet för att principerna för kvarhållning

```powershell
Get-OBPolicy | Get-OBSchedule
```

```Output
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing
```

```powershell
Get-OBPolicy | Get-OBRetentionPolicy
```

```Output
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing
```

```powershell
Get-OBPolicy | Get-OBFileSpec
```

```Output
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### <a name="performing-an-ad-hoc-backup"></a>Utför en ad hoc-säkerhetskopiering

När du har angett en princip för säkerhetskopiering sker säkerhetskopieringar per schemat. Aktiverar en ad hoc-säkerhetskopiering är också möjligt använder den [Start OBBackup](https://technet.microsoft.com/library/hh770426) cmdlet:

```powershell
Get-OBPolicy | Start-OBBackup
```

```Output
Initializing
Taking snapshot of volumes...
Preparing storage...
Generating backup metadata information and preparing the metadata VHD...
Data transfer is in progress. It might take longer since it is the first backup and all data needs to be transferred...
Data transfer completed and all backed up data is in the cloud. Verifying data integrity...
Data transfer completed
In progress...
Job completed.
The backup operation completed successfully.
```

## <a name="restore-data-from-azure-backup"></a>Återställa data från Azure Backup

Det här avsnittet vägleder dig genom stegen för att automatisera återställning av data från Azure Backup. Detta omfattar följande steg:

1. Välj källvolymen
2. Välj en säkerhetskopieringspunkt att återställa
3. Välj ett objekt att återställa
4. Utlös återställningen

### <a name="picking-the-source-volume"></a>Välja källvolymen

Om du vill återställa ett objekt från Azure Backup, måste du först identifiera källan till objektet. Eftersom vi kör kommandona i kontexten för en Windows Server eller en Windows-klient, identifieras redan datorn. Nästa steg i att identifiera källan är att identifiera volymen som innehåller den. En lista över volymer eller källor som säkerhetskopieras från den här datorn kan hämtas genom att köra den [Get-OBRecoverableSource](https://technet.microsoft.com/library/hh770410) cmdlet. Det här kommandot returnerar en matris med alla källor som säkerhetskopieras från den här servern/klienten.

```powershell
$Source = Get-OBRecoverableSource
$Source
```

```Output
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Välja en säkerhetskopieringspunkt för återställning

Du kan hämta en lista över säkerhetskopieringspunkter genom att köra den [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) cmdleten med lämpliga parametrar. I vårt exempel väljer den senaste säkerhetskopieringspunkten för källvolymen *D:* och använda den för att återställa en specifik fil.

```powershell
$Rps = Get-OBRecoverableItem -Source $Source[1]
```

```Output
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```

Objektet `$Rps` är en matris med säkerhetskopieringspunkter. Det första elementet är den senaste tidpunkten och n: te elementet är den äldsta. Vi använder för att välja den senaste tidpunkten `$Rps[0]`.

### <a name="choosing-an-item-to-restore"></a>Välja ett objekt att återställa

Att identifiera den exakta filen eller mappen att återställa rekursivt användning den [Get-OBRecoverableItem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet. På så sätt mapphierarkin kan sökas enbart med hjälp av den `Get-OBRecoverableItem`.

I det här exemplet, om vi vill att återställa filen *finances.xls* vi refererar till som använder objektet `$FilesFolders[1]`.

```powershell
$FilesFolders = Get-OBRecoverableItem $Rps[0]
$FilesFolders
```

```Output
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM
```

```powershell
$FilesFolders = Get-OBRecoverableItem $FilesFolders[0]
$FilesFolders
```

```Output
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

Du kan också söka efter objekt som kan återställa med hjälp av den ```Get-OBRecoverableItem``` cmdlet. I vårt exempel att söka efter *finances.xls* vi kunde få grepp om filen genom att köra det här kommandot:

```powershell
$Item = Get-OBRecoverableItem -RecoveryPoint $Rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### <a name="triggering-the-restore-process"></a>Utlösa återställningsprocessen

För att utlösa återställningen måste vi först ange återställningsalternativ. Detta kan göras med hjälp av den [New OBRecoveryOption](https://technet.microsoft.com/library/hh770417.aspx) cmdlet. I det här exemplet antar vi att vi vill återställa filer till *C:\temp*. Vi antar också att vi vill hoppa över filer som redan finns i målmappen *C:\temp*. Om du vill skapa ett återställningsalternativ, använder du följande kommando:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Utlös nu återställningsprocessen genom att använda den [Start OBRecovery](https://technet.microsoft.com/library/hh770402.aspx) kommandot på den valda `$Item` från utdata från den `Get-OBRecoverableItem` cmdlet:

```powershell
Start-OBRecovery -RecoverableItem $Item -RecoveryOption $RecoveryOption
```

```Output
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```

## <a name="uninstalling-the-azure-backup-agent"></a>Avinstallera Azure Backup-agenten

Avinstallera Azure Backup-agenten kan göras med hjälp av följande kommando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Avinstallera agent-binärfiler från datorn påverkar vissa att tänka på:

* Fil-filter tas bort från datorn och spårning av ändringar stoppas.
* All principinformation tas bort från datorn men principinformation fortsätter att lagras i tjänsten.
* Alla säkerhetskopieringsscheman tas bort och inga ytterligare säkerhetskopieringar kommer.

Data lagras i Azure finns kvar och sparas enligt konfigurationen av principer för kvarhållning av dig. Äldre återställningspunkter rensas automatiskt bort.

## <a name="remote-management"></a>Fjärrhantering

All hantering kring Azure Backup-agenten, principer och datakällor kan göras via en fjärranslutning via PowerShell. Den dator som ska hanteras via en fjärranslutning måste förberedas på rätt sätt.

WinRM-tjänsten har konfigurerats för manuell start som standard. Starttypen måste anges till *automatisk* och tjänsten ska startas. Om du vill kontrollera att WinRM-tjänsten körs, värdet för egenskapen Status ska vara *kör*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell ska konfigureras för fjärrkörning.

```powershell
Enable-PSRemoting -Force
```

```Output
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.
```

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force
```

Datorn kan nu hanteras via en fjärranslutning - från och med installationen av agenten. Följande skript kopierar agenten till fjärrdatorn och installerar den.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup för Windows Server /-klienten finns i

* [Introduktion till Azure Backup](backup-introduction-to-azure-backup.md)
* [Säkerhetskopiera Windows-servrar](backup-configure-vault.md)
