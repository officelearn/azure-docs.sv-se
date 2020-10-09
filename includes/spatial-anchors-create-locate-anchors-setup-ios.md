---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006422"
---
## <a name="setting-up-the-library"></a>Konfigurerar biblioteket

Anropa `Start()` för att göra det möjligt för din session att bearbeta miljö data.

Om du vill hantera händelser som aktive ras av sessionen ställer du in `delegate` sessionens egenskap till ett objekt, t. ex. vyn. Det här objektet måste implementera `SSCCloudSpatialAnchorSessionDelegate` protokollet.
