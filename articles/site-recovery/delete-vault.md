---
title: Ta bort ett Recovery Services-valv som har konfigurerats för Azure Site Recovery-tjänsten
description: Lär dig hur du tar bort ett Recovery Services-valv som konfigurerats för Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: 7dc1be1316d833d4a37a8b46bf0145089bb3ae22
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839142"
---
# <a name="delete-a-site-recovery-services-vault"></a>Ta bort ett Site Recovery Services-valv

Beroenden kan hindra dig från att ta bort en Azure Site Recovery-valv. De åtgärder som du behöver ta varierar beroende på Site Recovery-scenario. Om du vill ta bort ett valv som används i Azure Backup, se [Radera ett Backup-valv i Azure](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Ta bort ett Site Recovery-valv 
Följ de rekommenderade stegen för ditt scenario om du vill ta bort valvet.

### <a name="vmware-vms-to-azure"></a>Virtuella VMware-datorer till Azure

1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skyddet för en VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Ta bort alla replikeringsprinciper genom att följa stegen i [en replikeringsprincip](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Ta bort referenser till vCenter genom att följa stegen i [ta bort en vCenter-server](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Ta bort konfigurationsservern genom att följa stegen i [ställa av en konfigurationsserver](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Ta bort valvet.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Virtuella Hyper-V-datorer (med VMM) till Azure
1. Ta bort alla skyddade virtuella datorer genom att följa stegen i[inaktivera skydd för en Hyper-V-dator (med VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Ta bort koppling till och ta bort alla replikeringsprinciper genom att bläddra till valvet -> **Site Recovery-infrastruktur** -> **för System Center VMM** -> **replikering Principer**

3.  Ta bort referenser till VMM-servrar genom att följa stegen i [avregistrera en VMM-server som anslutna](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Ta bort valvet.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V-datorer (utan att Virtual Machine Manager) till Azure
1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skyddet för en Hyper-V virtuell dator (Hyper-V till Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Ta bort koppling till och ta bort alla replikeringsprinciper genom att bläddra till valvet -> **Site Recovery-infrastruktur** -> **för Hyper-V-platser** -> **replikeringsprinciper**

3. Ta bort referenser till Hyper-V-servrar genom att följa stegen i [Avregistrerar du en Hyper-V-värd](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Ta bort Hyper-V-platsen.

5. Ta bort valvet.


## <a name="use-powershell-to-force-delete-the-vault"></a>Använd PowerShell för att Framtvinga borttagning av valvet 

> [!Important]
> Om du testar produkten och inte tänker på förlust av data, kan du använda force delete-metoden för att snabbt bort valvet och alla dess beroenden.
> PowerShell-kommandot tar bort allt innehåll i valvet och är **inte ångra**.

Ta bort Site Recovery-valvet även om det finns skyddade objekt genom att använda dessa kommandon:

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Läs mer om [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0), och [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
