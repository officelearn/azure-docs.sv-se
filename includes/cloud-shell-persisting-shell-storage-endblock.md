---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187680"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Överföra lokala filer till Cloud Shell
Katalogen `clouddrive` synkroniseras med Azure portal lagringsblad. Använd det här bladet för att överföra lokala filer till eller från filresursen. Uppdatering av filer inifrån Cloud Shell återspeglas i fillagringsgränssnittet när du uppdaterar bladet.

### <a name="download-files"></a>Hämta filer

![Lista över lokala filer](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Gå till den monterade filresursen i Azure-portalen.
2. Markera målfilen.
3. Välj knappen **Hämta.**

### <a name="upload-files"></a>Överföra filer

![Lokala filer som ska laddas upp](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. Gå till den monterade filresursen.
2. Välj knappen **Ladda upp**.
3. Markera den eller de filer som du vill ladda upp.
4. Bekräfta uppladdningen.

Du bör nu se de filer `clouddrive` som är tillgängliga i din katalog i Cloud Shell.