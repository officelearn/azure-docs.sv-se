---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110720"
---
## <a name="setting-up-the-library"></a>Att konfigurera biblioteket

Anropa Start() om du vill aktivera din session att bearbeta miljödata.

För att hantera händelser som skapats av din session, ange den `delegate` egenskapen sessionen till ett objekt som vyn. Det här objektet måste implementera SSCCloudSpatialAnchorSessionDelegate-protokollet.
