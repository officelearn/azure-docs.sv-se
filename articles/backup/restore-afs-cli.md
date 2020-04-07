---
title: Återställa Azure-filresurser med Azure CLI
description: Lär dig hur du använder Azure CLI för att återställa säkerhetskopierade Azure-filresurser i Recovery Services-valvet
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757090"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Återställa Azure-filresurser med Azure CLI

Azure CLI ger en kommandoradsupplevelse för hantering av Azure-resurser. Det är ett bra verktyg för att skapa anpassad automatisering för att använda Azure-resurser. I den här artikeln beskrivs hur du återställer en hel filresurs eller specifika filer från en återställningspunkt som skapats av [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) med hjälp av Azure CLI. Du kan också utföra de här stegen med [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) eller [Azure Portal](backup-afs.md).

I slutet av den här artikeln får du lära dig hur du utför följande åtgärder med Azure CLI:

* Visa återställningspunkter för en säkerhetskopierad Azure-filresurs.
* Återställ en fullständig Azure-filresurs.
* Återställ enskilda filer eller mappar.

>[!NOTE]
> Azure Backup stöder nu återställning av flera filer eller mappar till originalet eller en alternativ plats med Azure CLI. Läs avsnittet [Återställ flera filer eller mappar till det här dokumentets ursprungliga eller alternativa platsavsnitt](#restore-multiple-files-or-folders-to-original-or-alternate-location) om du vill veta mer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du vill installera och använda CLI lokalt måste du köra Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta CLI-versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du redan har en Azure-filresurs som backas upp av Azure Backup. Om du inte har någon, se [Säkerhetskopiera Azure-filresurser med CLI](backup-afs-cli.md) för att konfigurera säkerhetskopiering för filresursen. I den här artikeln använder du följande resurser:

| Filresurs  | Lagringskonto | Region | Information                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount (afsaccount)*      | EastUS (östra) | Ursprunglig källa som säkerhetskopieras med hjälp av Azure Backup                 |
| *azurefiles1* | *afaccount1 (på andra)*      | EastUS (östra) | Målkälla som används för alternativ platsåterställning |

Du kan använda en liknande struktur för filresurserna för att prova de olika typer av återställningar som förklaras i den här artikeln.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Hämta återställningspunkter för Azure-filresursen

Använd cmdlet för [återställningspunkt för az säkerhetskopiering](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) för att visa alla återställningspunkter för den säkerhetskopierade filresursen.

I följande exempel hämtas listan över återställningspunkter för filresursresursen för *azurefiles* i *lagringskontot för afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Du kan också köra föregående cmdlet med hjälp av det egna namnet för behållaren och objektet genom att ange följande två ytterligare parametrar:

* **--backup-management-typ:** *azurestorage*
* **--workload-type**: azurefileshare --workload-type : azurefileshare --workload-type : *azurefileshare* --

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Resultatuppsättningen är en lista över återställningspunkter med tids- och konsekvensinformation för varje återställningspunkt.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

**Attributet Name** i utdata motsvarar återställningspunktnamnet som kan användas som ett värde för parametern **--rp-name** vid återställningsåtgärder.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Fullständig resursåterställning med hjälp av Azure CLI

Du kan använda det här återställningsalternativet för att återställa hela filresursen på den ursprungliga eller en annan plats.

Definiera följande parametrar för att utföra återställningsåtgärder:

* **--container-name**: Namnet på lagringskontot som är värd för den säkerhetskopierade ursprungliga filresursen. Om du vill hämta namnet eller det egna namnet på behållaren använder du kommandot för behållare för [az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Namnet på den säkerhetskopierade ursprungliga filresursen som du vill använda för återställningen. Om du vill hämta namnet eller det egna namnet på det säkerhetskopierade objektet använder du kommandot [för säkerhetskopior.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Återställa en fullständig resurs till den ursprungliga platsen

När du återställer till en ursprunglig plats behöver du inte ange målrelaterade parametrar. Endast **Lös konflikt** måste tillhandahållas.

I följande exempel används cmdlet för återställning av [az-återställningsåterställning](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) med återställningsläge inställt på *originallokalisering* för att återställa filresursresursen för *azurefiles* på den ursprungliga platsen. Du använder återställningspunkten 932883129628959823, som du erhöll i [Hämta återställningspunkter för Azure-filresursen:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för återställningen. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Återställa en fullständig resurs till en annan plats

Du kan använda det här alternativet för att återställa en filresurs till en alternativ plats och behålla den ursprungliga filresursen som den är. Ange följande parametrar för alternativ platsåterställning:

* **--target-storage-account**: Det lagringskonto som säkerhetskopieringsinnehållet återställs till. Mållagringskontot måste vara på samma plats som valvet.
* **--target-file-share**: Filresursen i mållagringskontot som säkerhetskopierade innehållet återställs till.
* **--target-folder**: Mappen under filresursen som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du värdena för målmappen som en tom sträng.
* **--resolve-konflikt:** Instruktion om det finns en konflikt med återställda data. Accepterar **Överskrivning** eller **Hoppa över**.

I följande exempel används [az backup restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) med återställningsläge som *alternativplats* för att återställa *azurefiles-filresursen* i kontot för *afsaccount-lagring* till filresursresursen *för azurefiles1* i lagringskontot *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för återställningen. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Återställning på objektnivå

Du kan använda det här återställningsalternativet för att återställa enskilda filer eller mappar på den ursprungliga eller en annan plats.

Definiera följande parametrar för att utföra återställningsåtgärder:

* **--container-name**: Namnet på lagringskontot som är värd för den säkerhetskopierade ursprungliga filresursen. Om du vill hämta namnet eller det egna namnet på behållaren använder du kommandot för behållare för [az-säkerhetskopiering.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--item-name**: Namnet på den säkerhetskopierade ursprungliga filresursen som du vill använda för återställningen. Om du vill hämta namnet eller det egna namnet på det säkerhetskopierade objektet använder du kommandot [för säkerhetskopior.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Ange följande parametrar för de objekt som du vill återställa:

* **SourceFilePath**: Den absoluta sökvägen till filen, som ska återställas i filresursen, som en sträng. Den här sökvägen är samma sökväg som används i [hämtningen av az-lagringsfiler](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) [eller az-lagringsfilen visar](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI-kommandon.
* **SourceFileType**: Välj om en katalog eller en fil ska vara markerad. Accepterar **katalog** eller **fil**.
* **ResolveConflict**: Instruktion om det finns en konflikt med återställda data. Accepterar **Överskrivning** eller **Hoppa över**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Återställa enskilda filer eller mappar till den ursprungliga platsen

Använd cmdlet för återställning av [az-återställningsåterställning](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) med återställningsläge inställt på *originallokalisering* för att återställa specifika filer eller mappar till sin ursprungliga plats.

I följande exempel återställs *filen RestoreTest.txt* på den ursprungliga platsen: filresursresursen *azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för återställningen. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Återställa enskilda filer eller mappar till en annan plats

Om du vill återställa specifika filer eller mappar till en annan plats använder du cmdleten [az backup restore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) med återställningsläge inställt på *alternatelocation* och anger följande målrelaterade parametrar:

* **--target-storage-account**: Det lagringskonto som säkerhetskopieringsinnehållet återställs till. Mållagringskontot måste vara på samma plats som valvet.
* **--target-file-share**: Filresursen i mållagringskontot som säkerhetskopierade innehållet återställs till.
* **--target-folder**: Mappen under filresursen som data återställs till. Om det säkerhetskopierade innehållet ska återställas till en rotmapp ger du målmappens värde som en tom sträng.

I följande exempel återställs filen *RestoreTest.txt* som ursprungligen fanns i filresursresursen *för azurefiles* till en alternativ plats: mappen *restoredata* i filresursresursen *azurefiles1* som finns i lagringskontot *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för återställningen. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Återställa flera filer eller mappar till ursprunglig eller alternativ plats

Om du vill utföra återställning för flera objekt skickar du värdet för parametern **source-file-path** som **utrymmesavgränsade** sökvägar för alla filer eller mappar som du vill återställa.

I följande exempel återställs *Filerna Restore.txt* och *AFS-testning report.docx* på sin ursprungliga plats.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Resultatet blir något som liknar:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

**Attributet Name** i utdata motsvarar namnet på det jobb som skapas av säkerhetskopieringstjänsten för återställningen. Om du vill spåra jobbets status använder du [jobbet az backup visa](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

Om du vill återställa flera objekt till en annan plats använder du kommandot ovan genom att ange målrelaterade parametrar som förklaras i avsnittet [Återställ enskilda filer eller mappar till ett alternativt platsavsnitt.](#restore-individual-files-or-folders-to-an-alternate-location)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar Azure-filresurssäkerhetskopior med Azure CLI](manage-afs-backup-cli.md).
