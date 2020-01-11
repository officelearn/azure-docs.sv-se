---
title: Självstudie – åtkomst till Blob Storage med hjälp av Key Vault med hjälp av Azure Databricks
description: I den här självstudien beskrivs hur du får åtkomst till Azure Blob Storage från Azure Databricks med hemligheter som lagras i ett nyckel valv.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889061"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Självstudie: få åtkomst till Azure Blob Storage från Azure Databricks med Azure Key Vault

I den här självstudien beskrivs hur du får åtkomst till Azure Blob Storage från Azure Databricks med hemligheter som lagras i ett nyckel valv.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa ett lagrings konto och en BLOB-behållare
> * Skapa en Azure Key Vault och Lägg till en hemlighet
> * Skapa en Azure Databricks arbets yta och Lägg till ett hemligt område
> * Få åtkomst till din BLOB-behållare från Azure Databricks

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!Note]
> Den här självstudien kan inte utföras med **Azures kostnads fri utvärderings prenumeration**.
> Om du har ett kostnads fritt konto går du till din profil och ändrar din prenumeration till **betala per**användning. Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgifts gränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)och [begär en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks arbets yta kan du välja pris nivån **utvärdering (Premium-14-dagar gratis DBU)** för att ge arbets ytan åtkomst till kostnads fria Premium Azure Databricks DBU i 14 dagar.

## <a name="create-a-storage-account-and-blob-container"></a>Skapa ett lagrings konto och en BLOB-behållare

1. I Azure Portal väljer du **skapa en resurs** > **lagring**. Välj sedan **lagrings konto**.

   ![Hitta resurs för Azure Storage-konto](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Välj din prenumeration och resurs grupp eller skapa en ny resurs grupp. Ange ett lagrings konto namn och välj en plats. Välj **Granska + Skapa**.

   ![Ange egenskaper för lagrings konto](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Om verifieringen Miss lyckas löser du problemen och försöker igen. Om verifieringen lyckas väljer du **skapa** och vänta tills lagrings kontot har skapats.

4. Navigera till ditt nyligen skapade lagrings konto och välj **blobbar** under **tjänster** på sidan **Översikt** . Välj sedan **+ container** och ange ett behållar namn. Välj **OK**.

   ![Skapa ny behållare](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Leta upp en fil som du vill överföra till din Blob Storage-behållare. Om du inte har någon-fil kan du använda en text redigerare för att skapa en ny textfil med viss information. I det här exemplet innehåller en fil med namnet **HW. txt** texten "Hello World". Spara text filen lokalt och ladda upp den till din Blob Storage-behållare.

   ![Ladda upp fil till behållare](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Gå tillbaka till ditt lagrings konto och välj **åtkomst nycklar** under **Inställningar**. Kopiera **lagrings kontots namn** och **nyckel 1** till en text redigerare för senare användning i den här självstudien.

   ![Hitta åtkomst nycklar för lagrings kontot](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Skapa en Azure Key Vault och Lägg till en hemlighet

1. I Azure Portal väljer du **skapa en resurs** och anger **Key Vault** i sökrutan.

   ![Skapa en Azure-resurs Sök ruta](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Den Key Vault resursen väljs automatiskt. Välj **Skapa**.

   ![Skapa en Key Vault resurs](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Ange följande information på sidan **skapa nyckel valv** och behåll standardvärdena för återstående fält:

   |Egenskap|Beskrivning|
   |--------|-----------|
   |Namn|Ett unikt namn för nyckel valvet.|
   |Prenumeration|Välj en prenumeration.|
   |Resursgrupp|Välj en resurs grupp eller skapa en ny.|
   |Location|Välj en plats.|

   ![Egenskaper för Azure Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. När du har angett den här informationen väljer du **Skapa**. 

4. Navigera till ditt nyligen skapade nyckel valv i Azure Portal och välj **hemligheter**. Välj sedan **+ generera/importera**. 

   ![Generera ny hemlighet för Key Vault](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Ange följande information på sidan **skapa en hemlig** enhet och behåll standardvärdena för de återstående fälten:

   |Egenskap|Värde|
   |--------|-----------|
   |Uppladdningsalternativ|Manuellt|
   |Namn|Eget namn för din lagrings konto nyckel.|
   |Värde|KEY1 från ditt lagrings konto.|

   ![Egenskaper för nytt Key Vault-hemlighet](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Spara nyckel namnet i en text redigerare för senare användning i den här självstudien och välj **skapa**. Navigera sedan till menyn **Egenskaper** . Kopiera **DNS-namnet** och **resurs-ID:** t till en text redigerare för senare användning i självstudien.

   ![Kopiera Azure Key Vault DNS-namn och resurs-ID](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Skapa en Azure Databricks arbets yta och Lägg till ett hemligt område

1. Välj **Skapa en resurs** > **Analys** > **Azure Databricks** i Azure-portalen.

    ![Databricks på Azure Portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Under **Azure Databricks tjänst**anger du följande värden för att skapa en Databricks-arbetsyta.

   |Egenskap  |Beskrivning  |
   |---------|---------|
   |Namn på arbetsyta     | Ange ett namn för Databricks-arbetsytan        |
   |Prenumeration     | I listrutan väljer du din Azure-prenumeration.        |
   |Resursgrupp     | Välj samma resurs grupp som innehåller nyckel valvet. |
   |Location     | Välj samma plats som Azure Key Vault. För alla tillgängliga regioner, se [Azure-tjänster tillgängliga per region](https://azure.microsoft.com/regions/services/).        |
   |Prisnivå     |  Välj mellan **Standard** och **Premium**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Egenskaper för Databricks-arbetsyta](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Välj **Skapa**.

3. Navigera till din nyligen skapade Azure Databricks-resurs i Azure Portal och välj **Starta arbets yta**.

   ![Starta Azure Databricks arbets yta](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. När din Azure Databricks arbets yta är öppen i ett separat fönster lägger du till **#secrets/createscope** i URL: en. URL: en ska ha följande format: 

   **https://< \location >. azuredatabricks. net/? o = < \orgID > #secrets/createscope**.
   

5. Ange ett namn på omfånget och ange Azure Key Vault DNS-namn och resurs-ID som du sparade tidigare. Spara omfångs namnet i en text redigerare för senare användning i den här självstudien. Välj sedan **Create** (Skapa).

   ![Skapa hemlig omfattning i arbets ytan Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Få åtkomst till din BLOB-behållare från Azure Databricks

1. På Start sidan i arbets ytan Azure Databricks väljer du **nytt kluster** under **vanliga uppgifter**.

   ![Skapa en ny Azure Databricks Notebook](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Ange ett kluster namn och välj **skapa kluster**. Det tar några minuter att slutföra skapandet av klustret.

3. När klustret har skapats går du till start sidan i Azure Databricks arbets ytan och väljer **ny Notebook** under **vanliga uppgifter**.

   ![Skapa en ny Azure Databricks Notebook](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Ange ett namn på antecknings boken och ange språket till python. Ange klustret till namnet på det kluster som du skapade i föregående steg.

5. Kör följande kommando för att montera din Blob Storage-behållare. Kom ihåg att ändra värdena för följande egenskaper:

   * namn på container
   * ditt-Storage – konto namn
   * Monterings namn
   * konfiguration – nyckel
   * omfång-namn
   * nyckel namn

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **Mount-Name** är en DBFS-sökväg som representerar var Blob Storage containern eller en mapp i behållaren (anges i källan) ska monteras.
   * **conf-nyckeln** kan vara antingen `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` eller `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **omfång – namn** är namnet på det hemliga omfång som du skapade i föregående avsnitt. 
   * **nyckel namn** är namnet på de hemligheter som du skapade för lagrings konto nyckeln i ditt nyckel valv.

   ![Skapa Blob Storage-montering i Notebook](./media/store-secrets-azure-key-vault/command1.png)

6. Kör följande kommando för att läsa text filen i Blob storage-behållaren till en dataframe. Ändra värdena i kommandot så att de överensstämmer med ditt monterings namn och fil namn.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Läs fil till dataframe](./media/store-secrets-azure-key-vault/command2.png)

7. Använd följande kommando för att visa innehållet i filen.

   ```python
   df.show()
   ```
   ![Visa dataframe](./media/store-secrets-azure-key-vault/command3.png)

8. Om du vill demontera Blob Storage kör du följande kommando:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Demontera lagrings konto](./media/store-secrets-azure-key-vault/command4.png)

9. Observera att när monteringen har demonterats kan du inte längre läsa från ditt Blob Storage-konto.

   ![Fel vid demontering av lagrings konto](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet tar du bort hela resurs gruppen med följande steg:

1. Välj **resurs grupper** på den vänstra menyn i Azure Portal och navigera till din resurs grupp.

2. Välj **ta bort resurs grupp** och skriv namnet på din resurs grupp. Välj sedan **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du implementerar en VNet-Databricks miljö med en tjänst slut punkt som är aktive rad för Cosmos DB.
> [!div class="nextstepaction"]
> [Självstudie: implementera Azure Databricks med en Cosmos DB-slutpunkt](service-endpoint-cosmosdb.md)
