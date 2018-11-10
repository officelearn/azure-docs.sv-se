---
title: Ta bort servrar och inaktivera skydd | Microsoft Docs
description: Den här artikeln beskriver hur du avregistrerar servrar från Site Recovery-valvet och inaktivera skydd för virtuella datorer och fysiska servrar.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: raynew
ms.openlocfilehash: 4dac0ed85500e4339f6389f05113dfd68b72c5ff
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244346"
---
# <a name="remove-servers-and-disable-protection"></a>Ta bort servrar och inaktivera skydd

Den här artikeln beskrivs hur du avregistrerar servrar från Recovery Services-valvet och hur du inaktiverar skydd för datorer som skyddas av Site Recovery.


## <a name="unregister-a--configuration-server"></a>Avregistrera en konfigurationsserver

Om du replikerar virtuella VMware-datorer eller fysiska Windows/Linux-servrar till Azure kan du avregistrera en konfigurationsserver som är anslutna från ett valv på följande sätt:

1. [Inaktivera skyddet av virtuella datorer](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. [Ta bort koppling till eller ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) replikeringsprinciper.
3. [Ta bort konfigurationsservern](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Avregistrera en VMM-server

1. Stoppa replikering av virtuella datorer i moln på VMM-servern som du vill ta bort.
2. Ta bort alla nätverksmappningar som används av moln på VMM-servern som du vill ta bort. I **Site Recovery-infrastruktur** > **för System Center VMM** > **nätverksmappning**, högerklicka på nätverksmappningen > **Ta bort**.
3. Observera ID för VMM-servern.
4. Kopplingen mellan replikeringsprinciper från moln på VMM-servern som du vill ta bort.  I **Site Recovery-infrastruktur** > **för System Center VMM** >  **replikeringsprinciper**, dubbelklicka på den associerade principen. Högerklicka på molnet > **ta bort association med**.
5. Ta bort VMM-servern eller den aktiva noden. I **Site Recovery-infrastruktur** > **för System Center VMM** > **VMM-servrar**, högerklicka på servern > **ta bort** .
6. Om din VMM-server har frånkopplat, ladda ned och kör den [raderingsskript](https://aka.ms/asr-cleanup-script-vmm) på VMM-servern. Öppna PowerShell med den **kör som administratör** alternativet för att ändra körningsprincipen för standardomfattning (LocalMachine). Ange ID för VMM-servern som du vill ta bort i skriptet. Skriptet tar bort registrering och molnlänkningen information från servern.
5. Kör rensning-skriptet på alla sekundära VMM-servern.
6. Kör rensning-skriptet på alla andra passiv VMM-klusternoder som har installerat providern.
7. Avinstallera providern manuellt på VMM-servern. Om du har ett kluster kan du ta bort från alla noder.
8. Om dina virtuella datorer replikerar till Azure måste du avinstallera Microsoft Recovery Services-agenten från Hyper-V-värdar i de borttagna moln.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Avregistrerar du en Hyper-V-värd på en Hyper-V-plats

Hyper-V-värdar som inte hanteras av VMM har samlats i en Hyper-V-plats. Ta bort en värd i en Hyper-V-plats på följande sätt:

1. Inaktivera replikering för Hyper-V-datorer som finns på värden.
2. Kopplingen mellan principer för Hyper-V-platsen. I **Site Recovery-infrastruktur** > **för Hyper-V-platser** >  **replikeringsprinciper**, dubbelklicka på den associerade principen. Högerklicka på platsen > **ta bort association med**.
3. Ta bort Hyper-V-värdar. I **Site Recovery-infrastruktur** > **för Hyper-V-platser** > **Hyper-V-värdar**, högerklicka på servern > **ta bort** .
4. Ta bort Hyper-V-platsen när alla värdar har tagits bort från den. I **Site Recovery-infrastruktur** > **för Hyper-V-platser** > **Hyper-V-platser**, högerklicka på platsen > **ta bort** .
5. Om Hyper-V-värden i en **frånkopplad** tillstånd, och kör sedan följande skript på varje Hyper-V-värd som du har tagit bort. Skriptet rensar inställningarna på servern och Avregistrerar den från valvet.



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
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
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
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd



## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Inaktivera skyddet för en VMware-VM eller fysisk server (VMware till Azure)

1. I **skyddade objekt** > **replikerade objekt**, högerklicka på datorn > **inaktivera replikering**.
2. I **inaktivera replikering** väljer du något av följande alternativ:
    - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet Ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Inställningar för replikering på konfigurationsservern rensas och Site Recovery-debitering för den här skyddade servern har stoppats.
    - **Ta bort** – det här alternativet ska bara användas om källmiljön har tagits bort eller inte tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Inställningar för replikering på konfigurationsservern **inte** rensas. 

> [!NOTE]
> I båda alternativen mobilitetstjänsten inte avinstalleras från de skyddade servrarna, måste du avinstallera den manuellt. Om du planerar att skydda servern igen med samma konfigurationsserver, kan du hoppa över avinstallera mobilitetstjänsten.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Inaktivera skyddet för en Hyper-V virtuell dator (Hyper-V till Azure)

> [!NOTE]
> Använd den här proceduren om du replikerar virtuella Hyper-V-datorer till Azure utan en VMM-servern. Om du replikerar dina virtuella datorer med den **System Center VMM till Azure** scenario, följ instruktionerna [inaktivera skyddet för en Hyper-V virtuell dator som replikeras med hjälp av System Center VMM till Azure-scenario](#disable-protection-for-a-hyper-v-virtual-machine-replicating-using-the-system-centet-vmm-to-azure-scenario)

1. I **skyddade objekt** > **replikerade objekt**, högerklicka på datorn > **inaktivera replikering**.
2. I **inaktivera replikering**, du kan välja följande alternativ:
     - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet Ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Inställningar för replikering på den lokala virtuella datorn tas bort och Site Recovery-debitering för den här skyddade servern har stoppats.
    - **Ta bort** – det här alternativet ska bara användas om källmiljön har tagits bort eller inte tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Inställningar för replikering på den lokala virtuella datorn **inte** rensas. 

    > [!NOTE]
    > Om du väljer den **ta bort** alternativet Kör sedan följande uppsättning skript för att rensa replikeringsinställningarna lokala Hyper-V-servern.
1. På källan Hyper-V-värdservern, ta bort replikering för den virtuella datorn. Ersätt SQLVM1 med namnet på den virtuella datorn och kör skriptet från en administrativ PowerShell


    
    $vmName = ”SQLVM1” $vm = Get-WmiObject – Namespace ”root\virtualization\v2”-frågan ”Välj * från Msvm_ComputerSystem där ElementName =” $vmName ”” $replicationService = Get-WmiObject – Namespace ”root\virtualization\v2”-fråga ”Välj * från Msvm_ ReplicationService ”$replicationService.RemoveReplicationRelationship($vm.__PATH)
    

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Inaktivera skyddet för en virtuell dator för Hyper-V som replikerar till Azure med hjälp av System Center VMM till Azure-scenariot

1. I **skyddade objekt** > **replikerade objekt**, högerklicka på datorn > **inaktivera replikering**.
2. I **inaktivera replikering**, Välj något av följande alternativ:

    - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet Ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Inställningar för replikering på den lokala virtuella datorn har rensats och Site Recovery-debitering för den här skyddade servern har stoppats.
    - **Ta bort** – det här alternativet ska bara användas om källmiljön har tagits bort eller inte tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Inställningar för replikering på den lokala virtuella datorn **inte** rensas. 

    > [!NOTE]
    > Om du väljer den **ta bort** alternativ, och sedan följande skript för att rensa replikeringsinställningarna tun lokala VMM-servern.
3. Kör skriptet på VMM-källservern, med hjälp av PowerShell (administratör krävs krävs) från VMM-konsolen. Ersätt platshållaren **SQLVM1** med namnet på den virtuella datorn.

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Stegen ovan Rensa replikeringsinställningarna på VMM-servern. Kör skriptet för att stoppa replikering för den virtuella datorn körs på Hyper-V-värdservern. Ersätt SQLVM1 med namnet på den virtuella datorn och host01.contoso.com med namnet på Hyper-V-värdservern.

    
    $vmName = ”SQLVM1” $hostName = ”host01.contoso.com” $vm = Get-WmiObject – Namespace ”root\virtualization\v2”-frågan ”Välj * från Msvm_ComputerSystem där ElementName =” $vmName ”” - computername $hostName $replicationService = Get-WmiObject – Namespace ”root\virtualization\v2”-fråga ”Välj * från Msvm_ReplicationService” - computername $hostName $replicationService.RemoveReplicationRelationship($vm.__PATH)
    
       
 
## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Inaktivera skyddet för en Hyper-V virtuell dator som replikeras till sekundär VMM-servern med System Center VMM till VMM-scenario

1. I **skyddade objekt** > **replikerade objekt**, högerklicka på datorn > **inaktivera replikering**.
2. I **inaktivera replikering**, Välj något av följande alternativ:

    - **Inaktivera replikering och ta bort (rekommenderas)** – det här alternativet Ta bort det replikerade objektet från Azure Site Recovery och replikeringen för datorn stoppas. Inställningar för replikering på den lokala virtuella datorn har rensats och Site Recovery-debitering för den här skyddade servern har stoppats.
    - **Ta bort** – det här alternativet ska bara användas om källmiljön har tagits bort eller inte tillgänglig (inte ansluten). Detta tar bort det replikerade objektet från Azure Site Recovery (faktureringen stoppas). Inställningar för replikering på den lokala virtuella datorn **inte** rensas. Kör följande skript för att rensa replikeringen inställningar för lokala virtuella datorer.
> [!NOTE]
> Om du väljer den **ta bort** alternativ, och sedan följande skript för att rensa replikeringsinställningarna tun lokala VMM-servern.

3. Kör skriptet på VMM-källservern, med hjälp av PowerShell (administratör krävs krävs) från VMM-konsolen. Ersätt platshållaren **SQLVM1** med namnet på den virtuella datorn.

         $vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection
4. Kör det här skriptet att rensa inställningarna för den sekundära virtuella datorn på den sekundära VMM-servern:

        $vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force
5. Uppdatera de virtuella datorerna på Hyper-V-värdserver på den sekundära VMM-servern så att den sekundära virtuella datorn hämtar upptäckte igen i VMM-konsolen.
6. Stegen ovan Rensa replikeringsinställningarna på VMM-servern. Om du vill stoppa replikering för den virtuella datorn kör följande skript ojsan de primära och sekundära virtuella datorerna. Ersätt SQLVM1 med namnet på den virtuella datorn.

        Remove-VMReplication –VMName “SQLVM1”




