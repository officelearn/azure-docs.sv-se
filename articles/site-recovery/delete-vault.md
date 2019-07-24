---
title: Ta bort ett Recovery Services valv konfigurerat för tjänsten Azure Site Recovery
description: Lär dig hur du tar bort ett Recovery Services valv konfigurerat för Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876032"
---
# <a name="delete-a-site-recovery-services-vault"></a>Ta bort ett Site Recovery Services-valv

Beroenden kan förhindra att du tar bort ett Azure Site Recovery valv. De åtgärder som du måste utföra varierar beroende på Site Recovery scenariot. Om du vill ta bort ett valv som används i Azure Backup, se [ta bort ett säkerhets kopierings valv i Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Ta bort ett Site Recovery-valv 
Om du vill ta bort valvet följer du de rekommenderade stegen för ditt scenario.
### <a name="azure-vms-to-azure"></a>Virtuella Azure-datorer till Azure

1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skydd för en VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure).
2. Ta bort valvet.

### <a name="vmware-vms-to-azure"></a>Virtuella VMware-datorer till Azure

1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skydd för en VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Ta bort alla principer för replikering genom att följa stegen i [ta bort en replikeringsprincip](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Ta bort referenser till vCenter genom att följa stegen i [ta bort en vCenter-Server](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Ta bort konfigurations servern genom att följa stegen i [inaktivera en konfigurations Server](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Ta bort valvet.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Virtuella Hyper-V-datorer (med VMM) till Azure
1. Ta bort alla skyddade virtuella datorer genom att följa stegen i[inaktivera skydd för en virtuell Hyper-V-dator (med VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Ta bort associationen & ta bort alla replikeringsprinciper genom att bläddra till ditt valv – > **Site Recovery infrastruktur** – > **för System Center VMM** -> -**replikeringsprinciper**

3.  Ta bort referenser till VMM-servrar genom att följa stegen i [avregistrera en ansluten VMM-Server](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Ta bort valvet.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Virtuella Hyper-V-datorer (utan Virtual Machine Manager) till Azure
1. Ta bort alla skyddade virtuella datorer genom att följa stegen i [inaktivera skydd för en virtuell Hyper-v-dator (Hyper-v till Azure)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Ta bort associationen & ta bort alla replikeringsprinciper genom att bläddra till ditt valv – > **Site Recovery infrastruktur** -> **för Hyper-V-platser** -> 

3. Ta bort referenser till Hyper-V-servrar genom att följa stegen i [avregistrera en Hyper-v-värd](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Ta bort Hyper-V-platsen.

5. Ta bort valvet.


## <a name="use-powershell-to-force-delete-the-vault"></a>Använd PowerShell för att framtvinga borttagning av valvet 

> [!Important]
> Om du testar produkten och inte bekymrar dig om data förlust använder du metoden Force Delete för att snabbt ta bort valvet och alla dess beroenden.
> PowerShell-kommandot tar bort allt innehåll i valvet och går **inte att ångra**.

Om du vill ta bort Site Recovery valvet även om det finns skyddade objekt, använder du följande kommandon:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Läs mer om [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)och [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
