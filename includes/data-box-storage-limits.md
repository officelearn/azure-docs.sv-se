---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 036e99a193d539bdcb204b5b062de261c30dffc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244653"
---
I det här avsnittet beskrivs gränserna för Azure Storage-tjänsten och de nödvändiga namngivningskonventionerna för Azure-filer, Azure-blockblobar och Azure-sidblobar, beroende på databox-tjänsten. Granska lagringsgränserna noggrant och följ alla rekommendationer.

Den senaste informationen om Azure-lagringstjänstgränser och metodtips för namngivning av resurser, behållare och filer finns i:

- [Namnge och referera behållare](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blockera blobbar och sidblobbkonventioner](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Om det finns några filer eller kataloger som överskrider Azure Storage-tjänstgränserna, eller inte överensstämmer med Azure Files/Blob-namngivningskonventioner, får dessa filer eller kataloger inte plats i Azure Storage via Data Box-tjänsten.
