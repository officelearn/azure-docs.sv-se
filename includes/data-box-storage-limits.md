---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: 1935e6a95906473fdb4c1c25039cfbca5bace526
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202674"
---
I det här avsnittet beskrivs gränserna för Azure Storage tjänst och de namngivnings konventioner som krävs för Azure Files, Azure block-blobbar och Azure Page-blobbar, som är tillämpliga för Data Box-enhet-tjänsten. Granska lagrings gränserna noggrant och följ alla rekommendationer.

Den senaste informationen om begränsningar för Azure Storage-tjänsten och metod tips för namngivning av resurser, behållare och filer finns på:

- [Namnge och referera till behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och Page BLOB-konventioner](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstens gränser eller inte uppfyller Azure Files/BLOB-namngivnings konventionerna, matas dessa filer eller kataloger inte in i Azure Storage via Data Box-enhet-tjänsten.
