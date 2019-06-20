---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187044"
---
## <a name="setting-up-the-library"></a>Att konfigurera biblioteket

Anropa Start() om du vill aktivera din session att bearbeta miljödata.

För att hantera händelser som skapats av din session, ange den `delegate` egenskapen sessionen till ett objekt som vyn. Det här objektet måste implementera SSCCloudSpatialAnchorSessionDelegate-protokollet.
