---
title: Uppdatera lagrings mål för Azure HPC cache
description: Redigera lagrings mål för Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rohogue
ms.openlocfilehash: 757fbc166687110b9061ab95e96b0182e0ad3774
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622773"
---
# <a name="edit-storage-targets"></a>Redigera lagrings mål

Du kan redigera lagrings mål för att ändra vissa av egenskaperna. Olika egenskaper kan redige ras för olika lagrings typer:

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
