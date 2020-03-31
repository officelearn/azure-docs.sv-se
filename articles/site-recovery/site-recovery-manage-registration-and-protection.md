---
title: Ta bort servrar och inaktivera skydd | Microsoft-dokument
description: I den här artikeln beskrivs hur du avregistrerar servrar från ett valv för platsåterställning och inaktiverar skydd för virtuella datorer och fysiska servrar.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: rajanaki
ms.openlocfilehash: a411fc9a95bef595a8fc49cad77189bb88fb7661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257633"
---
# <a name="remove-servers-and-disable-protection"></a>Ta bort servrar och inaktivera skydd

I den här artikeln beskrivs hur du avregistrerar servrar från ett Recovery Services-valv och hur du inaktiverar skydd för datorer som skyddas av Site Recovery.


## <a name="unregister-a--configuration-server"></a>Avregistrera en konfigurationsserver

Om du replikerar virtuella datorer med VMware eller Windows/Linux fysiska servrar till Azure kan du avregistrera en oansluten konfigurationsserver från ett valv enligt följande:

1. [Inaktivera skydd av virtuella datorer](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Ta bort kopplingen eller ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) replikeringsprinciper.
3. [Ta bort konfigurationsservern](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Avregistrera en VMM-server

1. Sluta replikera virtuella datorer i moln på den VMM-server som du vill ta bort.
2. Ta bort alla nätverksmappningar som används av moln på VMM-servern som du vill ta bort. Högerklicka på nätverksmappningen > Ta bort i > infrastruktur för platsåterställning**för VMM-nätverksmappning** > **Network Mapping**i **Platsåterställning.** **Delete**
3. Observera ID för VMM-servern.
4. Ta bort kopplingen mellan replikeringsprinciper från moln på den VMM-server som du vill ta bort.  Dubbelklicka på den associerade principen i **infrastruktur** > för återställning av**plats för SYSTEM Center VMM-replikeringsprinciper.** >  **Replication Policies** Högerklicka på molnet > **Ta bort frånassociera**.
5. Ta bort VMM-servern eller den aktiva noden. Högerklicka på servern > **Ta bort**i infrastruktur > för virtuell dator med **platsåterställning**för**VMM-servrar** > i**System Center.**
6. Om VMM-servern var i frånkopplat tillstånd hämtar och kör du [rensningsskriptet](https://aka.ms/asr-cleanup-script-vmm) på VMM-servern. Öppna PowerShell med alternativet **Kör som administratör** om du vill ändra körningsprincipen för standardomfånget (LocalMachine). I skriptet anger du ID:t för den VMM-server som du vill ta bort. Skriptet tar bort information om registrering och molnparning från servern.
5. Kör rensningsskriptet på valfri sekundär VMM-server.
6. Kör rensningsskriptet på andra passiva VMM-klusternoder som har providern installerad.
7. Avinstallera providern manuellt på VMM-servern. Om du har ett kluster tar du bort från alla noder.
8. Om dina virtuella datorer replikerade till Azure måste du avinstallera Microsoft Recovery Services-agenten från Hyper-V-värdar i de borttagna molnen.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Avregistrera en Hyper-V-värd på en Hyper-V-plats

Hyper-V-värdar som inte hanteras av VMM samlas in på en Hyper-V-plats. Ta bort en värd på en Hyper-V-webbplats enligt följande:

1. Inaktivera replikering för virtuella hyper-virtuella datorer som finns på värden.
2. Ta bort principer för Hyper-V-webbplatsen. Dubbelklicka på den associerade principen i infrastruktur > för **webbplatsåterställning****för hyper-V-platsers** >  **replikeringsprinciper.** Högerklicka på webbplatsen > **Ta bort från kopplingen**.
3. Ta bort Hyper-V-värdar. Högerklicka på servern > **Ta bort**i infrastruktur > för **webbplatsåterställning**för**Hyper-V-platser** > **Hyper-V-värdar.**
4. Ta bort Hyper-V-webbplatsen när alla värdar har tagits bort från den. Högerklicka på webbplatsen > **ta bort**i infrastruktur > för **webbplatsåterställning****för Hyper-V-platser** > **Hyper-V-platser.**
5. Om hyper-V-värden var i **frånkopplat** tillstånd kör du följande skript på varje Hyper-V-värd som du tog bort. Skriptet rensar inställningar på servern och avregistrerar det från valvet.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Inaktivera skydd för en virtuell VMware-dator eller en fysisk server (VMware till Azure)

1. Högerklicka på datorn > **Inaktivera replikering**i **Skyddade objekt** > **replikerade objekt**.
2. Välj något av följande alternativ på sidan **Inaktivera replikering:**
    - **Inaktivera replikering och ta bort (rekommenderas)** - Det här alternativet ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfigurationen på Konfigurationsservern rensas och site recovery-faktureringen för den här skyddade servern stoppas. Observera att det här alternativet endast kan användas när Configuration Server är i anslutet tillstånd.
    - **Ta bort** - Det här alternativet ska bara användas om källmiljön tas bort eller inte är tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Replikeringskonfigurationen på konfigurationsservern **rensas inte.** 

> [!NOTE]
> I båda alternativen mobilitetstjänsten kommer inte att avinstalleras från de skyddade servrarna, måste du avinstallera den manuellt. Om du planerar att skydda servern igen med samma konfigurationsserver kan du hoppa över att avinstallera mobilitetstjänsten.

> [!NOTE]
> Om du redan har misslyckats över en virtuell dator och den körs i Azure, observera att inaktivera skydd inte tar bort / påverkar det misslyckade över den virtuella datorn.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Inaktivera skydd för en virtuell Azure-dator (Azure till Azure)

-  Högerklicka på datorn > **Inaktivera replikering**i **Skyddade objekt** > **replikerade objekt**.
> [!NOTE]
> mobilitetstjänsten kommer inte att avinstalleras från de skyddade servrarna, du måste avinstallera den manuellt. Om du planerar att skydda servern igen kan du hoppa över att avinstallera mobilitetstjänsten.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Inaktivera skydd för en Hyper-V virtuell dator (Hyper-V till Azure)

> [!NOTE]
> Använd den här proceduren om du replikerar virtuella hyper-v-datorer till Azure utan en VMM-server. Om du replikerar dina virtuella datorer med **systemcenter-VMM till Azure** följer du instruktionerna Inaktivera skydd för en Hyper-V virtuell dator som replikerar med hjälp av System Center VMM till Azure-scenariot

1. Högerklicka på datorn > **Inaktivera replikering**i **Skyddade objekt** > **replikerade objekt**.
2. I **Inaktivera replikering**kan du välja följande alternativ:
   - **Inaktivera replikering och ta bort (rekommenderas)** - Det här alternativet ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfigurationen på den lokala virtuella datorn rensas och site recovery-faktureringen för den här skyddade servern stoppas.
   - **Ta bort** - Det här alternativet ska bara användas om källmiljön tas bort eller inte är tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Replikeringskonfigurationen på den lokala virtuella datorn rensas **inte.** 

 > [!NOTE]
     > Om du väljer alternativet **Ta bort** kör du följande uppsättning skript för att rensa replikeringsinställningarna lokalt Hyper-V Server.

> [!NOTE]
> Om du redan har misslyckats över en virtuell dator och den körs i Azure, observera att inaktivera skydd inte tar bort / påverkar det misslyckade över den virtuella datorn.

1. Ta bort replikering för den virtuella datorn på källhyper-V-värdservern. Ersätt SQLVM1 med namnet på din virtuella dator och kör skriptet från en administrativ PowerShell

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Inaktivera skydd för en Hyper-V virtuell dator som replikeras till Azure med hjälp av System Center VMM till Azure-scenariot

1. Högerklicka på datorn > **Inaktivera replikering**i **Skyddade objekt** > **replikerade objekt**.
2. I **Inaktivera replikering**väljer du något av följande alternativ:

   - **Inaktivera replikering och ta bort (rekommenderas)** - Det här alternativet ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfigurationen på den lokala virtuella datorn rensas och site recovery-faktureringen för den här skyddade servern stoppas.
   - **Ta bort** - Det här alternativet ska bara användas om källmiljön tas bort eller inte är tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Replikeringskonfigurationen på den lokala virtuella datorn rensas **inte.** 

     > [!NOTE]
     > Om du väljer alternativet **Ta bort** tunnar du upp följande skript för att rensa replikeringsinställningarna på lokal VMM-server.
3. Kör det här skriptet på käll-VMM-servern med PowerShell (administratörsbehörighet krävs) från VMM-konsolen. Ersätt platshållaren **SQLVM1** med namnet på den virtuella datorn.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Ovanstående steg rensar replikeringsinställningarna på VMM-servern. Om du vill stoppa replikeringen för den virtuella datorn som körs på Hyper-V-värdservern kör du det här skriptet. Ersätt SQLVM1 med namnet på den virtuella datorn och host01.contoso.com med namnet på Hyper-V-värdservern.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Inaktivera skydd för en hyper-V virtuell dator som replikerar till sekundär VMM-server med systemcenter-VMM till VMM-scenariot

1. Högerklicka på datorn > **Inaktivera replikering**i **Skyddade objekt** > **replikerade objekt**.
2. I **Inaktivera replikering**väljer du något av följande alternativ:

   - **Inaktivera replikering och ta bort (rekommenderas)** - Det här alternativet ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Replikeringskonfigurationen på den lokala virtuella datorn rensas och site recovery-faktureringen för den här skyddade servern stoppas.
   - **Ta bort** - Det här alternativet ska bara användas om källmiljön tas bort eller inte är tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Replikeringskonfigurationen på den lokala virtuella datorn rensas **inte.** Kör följande uppsättning skript för att rensa replikeringsinställningarna lokala virtuella datorer.
     > [!NOTE]
     > Om du väljer alternativet **Ta bort** tunnar du upp följande skript för att rensa replikeringsinställningarna på lokal VMM-server.

3. Kör det här skriptet på käll-VMM-servern med PowerShell (administratörsbehörighet krävs) från VMM-konsolen. Ersätt platshållaren **SQLVM1** med namnet på den virtuella datorn.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. På den sekundära VMM-servern kör du det här skriptet för att rensa inställningarna för den sekundära virtuella datorn:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. På den sekundära VMM-servern uppdaterar du de virtuella datorerna på Hyper-V-värdservern så att den sekundära virtuella datorn identifieras igen i VMM-konsolen.
6. Ovanstående steg rensa replikeringsinställningarna på VMM-servern. Om du vill stoppa replikeringen för den virtuella datorn kör du följande skript oh de primära och sekundära virtuella datorerna. Ersätt SQLVM1 med namnet på den virtuella datorn.

        Remove-VMReplication –VMName “SQLVM1”




