---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187146"
---
## <a name="deployment-customization"></a>Anpassning av distribution

Distributionsprocessen förutsätter att ZIP-filen som du skickar innehåller en färdig att köra-app. Som standard körs inga anpassningar. Om du vill aktivera samma byggprocesser som du får med kontinuerlig integrering lägger du till följande i programinställningarna:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

När du använder .zip push-distribution är den här inställningen **falsk** som standard. Standardinställningen **gäller** för kontinuerliga integrationsdistributioner. När den är inställd på **true**används dina distributionsrelaterade inställningar under distributionen. Du kan konfigurera dessa inställningar antingen som appinställningar eller i en distributionskonfigurationsfil som finns i roten på ZIP-filen. Mer information finns i [Databas- och distributionsrelaterade inställningar](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) i distributionsreferensen.