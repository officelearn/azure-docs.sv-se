---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: b871291a7645c43ff3c5c382905958d35331ce2a
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582656"
---
I det här avsnittet beskrivs begränsningarna för Azure Storage tjänst och de namngivnings konventioner som krävs för Azure Files, Azure block-blobbar och Azure Page-blobar, enligt vad som är tillämpligt för Azure Stack Edge/Data Box Gateway-tjänsten. Granska lagrings gränserna noggrant och följ alla rekommendationer.

Den senaste informationen om begränsningar för Azure Storage-tjänsten och metod tips för namngivning av resurser, behållare och filer finns på:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och Page BLOB-konventioner](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstens gränser eller inte uppfyller namngivningskonventionerna för Azure Files/blobar så är inte de här filerna eller katalogerna inmatade i Azure Storage via Azure Stack Edge/Data Box Gateway-tjänsten.