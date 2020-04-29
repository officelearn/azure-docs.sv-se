---
title: Säkerhetskopiera Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att säkerhetskopiera Azure-filresurser i Recovery Services valvet
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844049"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Säkerhetskopiera Azure-filresurser med CLI

Kommando rads gränssnittet för Azure (CLI) tillhandahåller kommando tolken för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. Den här artikeln beskriver hur du säkerhetskopierar Azure-filresurser med Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) eller [Azure Portal](backup-afs.md).

I slutet av den här självstudien får du lära dig hur du utför följande åtgärder med Azure CLI:

* Skapa ett Recovery Services-valv
* Aktivera säkerhets kopiering för Azure-filresurser
* Utlös en säkerhets kopiering på begäran för fil resurser

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. För att hitta CLI-versionen `run az --version`,. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services-valv

Ett Recovery Service-valv är en entitet som ger dig en samlad vy och hanterings funktion för alla säkerhets kopierings objekt. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Följ de här stegen för att skapa ett Recovery Services-valv:

1. Ett valv placeras i en resurs grupp. Om du inte har en befintlig resurs grupp skapar du en ny med [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . I den här självstudien skapar vi den nya resurs gruppen *migreringsåtgärden* i regionen USA, östra.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Använd [AZ Backup Vault Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet för att skapa valvet. Ange samma plats för valvet som användes för resurs gruppen.

    I följande exempel skapas ett Recovery Services-valv med namnet *azurefilesvault* i regionen USA, östra.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Ange vilken typ av redundans som ska användas för valv lagringen. Du kan använda [Lokalt Redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) eller [Geo-redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    I följande exempel anges ett alternativ för redundans för *azurefilesvault* till **ett** interaktivt med [AZ Backup Vault backup-Properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Du kan kontrol lera om valvet har skapats genom att använda [AZ Backup Vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) cmdlet för att få information om valvet. I följande exempel visas information om *azurefilesvault* som vi skapade i stegen ovan.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Utdata kommer att likna följande svar:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Aktivera säkerhets kopiering för Azure-filresurser

Det här avsnittet förutsätter att du redan har en Azure-filresurs som du vill konfigurera säkerhets kopiering för. Om du inte har ett kan du skapa en Azure-filresurs med hjälp av kommandot [AZ Storage Share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Om du vill aktivera säkerhets kopiering för fil resurser måste du skapa en skydds princip som definierar när ett säkerhets kopierings jobb körs och hur länge återställnings punkter lagras. Du kan skapa en säkerhets kopierings princip med hjälp av [AZ säkerhets kopierings princip skapa](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

I följande exempel används [AZ backup Protection Enable-to-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) för att aktivera säkerhets kopiering för *migreringsåtgärden* -filresursen i *afsaccount* -lagrings kontot med hjälp av säkerhets kopierings principen för *schema 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för åtgärden **Aktivera säkerhets kopiering** . Om du vill spåra status för jobbet använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Utlös en säkerhets kopiering på begäran för fil resurs

Om du vill utlösa en säkerhets kopiering på begäran för fil resursen i stället för att vänta på att säkerhets kopierings policyn ska köra jobbet vid den schemalagda tiden använder du cmdleten [AZ backup Protection backup-Now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) .

Du måste definiera följande parametrar för att utlösa en säkerhets kopiering på begäran:

* **--container-Name** är namnet på det lagrings konto som är värd för fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--objekt-Name** är namnet på den fil resurs som du vill aktivera en säkerhets kopiering på begäran för. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Behåll-tills** anger det datum då du vill behålla återställnings punkten. Värdet ska anges i UTC-timmarsformat (dd-mm-åååå).

I följande exempel utlöses en säkerhets kopiering på begäran för *azuresfiles* -fileshare i *afsaccount* Storage-kontot med kvarhållning till *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för åtgärden "säkerhets kopiering på begäran". Om du vill spåra status för ett jobb använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer Azure-filresurser med CLI](restore-afs-cli.md)
* Lär dig hur du [hanterar Azure File Share-ackups med CLI](manage-afs-backup-cli.md)
