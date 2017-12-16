---
title: "Distribuera och hantera säkerhetskopiering för virtuella Azure-datorer med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig hur du distribuerar och hanterar Azure Backup med hjälp av PowerShell."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
ms.assetid: 2e24b1d9-4375-4049-a28d-e3bc01152f32
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/9/2017
ms.author: cwatson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 379686f70518b3c7773f1e0e0461eda2a4934d64
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="use-azurermbackup-cmdlets-to-back-up-virtual-machines"></a>Använda AzureRM.Backup-cmdletar för att säkerhetskopiera virtuella datorer
> [!div class="op_single_selector"]
> * [Resource Manager](backup-azure-vms-automation.md)
> * [Klassisk](backup-azure-vms-classic-automation.md)
>
>

Den här artikeln visar hur du använder Azure PowerShell för säkerhetskopiering och återställning av virtuella datorer i Azure. I Azure finns två olika distributionsmodeller för att skapa och arbeta med resurser: Resource Manager och klassisk. Den här artikeln täcker den klassiska distributionsmodellen för att säkerhetskopiera data till en Backup-valvet. Om du inte har skapat ett säkerhetskopieringsvalv i din prenumeration finns i Resource Manager-versionen av den här artikeln [Använd AzureRM.RecoveryServices.Backup-cmdletar för att säkerhetskopiera virtuella datorer](backup-azure-vms-automation.md). Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

> [!IMPORTANT]
> Nu kan du uppgradera dina säkerhetskopieringsvalv till Recovery Services-valv. Mer information finns i artikeln [Upgrade a Backup vault to a Recovery Services vault](backup-azure-upgrade-backup-to-recovery-services.md) (Uppgradera ett säkerhetskopieringsvalv till ett Recovery Services-valv). Microsoft rekommenderar att du uppgraderar dina säkerhetskopieringsvalv till Recovery Services-valv.<br/> Efter den 30 November 2017 kommer du inte att kunna använda PowerShell för att skapa säkerhetskopieringsvalv.<br/> **Med 30 November 2017**:
>- Alla återstående säkerhetskopieringsvalv uppgraderas automatiskt till Recovery Services-valv.
>- Du kan inte längre komma åt dina säkerhetskopierade data i den klassiska portalen. Använd i stället Azure Portal till att få åtkomst till dina säkerhetskopierade data i Recovery Services-valv.
>

## <a name="concepts"></a>Koncept
Den här artikeln innehåller information om PowerShell-cmdlets som används för att säkerhetskopiera virtuella datorer. Grundläggande information om hur du skyddar virtuella Azure-datorer finns [planera din infrastruktur för VM-säkerhetskopiering i Azure](backup-azure-vms-introduction.md).

