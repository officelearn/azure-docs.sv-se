---
title: Uppdatera lagringsmål för Azure HPC-cache
description: Redigera Azure HPC-lagringsmål för cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866994"
---
# <a name="edit-storage-targets"></a>Redigera lagringsmål

Du kan ta bort eller ändra ett lagringsmål från cachens sida **Lagringsmål.**

## <a name="remove-a-storage-target"></a>Ta bort ett lagringsmål

Om du vill ta bort ett lagringsmål markerar du det i listan och klickar på knappen **Ta bort.**

Den här åtgärden tar bort lagringsmålsasociationen med det här Azure HPC-cachesystemet, men det ändrar inte backend-lagringssystemet. Om du till exempel använde en Azure Blob-lagringsbehållare finns behållaren och dess innehåll fortfarande kvar när du har tagit bort den från cacheminnet. Du kan lägga till behållaren i en annan Azure HPC-cache, lägga till den i cacheminnet igen eller ta bort den med Azure-portalen.

Alla filändringar som lagras i cacheminnet skrivs till backend-lagringssystemet innan lagringsmålet tas bort. Den här processen kan ta en timme eller mer om många ändrade data finns i cacheminnet.

## <a name="update-storage-targets"></a>Uppdatera lagringsmål

Du kan redigera lagringsmål för att ändra vissa av deras egenskaper. Olika egenskaper kan redigeras för olika typer av lagring:

* För Blob-lagringsmål kan du ändra namnområdessökvägen.

* För NFS-lagringsmål kan du ändra följande egenskaper:

  * Namnområdessökväg
  * Användningsmodell
  * Exportera
  * Exportera underkatalog

Du kan inte redigera ett lagringsmåls namn, typ eller backend-lagringssystem (Blob-behållare eller NFS-värdnamn/IP-adress). Om du behöver ändra dessa egenskaper tar du bort lagringsmålet och skapar en ersättning med det nya värdet.

Om du vill ändra ett lagringsmål klickar du på lagringsmålnamnet för att öppna informationssidan. Vissa fält på sidan kan redigeras.

![skärmbild av redigeringssidan för ett NFS-lagringsmål](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Uppdatera ett NFS-lagringsmål

För ett NFS-lagringsmål kan du uppdatera flera egenskaper. (Se skärmbilden ovan för en exempelredigeringssida.)

* **Användningsmodell** - Användningsmodellen påverkar hur cachen behåller data. Läs [Välj en användningsmodell](hpc-cache-add-storage.md#choose-a-usage-model) om du vill veta mer.
* **Sökväg till virtuellt namnområde** – Sökvägen som klienter använder för att montera det här lagringsmålet. Läs [Planera det aggregerade namnområdet](hpc-cache-namespace.md) för mer information.
* **NFS-exportsökväg** - Lagringssystemet exporteras som ska användas för den här namnområdessökvägen.
* **Underkatalogsökvägen** - Underkatalogen (under exporten) som ska associeras med den här namnområdessökvägen. Lämna det här fältet tomt om du inte behöver ange en underkatalog.

Varje namnområdessökväg behöver en unik kombination av export och underkatalog. Det vill säga, du kan inte göra två olika klientinriktade sökvägar till exakt samma katalog på backend-lagringssystemet.

När du har gjort ändringar klickar du på **OK** för att uppdatera lagringsmålet eller klickar på **Avbryt** om du vill ignorera ändringar.

## <a name="update-an-azure-blob-storage-target"></a>Uppdatera ett Azure Blob-lagringsmål

Med informationssidan för ett Blob-lagringsmål kan du ändra sökvägen till det virtuella namnområdet.

![skärmbild av redigeringssidan för ett blob-lagringsmål](media/hpc-cache-edit-storage-blob.png)

När du är klar klickar du på **OK** för att uppdatera lagringsmålet eller klickar på **Avbryt** om du vill ignorera ändringar.

## <a name="next-steps"></a>Nästa steg

* Läs [Lägg till lagringsmål](hpc-cache-add-storage.md) om du vill veta mer om de här alternativen.
* Läs [Planera det aggregerade namnområdet](hpc-cache-namespace.md) för fler tips om hur du använder virtuella sökvägar.
