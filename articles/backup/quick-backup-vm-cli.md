---
title: "Azure Snabbstart – Säkerhetskopiera en virtuell dator med Azure CLI | Microsoft Docs"
description: "Lär dig hur du säkerhetskopierar virtuella datorer med Azure CLI"
services: backup, virtual-machines-linux
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: storage-backup-recovery
ms.date: 10/02/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 63f4db61a82bdcefcaa71f494d733a3ac78bd505
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>Säkerhetskopiera en virtuell dator i Azure med CLI
Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Med Azure Backup skapas återställningspunkter som kan lagras i geo-redundanta återställningsvalv. Den här artikeln beskriver hur du säkerhetskopierar en virtuell dator i Azure med Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](quick-backup-vm-powershell.md) eller [Azure Portal](quick-backup-vm-portal.md).

I den här snabbguiden sker säkerhetskopieringen på en befintlig virtuell Azure-dator. Om du behöver skapa en virtuell dator kan du [skapa en virtuell dator med Azure CLI](../virtual-machines/linux/quick-create-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta CLI-versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv
Recovery Services-valvet är en logisk behållare som lagrar säkerhetskopierade data för varje skyddad resurs, till exempel virtuella Azure-datorer. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Skapa ett Recovery Services-valv med [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az_backup_vault_create). Ange samma resursgrupp och plats som den virtuella datorn som du vill skydda. Om du har använt [snabbstarten för virtuella datorer](../virtual-machines/linux/quick-create-cli.md) har du skapat:

- en resursgrupp som heter *myResourceGroup*,
- en virtuell dator med namnet *myVM*,
- resurser på platsen *eastus*.

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

Recovery Services-valvet är som standard inställt på geo-redundant lagring. Geo-redundant lagring ger ett extra skydd genom att dina säkerhetskopierade data replikeras till en sekundär Azure-region som ligger hundratals kilometer från den primära regionen.


## <a name="enable-backup-for-an-azure-vm"></a>Aktivera säkerhetskopiering för en virtuell Azure-dator
Skapa en skyddsprincip för att definiera: när ett säkerhetskopieringsjobb ska köras och hur länge återställningspunkterna ska sparas. Med standardskyddsprincipen körs ett säkerhetskopieringsjobb varje dag och återställningspunkterna sparas i 30 dagar. Du kan använda standardvärdena för att snabbt skydda din virtuella dator. Om du vill aktivera säkerhetskopieringsskyddet för en virtuell dator använder du [az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az_backup_protection_enable_for_vm). Ange vilken resursgrupp och virtuell dator som ska skyddas och sedan vilken princip som ska användas:

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```


## <a name="start-a-backup-job"></a>Starta ett säkerhetskopieringsjobb
Du kan starta en säkerhetskopiering nu, i stället för att vänta tills jobbet körs vid standardprincipens schemalagda tidpunkt, med hjälp av [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az_backup_protection_backup_now). När det första säkerhetskopieringsjobbet körs skapas en fullständig återställningspunkt. Vid varje säkerhetskopiering, efter den första säkerhetskopieringen, skapas inkrementella återställningspunkter. Inkrementella återställningspunkter är lagrings- och tidseffektiva eftersom de bara överför de ändringar som gjorts sedan den senaste säkerhetskopieringen.

Följande parametrar används för att säkerhetskopiera den virtuella datorn:

- `--container-name` är namnet på din virtuella dator
- `--item-name` är namnet på din virtuella dator
- Värdet `--retain-until` ska vara angivet till det senaste datumet, i UTC-tidsformat (**dd-mm-åååå**), som du vill att återställningspunkten ska vara tillgänglig

I följande exempel säkerhetskopieras den virtuella datorn *myVM* och utgångsdatumet för återställningspunkten är 18 oktober 2017:

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>Övervaka säkerhetskopieringen
Om du vill övervaka status för säkerhetskopieringsjobb använder du [az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De utdata som returneras liknar dem i följande exempel, som visar att säkerhetskopieringsjobbet har status *Pågår*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

När *status* för säkerhetskopieringsjobbet är *Slutförd* skyddas din virtuella dator med Recovery Services och en fullständig återställningspunkt har sparats.


## <a name="clean-up-deployment"></a>Rensa distribution
När det inte längre behövs kan du inaktivera skyddet av den virtuella datorn, ta bort återställningspunkterna och Recovery Services-valvet och sedan ta bort resursgruppen och de relaterade virtuella datorresurserna. Om du använde en befintlig virtuell dator kan du hoppa över det sista [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete)-kommandot och ha kvar resursgruppen och den virtuella datorn.

Om du vill prova en självstudie om säkerhetskopiering som förklarar hur man återställer data för en virtuell dator kan du gå till [Nästa steg](#next-steps). 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du skapat ett Recovery Services-valv, aktiverat skydd på en virtuell dator och skapat den första återställningspunkten. Om du vill lära dig mer om Azure Backup och Recovery Services kan du gå vidare till självstudiekurserna.

> [!div class="nextstepaction"]
> [Säkerhetskopiera flera virtuella Azure-datorer](./tutorial-backup-vm-at-scale.md)
