---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110720"
---
## <a name="setting-up-the-library"></a>Att konfigurera biblioteket

Anropa Start() om du vill aktivera din session att bearbeta miljödata.

För att hantera händelser som skapats av din session, ange den `delegate` egenskapen sessionen till ett objekt som vyn. Det här objektet måste implementera SSCCloudSpatialAnchorSessionDelegate-protokollet.
