---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244612"
---
- Inte kopiera filerna direkt till någon av de införande resurserna. Du måste skapa en mapp under resursen och kopierar sedan filerna till mappen.
- En mapp under den *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* är en behållare. Exempelvis behållare skapas som *StorageAccount_BlockBlob/behållare* och *StorageAccount_PageBlob/behållare*.
- Varje mapp som skapas direkt under *StorageAccount_AzureFiles* översätts till en Azure-filresurs.
- Om du har ett befintligt Azure objekt (till exempel en blob eller en fil) i molnet med samma namn som det objekt som ska kopieras skrivs Data Box till filen i molnet.
- Alla filer som skrivits till *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* resurser överförs som en blockblob och sidblob respektive.
- Azure blob storage stöder inte kataloger. Om du skapar en mapp under den *StorageAccount_BlockBlob* mapp, skapas virtuella mappar i blobnamnet. För Azure Files bevaras faktiska katalogstruktur.
- Eventuella tomma Kataloghierarki (utan några filer) som skapats under *StorageAccount_BlockBlob* och *StorageAccount_PageBlob* mappar inte överförts.
- Om det finns några fel när du överför data till Azure, skapas en fellogg i mållagringskontot. Sökvägen till den här loggen är tillgänglig när överföringen är klar och du kan granska loggen för att vidta åtgärder. Ta inte bort data från källan utan att verifiera överförda data.
