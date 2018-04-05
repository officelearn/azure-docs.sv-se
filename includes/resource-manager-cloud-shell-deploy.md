## <a name="deploy-template-from-cloud-shell"></a>Distribuera mallen från Cloud Shell

Du kan använda [Cloud Shell](../articles/cloud-shell/overview.md) till att distribuera mallen. Dock måste du först läsa mallen till storage-konto för moln-gränssnittet. Om du inte har använt Cloud Shell tidigare läser du [Overview of Azure Cloud Shell](../articles/cloud-shell/overview.md) (Översikt över Azure Cloud Shell), som innehåller information om hur du konfigurerar Cloud Shell.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj en resursgrupp](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Välj lagringskontot för Cloud Shell.

   ![Välj lagringskonto](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Välj **Blobbar**.

   ![Välj blobbar](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Välj **+ behållare**.

   ![Lägg till behållare](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Ge din behållaren ett namn och en åtkomstnivå. Exempelmall i den här artikeln innehåller ingen känslig information, så tillåter anonym läsbehörighet. Välj **OK**.

   ![Ange värden för behållaren](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Markera den behållare som du skapade.

   ![Välj ny behållare](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Välj **Överför**.

   ![Överför blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Leta upp och överför mallen.

   ![Överför fil](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Välj mallen när paketet har överförts.

   ![Välj mall](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Kopiera URL-Adressen.

   ![Kopiera URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Öppna kommandotolken.

   ![Öppna Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
