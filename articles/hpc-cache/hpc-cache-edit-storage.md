---
title: Uppdatera lagrings mål för Azure HPC cache
description: Redigera lagrings mål för Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/02/2020
ms.author: v-erkel
ms.openlocfilehash: f11e12c4f30977514e04b09c7e1c3012eb7888a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092464"
---
# <a name="edit-storage-targets"></a>Redigera lagringsmål

Du kan ta bort eller ändra ett lagrings mål från sidan **lagrings mål** Portal för cache eller med hjälp av Azure CLI.

> [!TIP]
> I [Hantera Azure HPC cache-video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) visas hur du redigerar ett lagrings mål i Azure Portal.

## <a name="remove-a-storage-target"></a>Ta bort ett lagrings mål

### <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill ta bort ett lagrings mål markerar du det i listan och klickar på knappen **ta bort** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Använd [AZ HPC-cache Storage – Target Remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) för att ta bort ett lagrings mål från cachen.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Den här åtgärden tar bort mål associationen för lagrings målet med det här Azure HPC cache-systemet, men ändrar inte Server dels lagrings systemet. Om du till exempel använde en Azure Blob Storage-behållare finns behållaren och dess innehåll fortfarande kvar när du har tagit bort den från cachen. Du kan lägga till behållaren i en annan Azure HPC-cache, lägga till den i cacheminnet igen eller ta bort den med Azure Portal.

Alla fil ändringar som lagras i cacheminnet skrivs till Server dels lagrings systemet innan lagrings målet tas bort. Den här processen kan ta en timme eller mer om mycket ändrade data finns i cacheminnet.

## <a name="update-storage-targets"></a>Uppdatera lagrings mål

Du kan redigera lagrings mål för att ändra vissa av egenskaperna. Olika egenskaper kan redige ras för olika typer av lagring:

* För Blob Storage-mål kan du ändra namn områdets sökväg.

* För NFS-lagrings mål kan du ändra följande egenskaper:

  * Sökväg till namnrymd
  * Användnings modell
  * Exportera
  * Exportera under Katalog

Du kan inte redigera ett lagrings måls namn, typ eller backend-filsystem (BLOB container eller NFS-värdnamn/IP-adress). Om du behöver ändra dessa egenskaper tar du bort lagrings målet och skapar en ersättning med det nya värdet.

I Azure Portal kan du se vilka fält som kan redige ras genom att klicka på lagrings mål namnet och öppna dess informations sida. Du kan också ändra lagrings mål med Azure CLI.

![skärm bild av redigerings sidan för ett NFS-lagrings mål](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Uppdatera ett NFS-lagrings mål

Du kan uppdatera flera egenskaper för ett NFS-lagrings mål. (Se skärm bilden ovan för ett exempel på redigerings sida.)

* **Användnings modell** – användnings modellen påverkar hur cachen behåller data. Läs [Välj en användnings modell](hpc-cache-add-storage.md#choose-a-usage-model) om du vill veta mer.
* **Sökväg till virtuellt namn område** – den sökväg som klienter använder för att montera det här lagrings målet. Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för mer information.
* **NFS-export Sök väg** – lagrings system exporten som ska användas för namn områdets sökväg.
* **Sökväg** till under katalog – under katalogen (under exporten) som ska associeras med den här namn områdets sökväg. Lämna fältet tomt om du inte behöver ange en under katalog.

Varje namn områdes Sök väg behöver en unik kombination av export och under katalog. Det innebär att du inte kan göra två olika klient Sök vägar till exakt samma katalog på backend-lagrings systemet.

### <a name="portal"></a>[Portal](#tab/azure-portal)

När du har gjort ändringarna klickar du på **OK** för att uppdatera lagrings målet, eller klicka på **Avbryt** om du vill ignorera ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Använd kommandot [AZ NFS-Storage-Target](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) för att ändra användnings modellen, sökvägen till den virtuella namn rymden och NFS-export-eller under katalogs värden för ett lagrings mål.

* Använd alternativet för att ändra användnings modellen ``--nfs3-usage-model`` . Exempel: ``--nfs3-usage-model WRITE_WORKLOAD_15``

* Använd alternativet för att ändra namn områdets sökväg, exportera eller exportera under katalog ``--junction`` .

  ``--junction``Parametern använder följande värden:

  * ``namespace-path``-Klientens virtuella fil Sök väg
  * ``nfs-export``– Lagrings system exporten som ska associeras med den klient-riktade sökvägen
  * ``target-path``(valfritt) – en under katalog till exporten, om det behövs

  Exempel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Cache-namn, lagrings mål namn och resurs grupp krävs i alla uppdaterings kommandon.

Kommando exempel: <!-- having problem testing this -->

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
    --name rivernfs0 --resource-group doc-rg0619 \
    --nfs3-usage-model READ_HEAVY_INFREQ
```

Om cachen har stoppats eller inte är i felfritt tillstånd, gäller uppdateringen när cachen är felfri.

---

## <a name="update-an-azure-blob-storage-target"></a>Uppdatera ett Azure Blob Storage-mål

För ett Blob Storage-mål kan du ändra sökvägen till den virtuella namn rymden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

På informations sidan för ett Blob Storage-mål kan du ändra sökvägen till den virtuella namn rymden.

![skärm bild av redigerings sidan för ett Blob Storage-mål](media/hpc-cache-edit-storage-blob.png)

När du är färdig klickar du på **OK** för att uppdatera lagrings målet, eller klicka på **Avbryt** om du vill ignorera ändringarna.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Använd [AZ HPC-cache Blob-Storage-Target Update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update) för att uppdatera namn områdets sökväg för ett mål.

```azurecli
az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --storage-account "/subscriptions/<subscription_ID>/resourceGroups/erinazcli/providers/Microsoft.Storage/storageAccounts/rg"  \
    --container-name "container-name" --virtual-namespace-path "/new-path"
```

Om cachen har stoppats eller inte är i felfritt tillstånd kommer uppdateringen att gälla när cachen är felfri.

---

## <a name="next-steps"></a>Nästa steg

* Läs [Lägg till lagrings mål](hpc-cache-add-storage.md) för mer information om de här alternativen.
* Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för fler tips om hur du använder virtuella sökvägar.
