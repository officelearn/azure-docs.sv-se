---
title: Hantera säkerhets kopior av Azure-filresurser med CLI
description: Lär dig hur du använder Azure CLI för att hantera och övervaka Azure-filresurser som har säkerhetskopierats av Azure Backups tjänsten.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294479"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Hantera säkerhets kopior av Azure-filresurser med Azure CLI

Kommando rads gränssnittet för Azure (CLI) tillhandahåller kommando tolken för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. Den här artikeln förklarar hur du utför uppgifterna nedan för att hantera och övervaka de Azure-filresurser som säkerhets kopie ras av [Azure backups tjänsten](https://docs.microsoft.com/azure/backup/backup-overview). Du kan också utföra de här stegen med [Azure Portal](https://portal.azure.com/).

* [Övervaka jobb](#monitor-jobs)
* [Ändra princip](#modify-policy)
* [Stoppa skyddet av en fil resurs](#stop-protection-on-a-file-share)
* [Återuppta skyddet av en fil resurs](#resume-protection-on-a-file-share)
* [Avregistrera ett lagrings konto](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta CLI-versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du redan har en Azure-filresurs som har säkerhetskopierats av [Azure backups](https://docs.microsoft.com/azure/backup/backup-overview) tjänsten. Om du inte har någon kan du läsa [säkerhetskopiera Azure-filresurser med CLI](backup-afs-cli.md) för att konfigurera säkerhets kopiering för dina fil resurser. I den här artikeln använder vi följande resurser:

* **Resurs grupp**: *migreringsåtgärden*
* **RecoveryServicesVault**: *azurefilesvault*
* **Lagrings konto**: *afsaccount*
* **Fil resurs**: *migreringsåtgärden*

## <a name="monitor-jobs"></a>Övervaka jobb

När du utlöser säkerhets kopierings-eller återställnings åtgärder skapar säkerhets kopierings tjänsten ett jobb för spårning. Om du vill övervaka slutförda eller pågående jobb använder du cmdleten [AZ backup Job List](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) . Med CLI kan du också [pausa ett pågående jobb](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) eller [vänta tills ett jobb har slutförts](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait).

I följande exempel visas status för säkerhets kopierings jobb för *azurefilesvault* Recovery Services-valvet:

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

Du kan ändra en säkerhets kopierings princip om du vill ändra säkerhets kopierings frekvensen eller kvarhållningsintervallet med [AZ säkerhets kopierings princip](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy).

Definiera följande parametrar för att ändra principen:

* **--container-Name** är namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Name** är namnet på den fil resurs som du vill ändra principen för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--princip-Name** är namnet på den säkerhets kopierings princip som du vill ange för fil resursen. Du kan använda [AZ säkerhets kopierings princip lista](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) för att visa alla principer för ditt valv.

I följande exempel anges *schedule2* säkerhets kopierings policy för *migreringsåtgärden* -filresursen i *afsaccount* Storage-kontot.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Du kan också köra kommandot ovan med hjälp av "användarvänliga namn" för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för åtgärden "ändra princip". Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Stoppa skyddet av en fil resurs

Det finns två sätt att sluta skydda Azure-filresurser:

* Stoppa alla framtida säkerhets kopierings jobb och *ta bort* alla återställnings punkter
* Stoppa alla framtida säkerhets kopierings jobb men *lämna* återställnings punkterna

Det kan finnas en kostnad för att lämna återställnings punkterna i lagret, eftersom de underliggande ögonblicks bilderna som skapats av Azure Backup bevaras. Fördelen med att lämna återställnings punkterna är dock möjligheten att återställa fil resursen senare, om så önskas. Information om kostnaden för att lämna återställnings punkterna finns i [pris informationen](https://azure.microsoft.com/pricing/details/storage/files). Om du väljer att ta bort alla återställningspunkter kan du inte återställa filresursen.

Definiera följande parametrar för att stoppa skyddet av fil resursen:

* **--container-Name** är namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--objekt-Name** är namnet på den fil resurs som du vill stoppa skyddet för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="stop-protection-and-retain-recovery-points"></a>Stoppa skyddet och behåll återställnings punkter

Om du vill stoppa skyddet och behålla data använder du [AZ backup Protection Disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

Exemplet nedan stoppar skyddet för *migreringsåtgärden* -filresursen men behåller alla återställnings punkter.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Du kan också köra kommandot ovan genom att använda det egna namnet för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för åtgärden "stoppa skydd". Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Stoppa skyddet utan att behålla återställnings punkter

Om du vill stoppa skyddet utan att behålla återställnings punkter använder du [säkerhets kopierings skydd inaktivera](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet med e **-postsäkerhetskopiering-data alternativ** inställt på **True**.

I följande exempel stoppas skyddet för *migreringsåtgärden* -filresursen utan att återställnings punkterna bevaras:

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Du kan också köra kommandot ovan med hjälp av "eget namn" för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Återuppta skyddet av en fil resurs

Om du har stoppat skyddet för en Azure-filresurs men behåller återställnings punkter kan du återuppta skyddet senare. Om du inte behåller återställnings punkterna kan du inte återuppta skyddet.

Definiera följande parametrar för att återuppta skyddet av fil resursen:

* **--container-Name** är namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--objekt namn** är namnet på den fil resurs som du vill återuppta skyddet för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--princip namn** är namnet på den säkerhets kopierings princip för vilken du vill återuppta skyddet av fil resursen.

I följande exempel används cmdleten [AZ backup Protection Resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) för att återuppta skyddet av fil resursen *migreringsåtgärden* med *schedule1* säkerhets kopierings princip.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Du kan också köra kommandot ovan med hjälp av "eget namn" för behållaren och objektet genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för åtgärden "återuppta skydd". Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Avregistrera ett lagrings konto

Om du vill skydda dina fil resurser i ett visst lagrings konto med ett annat Recovery Services-valv måste du först [stoppa skyddet för alla fil resurser](#stop-protection-on-a-file-share) i det lagrings kontot. Avregistrera sedan kontot från Recovery Services-valvet som för närvarande används för skydd.

Du måste ange ett behållar namn för att avregistrera lagrings kontot. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .

I följande exempel avregistreras lagrings kontot för *afsaccount* från *azurefilesvault* med hjälp av cmdleten [AZ backup deregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) .

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Du kan också köra cmdleten ovan med hjälp av "eget namn" för behållaren genom att tillhandahålla följande ytterligare parameter:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Felsöka säkerhets kopierings-/återställnings fel för Azure-filresurser](troubleshoot-azure-files.md)
