---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244564"
---
Här är storleken på Azure-objekt som kan skrivas. Kontrollera att alla filer som överförs överensstämmer med dessa gränser.

| Azure-objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> Varje fil som laddas upp i sidblobbformat måste vara 512 byte justerade (en integrerad multipel), annars misslyckas överföringen. <br> VHD och VHDX är 512 byte justerade. |
| Azure Files        | 1 TiB                                                      |
| Hanterade diskar     | 4 TiB <br> Mer information om storlek och begränsningar finns i: <li>[Skalbarhetsmål för standard-SSD-enheter](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Skalbarhetsmål för Premium SSD-enheter](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Skalbarhetsmål för standard-hårddiskar](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Prissättning och fakturering av hanterade diskar](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
