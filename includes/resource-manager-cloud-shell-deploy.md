---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302350"
---
## <a name="deploy-template-from-cloud-shell"></a>Distribuera mallen från Cloud Shell

Du kan använda [Cloud Shell](../articles/cloud-shell/overview.md) till att distribuera mallen. Ange URI för mallen för att distribuera en mall för externa, precis som för alla externa distributioner. Om du vill distribuera en lokal mall måste du först läsa in mallen till lagringskontot för Cloud Shell. Det här avsnittet beskriver hur du läsa in mallen till ditt konto för cloud shell och distribuera det som en lokal fil. Om du inte har använt Cloud Shell, se [översikt av Azure Cloud Shell](../articles/cloud-shell/overview.md) information om hur du konfigurerar.

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