> [!NOTE]
> Innan du börjar bör du läsa den [krav](backup-azure-vms-prepare.md) krävs för att arbeta med Azure Backup och [begränsningar](backup-azure-vms-prepare.md#limitations-when-backing-up-and-restoring-a-vm) säkerhetskopieringslösning för den aktuella virtuella datorn.
>
>

Ta en stund att förstå hierarkin med objekt och från där du vill börja om du vill använda PowerShell effektivt.

![Objekthierarkin](./media/backup-azure-vms-classic-automation/object-hierarchy.png)

De två viktigaste flödena att aktivera skydd för en virtuell dator och återställa data från en återställningspunkt. Den här artikeln fokuserar på att hjälpa dig att bli bra när du arbetar med PowerShell-cmdletar för att aktivera dessa två scenarier.

## <a name="setup-and-registration"></a>Installation och registrering
Börja:

1. [Hämta senaste PowerShell](https://github.com/Azure/azure-powershell/releases) (lägsta version som krävs är: 1.0.0)
2. Hitta av Azure Backup PowerShell-cmdlets som är tillgängliga genom att skriva följande kommando:

```
PS C:\> Get-Command *azurermbackup*

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Backup-AzureRmBackupItem                           1.0.1      AzureRM.Backup
Cmdlet          Disable-AzureRmBackupProtection                    1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupContainerReregistration        1.0.1      AzureRM.Backup
Cmdlet          Enable-AzureRmBackupProtection                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupContainer                         1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupItem                              1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJob                               1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupJobDetails                        1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupRecoveryPoint                     1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Get-AzureRmBackupVaultCredentials                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupRetentionPolicyObject             1.0.1      AzureRM.Backup
Cmdlet          New-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Register-AzureRmBackupContainer                    1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupProtectionPolicy               1.0.1      AzureRM.Backup
Cmdlet          Remove-AzureRmBackupVault                          1.0.1      AzureRM.Backup
Cmdlet          Restore-AzureRmBackupItem                          1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupProtectionPolicy                  1.0.1      AzureRM.Backup
Cmdlet          Set-AzureRmBackupVault                             1.0.1      AzureRM.Backup
Cmdlet          Stop-AzureRmBackupJob                              1.0.1      AzureRM.Backup
Cmdlet          Unregister-AzureRmBackupContainer                  1.0.1      AzureRM.Backup
Cmdlet          Wait-AzureRmBackupJob                              1.0.1      AzureRM.Backup
```

Följande uppgifter för installation och registrering kan automatiseras med PowerShell:

* Skapa ett säkerhetskopieringsvalv
* Registrerar de virtuella datorerna med Azure Backup-tjänsten

### <a name="create-a-backup-vault"></a>Skapa ett säkerhetskopieringsvalv
> [!WARNING]
> För kunder som använder Azure Backup för första gången, som du behöver registrera Azure Backup-provider som ska användas med din prenumeration. Detta kan göras genom att köra följande kommando: registrera AzureRmResourceProvider - ProviderNamespace ”Microsoft.Backup”
>
>

Du kan skapa ett nytt säkerhetskopieringsvalv med den **ny AzureRmBackupVault** cmdlet. Säkerhetskopieringsvalvet är en ARM-resurs, så du måste placera det inom en resursgrupp. Kör följande kommandon i en kommandotolk med förhöjd Azure PowerShell-konsol:

```
PS C:\> New-AzureRmResourceGroup –Name “test-rg” –Location “West US”
PS C:\> $backupvault = New-AzureRmBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

Du kan hämta en lista över alla säkerhetskopieringsvalv i en viss prenumeration med hjälp av den **Get-AzureRmBackupVault** cmdlet.

> [!NOTE]
> Det är praktiskt att lagra objektet säkerhetskopieringsvalvet i en variabel. Objektet valvet krävs som indata för många Azure Backup-cmdletar.
>
>

### <a name="registering-the-vms"></a>Registrerar de virtuella datorerna
Det första steget mot att konfigurera säkerhetskopiering med Azure Backup är att registrera din dator eller virtuell dator med Azure Backup-valvet. Den **registrera AzureRmBackupContainer** cmdlet tar inkommande informationen för en virtuell Azure IaaS-dator och registrerar den med angivna valvet. Registrera igen associerar den virtuella Azure-datorn med säkerhetskopieringsvalvet och spårar den virtuella datorn under säkerhetskopiering livscykel.

Registrera den virtuella datorn med Azure Backup-tjänsten skapar ett översta behållarobjekt. En behållare innehåller vanligtvis flera objekt som kan säkerhetskopieras, men för virtuella datorer finns endast en säkerhetskopiering artikel för behållaren.

```
PS C:\> $registerjob = Register-AzureRmBackupContainer -Vault $backupvault -Name "testvm" -ServiceName "testvm"
```

## <a name="backup-azure-vms"></a>Säkerhetskopiera virtuella Azure-datorer
### <a name="create-a-protection-policy"></a>Skapa en skyddsprincip för
Det är inte nödvändigt att skapa en ny skyddsprincip att starta säkerhetskopiering av dina virtuella datorer. Valvet levereras med en 'standardprincip' som kan användas för att snabbt aktivera skydd och sedan redigera senare med rätt information. Du kan hämta en lista över principerna som är tillgängliga i valvet med hjälp av den **Get-AzureRmBackupProtectionPolicy** cmdlet:

```
PS C:\> Get-AzureRmBackupProtectionPolicy -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DefaultPolicy             AzureVM            Daily              26-Aug-15 12:30:00 AM
```

> [!NOTE]
> Tidszonen i fältet BackupTime i PowerShell är UTC. När säkerhetskopieringstiden visas i Azure-portalen, justeras tidszonen till det lokala systemet tillsammans med UTC-förskjutningen.
>
>

En princip för säkerhetskopiering är associerad med minst en bevarandeprincip. Bevarandeprincipen definierar hur länge en återställningspunkt sparas med Azure Backup. Den **ny AzureRmBackupRetentionPolicy** cmdleten skapar PowerShell-objekt som innehåller information om principer bevarande. Dessa kvarhållning principobjekt används som indata för den *ny AzureRmBackupProtectionPolicy* cmdlet, eller direkt med den *aktivera AzureRmBackupProtection* cmdlet.

En princip för säkerhetskopiering definierar när och hur ofta säkerhetskopieringen av ett objekt är klar. Den **ny AzureRmBackupProtectionPolicy** cmdlet skapar ett PowerShell-objekt som innehåller information om princip för säkerhetskopiering. Säkerhetskopieringsprincipen som används som indata för den *aktivera AzureRmBackupProtection* cmdlet.

```
PS C:\> $Daily = New-AzureRmBackupRetentionPolicyObject -DailyRetention -Retention 30
PS C:\> $newpolicy = New-AzureRmBackupProtectionPolicy -Name DailyBackup01 -Type AzureVM -Daily -BackupTime ([datetime]"3:30 PM") -RetentionPolicy $Daily -Vault $backupvault

Name                      Type               ScheduleType       BackupTime
----                      ----               ------------       ----------
DailyBackup01             AzureVM            Daily              01-Sep-15 3:30:00 PM
```

### <a name="enable-protection"></a>Aktivera skydd
Aktiverar skydd innebär två objekt - objektet och principen, och båda måste tillhöra samma valvet. När principen har associerats med objektet, startar Säkerhetskopiering arbetsflödet på definierat schema.

```
PS C:\> Get-AzureRmBackupContainer -Type AzureVM -Status Registered -Vault $backupvault | Get-AzureRmBackupItem | Enable-AzureRmBackupProtection -Policy $newpolicy
```

### <a name="initial-backup"></a>Den första säkerhetskopieringen
Schemat för säkerhetskopiering hand tar om du gör den första fullständig för objektet och inkrementell kopian för efterföljande säkerhetskopieringar. Om du vill framtvinga den första säkerhetskopian ska hända vid en viss tidpunkt eller även omedelbart sedan använda den **säkerhetskopiering AzureRmBackupItem** cmdlet:

```
PS C:\> $container = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -Name "testvm"
PS C:\> $backupjob = Get-AzureRmBackupItem -Container $container | Backup-AzureRmBackupItem
PS C:\> $backupjob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

> [!NOTE]
> Tidszonen StartTime och EndTime fält visas i PowerShell är UTC. När liknande information visas i Azure-portalen, justeras tidszonen till din lokala systemklocka.
>
>

### <a name="monitoring-a-backup-job"></a>Övervakning av ett säkerhetskopieringsjobb
De flesta långvariga åtgärder i Azure Backup utformas som ett jobb. Detta gör det enkelt att följa förloppet utan att behålla den Azure-portalen öppna hela tiden.

För att få den senaste statusen för ett pågående jobb kan använda den **Get-AzureRmBackupJob** cmdlet.

```
PS C:\> $joblist = Get-AzureRmBackupJob -Vault $backupvault -Status InProgress
PS C:\> $joblist[0]

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Backup          InProgress      01-Sep-15 12:24:01 PM  01-Jan-01 12:00:00 AM
```

I stället för avsökning av dessa jobb för slutförande - som är onödiga, ytterligare kod – det är enklare att använda den **vänta AzureRmBackupJob** cmdlet. När den används i ett skript, pausar körningen cmdlet tills jobbet har slutförts eller det angivna tidsgränsvärdet har nåtts.

```
PS C:\> Wait-AzureRmBackupJob -Job $joblist[0] -Timeout 43200
```


## <a name="restore-an-azure-vm"></a>Återställa en virtuell dator i Azure
För att kunna återställa säkerhetskopierade data, måste du identifiera säkerhetskopierade artikeln och den återställningspunkt som innehåller data i tidpunkt. Den här informationen anges till cmdleten återställning AzureRmBackupItem att starta en återställning av data från valvet på kundens konto.

### <a name="select-the-vm"></a>Välj den virtuella datorn
För att få PowerShell-objektet som identifierar just säkerhetskopiering artikeln, måste du starta från en behållare i valvet och gå nedåt objekthierarkin. Markera den behållare som representerar den virtuella datorn med hjälp av **Get-AzureRmBackupContainer** cmdlet och skicka som att den **Get-AzureRmBackupItem** cmdlet.

```
PS C:\> $backupitem = Get-AzureRmBackupContainer -Vault $backupvault -Type AzureVM -name "testvm" | Get-AzureRmBackupItem
```

### <a name="choose-a-recovery-point"></a>Välj en återställningspunkt
Du kan nu visa alla återställningspunkter för Säkerhetskopiera objekt med den **Get-AzureRmBackupRecoveryPoint** cmdlet, och Välj återställningspunkt att återställa. Vanligtvis användare välja den senaste *AppConsistent* peka i listan.

```
PS C:\> $rp =  Get-AzureRmBackupRecoveryPoint -Item $backupitem
PS C:\> $rp

RecoveryPointId    RecoveryPointType  RecoveryPointTime      ContainerName
---------------    -----------------  -----------------      -------------
15273496567119     AppConsistent      01-Sep-15 12:27:38 PM  iaasvmcontainer;testvm;testv...
```

Variabeln ```$rp``` är en matris med återställningspunkter för den valda säkerhetskopian artikel, sorterade i omvänd ordning tid - den senaste återställningspunkten är vid index 0. Använd standard PowerShell matris indexering för att välja återställningspunkten. Till exempel: ```$rp[0]``` väljer den senaste återställningspunkten.

### <a name="restoring-disks"></a>Återställning av diskar
Det finns en nyckel skillnaden mellan återställningsåtgärderna klar via Azure-portalen och via Azure PowerShell. Med PowerShell kan stannar återställningen vid återställer diskar och konfigurationsinformation från återställningspunkten. Den skapar inte en virtuell dator.

> [!WARNING]
> Restore-AzureRmBackupItem kan inte skapa en virtuell dator. Den återställer endast diskarna till det angivna lagringskontot. Detta är inte på samma sätt som du får i Azure-portalen.
>
>

```
PS C:\> $restorejob = Restore-AzureRmBackupItem -StorageAccountName "DestAccount" -RecoveryPoint $rp[0]
PS C:\> $restorejob

WorkloadName    Operation       Status          StartTime              EndTime
------------    ---------       ------          ---------              -------
testvm          Restore         InProgress      01-Sep-15 1:14:01 PM   01-Jan-01 12:00:00 AM
```

Du kan hämta information om återställning åtgärden med den **Get-AzureRmBackupJobDetails** cmdlet när Återställningsjobbet har slutförts. Den *felinformation* egenskapen har information som behövs för att återskapa den virtuella datorn.

```
PS C:\> $restorejob = Get-AzureRmBackupJob -Job $restorejob
PS C:\> $details = Get-AzureRmBackupJobDetails -Job $restorejob
```

### <a name="build-the-vm"></a>Skapa den virtuella datorn
Skapa den virtuella datorn utanför de återställda diskarna kan göras med hjälp av äldre Azure Service Management PowerShell-cmdlets, de nya Azure Resource Manager-mallarna eller via Azure-portalen. I ett enkelt exempel visar vi hur du hämtar det med hjälp av Azure Service Management-cmdlets.

```
$properties  = $details.Properties

$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$blobName = $properties["Config Blob Name"]

$keys = Get-AzureStorageKey -StorageAccountName $storageAccountName
$storageAccountKey = $keys.Primary
$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey


$destination_path = "C:\Users\admin\Desktop\vmconfig.xml"
Get-AzureStorageBlobContent -Container $containerName -Blob $blobName -Destination $destination_path -Context $storageContext


$obj = [xml](((Get-Content -Path $destination_path -Encoding UniCode)).TrimEnd([char]0x00))
$pvr = $obj.PersistentVMRole
$os = $pvr.OSVirtualHardDisk
$dds = $pvr.DataVirtualHardDisks
$osDisk = Add-AzureDisk -MediaLocation $os.MediaLink -OS $os.OS -DiskName "panbhaosdisk"
$vm = New-AzureVMConfig -Name $pvr.RoleName -InstanceSize $pvr.RoleSize -DiskName $osDisk.DiskName

if (!($dds -eq $null))
{
    foreach($d in $dds.DataVirtualHardDisk)
    {
        $lun = 0
        if(!($d.Lun -eq $null))
        {
            $lun = $d.Lun
        }
        $name = "panbhadataDisk" + $lun
        Add-AzureDisk -DiskName $name -MediaLocation $d.MediaLink
        $vm | Add-AzureDataDisk -Import -DiskName $name -LUN $lun
    }
}

New-AzureVM -ServiceName "panbhasample" -Location "SouthEast Asia" -VM $vm
```

Mer information om hur du skapar en virtuell dator från de återställda diskarna Läs mer om följande cmdlets:

* [Lägg till AzureDisk](https://msdn.microsoft.com/library/azure/dn495252.aspx)
* [Ny AzureVMConfig](https://msdn.microsoft.com/library/azure/dn495159.aspx)
* [Ny AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)

## <a name="code-samples"></a>Kodexempel
### <a name="1-get-the-completion-status-of-job-sub-tasks"></a>1. Hämta status för slutförande av jobbet underordnade aktiviteter
Du kan använda för att spåra status för slutförande av enskilda underordnade aktiviteter måste den **Get-AzureRmBackupJobDetails** cmdlet:

```
PS C:\> $details = Get-AzureRmBackupJobDetails -JobId $backupjob.InstanceId -Vault $backupvault
PS C:\> $details.SubTasks

Name                                                        Status
----                                                        ------
Take Snapshot                                               Completed
Transfer data to Backup vault                               InProgress
```

### <a name="2-create-a-dailyweekly-report-of-backup-jobs"></a>2. Skapa en rapport för varje dag/vecka säkerhetskopieringsjobb
Administratörer vill förmodligen veta säkerhetskopieringsjobb kördes under de senaste 24 timmarna statusen för dessa säkerhetskopieringsjobb. Dessutom ger mängden data som överförs administratörer ett sätt att beräkna sin månatlig dataanvändning. Skriptet nedan hämtar rådata hämtas från Azure Backup-tjänsten och visar information i PowerShell-konsolen.

```
param(  [Parameter(Mandatory=$True,Position=1)]
        [string]$backupvaultname,

        [Parameter(Mandatory=$False,Position=2)]
        [int]$numberofdays = 7)


#Initialize variables
$DAILYBACKUPSTATS = @()
$backupvault = Get-AzureRmBackupVault -Name $backupvaultname
$enddate = ([datetime]::Today).AddDays(1)
$startdate = ([datetime]::Today)

for( $i = 1; $i -le $numberofdays; $i++ )
{
    # We query one day at a time because pulling 7 days of data might be too much
    $dailyjoblist = Get-AzureRmBackupJob -Vault $backupvault -From $startdate -To $enddate -Type AzureVM -Operation Backup
    Write-Progress -Activity "Getting job information for the last $numberofdays days" -Status "Day -$i" -PercentComplete ([int]([decimal]$i*100/$numberofdays))

    foreach( $job in $dailyjoblist )
    {
        #Extract the information for the reports
        $newstatsobj = New-Object System.Object
        $newstatsobj | Add-Member -Type NoteProperty -Name Date -Value $startdate
        $newstatsobj | Add-Member -Type NoteProperty -Name VMName -Value $job.WorkloadName
        $newstatsobj | Add-Member -Type NoteProperty -Name Duration -Value $job.Duration
        $newstatsobj | Add-Member -Type NoteProperty -Name Status -Value $job.Status

        $details = Get-AzureRmBackupJobDetails -Job $job
        $newstatsobj | Add-Member -Type NoteProperty -Name BackupSize -Value $details.Properties["Backup Size"]
        $DAILYBACKUPSTATS += $newstatsobj
    }

    $enddate = $enddate.AddDays(-1)
    $startdate = $startdate.AddDays(-1)
}

$DAILYBACKUPSTATS | Out-GridView
```

Om du vill lägga till diagram funktioner i den här rapportutdata Läs från TechNet blogginlägget [diagram med PowerShell](http://blogs.technet.com/b/richard_macdonald/archive/2009/04/28/3231887.aspx)

## <a name="next-steps"></a>Nästa steg
Om du vill använda PowerShell för att interagera med dina Azure-resurser kan ta en titt i PowerShell-artikeln för att skydda Windows Server [distribuera och hantera säkerhetskopiering för Windows Server](backup-client-automation-classic.md). Det finns också en artikel i PowerShell för att hantera DPM-säkerhetskopieringar [distribuera och hantera säkerhetskopiering för DPM](backup-dpm-automation-classic.md). Båda dessa artiklar har en version för Resource Manager distributioner samt klassiska distributioner.
