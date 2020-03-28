---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67187044"
---
## <a name="setting-up-the-library"></a>Ställa in biblioteket

Anropa Start() för att göra det möjligt för sessionen att bearbeta miljödata.

Om du vill hantera händelser `delegate` som utlöses av sessionen anger du sessionens egenskap på ett objekt, till exempel vyn. Det här objektet måste implementera SSCCloudSpatialAnchorSessionDelegate-protokollet.
