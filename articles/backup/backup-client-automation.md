---
title: Använda PowerShell för att säkerhetskopiera Windows Server till Azure
description: I den här artikeln kan du läsa om hur du använder PowerShell för att konfigurera Azure Backup på Windows Server eller en Windows-klient och hantera säkerhetskopiering och återställning.
ms.topic: conceptual
ms.date: 12/2/2019
ms.openlocfilehash: efe0b93fe1e37990422ffbd2256e38c12401dca5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673203"
---
# <a name="deploy-and-manage-backup-to-azure-for-windows-serverwindows-client-using-powershell"></a>Distribuera och hantera säkerhetskopiering till Azure för Windows Server/Windows-klient med hjälp av PowerShell

I den här artikeln visas hur du använder PowerShell för att konfigurera Azure Backup på Windows Server eller en Windows-klient och hantera säkerhetskopiering och återställning.

## <a name="install-azure-powershell"></a>Installera Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

För att komma igång [installerar du den senaste PowerShell-versionen](/powershell/azure/install-az-ps).

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Följande steg leder dig genom att skapa ett Recovery Services-valv. Ett Recovery Services-valv skiljer sig från ett valv för säkerhetskopiering.

1. Om du använder Azure Backup för första gången måste du använda cmdleten **Register-AzResourceProvider** för att registrera Azure Recovery Service-providern med din prenumeration.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

2. Valvet för återställningstjänster är en ARM-resurs, så du måste placera det i en resursgrupp. Du kan använda en befintlig resursgrupp eller skapa en ny. När du skapar en ny resursgrupp anger du namn och plats för resursgruppen.  

    ```powershell
    New-AzResourceGroup –Name "test-rg" –Location "WestUS"
    ```

3. Använd cmdleten **New-AzRecoveryServicesVault** för att skapa det nya valvet. Var noga med att ange samma plats för valvet som användes för resursgruppen.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName " test-rg" -Location "WestUS"
    ```

4. Ange vilken typ av lagringsredundans som ska användas. Du kan använda [Lokalt redundant lagring (LRS)](../storage/common/storage-redundancy-lrs.md) eller [GEO Redundant Storage (GRS)](../storage/common/storage-redundancy-grs.md). I följande exempel visas alternativet -BackupStorageRedundancy för testVault är inställt på GeoRedundant.

   > [!TIP]
   > Många Azure Backup-cmdletar kräver Recovery Services-valvobjekt som indata. Därför är det praktiskt att lagra säkerhetskopians Recovery Services-valvobjekt i en variabel.
   >
   >

    ```powershell
    $Vault1 = Get-AzRecoveryServicesVault –Name "testVault"
    Set-AzRecoveryServicesBackupProperties -Vault $Vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>Visa valven i en prenumeration

Använd **Get-AzRecoveryServicesVault** om du vill visa listan över alla valv i den aktuella prenumerationen. Du kan använda det här kommandot för att kontrollera att ett nytt valv har skapats eller för att se vilka valv som är tillgängliga i prenumerationen.

Kör kommandot **Get-AzRecoveryServicesVault**och alla valv i prenumerationen visas.

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

