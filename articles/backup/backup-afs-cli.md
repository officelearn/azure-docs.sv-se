---
title: Säkerhetskopiera Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att säkerhetskopiera Azure-filresurser i Recovery Services Vault
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844049"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Säkerhetskopiera Azure-filresurser med CLI

Azure-kommandoradsgränssnittet (CLI) ger en kommandoradsupplevelse för hantering av Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. I den här artikeln beskrivs hur du säkerhetskopierar Azure-filresurser med Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) eller [Azure Portal](backup-afs.md).

I slutet av den här självstudien får du lära dig hur du utför nedanstående åtgärder med Azure CLI:

* Skapa ett Recovery Services-valv
* Aktivera säkerhetskopiering för Azure-filresurser
* Utlösa en säkerhetskopiering på begäran för filresurser

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. För att hitta `run az --version`CLI-versionen, . Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Skapa ett Recovery Services Vault

Ett återställningstjänstvalv är en entitet som ger dig en konsoliderad vy- och hanteringskapacitet för alla säkerhetskopior. När säkerhetskopieringsjobbet för en skyddad resurs körs, skapas en återställningspunkt i Recovery Services-valvet. Du kan sedan använda någon av dessa återställningspunkter för att återställa data till en given tidpunkt.

Så här skapar du ett valv för återställningstjänster:

1. Ett valv placeras i en resursgrupp. Om du inte har en befintlig resursgrupp skapar du en ny med [az-gruppge](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . I den här självstudien skapar vi den nya resursgruppen *azurefiles* i regionen östra USA.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Använd [az backup valv skapa](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet för att skapa valvet. Ange samma plats för valvet som användes för resursgruppen.

    I följande exempel skapas ett valv för återställningstjänster med namnet *azurefilesvault* i regionen östra USA.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Ange vilken typ av redundans som ska användas för arkivlagringen. Du kan använda [lokalt redundant lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) eller [geoupptundsst lagring](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    I följande exempel anges alternativet lagringsredundans för *azurefilesvault* till **Georedundant** med hjälp av uppsättning cmdlet för säkerhetskopiering av [az-säkerhetskopieringsvalv.](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set)

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    För att kontrollera om valvet har skapats kan du använda [az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) cmdlet för att få information om ditt valv. I följande exempel visas information om *azurefilesvault* som vi skapade i stegen ovan.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Utdata kommer att likna följande svar:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Aktivera säkerhetskopiering för Azure-filresurser

Det här avsnittet förutsätter att du redan har en Azure-filresurs som du vill konfigurera säkerhetskopiering för. Om du inte har en, skapa en Azure-filresurs med kommandot [az storage share create.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Om du vill aktivera säkerhetskopiering för filresurser måste du skapa en skyddsprincip som definierar när ett säkerhetskopieringsjobb körs och hur länge återställningspunkter lagras. Du kan skapa en princip för säkerhetskopiering med hjälp av [az backup-principen skapa](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

I följande exempel används [az backup protection enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) cmdlet för att aktivera säkerhetskopiering för *azurefiles-filresursen* i *lagringskontot för afsaccount* med hjälp av säkerhetskopieringsprincipen *schema 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för aktivera **säkerhetskopiering.** Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Utlösa en säkerhetskopiering på begäran för filresurs

Om du vill utlösa en säkerhetskopiering på begäran för filresursen i stället för att vänta på att säkerhetskopieringsprincipen ska köra jobbet vid den schemalagda tiden använder du cmdlet för [säkerhetskopieringsskydd för az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

Du måste definiera följande parametrar för att utlösa en säkerhetskopiering på begäran:

* **--container-namn** är namnet på lagringskontot som är värd för filresursen. Om du vill hämta **namnet** eller **det egna namnet** på behållaren använder du kommandot för behållare för [az-säkerhetskopiering.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name** är namnet på den filresurs som du vill utlösa en säkerhetskopiering på begäran. Om du vill hämta **namnet** eller **det egna namnet** på det säkerhetskopierade objektet använder du kommandot för [säkerhetskopior.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--retain-until** anger datumet tills du vill behålla återställningspunkten. Värdet ska anges i UTC-tidsformat (dd-mm-yyyy).

I följande exempel utlöses en säkerhetskopiering på begäran för *azuresfiles* fileshare i *kontot för afsaccount-lagring* med kvarhållning till *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för din åtgärd för säkerhetskopiering på begäran. Om du vill spåra status för ett jobb använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer Azure-filresurser med CLI](restore-afs-cli.md)
* Lär dig hur du [hanterar Azure-fildelningsakukups med CLI](manage-afs-backup-cli.md)
