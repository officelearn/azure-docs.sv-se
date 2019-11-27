---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74451644"
---
## <a name="deploy-template-from-cloud-shell"></a>Distribuera mallen från Cloud Shell

Du kan använda [Cloud Shell](../articles/cloud-shell/overview.md) till att distribuera mallen. Om du vill distribuera en extern mall anger du URI för mallen exakt som du skulle göra för alla externa distributioner. Om du vill distribuera en lokal mall måste du först läsa in din mall till lagrings kontot för Cloud Shell. I det här avsnittet beskrivs hur du läser in mallen till ditt Cloud Shell-konto och distribuerar den som en lokal fil. Om du inte har använt Cloud Shell går du till [Översikt över Azure Cloud Shell](../articles/cloud-shell/overview.md) för information om hur du konfigurerar den.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj resursgrupp](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Välj lagringskontot för Cloud Shell.

   ![Välj lagringskonto](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Välj **Blobar**.

   ![Välj blobbar](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Välj **+ Container**.

   ![Lägga till containern](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Ge din behållare ett namn och en åtkomst nivå. Exempel mal len i den här artikeln innehåller ingen känslig information, så Tillåt anonym Läs åtkomst. Välj **OK**.

   ![Ange container värden](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Välj den behållare som du skapade.

   ![Välj ny behållare](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Välj **Överför**.

   ![Ladda upp BLOB](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Leta upp och överför mallen.

   ![Ladda upp filen](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. När den har laddats upp väljer du mallen.

   ![Välj ny mall](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Kopiera URL: en.

   ![Kopiera URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Öppna kommandotolken.

   ![Öppna Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
