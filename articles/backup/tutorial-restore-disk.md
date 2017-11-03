---
title: "Återställa en virtuell disk med Azure Backup | Microsoft Docs"
description: "Lär dig hur du återställer en disk och skapa en återställa en virtuell dator i Azure med säkerhetskopiering och Recovery Services."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bc6da13786eb9eb6186ceadf0432b3a3ec2c941
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Återställa en disk och skapa en återställd virtuell dator i Azure
Azure-säkerhetskopiering skapar återställningspunkter som är lagrade i geo-redundant recovery-valv. När du återställer från en återställningspunkt kan du återställa hela VM eller enskilda filer. Den här artikeln förklarar hur du återställer en fullständig virtuell dator. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Listan och väljer återställningspunkter
> * Återställa en disk från en återställningspunkt
> * Skapa en virtuell dator från den återställda disken

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Krav
Den här kursen kräver en Linux VM som har skyddats med Azure Backup. För att simulera en oavsiktlig borttagning av VM- och återställningsprocessen kan skapa du en virtuell dator från en disk i en återställningspunkt. Om du behöver en Linux VM som har skyddats med Azure Backup finns [säkerhetskopiera en virtuell dator i Azure med CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Översikt över Backup
När Azure initierar en säkerhetskopiering, tar en ögonblicksbild av en tidpunkt i säkerhetskopiering tillägget på den virtuella datorn. Sekundär anknytning installeras på den virtuella datorn när den första säkerhetskopieringen har begärts. Azure Backup kan också ta en ögonblicksbild av det underliggande lagringsutrymmet om den virtuella datorn inte körs när säkerhetskopieringen sker.

Som standard tar Azure Backup en konsekvent filsystemsäkerhetskopia. När Azure Backup tar ögonblicksbilden, överförs data till Recovery Services-valvet. För att maximera effektiviteten, Azure Backup identifierar och överför endast block av data som har ändrats sedan den tidigare säkerhetskopian.

När dataöverföringen har slutförts ögonblicksbilden tas bort och skapa en återställningspunkt.


## <a name="list-available-recovery-points"></a>Lista över tillgängliga återställningspunkter
Om du vill återställa en disk, väljer du en återställningspunkt som källa för återställningsdata. Standardprincipen och skapar en återställningspunkt varje dag och lagrar dem i 30 dagar, kan du behålla en uppsättning återställningspunkter som kan du välja en viss punkt i tiden för återställning. 

Om du vill se en lista över tillgängliga återställningspunkter [az säkerhetskopiering recoverypoint listan](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). Återställningspunkten **namnet** används för att återställa diskar. I den här självstudiekursen kommer vill vi den senaste återställningspunkten. Den `--query [0].name` parametern väljer det senaste återställningsnamnet på följande sätt:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Återställa en virtuell disk
Om du vill återställa disken från återställningspunkten skapa du först ett Azure storage-konto. Det här lagringskontot används för att lagra den återställda disken. Ytterligare steg används återställda disken för att skapa en virtuell dator.

1. Du kan skapa ett lagringskonto med [az storage-konto skapar](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). Lagringskontonamnet måste vara gemener och vara globalt unika. Ersätt *mittlagringskonto* med dina egna unika namn:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Återställa disken från återställningspunkten med [az säkerhetskopiering återställa återställning diskar](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks). Ersätt *mittlagringskonto* med namnet på lagringskontot som du skapade i föregående kommando. Ersätt *myRecoveryPointName* med återställning punkt namn som du fick i utdata från den tidigare [az säkerhetskopiering recoverypoint lista](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) kommando:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Övervaka återställningsjobbet
Om du vill övervaka status för återställningsjobbet använda [az säkerhetskopieringsjobbet listan](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Utdata liknar följande exempel som visar återställningsjobbet är *InProgress*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

När den *Status* återställning jobbet rapporter *slutförd*, disken har återställts till lagringskontot.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Konvertera återställda disk till en hanterad
Återställningsjobbet skapar en ohanterad disk. För att skapa en virtuell dator från disken, måste du först konvertera den till en hanterad disk.

1. Hämta anslutningsinformationen för ditt lagringskonto med [az lagring konto visa anslutningssträng](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Ersätt *mittlagringskonto* med namnet på din lagring konto på följande sätt:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Ohanterad disken är skyddad i storage-konto. Följande kommandon får information om ohanterade disken och skapa en variabel med namnet *uri* som används i nästa steg när du skapar den hanterade disken.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Nu kan du skapa en Disk som hanteras från återställda disken med [az disk skapa](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create). Den *uri* variabel från det föregående steget används som källa för den hanterade disken.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Som du nu har en Disk som hanteras från en återställd disk, rensa ohanterade disk och kontot med [az storage-konto bort](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Ersätt *mittlagringskonto* med namnet på din lagring konto på följande sätt:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Skapa en virtuell dator från den återställda disken
Det sista steget är att skapa en virtuell dator från den hanterade disken.

1. Skapa en virtuell dator från hanterade disken med [az vm skapa](/cli/azure/vm?view=azure-cli-latest#az_vm_create) på följande sätt:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. För att bekräfta att den virtuella datorn har skapats från återställda disken, visa en lista över de virtuella datorerna i resursgruppen med [az vm listan](/cli/azure/vm?view=azure-cli-latest#az_vm_list) på följande sätt:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen återställt en disk från en återställningspunkt och sedan skapa en virtuell dator från disken. Du har lärt dig att:

> [!div class="checklist"]
> * Listan och väljer återställningspunkter
> * Återställa en disk från en återställningspunkt
> * Skapa en virtuell dator från den återställda disken

Gå vidare till nästa kurs att lära dig om att återställa enskilda filer från en återställningspunkt.

> [!div class="nextstepaction"]
> [Återställ filer till en virtuell dator i Azure](tutorial-restore-files.md)

