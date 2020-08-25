---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006422"
---
## <a name="setting-up-the-library"></a>Konfigurerar biblioteket

Anropa `Start()` för att göra det möjligt för din session att bearbeta miljö data.

Om du vill hantera händelser som aktive ras av sessionen ställer du in `delegate` sessionens egenskap till ett objekt, t. ex. vyn. Det här objektet måste implementera `SSCCloudSpatialAnchorSessionDelegate` protokollet.
