---
title: Ta bort ett Azure Site Recovery-valv
description: Lär dig hur du tar bort ett Recovery Services-valv som konfigurerats för Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894970"
---
# <a name="delete-a-site-recovery-services-vault"></a>Ta bort ett Site Recovery Services-valv

I den här artikeln beskrivs hur du tar bort ett Recovery Services-valv för webbplatsåterställning. Information om hur du tar bort ett valv som används i Azure Backup finns [i Ta bort ett valv för säkerhetskopiering i Azure](../backup/backup-azure-delete-vault.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Innan du börjar

Innan du kan ta bort ett valv måste du ta bort registrerade servrar och objekt i valvet. Vad du behöver ta bort beror på replikeringsscenarierna som du har distribuerat. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Ta bort en virtuell dator med arkiv-Azure till Azure

1. Följ [dessa instruktioner](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) för att ta bort alla skyddade virtuella datorer.
2. Ta sedan bort valvet.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Ta bort en virtuell virtuell valv-VMware till Azure

1. Följ [dessa instruktioner](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för att ta bort alla skyddade virtuella datorer.
2. Följ [dessa steg](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) för att ta bort alla replikeringsprinciper.
3. Ta bort referenser till vCenter med hjälp av [dessa steg](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Följ [dessa instruktioner](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) för att inaktivera en konfigurationsserver.
5. Ta sedan bort valvet.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Ta bort en virtuell virtuell valv-Hyper-V (med VMM) till Azure

1. Följ [dessa steg](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) för att ta bort virtuella hyper-virtuella datorer som hanteras av System Center VMM.
2. Ta bort och ta bort alla replikeringsprinciper. Gör detta i ditt valv > **platsåterställningsinfrastruktur** > **för systemcenter VMM-replikeringsprinciper** > **Replication Policies**.
3. Följ [dessa steg](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) för att avregistrera en ansluten VMM-server.
4. Ta sedan bort valvet.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Ta bort en virtuell virtuell valv-Hyper-V till Azure

1. Följ [dessa steg](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) för att ta bort alla skyddade virtuella datorer.
2. Ta bort och ta bort alla replikeringsprinciper. Gör detta i ditt valv > **infrastruktur** > för återställning av**platser för Hyper-V-platser.** > **Replication Policies**
3. Följ [dessa instruktioner](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) för att avregistrera en Hyper-V-värd.
4. Ta bort Hyper-V-webbplatsen.
5. Ta sedan bort valvet.


## <a name="use-powershell-to-force-delete-the-vault"></a>Använda PowerShell för att tvinga bort valvet 

> [!Important]
> Om du testar produkten och inte är orolig för dataförlust använder du metoden force delete för att snabbt ta bort valvet och alla dess beroenden.
> PowerShell-kommandot tar bort allt innehåll i valvet och är **inte reversibelt**.

Om du vill ta bort valvet för platsåterställning även om det finns skyddade objekt använder du följande kommandon:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Läs mer om [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)och [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).
