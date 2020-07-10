---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200315"
---
Här är en lista över de lagrings konton som stöds och lagrings typer för den Data Box-enhet enheten. En fullständig lista över alla typer av lagrings konton och deras fullständiga funktioner finns i [typer av lagrings konton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

I följande tabell visas de lagrings konton som stöds för import order.

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB*** |**Azure Files** |**Kommentarer**|
| --- | --- | -- | -- | -- |
| Klassisk standard | J | J | J |
| Generell användning v1-standard  | J | J | J | Både frekvent och låg frekvent stöds.|
| Generell användning v1 Premium  |  | J| | |
| Generell användning v2 standard  | J | J | J | Både frekvent och låg frekvent stöds.|
| Generell användning v2 Premium  |  |J | | |
| Blob Storage Standard |J | | |Både frekvent och låg frekvent stöds. |

\**-Data som laddats upp till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.*

I följande tabell visas de lagrings konton som stöds för export order.

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB*** |**Azure Files** |**Åtkomst nivåer som stöds**|
| --- | --- | -- | -- | -- |
| Klassisk standard | J | J | J | |
| Generell användning v1-standard  | J | J | J | Frekvent, låg frekvent|
| Generell användning v1 Premium  |  | J| | |
| Generell användning v2 standard  | J | J | J | Frekvent, låg frekvent|
| Generell användning v2 Premium  |  |J | | |
| Blob Storage Standard |J | | |Frekvent, låg frekvent |
| Blockera Blob Storage Premium |J | | |Frekvent, låg frekvent |
| Page Blob Storage Premium | |J | | |

> [!IMPORTANT]
> - För generella konton har Data Box-enhet inte stöd för kö-, tabell-, disk-och Azure Data Lake gen 2-lagrings typer.
> - Data Box-enhet har inte stöd för att lägga till blobar för Blob Storage och blockera Blob Storage konton.
> - Data Box-enhet stöder inte Premium File Storage-konton.
> - Data som överförs till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.
> - Högst 80 TB kan exporteras.
> - Fil historik och blob-ögonblicksbilder exporteras inte.


