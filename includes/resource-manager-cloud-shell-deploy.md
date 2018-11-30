---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: a2ee8705be3f34b6df113c68d88e375411f84bf2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440468"
---
## <a name="deploy-template-from-cloud-shell"></a>Distribuera mallen från Cloud Shell

Du kan använda [Cloud Shell](../articles/cloud-shell/overview.md) till att distribuera mallen. Dock måste du först läsa in mallen till lagringskontot för Cloud Shell. Om du inte har använt Cloud Shell tidigare läser du [Overview of Azure Cloud Shell](../articles/cloud-shell/overview.md) (Översikt över Azure Cloud Shell), som innehåller information om hur du konfigurerar Cloud Shell.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj resursgrupp](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Välj lagringskontot för Cloud Shell.

   ![Välj lagringskonto](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Välj **Blobar**.

   ![Välj blobbar](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Välj **+ Container**.

   ![Lägga till containern](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Ge behållaren ett namn och en åtkomstnivå. Exempelmallen i den här artikeln innehåller ingen känslig information, så tillåter anonym läsbehörighet. Välj **OK**.

   ![Ange värden för behållare](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Välj behållaren som du skapade.

   ![Välj ny behållare](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Välj **Överför**.

   ![Ladda upp blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Leta upp och överför mallen.

   ![Ladda upp filen](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Välj mallen när paketet har överförts.

   ![Välj mall](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Kopiera URL: en.

   ![Kopiera URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Öppna kommandotolken.

   ![Öppna Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
