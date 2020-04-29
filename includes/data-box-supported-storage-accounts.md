---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "66242150"
---
Här är en lista över de lagrings konton som stöds och lagrings typer för den Data Box-enhet enheten. En fullständig lista över alla typer av lagrings konton och deras fullständiga funktioner finns i [typer av lagrings konton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB*** |**Azure Files** |**Obs!**|
| --- | --- | -- | -- | -- |
| Klassisk standard | Y | Y | Y |
| Generell användning v1-standard  | Y | Y | Y | Både frekvent och låg frekvent stöds.|
| Generell användning v1 Premium  |  | Y| | |
| Generell användning v2 standard  | Y | Y | Y | Både frekvent och låg frekvent stöds.|
| Generell användning v2 Premium  |  |Y | | |
| Blob Storage Standard |Y | | |Både frekvent och låg frekvent stöds. |

\**-Data som laddats upp till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.*

>[!NOTE]
> Konton för Azure Data Lake Storage Gen 2 stöds inte.
