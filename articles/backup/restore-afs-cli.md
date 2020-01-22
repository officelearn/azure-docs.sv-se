---
title: Återställa Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att återställa säkerhets kopie rad Azure-filresurser i Recovery Services valvet
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 8d91031ff9ee0d4f32cc74ff7031d39ddab3e2b4
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294414"
---
# <a name="restore-azure-file-shares-with-cli"></a>Återställa Azure-filresurser med CLI

Kommando rads gränssnittet för Azure (CLI) tillhandahåller kommando tolken för att hantera Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. Den här artikeln förklarar hur du återställer en hel fil resurs eller vissa filer från en återställnings punkt som skapats av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) tjänsten med hjälp av Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) eller [Azure Portal](backup-afs.md).

I slutet av den här självstudien får du lära dig hur du utför följande åtgärder med Azure CLI:

* Visa återställnings punkter för en säkerhets kopie rad Azure-filresurs
* Återställa en fullständig Azure-filresurs
* Återställa enskilda filer eller mappar

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. För att hitta CLI-versionen `run az --version`. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har en Azure-filresurs som säkerhets kopie ras av Azure Backups tjänsten. Om du inte har någon kan du läsa [säkerhetskopiera Azure-filresurser med CLI](backup-afs-cli.md) för att konfigurera säkerhets kopiering för din fil resurs. I den här artikeln kommer vi att använda följande resurser:

| Filresurs  | Lagringskonto | Region | Information                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *migreringsåtgärden*  | *afsaccount*      | USAöstra | Original källan säkerhets kopie ras med Azure Backup                 |
| *azurefiles1* | *afaccount1*      | USAöstra | Mål källa som används för återställning av alternativ plats |

Du kan använda en liknande struktur för dina fil resurser för att testa de olika typerna av Restore som förklaras i det här dokumentet.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Hämta återställnings punkter för Azure-filresursen

Använd cmdleten [AZ backup recoverypoint List](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) för att visa en lista över alla återställnings punkter för den säkerhetskopierade fil resursen.

I följande exempel hämtar listan över återställnings punkter för *migreringsåtgärden* -filresursen i *afsaccount* Storage-kontot.

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

Du kan också köra cmdleten ovan med hjälp av "eget namn" för behållare och objekt genom att tillhandahålla följande två ytterligare parametrar:

* **--säkerhets kopiering-hantering-typ**: *azurestorage*
* **--arbets belastning-typ**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Resultat uppsättningen är en lista över återställnings punkter med tids-och konsekvens information för varje återställnings punkt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Namnattributet **i** utdata motsvarar återställnings punktens namn som kan användas som ett värde för parametern **--RP-Name** i återställnings åtgärder.

## <a name="full-share-recovery-using-azure-cli"></a>Fullständig delnings återställning med Azure CLI

Du kan använda det här alternativet för återställning för att återställa hela fil resursen på den ursprungliga platsen eller en annan plats.

Definiera följande parametrar för att utföra återställnings åtgärder:

* **--container-Name** är namnet på det lagrings konto som är värd för säkerhets kopian av den ursprungliga fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--objekt namnet** är namnet på den säkerhetskopierade original fil resurs som du vill använda för återställnings åtgärden. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

### <a name="restore-full-share-to-the-original-location"></a>Återställ fullständig resurs till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange mål-relaterade parametrar. Endast **konflikt vid matchning** måste anges.

