---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66242150"
---
Här är en lista över lagringskontona som stöds och lagringstyper för Data Box-enhet. En fullständig lista över alla olika typer av lagringskonton och deras fullständiga funktioner Se [typer av lagringskonton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Storage-konto / stöds lagringstyper** | **Blockblob** |**Sidblob*** |**Azure filer** |**Anteckningar**|
| --- | --- | -- | -- | -- |
| Klassiska Standard | Y | Y | Y |
| General-Purpose v1-Standard  | Y | Y | Y | Frekvent och lågfrekvent stöds.|
| General-Purpose v1 Premium  |  | Y| | |
| Gpv2 Standard  | Y | Y | Y | Frekvent och lågfrekvent stöds.|
| Generell användning v2 Premium  |  |Y | | |
| BLOB-lagring Standard |Y | | |Frekvent och lågfrekvent stöds. |

\* *-Data som överförs till sidblobar måste vara 512 byte justerad, till exempel virtuella hårddiskar.*

>[!NOTE]
> Azure Data Lake Storage Gen 2-konton stöds inte.
