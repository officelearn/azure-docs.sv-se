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

Du kan använda [Cloud Shell](../articles/cloud-shell/overview.md) till att distribuera mallen. To deploy an external template, provide the URI of the template exactly as you would for any external deployment. To deploy a local template, you must first load your template into the storage account for your Cloud Shell. This section describes how to load the template to your cloud shell account, and deploy it as a local file. If you haven't used Cloud Shell, see [Overview of Azure Cloud Shell](../articles/cloud-shell/overview.md) for information about setting it up.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj resursgrupp](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Välj lagringskontot för Cloud Shell.

   ![Välj lagringskonto](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Välj **Blobar**.

   ![Välj blobbar](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Välj **+ Container**.

   ![Lägga till containern](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Give your container a name and an access level. The sample template in this article contains no sensitive information, so allow anonymous read access. Välj **OK**.

   ![Provide container values](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Select the container you created.

   ![Select new container](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Välj **Överför**.

   ![Upload blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Leta upp och överför mallen.

   ![Ladda upp filen](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. After it has uploaded, select the template.

   ![Select new template](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copy the URL.

   ![Copy URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Öppna kommandotolken.

   ![Öppna Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
