---
title: Uppdatera lagrings mål för Azure HPC cache
description: Redigera lagrings mål för Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/08/2020
ms.author: rohogue
ms.openlocfilehash: 5635bfc6ea5faea41b125037c76c0b8635e0f528
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75866994"
---
# <a name="edit-storage-targets"></a>Redigera lagringsmål

Du kan ta bort eller ändra ett lagrings mål från sidan **lagrings mål** för cacheminnet.

## <a name="remove-a-storage-target"></a>Ta bort ett lagrings mål

Om du vill ta bort ett lagrings mål markerar du det i listan och klickar på knappen **ta bort** .

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

Om du vill ändra ett lagrings mål klickar du på namnet på lagrings målet för att öppna dess informations sida. Vissa fält på sidan kan redige ras.

![skärm bild av redigerings sidan för ett NFS-lagrings mål](media/hpc-cache-edit-storage-nfs.png)

## <a name="update-an-nfs-storage-target"></a>Uppdatera ett NFS-lagrings mål

Du kan uppdatera flera egenskaper för ett NFS-lagrings mål. (Se skärm bilden ovan för ett exempel på redigerings sida.)

* **Användnings modell** – användnings modellen påverkar hur cachen behåller data. Läs [Välj en användnings modell](hpc-cache-add-storage.md#choose-a-usage-model) om du vill veta mer.
* **Sökväg till virtuellt namn område** – den sökväg som klienter använder för att montera det här lagrings målet. Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för mer information.
* **NFS-export Sök väg** – lagrings system exporten som ska användas för namn områdets sökväg.
* **Sökväg** till under katalog – under katalogen (under exporten) som ska associeras med den här namn områdets sökväg. Lämna fältet tomt om du inte behöver ange en under katalog.

Varje namn områdes Sök väg behöver en unik kombination av export och under katalog. Det innebär att du inte kan göra två olika klient Sök vägar till exakt samma katalog på backend-lagrings systemet.

När du har gjort ändringarna klickar du på **OK** för att uppdatera lagrings målet, eller klicka på **Avbryt** om du vill ignorera ändringarna.

## <a name="update-an-azure-blob-storage-target"></a>Uppdatera ett Azure Blob Storage-mål

På informations sidan för ett Blob Storage-mål kan du ändra sökvägen till den virtuella namn rymden.

![skärm bild av redigerings sidan för ett Blob Storage-mål](media/hpc-cache-edit-storage-blob.png)

När du är färdig klickar du på **OK** för att uppdatera lagrings målet, eller klicka på **Avbryt** om du vill ignorera ändringarna.

## <a name="next-steps"></a>Nästa steg

* Läs [Lägg till lagrings mål](hpc-cache-add-storage.md) för mer information om de här alternativen.
* Läs [planera det sammanställda namn området](hpc-cache-namespace.md) för fler tips om hur du använder virtuella sökvägar.
