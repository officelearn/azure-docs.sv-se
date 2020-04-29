---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "73800141"
---
- Kopiera inte filer direkt till någon av de förskapade resurserna. Du måste skapa en mapp under resursen och sedan kopiera filer till den mappen.
- En mapp under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* är en behållare. Till exempel skapas behållare som *StorageAccount_BlockBlob/container* och *StorageAccount_PageBlob/container*.
- Varje mapp som skapas direkt under *StorageAccount_AzureFiles* översätts till en Azure-filresurs.
- Om du har ett befintligt Azure-objekt (till exempel en BLOB eller en fil) i molnet med samma namn som det objekt som ska kopieras skrivs filen i molnet om Data Box-enhet.
- Varje fil som skrivs till *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* resurser laddas upp som en Block-Blob och Page blob.
- Azure Blob Storage stöder inte kataloger. Om du skapar en mapp under mappen *StorageAccount_BlockBlob* skapas virtuella mappar i BLOB-namnet. För Azure Files underhålls den faktiska katalog strukturen.
- En tom katalogpartition (utan några filer) som skapats under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* mappar laddas inte upp.
- Om det uppstår några fel när data överförs till Azure skapas en fellogg på mål lagrings kontot. Sökvägen till den här fel loggen är tillgänglig när överföringen är klar och du kan granska loggen för att vidta lämpliga åtgärder. Ta inte bort data från källan utan att verifiera överförda data.
- Fil-metadata och NTFS-behörigheter bevaras inte när data överförs till Azure Files. Till exempel kommer det *senast ändrade attributet* för filerna inte att sparas när data kopieras.
