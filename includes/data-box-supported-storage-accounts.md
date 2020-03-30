---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "66242150"
---
Här är en lista över de lagringskonton som stöds och lagringstyperna för Data Box-enheten. En fullständig lista över alla olika typer av lagringskonton och deras fullständiga funktioner finns i [Typer av lagringskonton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Lagringskonto / Lagringstyper som stöds** | **Blockblob** |**Sidblob*** |**Azure-filer** |**Obs!**|
| --- | --- | -- | -- | -- |
| Klassisk standard | Y | Y | Y |
| Allmänt ändamål v1 Standard  | Y | Y | Y | Både varm och sval stöds.|
| Allmänt ändamål v1 Premium  |  | Y| | |
| Allmänt ändamål v2 Standard  | Y | Y | Y | Både varm och sval stöds.|
| Allmänt ändamål v2 Premium  |  |Y | | |
| Blob lagring Standard |Y | | |Både varm och sval stöds. |

\**- Data som överförs till sidblobar måste vara 512 byte justerade, till exempel virtuella hårddiskar.*

>[!NOTE]
> Konton för Azure Data Lake Storage Gen 2 stöds inte.
