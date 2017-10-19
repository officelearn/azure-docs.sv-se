## <a name="deploy-template-from-cloud-shell"></a>Distribuera mallen från Cloud Shell

Du kan använda [Cloud Shell](../articles/cloud-shell/overview.md) till att distribuera mallen. Först måste du dock läsa in mallen till filresursen för Cloud Shell. Om du inte har använt Cloud Shell tidigare läser du [Overview of Azure Cloud Shell](../articles/cloud-shell/overview.md) (Översikt över Azure Cloud Shell), som innehåller information om hur du konfigurerar Cloud Shell.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj resursgrupp](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Välj lagringskontot för Cloud Shell.

   ![Välj lagringskonto](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Välj **Filer**.

   ![Välj filer](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Välj filresursen för Cloud Shell. Namnet har formatet `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Välj filresurs](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Välj **Lägg till katalog**.

   ![Lägg till katalog](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Ge den namnet **templates** och välj **OK**.

   ![Namnge katalogen](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Välj den nya katalogen.

   ![Välj katalog](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Välj **Överför**.

   ![Välj Överför](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Leta upp och överför mallen.

   ![Ladda upp filen](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Öppna kommandotolken.

   ![Öppna Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
