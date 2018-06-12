---
title: Ta bort ett Site Recovery-valv
description: Lär dig hur du tar bort en Azure Site Recovery-valvet, baserat på scenariot Site Recovery.
service: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 80c479aa23da2a8471af3fd83879a2dbfc5d6195
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300579"
---
# <a name="delete-a-site-recovery-vault"></a>Ta bort ett Site Recovery-valv

Beroenden kan hindra dig från att ta bort en Azure Site Recovery-valvet. Du behöver vidta åtgärder beror på Site Recovery-scenario. Om du vill ta bort ett valv som används i Azure Backup finns [ta bort ett säkerhetskopieringsvalv i Azure](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Ta bort ett Site Recovery-valv 
Följ rekommenderade åtgärder för ditt scenario om du vill ta bort valvet.

### <a name="vmware-vms-to-azure"></a>Virtuella VMware-datorer till Azure

1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skyddet för en VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Ta bort alla replikeringsprinciper genom att följa stegen i [ta bort en replikeringsprincip](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Ta bort referenser till vCenter genom att följa stegen i [ta bort en vCenter-server](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Ta bort konfigurationsservern genom att följa stegen i [inaktivera en konfigurationsserver](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Ta bort valvet.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Virtuella Hyper-V-datorer (med VMM) till Azure
1. Ta bort alla skyddade virtuella datorer genom att följa stegen i[inaktivera skyddet för en Hyper-V virtuell dator (med VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Ta bort alla replikeringsprinciper genom att bläddra till ditt valv & avassociera -> **Site Recovery-infrastruktur** -> **för System Center VMM** -> **replikering Principer**

3.  Ta bort referenser till VMM-servrar genom att följa stegen i [avregistrera en VMM-server med anslutna](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Ta bort valvet.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V-datorer (utan Virtual Machine Manager) till Azure
1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skyddet för en Hyper-V virtuell dator (Hyper-V till Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Ta bort alla replikeringsprinciper genom att bläddra till ditt valv & avassociera -> **Site Recovery-infrastruktur** -> **för Hyper-V-platser** -> **replikeringsprinciper**

3. Ta bort referenser till Hyper-V-servrar genom att följa stegen i [avregistrera Hyper-V-värd](/site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Ta bort Hyper-V-platsen.

5. Ta bort valvet.


## <a name="use-powershell-to-force-delete-the-vault"></a>Använd PowerShell för att tvinga ta bort valvet 

> [!Important]
> Om du testar produkten och inte är orolig för dataförlust, Använd kraft delete-metoden snabbt ta bort valvet och dess beroenden.
> PowerShell-kommando tar bort allt innehåll på valvet och är **inte omvändbar**.

Ta bort Site Recovery-valvet även om det finns skyddade objekt genom att använda dessa kommandon:

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Lär dig mer om [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0), och [ta bort AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