I följande exempel används AZ Backup Restore [-azurefileshare-](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) cmdleten med återställnings läge inställt på *originallocation* för att återställa *migreringsåtgärden* -filresursen på den ursprungliga platsen med återställnings punkten 932883129628959823 som vi fick med att [Hämta återställnings punkter för Azure-filresursen](#fetch-recovery-points-for-the-azure-file-share):

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för återställnings åtgärden. Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-full-share-to-an-alternate-location"></a>Återställ fullständig resurs till en annan plats

Du kan använda det här alternativet för att återställa en fil resurs till en annan plats och behålla den ursprungliga fil resursen som den är. Ange följande parametrar för alternativ plats återställning:

* **--mål-Storage – konto**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **--mål-fildelning**: fil resursen i det mål lagrings konto som det säkerhetskopierade innehållet återställs till.
* **--** målmapp: mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp, ger du målmappens värden som en tom sträng.
* **--resolve-konflikt**: instruktioner om det finns en konflikt med återställda data. Accepterar **överskrivning** eller **Skip**.

I följande exempel används [AZ backup](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) Restore Restore-azurefileshare med Restore Mode som *alternatelocation* för att återställa *migreringsåtgärden* -filresursen i *afsaccount* -lagrings kontot till *azurefiles1* -filresursen i *afaccount1* lagrings konto.

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för återställnings åtgärden. Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Återställning på objekt nivå

Du kan använda det här alternativet för återställning för att återställa enskilda filer eller mappar på den ursprungliga platsen eller en annan plats.

Definiera följande parametrar för att utföra återställnings åtgärder:

* **--container-Name** är namnet på det lagrings konto som är värd för säkerhets kopian av den ursprungliga fil resursen. Om du vill hämta **namnet** eller det **egna namnet** på din behållare använder du kommandot [AZ backup container List](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--objekt-Name** är namnet på den säkerhetskopierade original fil resurs som du vill använda för återställningen. Om du vill hämta **namnet** eller det **egna namnet** på det säkerhetskopierade objektet använder du kommandot [AZ backup item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .

Ange följande parametrar för de objekt som du vill återställa:

* **SourceFilePath**: den absoluta sökvägen till filen som ska återställas inom fil resursen, som en sträng. Den här sökvägen är samma sökväg som används i [AZ Storage File Download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) eller [AZ Storage-filen show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI-kommandon.
* **SourceFileType**: Välj om en katalog eller en fil är markerad. Accepterar **katalog** eller **fil**.
* **ResolveConflict**: instruktion om det finns en konflikt med återställda data. Accepterar **överskrivning** eller **Skip**.

### <a name="restore-individual-filesfolders-to-the-original-location"></a>Återställa enskilda filer/mappar till den ursprungliga platsen

Använd AZ Backup Restore [-migreringsåtgärden-](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdleten med återställnings läge inställt på *originallocation* för att återställa vissa filer/mappar till sin ursprungliga plats.

I följande exempel återställs filen *"RestoreTest. txt"* på den ursprungliga platsen: *migreringsåtgärden* -fileshare.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för återställnings åtgärden. Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-individual-filesfolders-to-an-alternate-location"></a>Återställa enskilda filer/mappar till en annan plats

Om du vill återställa specifika filer/mappar till en annan plats använder du AZ Backup Restore [-migreringsåtgärden-](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdleten med återställnings läge inställt på *alternatelocation* och anger följande Target-relaterade parametrar:

* **--mål-Storage – konto**: det lagrings konto som det säkerhetskopierade innehållet återställs till. Mål lagrings kontot måste finnas på samma plats som valvet.
* **--mål-fildelning**: fil resursen i det mål lagrings konto som det säkerhetskopierade innehållet återställs till.
* **--** målmapp: mappen under den fil resurs som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du målmappens värde som en tom sträng.

I följande exempel återställs den *RestoreTest. txt* -fil som ursprungligen fanns i *migreringsåtgärden* -fileshare till en alternativ plats: *mappen restoredata* i *azurefiles1* -fileshare som finns i *afaccount1* lagrings konto.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Namnattributet **i** utdata motsvarar namnet på det jobb som skapas av säkerhets kopierings tjänsten för återställnings åtgärden. Om du vill spåra jobbets status använder du [AZ backup Job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar säkerhets kopior av Azure-filresurser med Azure CLI](manage-afs-backup-cli.md)
