---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423549"
---
I den [Azure-portalen](https://portal.azure.com), klickar du på **resursgrupper** > **cloud-shell-storage -\<your_region >**  >   **\<storage_account_name >**.

![Hitta Cloud Shell-storage-konto](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

I den **översikt** sidan i lagringskontot, Välj **filer**.

Välj den automatiskt genererade filresursen och välj **överför**. Den här filresursen monteras i Cloud Shell som `clouddrive`.

![Hitta överför-knappen](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Klickar du på väljaren för filen och välj din ZIP-fil och sedan på **överför**. 

I Cloud Shell använder `ls` att verifiera att du kan se den uppladdade ZIP-filen i standard `clouddrive` dela.

```azurecli-interactive
ls clouddrive
```
