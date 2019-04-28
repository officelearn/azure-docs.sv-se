---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232675"
---
## <a name="setting-up-the-library"></a>Att konfigurera biblioteket

Anropa Start() om du vill aktivera din session att bearbeta miljödata.

För att hantera händelser som skapats av din session, ange den `delegate` egenskapen sessionen till ett objekt som vyn. Det här objektet måste implementera SSCCloudSpatialAnchorSessionDelegate-protokollet.
