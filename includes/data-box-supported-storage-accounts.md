---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: f230fc247c6ad94bfdfb3cdbc0f897d66313b039
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696509"
---
Här är en lista över de lagrings konton som stöds och lagrings typer för den Data Box-enhet enheten. En fullständig lista över alla typer av lagrings konton och deras fullständiga funktioner finns i [typer av lagrings konton](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Lagrings konto/lagrings typer som stöds** | **Blockblob** |**Sid-BLOB*** |**Azure Files** |**Anteckningar**|
| --- | --- | -- | -- | -- |
| Klassisk standard | J | J | J |
| Generell användning v1-standard  | J | J | J | Både frekvent och låg frekvent stöds.|
| Generell användning v1 Premium  |  | J| | |
| Generell användning v2 standard  | J | J | J | Både frekvent och låg frekvent stöds.|
| Generell användning v2 Premium  |  |J | | |
| Blob Storage Standard |J | | |Både frekvent och låg frekvent stöds. |

\**-Data som laddats upp till sid-blobar måste vara 512 byte justerade, till exempel virtuella hård diskar.*
