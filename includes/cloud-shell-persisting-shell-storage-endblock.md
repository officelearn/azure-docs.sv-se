---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60200300"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Överför lokala filer till Cloud Shell
Den `clouddrive` directory synkroniseras med Azure portal lagring-bladet. Använd det här bladet för att överföra filer till eller från din filresurs. Uppdaterar filer från Cloud Shell återspeglas i file storage GUI när du uppdaterar bladet.

### <a name="download-files"></a>Hämta filer

![Lista över lokala filer](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Gå till den monterade filresursen i Azure-portalen.
2. Välj målfilen.
3. Välj den **hämta** knappen.

### <a name="upload-files"></a>Överföra filer

![Lokala filer som ska laddas upp](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Gå till monterade filresursen.
2. Välj knappen **Ladda upp**.
3. Markera den eller de filer som du vill överföra.
4. Bekräfta överföringen.

Du bör nu se de filer som är tillgängliga i din `clouddrive` katalogen i Cloud Shell.