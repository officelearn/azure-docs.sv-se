---
title: Hantera Azure-filresurssäkerhetskopior med Azure CLI
description: Lär dig hur du använder Azure CLI för att hantera och övervaka Azure-filresurser som backas upp av Azure Backup.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934878"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Hantera Azure-filresurssäkerhetskopior med Azure CLI

Azure CLI ger en kommandoradsupplevelse för hantering av Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. I den här artikeln beskrivs hur du utför uppgifter för att hantera och övervaka Azure-filresurser som backas upp av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Du kan också utföra dessa steg med [Azure-portalen](https://portal.azure.com/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta CLI-versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har en Azure-filresurs som backas upp av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Om du inte har någon, se [Säkerhetskopiera Azure-filresurser med CLI](backup-afs-cli.md) för att konfigurera säkerhetskopiering för dina filresurser. I den här artikeln använder du följande resurser:

* **Resursgrupp**: *azurefiles*
* **RecoveryServicesVault**: *azurefilesvault*
* **Lagringskonto** *: afsaccount*
* **Fildelning**: *azurefiles*

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser säkerhetskopiering eller återställning skapar säkerhetskopieringstjänsten ett jobb för spårning. Om du vill övervaka slutförda eller jobb som körs använder du cmdlet för [jobblistan för az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) Med CLI kan du också [avbryta ett jobb som körs](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) eller vänta [tills ett jobb är klart](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

I följande exempel visas status för säkerhetskopieringsjobb för *azurefilesvault* Recovery Services-valvet:

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Ändra princip

Du kan ändra en princip för säkerhetskopiering för att ändra säkerhetskopieringsfrekvens eller kvarhållningsintervall med hjälp av [az backup-objektuppsättningsprincip](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Om du vill ändra principen definierar du följande parametrar:

* **--container-name**: Namnet på lagringskontot som är värd för filresursen. Om du vill hämta **namnet** eller **det egna namnet** på behållaren använder du kommandot för behållare för [az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--name**: Namnet på den filresurs som du vill ändra principen för. Om du vill hämta **namnet** eller **det egna namnet** på det säkerhetskopierade objektet använder du kommandot för [säkerhetskopior.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: Namnet på den säkerhetskopieringsprincip som du vill ange för filresursen. Du kan använda [principlistan för az-säkerhetskopiering](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) för att visa alla principer för valvet.

I följande exempel anges säkerhetskopieringsprincipen *schedule2* för filresursen för *azurefiles* som finns i *lagringskontot för afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Du kan också köra föregående kommando genom att använda de egna namnen för behållaren och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-typ:** *azurestorage*
* **--workload-type**: azurefileshare --workload-type : azurefileshare --workload-type : *azurefileshare* --

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för ändringsprincipåtgärden. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Avbryta skyddet av en filresurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhetskopieringsjobb och *ta bort* alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb men *lämna* återställningspunkterna.

Det kan finnas en kostnad som är associerad med att lämna återställningspunkterna i lagring, eftersom de underliggande ögonblicksbilderna som skapats av Azure Backup behålls. Fördelen med att lämna återställningspunkterna är alternativet att återställa filresursen senare, om du vill. Information om kostnaden för att lämna återställningspunkterna finns i [prisinformationen](https://azure.microsoft.com/pricing/details/storage/files). Om du väljer att ta bort alla återställningspunkter kan du inte återställa filresursen.

Om du vill stoppa skyddet för filresursen definierar du följande parametrar:

* **--container-name**: Namnet på lagringskontot som är värd för filresursen. Om du vill hämta **namnet** eller **det egna namnet** på behållaren använder du kommandot för behållare för [az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Namnet på den filresurs som du vill stoppa skyddet för. Om du vill hämta **namnet** eller **det egna namnet** på det säkerhetskopierade objektet använder du kommandot för [säkerhetskopior.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Stoppa skyddet och behåll återställningspunkter

Om du vill stoppa skyddet när du behåller data använder du [az backup-skyddet inaktivera](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

Följande exempel stoppar skyddet för filresursresursen *azurefiles* men behåller alla återställningspunkter.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Du kan också köra föregående kommando genom att använda det egna namnet för behållaren och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-typ:** *azurestorage*
* **--workload-type**: azurefileshare --workload-type : azurefileshare --workload-type : *azurefileshare* --

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för stoppskyddsåtgärden. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Stoppa skyddet utan att behålla återställningspunkter

Om du vill stoppa skyddet utan att behålla återställningspunkter använder du [az backup-skyddet inaktivera](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet med alternativet **delete-backup-data** inställt på **true**.

I följande exempel stoppas skyddet för filresursresursen *azurefiles* utan att behålla återställningspunkter.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Du kan också köra föregående kommando genom att använda det egna namnet för behållaren och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-typ:** *azurestorage*
* **--workload-type**: azurefileshare --workload-type : azurefileshare --workload-type : *azurefileshare* --

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en filresurs

Om du har stoppat skyddet för en Azure-filresurs men behållit återställningspunkter kan du återuppta skyddet senare. Om du inte behåller återställningspunkterna kan du inte återuppta skyddet.

Om du vill återuppta skyddet för filresursen definierar du följande parametrar:

* **--container-name**: Namnet på lagringskontot som är värd för filresursen. Om du vill hämta **namnet** eller **det egna namnet** på behållaren använder du kommandot för behållare för [az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Namnet på den filresurs som du vill återuppta skyddet för. Om du vill hämta **namnet** eller **det egna namnet** på det säkerhetskopierade objektet använder du kommandot för [säkerhetskopior.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--policy-name**: Namnet på den säkerhetskopieringsprincip som du vill återuppta skyddet för filresursen för.

I följande exempel används [cmdlet för az backup protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) för att återuppta skyddet för filresursresursen *azurefiles* med hjälp av säkerhetskopieringsprincipen *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Du kan också köra föregående kommando genom att använda det egna namnet för behållaren och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-typ:** *azurestorage*
* **--workload-type**: azurefileshare --workload-type : azurefileshare --workload-type : *azurefileshare* --

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för återupptarrråtgärden. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Avregistrera ett lagringskonto

Om du vill skydda dina filresurser i ett visst lagringskonto med hjälp av ett annat Recovery Services-valv stoppar du först [skyddet för alla filresurser](#stop-protection-on-a-file-share) i det lagringskontot. Avregistrera sedan kontot från valvet för återställningstjänster som för närvarande används för skydd.

Du måste ange ett behållarnamn för att avregistrera lagringskontot. Om du vill hämta **namnet** eller det **egna namnet** på behållaren använder du kommandot för behållare [för az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

I följande exempel avregistreras *lagringskontot afsaccount* från *azurefilesvault* med hjälp av [cmdlet för az-säkerhetskopieringsbehållaren.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister)

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Du kan också köra föregående cmdlet med hjälp av det egna namnet för behållaren genom att ange följande ytterligare parameter:

* **--backup-management-typ:** *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhetskopiering av Azure-filresurser](troubleshoot-azure-files.md).
