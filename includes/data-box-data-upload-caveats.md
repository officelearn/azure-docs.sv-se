---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73800141"
---
- Kopiera inte filer direkt till någon av de förskapade resurserna. Du måste skapa en mapp under resursen och sedan kopiera filer till den mappen.
- En mapp under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* är en behållare. Behållare skapas till exempel som *StorageAccount_BlockBlob/behållare* och *StorageAccount_PageBlob/behållare*.
- Varje mapp som skapas direkt under *StorageAccount_AzureFiles* översätts till en Azure File Share.
- Om du har ett befintligt Azure-objekt (till exempel en blob eller en fil) i molnet med samma namn som objektet som kopieras, skriver Data Box över filen i molnet.
- Varje fil som skrivs in i *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* aktier laddas upp som ett blockblob respektive sidblob.
- Azure blob storage stöder inte kataloger. Om du skapar en mapp under *mappen StorageAccount_BlockBlob* skapas virtuella mappar i blobnamnet. För Azure-filer underhålls den faktiska katalogstrukturen.
- Alla tomma kataloghierarkier (utan några filer) som skapas under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* mappar överförs inte.
- Om det finns några fel när du överför data till Azure skapas en fellogg i mållagringskontot. Sökvägen till den här felloggen är tillgänglig när överföringen är klar och du kan granska loggen för att vidta korrigerande åtgärder. Ta inte bort data från källan utan att verifiera de överförda data.
- Filmetadata och NTFS-behörigheter bevaras inte när data överförs till Azure-filer. Det senast *ändrade attributet* för filerna sparas till exempel inte när data kopieras.
