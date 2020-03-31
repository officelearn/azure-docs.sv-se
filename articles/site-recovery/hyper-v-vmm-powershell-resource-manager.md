---
title: Konfigurera Hyper-V-haveriberedskap (med VMM) till en sekundär plats med Azure Site Recovery/PowerShell
description: Beskriver hur du ställer in haveriberedskap av virtuella hyper-v-datorer i VMM-moln till en sekundär VMM-plats med Azure Site Recovery och PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048603"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Konfigurera haveriberedskap av virtuella hyper-virtuella datorer till en sekundär plats med hjälp av PowerShell (Resource Manager)

Den här artikeln visar hur du automatiserar stegen för replikering av virtuella hyper-virtuella datorer i System Center-moln för virtual machine manager till ett moln för Virtual Machine Manager i en sekundär lokal plats med hjälp av [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

- Granska [arkitekturen och komponenterna för scenariot](hyper-v-vmm-architecture.md).
- Granska [kraven för stöd](site-recovery-support-matrix-to-sec-site.md) för alla komponenter.
- Kontrollera att Virtual Machine Manager-servrar och Hyper-V-värdar uppfyller [supportkraven](site-recovery-support-matrix-to-sec-site.md).
- Kontrollera att de virtuella datorer som du vill replikera uppfyller stödet för [replikerade datorer](site-recovery-support-matrix-to-sec-site.md).

## <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappningsmappningsmappningar](hyper-v-vmm-network-mapping.md) mellan lokala virtuella datornätverk för Virtual Machine Manager i käll- och målmoln. Mappning utför följande:

- Ansluter virtuella datorer till lämpliga VM-målnätverk efter redundansväxling.
- Placerar virtuella replikdatorer optimalt på Hyper-V-målvärdservrar.
- Om du inte konfigurerar nätverksmappning ansluts inte virtuella repliker till ett VM-nätverk efter redundans.

Förbered Hanteraren för virtuella datorer enligt följande:

- Kontrollera att du har [logiska nätverk](https://docs.microsoft.com/system-center/vmm/network-logical) för Virtual Machine Manager på käll- och målservrarna för Virtual Machine Manager:
  - Det logiska nätverket på källservern ska vara associerat med det källmoln där Hyper-V-värdarna finns.
  - Det logiska nätverket på målservern ska vara associerat med målmolnet.
- Kontrollera att du har [VM-nätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på käll- och målservrarna för Virtual Machine Manager. VM-nätverk ska vara länkade till det logiska nätverket på varje plats.
- Ansluta virtuella datorer på Hyper-V-källvärdar till VM-källnätverket.

## <a name="prepare-for-powershell"></a>Förbered dig för PowerShell

Se till att du har Azure PowerShell redo att gå:

- Om du redan använder PowerShell uppgraderar du till version 0.8.10 eller senare. [Läs mer](/powershell/azureps-cmdlets-docs) om hur du konfigurerar PowerShell.
- När du har konfigurerat och konfigurerat PowerShell granskar du [tjänst cmdlets](/powershell/azure/overview).
- Mer information om hur du använder parametervärden, indata och utdata i PowerShell läser du guiden [Kom igång.](/powershell/azure/get-started-azureps)

## <a name="set-up-a-subscription"></a>Konfigurera en prenumeration

1. Logga in på ditt Azure-konto från PowerShell.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Hämta en lista över dina prenumerationer med prenumerations-ID: er. Observera ID:t för den prenumeration där du vill skapa valvet för Återställningstjänster.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Ange prenumerationen för valvet.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

1. Skapa en Azure Resource Manager-resursgrupp om du inte har någon.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Skapa ett nytt Recovery Services-valv. Spara arkivobjektet i en variabel som ska användas senare.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Du kan hämta valve-objektet när du `Get-AzRecoveryServicesVault` har skapat det med hjälp av cmdleten.

## <a name="set-the-vault-context"></a>Ange valvkontext

1. Hämta ett befintligt valv.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Ange valvkontexten.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Installera providern för platsåterställning

1. Skapa en katalog på datorn För virtual Machine Manager genom att köra följande kommando:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Extrahera filerna med hjälp av den hämtade providerinstallationsfilen.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Installera providern och vänta tills installationen är klar.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Registrera servern i valvet.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Skapa och associera en replikeringsprincip

1. Skapa en replikeringsprincip, i det här fallet för Hyper-V 2012 R2, enligt följande:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Molnet för Virtual Machine Manager kan innehålla Hyper-V-värdar som kör olika versioner av Windows Server, men replikeringsprincipen gäller för en viss version av ett operativsystem. Om du har olika värdar som körs på olika operativsystem skapar du separata replikeringsprinciper för varje system. Om du till exempel har fem värdar som körs på Windows Server 2012 och tre värdar som körs på Windows Server 2012 R2 skapar du två replikeringsprinciper. Du skapar en för varje typ av operativsystem.

1. Hämta den primära skyddsbehållaren (primärt virtual machine manager-moln) och återställningsskyddsbehållaren (recovery Virtual Machine Manager-molnet).

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. Hämta replikeringsprincipen som du skapade med det egna namnet.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Starta kopplingen mellan skyddsbehållaren (molnhanteraren för virtuell dator) med replikeringsprincipen.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Vänta tills principanslutningsjobbet är klart. Om du vill kontrollera om jobbet är klart använder du följande PowerShell-kodavsnitt:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. När jobbet har slutförts kör du följande kommando:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

Följ stegen i [Övervaka aktivitet](#monitor-activity)om du vill kontrollera att åtgärden är slutförd .

##  <a name="configure-network-mapping"></a>Konfigurera nätverksmappning

1. Använd det här kommandot för att hämta servrar för det aktuella valvet. Kommandot lagrar platsåterställningsservrarna `$Servers` i matrisvariabeln.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Kör det här kommandot om du vill hämta nätverken för källservern för Virtual Machine Manager och målservern för Virtual Machine Manager.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > Källservern för Virtual Machine Manager kan vara den första eller andra i servermatrisen. Kontrollera servernamnen för Virtual Machine Manager och hämta nätverken på rätt sätt.

1. Den här cmdleten skapar en mappning mellan det primära nätverket och återställningsnätverket. Det anger det primära nätverket som `$PrimaryNetworks`det första elementet i . Det anger återställningsnätverket som `$RecoveryNetworks`det första elementet i .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Aktivera skydd för virtuella datorer

När servrar, moln och nätverk har konfigurerats korrekt aktiverar du skydd för virtuella datorer i molnet.

1. Om du vill aktivera skydd kör du följande kommando för att hämta skyddsbehållaren:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. Hämta skyddsentiteten (VM), enligt följande:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Aktivera replikering för den virtuella datorn.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Om du vill replikera till CMK-aktiverade hanterade diskar i Azure gör du följande steg med Az PowerShell 3.3.0 och framåt:
>
> 1. Aktivera redundans till hanterade diskar genom att uppdatera vm-egenskaper
> 1. Använd `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet för att hämta disk-ID:n för varje disk i det skyddade objektet
> 1. Skapa ett ordlisteobjekt med cmdlet `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` för att innehålla mappning av disk-ID till diskkrypteringsuppsättning. Dessa diskkrypteringsuppsättningar ska skapas i föraningen av dig i målregionen.
> 1. Uppdatera egenskaperna för `Set-AzRecoveryServicesAsrReplicationProtectedItem` virtuella datorer med cmdlet genom att skicka ordlisteobjektet i parametern **DiskIdToDiskEncryptionSetMap.**

## <a name="run-a-test-failover"></a>Köra ett redundanstest

Om du vill testa distributionen kör du en testundanställning för en enda virtuell dator. Du kan också skapa en återställningsplan som innehåller flera virtuella datorer och köra en testundanställning för planen. Ett redundanstest simulerar redundans- och återställningsmekanismen i ett isolerat nätverk.

1. Hämta den virtuella datorn som virtuella datorer kommer att växla över.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Utför en testväxling.

   För en enda virtuell dator:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   För en återhämtningsplan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

Följ stegen i [Övervaka aktivitet](#monitor-activity)om du vill kontrollera att åtgärden är slutförd .

## <a name="run-planned-and-unplanned-failovers"></a>Kör planerade och oplanerade redundans

1. Utför en planerad redundans.

   För en enda virtuell dator:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   För en återhämtningsplan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Utför en oplanerad redundans.

   För en enda virtuell dator:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   För en återhämtningsplan:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Övervaka aktivitet

Använd följande kommandon för att övervaka redundansaktivitet. Vänta tills bearbetningen har avslutats mellan jobben.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>Nästa steg

[Läs mer](/powershell/module/az.recoveryservices) om Site Recovery med PowerShell-cmdlets i Resource Manager.
