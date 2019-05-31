---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244564"
---
Här följer storlekarna på de Azure-objekt som kan skrivas. Se till att alla filer som har laddats upp överensstämmer med de här gränserna.

| Azure objekttyp | Standardgräns                                             |
|-------------------|-----------------------------------------------------------|
| Blockblob        | ~ 4,75 TiB                                                 |
| Sidblob         | 8 TiB <br> Varje fil som laddats upp sidan blobformatet måste vara justerad 512 byte (en integrerad flera), annars överföringen misslyckas. <br> VHD- och VHDX är 512 byte justerad. |
| Azure Files        | 1 TiB                                                      |
| Hanterade diskar     | 4 TiB <br> Mer information om storlek och begränsningar finns: <li>[Skalbarhetsmål för Standard SSD-enheter](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Skalbarhetsmål för Premium SSD: er](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Skalbarhetsmål för standardhårddiskar](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Priser och fakturering av hanterade diskar](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
