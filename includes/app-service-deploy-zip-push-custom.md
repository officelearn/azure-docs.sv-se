---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134092"
---
## <a name="deployment-customization"></a>Distributionsanpassning

Distributionsprocessen förutsätter att ZIP-filen som du överför innehåller en redo att köra appen. Som standard körs utan anpassningar. Om du vill aktivera samma produktionsprocesser som du får med kontinuerlig integrering, lägger du till följande inställningar för program:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

När du använder .zip push-distribution kan den här inställningen är **FALSKT** som standard. Standardvärdet är **SANT** för kontinuerlig integrering distributioner. När värdet **SANT**, din distribution-relaterade inställningar som används under distributionen. Du kan konfigurera dessa inställningar som appinställningar eller i en konfigurationsfil för .deployment som finns i roten av din ZIP-fil. Mer information finns i [lagringsplats och distribution-relaterade inställningar](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) i referens för distribution.