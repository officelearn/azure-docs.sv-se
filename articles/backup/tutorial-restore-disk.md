---
title: Självstudiekurs - Återställa en VM-disk med Azure Backup
description: Lär dig hur du återställer en disk och återskapar en virtuell dator i Azure med Backup och Recovery Services.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 8a66cee7e844f0049f2d2ca2f6841943aa267f3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238736"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Återställa en disk och skapa en återställd virtuell dator i Azure

Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller enskilda filer. Den här artikeln förklarar hur du återställer hela den virtuella datorn med CLI. I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
>
> * lista och välja återställningspunkter
> * återställa en disk från en återställningspunkt
> * Skapa en virtuell dator från den återställda disken

Information om hur du återställer en disk och skapar en återställd virtuell dator med PowerShell finns i [Säkerhetskopiera och återställa virtuella Azure-datorer med PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Krav

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
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Återställa en disk från en virtuell dator

> [!IMPORTANT]
> Det rekommenderas starkt att använda Az CLI version 2.0.74 eller senare för att få alla fördelar med en snabb återställning inklusive hanterad diskåterställning. Det är bäst om användaren alltid använder den senaste versionen.

### <a name="managed-disk-restore"></a>Hanterad diskåterställning

Om den säkerhetskopierade virtuella datorn har hanterade diskar och om avsikten är att återställa hanterade diskar från återställningspunkten, anger du först ett Azure-lagringskonto. Det här lagringskontot används för att lagra vm-konfigurationen och distributionsmallen som senare kan användas för att distribuera den virtuella datorn från de återställda diskarna. Sedan tillhandahåller du också en målgrupp för de hanterade diskarna som ska återställas till.

1. Skapa ett lagringskonto med [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). Lagringskontonamnet måste vara med gemener endast och globalt unikt. Ersätt *mystorageaccount* med ditt eget unika namn:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Återställ disken från återställningspunkten med [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks). Ersätt *mystorageaccount* med namnet på det lagringskonto du skapade i föregående kommando. Ersätt *myRecoveryPointName* med återställningspunktnamnet som du fick i utdata från det tidigare kommandot [för återställningspunkt för az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) ***Ange också den målgrupp som de hanterade diskarna återställs till***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --target-resource-group targetRG
    ```

> [!WARNING]
> Om målresursgruppen inte tillhandahålls återställs de hanterade diskarna som ohanterade diskar till det angivna lagringskontot. Detta kommer att få betydande konsekvenser för återställningstiden eftersom den tid det tar att återställa diskarna helt beror på det angivna lagringskontot.

### <a name="unmanaged-disks-restore"></a>Återställa ohanterade diskar

Om den säkerhetskopierade virtuella datorn har ohanterade diskar och om avsikten är att återställa diskar från återställningspunkten, anger du först ett Azure-lagringskonto. Det här lagringskontot används för att lagra vm-konfigurationen och distributionsmallen som senare kan användas för att distribuera den virtuella datorn från de återställda diskarna. Som standard återställs de ohanterade diskarna till sina ursprungliga lagringskonton. Om användaren vill återställa alla ohanterade diskar till en enda plats, kan det angivna lagringskontot också användas som mellanlagringsplats för dessa diskar också.

I senare steg används den återställda disken för att skapa en virtuell dator.

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

Som nämnts ovan kommer de ohanterade diskarna att återställas till sitt ursprungliga lagringskonto. Detta ger den bästa återställningsprestanda. Men om alla ohanterade diskar måste återställas till ett visst lagringskonto, använd sedan den relevanta flaggan som visas nedan.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

De utdata som returneras liknar dem i följande exempel, som visar att återställningsjobbet har status *InProgress* (Pågår):

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

När *status* för återställningsjobbrapporterna *har slutförts*har nödvändig information (VM-konfiguration och distributionsmallen) återställts till lagringskontot.

## <a name="create-a-vm-from-the-restored-disk"></a>Skapa en virtuell dator från den återställda disken

Det sista steget är att skapa en virtuell dator från de återställda diskarna. Du kan använda distributionsmallen som hämtats till det angivna lagringskontot för att skapa den virtuella datorn.

### <a name="fetch-the-job-details"></a>Hämta jobbinformationen

Den resulterande jobbinformationen ger mallen URI som kan efterfrågas och distribueras. Använd kommandot Job show för att få mer information om det utlösta återställda jobbet.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

Utdata från den här frågan kommer att ge alla detaljer, men vi är bara intresserade av innehållet i lagringskontot. Vi kan använda [frågefunktionen](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest) i Azure CLI för att hämta relevant information

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>Hämta distributionsmallen

Mallen är inte direkt tillgänglig eftersom den finns under en kunds lagringskonto och den angivna behållaren. Vi behöver den fullständiga webbadressen (tillsammans med en tillfällig SAS-token) för att komma åt den här mallen.

Extrahera först mallblobben Uri från jobbinformation

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

Mallen blob Uri kommer att vara av detta format och extrahera mallens namn

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

Så mallnamnet från exemplet ovan ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json``` kommer att vara och behållarnamnet är```myVM-daa1931199fd4a22ae601f46d8812276```

Nu få SAS-token för denna behållare och mall som beskrivs [här](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-cli#provide-sas-token-during-deployment)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>Distribuera mallen för att skapa den virtuella datorn

Distribuera nu mallen för att skapa den virtuella datorn som förklaras [här](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli).

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

Du kan bekräfta att den virtuella datorn har skapats från återställda disken genom att visa en lista över de virtuella datorerna i resursgruppen med [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) enligt följande:

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du återställt en disk från en återställningspunkt och sedan skapat en virtuell dator från disken. Du har lärt dig att:

> [!div class="checklist"]
>
> * lista och välja återställningspunkter
> * återställa en disk från en återställningspunkt
> * Skapa en virtuell dator från den återställda disken

Gå vidare till nästa självstudiekurs där du lär dig att återställa enskilda filer från en återställningspunkt.

> [!div class="nextstepaction"]
> [Återställa filer till en virtuell dator i Azure](tutorial-restore-files.md)
