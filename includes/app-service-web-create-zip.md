---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: d804cb310a8638713cabf76c2f4192a0e4d0f43d
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411873"
---
## <a name="create-a-project-zip-file"></a>Skapa en ZIP-fil av projektet

Kontrollera att du fortfarande befinner dig i rotkatalogen för exempelprojektet. Skapa ett ZIP-arkiv med allt i projektet. Följande kommando använder standardverktyget i terminalen:

```
# Bash
zip -r myAppFiles.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath myAppFiles.zip
``` 

Senare kan du överföra den här ZIP-filen till Azure och distribuera den till App Service.