Innan du installerar Azure Backup-agenten måste du ha installerat om det finns en installationsprogram på Windows Server. Du kan hämta den senaste versionen av installationsprogrammet från [Microsoft Download Center](https://aka.ms/azurebackup_agent) eller från recovery services-valvets instrumentpanelssida. Spara installationsprogrammet på en lättillgänglig plats som *C:\Downloads\*.

Du kan också använda PowerShell för att hämta hämtningsaren:

 ```powershell
 $MarsAURL = 'https://aka.ms/Azurebackup_Agent'
 $WC = New-Object System.Net.WebClient
 $WC.DownloadFile($MarsAURL,'C:\downloads\MARSAgentInstaller.EXE')
 C:\Downloads\MARSAgentInstaller.EXE /q
 ```

Om du vill installera agenten kör du följande kommando i en upphöjd PowerShell-konsol:

```powershell
MARSAgentInstaller.exe /q
```

Detta installerar agenten med alla standardalternativ. Installationen tar några minuter i bakgrunden. Om du inte anger alternativet */nu* öppnas fönstret **Windows Update** i slutet av installationen för att söka efter uppdateringar. En gång installerat, kommer agenten att visas i listan över installerade program.

Om du vill se listan över installerade program går du till Program**Programs** > **och funktioner på** **Kontrollpanelen** > .

![Agent installerad](./media/backup-client-automation/installed-agent-listing.png)

### <a name="installation-options"></a>Installationsalternativ

Om du vill visa alla tillgängliga alternativ via kommandoraden använder du följande kommando:

```powershell
MARSAgentInstaller.exe /?
```

De tillgängliga alternativen är:

| Alternativ | Information | Default |
| --- | --- | --- |
| /q |Tyst installation |- |
| /p:"plats" |Sökväg till installationsmappen för Azure Backup-agenten. |C:\Program\Microsoft Azure Recovery Services Agent |
| /s:"plats" |Sökväg till cachemappen för Azure Backup-agenten. |C:\Program\Microsoft Azure Recovery Services Agent\Scratch |
| /m |Anmäl dig till Microsoft Update |- |
| /nu |Sök inte efter uppdateringar när installationen är klar |- |
| /d |Avinstallerar Microsoft Azure Recovery Services Agent |- |
| /ph (ph) |Proxyvärdadress |- |
| /po (po) |Portnummer för proxyvärdport |- |
| /pu |Användarnamn för proxyvärd |- |
| /pw (pw) |Proxy lösenord |- |

## <a name="registering-windows-server-or-windows-client-machine-to-a-recovery-services-vault"></a>Registrera Windows Server- eller Windows-klientdator i ett Recovery Services Vault

När du har skapat valvet för Återställningstjänster hämtar du den senaste agenten och autentiseringsuppgifterna för valvet och lagrar det på ett bekvämt ställe som C:\Downloads.

```powershell
$CredsPath = "C:\downloads"
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault1 -Path $CredsPath
```

### <a name="registering-using-the-ps-az-module"></a>Registrera dig med PS Az-modulen

> [!NOTE]
> En bugg med generering av valvcertifikat är fast i Az 3.5.0 release. Använd Az 3.5.0 release version eller mer för att ladda ner ett valv certifikat.

I den senaste Az-modulen i PowerShell, på grund av underliggande plattformsbegränsningar, kräver nedladdning av valvautentiseringsuppgifterna ett självsignerat certifikat. I följande exempel visas hur du tillhandahåller ett självsignerat certifikat och hämtar autentiseringsuppgifterna för valvet.

```powershell
$dt = $(Get-Date).ToString("M-d-yyyy")
$cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -FriendlyName 'test-vaultcredentials' -subject "Windows Azure Tools" -KeyExportPolicy Exportable -NotAfter $(Get-Date).AddHours(48) -NotBefore $(Get-Date).AddHours(-24) -KeyProtection None -KeyUsage None -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2") -Provider "Microsoft Enhanced Cryptographic Provider v1.0"
$certficate = [convert]::ToBase64String($cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx))
$CredsFilename = Get-AzRecoveryServicesVaultSettingsFile -Backup -Vault $Vault -Path $CredsPath -Certificate $certficate
```

På Windows Server- eller Windows-klientdatorn kör du cmdleten [Start-OBRegistration](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obregistration?view=winserver2012-ps) för att registrera datorn med valvet.
Detta, och andra cmdlets som används för säkerhetskopiering, kommer från MSONLINE-modulen, som Mars AgentInstaller lade till som en del av installationsprocessen.

Agentinstallationsprogrammet uppdaterar inte variabeln $Env:PSModulePath. Det innebär att automatisk belastning för modulen misslyckas. Lös detta kan du göra följande:

```powershell
$Env:PSModulePath += ';C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules'
```

Du kan också läsa in modulen manuellt på följande sätt:

```powershell
Import-Module -Name 'C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup'
```

När du har laddat cmdlets onlinebackup registrerar du autentiseringsuppgifterna för valvet:

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
> Använd inte relativa sökvägar för att ange autentiseringsfilen för valvet. Du måste ange en absolut sökväg som indata till cmdleten.
>
>

## <a name="networking-settings"></a>Inställningar för nätverk

När anslutningsmöjligheten för Windows-datorn till Internet är via en proxyserver kan proxyinställningarna också tillhandahållas agenten. I det här exemplet finns det ingen proxyserver, så vi rensar uttryckligen all proxyrelaterad information.

Bandbreddsanvändning kan också styras `work hour bandwidth` `non-work hour bandwidth` med alternativen för och för en viss uppsättning dagar i veckan.

Ställa in proxy- och bandbreddsinformationen görs med hjälp av cmdleten [Set-OBMachineSetting:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obmachinesetting?view=winserver2012-ps)

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

Säkerhetskopieringsdata som skickas till Azure Backup krypteras för att skydda sekretessen för data. Krypteringslösenfrasen är "lösenordet" för att dekryptera data vid återställningen.

Du måste generera en säkerhetsnål genom att välja **Generera**under **Inställningar** > **Egenskaper** > **Security PIN i** recovery **services-valvavsnittet** i Azure-portalen. Använd sedan detta `generatedPIN` som kommandot i:

```powershell
$PassPhrase = ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force
Set-OBMachineSetting -EncryptionPassPhrase $PassPhrase -SecurityPin "<generatedPIN>"
```

```Output
Server properties updated successfully
```

> [!IMPORTANT]
> Skydda lösenfrasinformationen på ett säkert och säkert sätt när den är inställd. Du kan inte återställa data från Azure utan den här lösenfrasen.
>
>

## <a name="back-up-files-and-folders"></a>Säkerhetskopiera filer och mappar

Alla säkerhetskopior från Windows-servrar och klienter till Azure Backup styrs av en princip. Policyn består av tre delar:

1. Ett **säkerhetskopieringsschema** som anger när säkerhetskopior måste tas och synkroniseras med tjänsten.
2. Ett **bevarandeschema** som anger hur länge återställningspunkterna ska behållas i Azure.
3. En **specifikation för inkludering/undantag** för filer som bestämmer vad som ska säkerhetskopieras.

I det här dokumentet, eftersom vi automatiserar säkerhetskopiering, antar vi att inget har konfigurerats. Vi börjar med att skapa en ny princip för säkerhetskopiering med cmdleten [New-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obpolicy?view=winserver2012-ps)

```powershell
$NewPolicy = New-OBPolicy
```

För närvarande är principen tom och andra cmdlets behövs för att definiera vilka objekt som ska inkluderas eller uteslutas, när säkerhetskopior körs och var säkerhetskopiorna ska lagras.

### <a name="configuring-the-backup-schedule"></a>Konfigurera säkerhetskopieringsschemat

Den första av de tre delarna av en princip är säkerhetskopieringsschemat, som skapas med cmdleten [New-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obschedule?view=winserver2012-ps) Schemat för säkerhetskopiering definierar när säkerhetskopieringar måste göras. När du skapar ett schema måste du ange två indataparametrar:

* **Dagar i veckan** som säkerhetskopian ska köras. Du kan köra säkerhetskopieringsjobbet på bara en dag, eller alla dagar i veckan, eller en kombination däremellan.
* **Tider på dagen** när säkerhetskopieringen ska köras. Du kan definiera upp till tre olika tider på dagen när säkerhetskopian ska utlösas.

Du kan till exempel konfigurera en princip för säkerhetskopiering som körs klockan 16:00 varje lördag och söndag.

```powershell
$Schedule = New-OBSchedule -DaysOfWeek Saturday, Sunday -TimesOfDay 16:00
```

Säkerhetskopieringsschemat måste associeras med en princip, och detta kan uppnås med hjälp av cmdleten [Set-OBSchedule.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obschedule?view=winserver2012-ps)

```powershell
Set-OBSchedule -Policy $NewPolicy -Schedule $Schedule
```

```Output
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```

### <a name="configuring-a-retention-policy"></a>Konfigurera en bevarandeprincip

Bevarandeprincipen definierar hur länge återställningspunkter som skapas från säkerhetskopieringsjobb behålls. När du skapar en ny bevarandeprincip med cmdleten [New-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obretentionpolicy?view=winserver2012-ps) kan du ange hur många dagar återställningspunkterna för säkerhetskopiering måste behållas med Azure Backup. Exemplet nedan anger en bevarandeprincip på sju dagar.

```powershell
$RetentionPolicy = New-OBRetentionPolicy -RetentionDays 7
```

Bevarandeprincipen måste associeras med huvudprincipen med hjälp av cmdlet [Set-OBRetentionPolicy:](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obretentionpolicy?view=winserver2012-ps)

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

### <a name="including-and-excluding-files-to-be-backed-up"></a>Inkludera och utesluta filer som ska säkerhetskopieras

Ett `OBFileSpec` objekt definierar de filer som ska inkluderas och uteslutas i en säkerhetskopia. Detta är en uppsättning regler som omfattar skyddade filer och mappar på en dator. Du kan ha så många filinkluder eller undantagsregler som krävs och associera dem med en princip. När du skapar ett nytt OBFileSpec-objekt kan du:

* Ange vilka filer och mappar som ska ingå
* Ange vilka filer och mappar som ska uteslutas
* Ange rekursiv säkerhetskopiering av data i en mapp (eller) om endast de översta filerna i den angivna mappen ska säkerhetskopieras.

Det senare uppnås med flaggan -NonRecursive i kommandot New-OBFileSpec.

I exemplet nedan säkerhetskopierar vi volym C: och D: och utesluter os-binärfilerna i Windows-mappen och eventuella tillfälliga mappar. För att göra det skapar vi två filspecifikationer med hjälp av [cmdleten New-OBFileSpec](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obfilespec?view=winserver2012-ps) - en för inkludering och en för uteslutning. När filspecifikationerna har skapats associeras de med principen med cmdleten [Add-OBFileSpec.](https://docs.microsoft.com/powershell/module/msonlinebackup/add-obfilespec?view=winserver2012-ps)

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

### <a name="applying-the-policy"></a>Tillämpning av policyn

Nu är principobjektet slutfört och har ett associerat schema för säkerhetskopiering, bevarandeprincip och en lista över filer med inneslutning/uteslutning. Den här principen kan nu bekräftas för Azure Backup att använda. Innan du installerar den nyligen skapade principen ska du se till att det inte finns några befintliga principer för säkerhetskopiering som är associerade med servern med hjälp av cmdleten [Remove-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/remove-obpolicy?view=winserver2012-ps) Om du tar bort principen uppmanas du att bekräfta. Om du vill hoppa `-Confirm:$false` över bekräftelsen använder du flaggan med cmdleten.

```powershell
Get-OBPolicy | Remove-OBPolicy
```

```Output
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

Att genomföra principobjektet görs med cmdleten [Set-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/set-obpolicy?view=winserver2012-ps) Detta kommer också att be om bekräftelse. Om du vill hoppa `-Confirm:$false` över bekräftelsen använder du flaggan med cmdleten.

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

Du kan visa information om den befintliga säkerhetskopieringsprincipen med hjälp av cmdleten [Get-OBPolicy.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obpolicy?view=winserver2012-ps) Du kan öka detaljnivån ytterligare med [get-OBSchedule-cmdleten](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obschedule?view=winserver2012-ps) för säkerhetskopieringsschemat och cmdleten [Get-OBRetentionPolicy](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obretentionpolicy?view=winserver2012-ps) för bevarandeprinciperna

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

### <a name="performing-an-on-demand-backup"></a>Utföra en säkerhetskopiering på begäran

När en princip för säkerhetskopiering har ställts in sker säkerhetskopieringarna enligt schemat. Det är också möjligt att utlösa en säkerhetskopiering på begäran med [start-OBBackup-cmdlet:](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obbackup?view=winserver2012-ps)

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

## <a name="back-up-windows-server-system-state-in-mabs-agent"></a>Säkerhetskopiera Windows Server System-tillstånd i MABS-agent

Det här avsnittet täcker PowerShell-kommandot för att konfigurera systemtillstånd i MABS-agent

### <a name="schedule"></a>Schema

```powershell
$sched = New-OBSchedule -DaysOfWeek Sunday,Monday,Tuesday,Wednesday,Thursday,Friday,Saturday -TimesOfDay 2:00
```

### <a name="retention"></a>Kvarhållning

```powershell
$rtn = New-OBRetentionPolicy -RetentionDays 32 -RetentionWeeklyPolicy -RetentionWeeks 13 -WeekDaysOfWeek Sunday -WeekTimesOfDay 2:00  -RetentionMonthlyPolicy -RetentionMonths 13 -MonthDaysOfMonth 1 -MonthTimesOfDay 2:00
```

### <a name="configuring-schedule-and-retention"></a>Konfigurera schema och kvarhållning

```powershell
New-OBPolicy | Add-OBSystemState |  Set-OBRetentionPolicy -RetentionPolicy $rtn | Set-OBSchedule -Schedule $sched | Set-OBSystemStatePolicy
 ```

### <a name="verifying-the-policy"></a>Verifiera principen

```powershell
Get-OBSystemStatePolicy
 ```

## <a name="restore-data-from-azure-backup"></a>Återställa data från Azure Backup

Det här avsnittet guidar dig genom stegen för att automatisera återställning av data från Azure Backup. Detta innebär följande steg:

1. Välj källvolymen
2. Välj en säkerhetskopieringspunkt för att återställa
3. Ange ett objekt som ska återställas
4. Utlösa återställningsprocessen

### <a name="picking-the-source-volume"></a>Plocka källvolymen

För att återställa ett objekt från Azure Backup måste du först identifiera källan till objektet. Eftersom vi kör kommandona i samband med en Windows Server eller en Windows-klient har datorn redan identifierats. Nästa steg för att identifiera källan är att identifiera volymen som innehåller den. En lista över volymer eller källor som säkerhetskopieras från den här datorn kan hämtas genom att köra cmdleten [Get-OBRecoverableSource.](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverablesource?view=winserver2012-ps) Det här kommandot returnerar en matris med alla källor som säkerhetskopierats från den här servern/klienten.

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

### <a name="choosing-a-backup-point-from-which-to-restore"></a>Välja en säkerhetskopieringspunkt som du vill återställa

Du hämtar en lista över säkerhetskopieringspunkter genom att köra cmdleten [Get-OBRecoverableItem](https://docs.microsoft.com/powershell/module/msonlinebackup/get-obrecoverableitem?view=winserver2012-ps) med lämpliga parametrar. I vårt exempel väljer vi den senaste säkerhetskopieringspunkten för källvolymen *C:* och använder den för att återställa en viss fil.

```powershell
$Rps = Get-OBRecoverableItem $Source[0]
$Rps
```

```Output

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :

IsDir                : False
ItemNameFriendly     : C:\
ItemNameGuid         : \\?\Volume{297cbf7a-0000-0000-0000-401f00000000}\
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : C:\
PointInTime          : 10/16/2019 7:00:19 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime :
```

Objektet `$Rps` är en matris med säkerhetskopieringspunkter. Det första elementet är den senaste punkten och Nth-elementet är den äldsta punkten. För att välja den senaste punkten, kommer vi att använda `$Rps[0]`.

### <a name="specifying-an-item-to-restore"></a>Ange ett objekt som ska återställas

Om du vill återställa en viss fil anger du filnamnet i förhållande till rotvolymen. Om du till exempel vill hämta C:\Test\Cat.job kör du följande kommando.

```powershell
$Item = New-OBRecoverableItem $Rps[0] "Test\cat.jpg" $FALSE
$Item
```

```Output
IsDir                : False
ItemNameFriendly     : C:\Test\cat.jpg
ItemNameGuid         :
LocalMountPoint      : C:\
MountPointName       : C:\
Name                 : cat.jpg
PointInTime          : 10/17/2019 7:52:13 PM
ServerName           : myserver.microsoft.com
ItemSize             :
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM

```

### <a name="triggering-the-restore-process"></a>Utlöser återställningsprocessen

För att utlösa återställningsprocessen måste vi först ange återställningsalternativen. Detta kan göras med hjälp av [cmdleten New-OBRecoveryOption.](https://docs.microsoft.com/powershell/module/msonlinebackup/new-obrecoveryoption?view=winserver2012-ps) I det här exemplet bör vi anta att vi vill återställa filerna till *C:\temp*. Låt oss också anta att vi vill hoppa över filer som redan finns på målmappen *C:\temp*. Om du vill skapa ett sådant återställningsalternativ använder du följande kommando:

```powershell
$RecoveryOption = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

Nu utlösa återställningsprocessen med hjälp av [Start-OBRecovery](https://docs.microsoft.com/powershell/module/msonlinebackup/start-obrecovery?view=winserver2012-ps) kommandot på den valda `$Item` från utdata från `Get-OBRecoverableItem` cmdlet:

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

Avinstallation av Azure Backup-agenten kan göras med hjälp av följande kommando:

```powershell
.\MARSAgentInstaller.exe /d /q
```

Att avinstallera agentbinärerna från datorn har vissa konsekvenser att tänka på:

* Filfiltret tas bort från datorn och spårning av ändringar stoppas.
* All principinformation tas bort från datorn, men principinformationen fortsätter att lagras i tjänsten.
* Alla säkerhetskopieringsscheman tas bort och inga ytterligare säkerhetskopior vidtas.

Data som lagras i Azure finns dock kvar och behålls enligt bevarandeprincipinställningarna av dig. Äldre punkter åldras automatiskt.

## <a name="remote-management"></a>Fjärrhantering

All hantering runt Azure Backup-agenten, principer och datakällor kan göras på distans via PowerShell. Maskinen som ska hanteras på distans måste förberedas korrekt.

Som standard är WinRM-tjänsten konfigurerad för manuell start. Starttypen måste ställas in på *Automatisk* och tjänsten ska startas. Om du vill kontrollera att WinRM-tjänsten körs ska värdet för egenskapen Status *köras*.

```powershell
Get-Service -Name WinRM
```

```Output
Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

PowerShell bör konfigureras för ommotsättning.

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

Maskinen kan nu hanteras på distans - från installationen av agenten. Följande skript kopierar till exempel agenten till fjärrdatorn och installerar den.

```powershell
$DLoc = "\\REMOTESERVER01\c$\Windows\Temp"
$Agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
$Args = "/q"
Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $DLoc -Force

$Session = New-PSSession -ComputerName REMOTESERVER01
Invoke-Command -Session $Session -Script { param($D, $A) Start-Process -FilePath $D $A -Wait } -ArgumentList $Agent, $Args
```

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup för Windows Server/Client:

* [Introduktion till Azure Backup](backup-introduction-to-azure-backup.md)
* [Säkerhetskopiera Windows-servrar](backup-windows-with-mars-agent.md)
