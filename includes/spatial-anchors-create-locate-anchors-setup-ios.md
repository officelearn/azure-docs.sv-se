---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006422"
---
## <a name="setting-up-the-library"></a>Konfigurerar biblioteket

Anropa `Start()` för att göra det möjligt för din session att bearbeta miljö data.

Om du vill hantera händelser som aktive ras av `delegate` sessionen ställer du in sessionens egenskap till ett objekt, t. ex. vyn. Det här objektet måste implementera `SSCCloudSpatialAnchorSessionDelegate` protokollet.
