---
title: Återställa en disk på en virtuell dator med Azure Backup
description: Lär dig hur du återställer en disk och återskapar en virtuell dator i Azure med Backup och Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 4/17/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 76b5a5743fd6ac715eca45e49cc08d5006522ad0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871549"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Återställa en disk och skapa en återställd virtuell dator i Azure
Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller enskilda filer. Den här artikeln förklarar hur du återställer hela den virtuella datorn med CLI. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * lista och välja återställningspunkter
> * återställa en disk från en återställningspunkt
> * skapa en virtuell dator från den återställda disken.

Information om hur du återställer en disk och skapar en återställd virtuell dator med PowerShell finns i [Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Nödvändiga komponenter
För den här självstudiekursen måste du ha en virtuell Linux-dator som har skyddats med Azure Backup. Du simulerar en oavsiktlig borttagning och återställning av den virtuella datorn genom att skapa en virtuell dator från en disk i en återställningspunkt. Om du behöver en virtuell Linux-dator som har skyddats med Azure Backup kan du läsa mer i [Säkerhetskopiera en virtuell dator i Azure med CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Översikt över Backup
När Azure initierar en säkerhetskopiering tar tillägget på den virtuella datorn en ögonblicksbild. Säkerhetskopieringstillägget installeras på den virtuella datorn när den första säkerhetskopieringen begärs. Azure Backup kan också ta en ögonblicksbild av det underliggande lagringsutrymmet om den virtuella datorn inte körs när säkerhetskopieringen sker.

Som standard skapar Azure Backup en filsystemkonsekvent säkerhetskopia. När Azure Backup har tagit ögonblicksbilden överförs data till Recovery Services-valvet. För att maximera effektiviteten identifierar och överför Azure Backup endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.


## <a name="list-available-recovery-points"></a>Visa lista över tillgängliga återställningspunkter
Om du vill återställa en disk väljer du en återställningspunkt som källa för återställningsdata. Standardprincipen skapar en återställningspunkt varje dag och lagrar dem i 30 dagar, så du kan du behålla en uppsättning återställningspunkter och välja en viss tidpunkt för återställning. 

Visa en lista över tillgängliga återställningspunkter kommandot [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). Återställningspunktens **namn** används för att återställa diskar. I den här självstudiekursen vill vi ha den senaste återställningspunkten. Parametern `--query [0].name` väljer det senaste återställningsnamnet enligt följande:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Återställa en disk från en virtuell dator
Om du vill återställa disken från återställningspunkten måste du först skapa ett Azure-lagringskonto. Det här lagringskontot används för att lagra den återställda disken. I senare steg används den återställda disken för att skapa en virtuell dator.

1. Skapa ett lagringskonto med [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Lagringskontonamnet måste vara med gemener endast och globalt unikt. Ersätt *mystorageaccount* med ditt eget unika namn:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Återställ disken från återställningspunkten med [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Ersätt *mystorageaccount* med namnet på det lagringskonto du skapade i föregående kommando. Ersätt *myRecoveryPointName* med återställningspunktens namn som du fick i utdata från det tidigare kommandot [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list):

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
Om du vill övervaka status för återställningsjobbet använder du [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De utdata som returneras liknar dem i följande exempel, som visar att återställningsjobbet har status *InProgress* (Pågår):

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

När *Status* för återställningsjobbet är *Completed* (Slutfört) har disken återställts i lagringskontot.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Konvertera den återställda disken till en hanterad disk
Återställningsjobbet skapar en ohanterad disk. För att skapa en virtuell dator från disken måste den först konverteras till en hanterad disk.

1. Hämta anslutningsinformationen för ditt lagringskonto med [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string). Ersätt *storageaccountname* med namnet på ditt lagringskonto enligt följande:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Den ohanterade disken är säkrad i lagringskontot. Följande kommandon hämtar information om den ohanterade disken och skapar en variabel med namnet *uri* som används i nästa steg när du skapar den hanterade disken.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Nu kan du skapa en hanterad disk från den återställda disken med [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create). Variabeln *uri* från det föregående steget används som källa för den hanterade disken.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Nu när du har en hanterad disk från den återställda disken kan du rensa den ohanterade disken och lagringskontot med [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete). Ersätt *storageaccountname* med namnet på ditt lagringskonto enligt följande:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Skapa en virtuell dator från den återställda disken
Det sista steget är att skapa en virtuell dator från den hanterade disken.

1. Skapa en virtuell dator från den hanterade disken med [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) enligt följande:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Du kan bekräfta att den virtuella datorn har skapats från återställda disken genom att visa en lista över de virtuella datorerna i resursgruppen med [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) enligt följande:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen har du återställt en disk från en återställningspunkt och sedan skapat en virtuell dator från disken. Du har lärt dig att:

> [!div class="checklist"]
> * lista och välja återställningspunkter
> * återställa en disk från en återställningspunkt
> * skapa en virtuell dator från den återställda disken.

Gå vidare till nästa självstudiekurs där du lär dig att återställa enskilda filer från en återställningspunkt.

> [!div class="nextstepaction"]
> [Återställa filer till en virtuell dator i Azure](tutorial-restore-files.md)

