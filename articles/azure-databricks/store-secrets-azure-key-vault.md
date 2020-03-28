---
title: Självstudiekurs - Access blob-lagring med hjälp av nyckelvalv med Azure Databricks
description: Den här självstudien beskriver hur du kommer åt Azure Blob Storage från Azure Databricks med hemligheter som lagras i ett nyckelvalv.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889061"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Självstudiekurs: Få tillgång till Azure Blob Storage från Azure Databricks med Azure Key Vault

Den här självstudien beskriver hur du kommer åt Azure Blob Storage från Azure Databricks med hemligheter som lagras i ett nyckelvalv.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett lagringskonto och en blob-behållare
> * Skapa ett Azure Key Vault och lägg till en hemlighet
> * Skapa en Azure Databricks-arbetsyta och lägg till ett hemligt scope
> * Få tillgång till din blob-behållare från Azure Databricks

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

> [!Note]
> Den här självstudien kan inte utföras med **Azure Free Trial Subscription**.
> Om du har ett gratis konto går du till din profil och ändrar din prenumeration **på användningsbaserad betalning.** Mer information finns i [Kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Ta sedan [bort utgiftsgränsen](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)och [begär en kvotökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) för virtuella processorer i din region. När du skapar din Azure Databricks-arbetsyta kan du välja prisnivån **Utvärderingsversion (Premium – 14 dagar gratis dbUs)** för att ge arbetsytan åtkomst till kostnadsfria Premium Azure Databricks DBUs i 14 dagar.

## <a name="create-a-storage-account-and-blob-container"></a>Skapa ett lagringskonto och en blob-behållare

1. I Azure-portalen väljer du **Skapa en resurslagring** > **Storage**. Välj sedan **Lagringskonto**.

   ![Hitta Azure-lagringskontoresurs](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Välj din prenumeration och resursgrupp eller skapa en ny resursgrupp. Ange sedan ett lagringskontonamn och välj en plats. Välj **Granska + Skapa**.

   ![Ange egenskaper för lagringskonto](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Om verifieringen misslyckas åtgärdar du problemen och försöker igen. Om valideringen lyckas väljer du **Skapa** och väntar på att lagringskontot ska skapas.

4. Navigera till ditt nyskapade **lagringskonto** och välj Blobbar under **Tjänster** på sidan **Översikt.** Välj sedan **+ Behållare** och ange ett behållarnamn. Välj **OK**.

   ![Skapa ny behållare](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Leta reda på en fil som du vill överföra till din blob lagringsbehållare. Om du inte har en fil kan du använda en textredigerare för att skapa en ny textfil med viss information. I det här exemplet innehåller en fil med namnet **hw.txt** texten "hello world". Spara textfilen lokalt och ladda upp den till din blob-lagringsbehållare.

   ![Ladda upp fil till behållare](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Gå tillbaka till ditt lagringskonto och välj **Åtkomstnycklar** under **Inställningar**. Kopiera **lagringskontonamn** och **nyckel 1** till en textredigerare för senare användning i den här självstudien.

   ![Hitta åtkomstnycklar för lagringskonto](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Skapa ett Azure Key Vault och lägg till en hemlighet

1. I Azure-portalen väljer du **Skapa en resurs** och anger Key **Vault** i sökrutan.

   ![Skapa en sökruta för Azure-resurser](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Key Vault-resursen väljs automatiskt. Välj **Skapa**.

   ![Skapa en Key Vault-resurs](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. På sidan **Skapa nyckelvalv** anger du följande information och behåller standardvärdena för de återstående fälten:

   |Egenskap|Beskrivning|
   |--------|-----------|
   |Namn|Ett unikt namn på ditt nyckelvalv.|
   |Prenumeration|Välj en prenumeration.|
   |Resursgrupp|Välj en resursgrupp eller skapa en ny.|
   |Location|Välj en plats.|

   ![Egenskaper för Azure-nyckelvalv](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. När du har angett den här informationen väljer du **Skapa**. 

4. Navigera till ditt nyskapade nyckelvalv i Azure-portalen och välj **Hemligheter**. Välj sedan **+ Generera/importera**. 

   ![Generera ny nyckelvalvshemlighet](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. På sidan **Skapa en hemlighet** anger du följande information och behåller standardvärdena för de återstående fälten:

   |Egenskap|Värde|
   |--------|-----------|
   |Alternativ för överföring|Manuell|
   |Namn|Eget namn för din lagringskontonyckel.|
   |Värde|nyckel1 från ditt lagringskonto.|

   ![Egenskaper för ny nyckelvalvshemlighet](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Spara nyckelnamnet i en textredigerare för användning senare i den här självstudien och välj **Skapa**. Navigera sedan till menyn **Egenskaper.** Kopiera **DNS-namn** och **resurs-ID** till en textredigerare för användning senare i självstudien.

   ![Kopiera DNS-namn och resurs-ID för Azure Key Vault](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Skapa en Azure Databricks-arbetsyta och lägg till ett hemligt scope

1. I Azure-portalen väljer du **Skapa en resurs** > **Analytics** > **Azure Databricks**.

    ![Databricks på Azure-portalen](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Under **Azure Databricks Service**anger du följande värden för att skapa en Databricks-arbetsyta.

   |Egenskap  |Beskrivning  |
   |---------|---------|
   |Namn på arbetsyta     | Ange ett namn för Databricks-arbetsytan        |
   |Prenumeration     | I listrutan väljer du din Azure-prenumeration.        |
   |Resursgrupp     | Markera samma resursgrupp som innehåller nyckelvalvet. |
   |Location     | Välj samma plats som ditt Azure Key Vault. För alla tillgängliga regioner, se [Azure-tjänster som är tillgängliga efter region](https://azure.microsoft.com/regions/services/).        |
   |Prisnivå     |  Välj mellan **Standard** och **Premium**. Mer information om de här nivåerna finns på [prissättningssidan för Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Egenskaper för Databricks-arbetsyta](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Välj **Skapa**.

3. Navigera till din nyligen skapade Azure Databricks-resurs i Azure-portalen och välj **Starta arbetsyta**.

   ![Starta Azure Databricks arbetsyta](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. När din Azure Databricks-arbetsyta är öppen i ett separat fönster lägger du **till #secrets/createScope** i URL:en. Webbadressen bör ha följande format: 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Ange ett scopenamn och ange DNS-namnet och resurs-ID:t i Azure Key Vault som du sparade tidigare. Spara scopenamnet i en textredigerare för användning senare i den här självstudien. Välj sedan **Create** (Skapa).

   ![Skapa hemligt scope på arbetsytan Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Få tillgång till din blob-behållare från Azure Databricks

1. Välj **Nytt kluster** under **Vanliga uppgifter**på startsidan för arbetsytan Azure Databricks .

   ![Skapa en ny Azure Databricks-anteckningsbok](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Ange ett klusternamn och välj **Skapa kluster**. Det tar några minuter att skapa klustret.

3. När klustret har skapats navigerar du till startsidan för din Azure Databricks-arbetsyta och väljer **Ny anteckningsbok** under **Vanliga uppgifter**.

   ![Skapa en ny Azure Databricks-anteckningsbok](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Ange ett anteckningsboksnamn och ange språket till Python. Ange klustret till namnet på det kluster som du skapade i föregående steg.

5. Kör följande kommando för att montera din blob lagringsbehållare. Kom ihåg att ändra värdena för följande egenskaper:

   * ditt-behållarnamn
   * ditt-lagringskonto-namn
   * montera-namn
   * config-nyckel
   * scope-namn
   * nyckelnamn

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **monteringsnamn** är en DBFS-sökväg som representerar var behållaren för Blob-lagring eller en mapp i behållaren (som anges i källan) ska monteras.
   * **conf-key** kan `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` vara antingen eller`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** är namnet på det hemliga scope som du skapade i föregående avsnitt. 
   * **nyckelnamn** är namnet på de hemliga som du skapade för lagringskontonyckeln i nyckelvalvet.

   ![Skapa blob-lagringsfäste i anteckningsboken](./media/store-secrets-azure-key-vault/command1.png)

6. Kör följande kommando för att läsa textfilen i behållaren för bloblagring till en dataram. Ändra värdena i kommandot så att de matchar ditt monteringsnamn och filnamn.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Läsa fil till dataram](./media/store-secrets-azure-key-vault/command2.png)

7. Använd följande kommando för att visa innehållet i filen.

   ```python
   df.show()
   ```
   ![Visa dataram](./media/store-secrets-azure-key-vault/command3.png)

8. Om du vill avmontera blob-lagringen kör du följande kommando:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Avmontera lagringskonto](./media/store-secrets-azure-key-vault/command4.png)

9. Observera att när fästet har avmonterats kan du inte längre läsa från ditt blob storage-konto.

   ![Avmontera lagringskontofel](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte ska fortsätta att använda det här programmet tar du bort hela resursgruppen med följande steg:

1. På menyn till vänster i Azure-portalen väljer du **Resursgrupper** och navigerar till din resursgrupp.

2. Välj **Ta bort resursgrupp** och skriv resursgruppsnamnet. Välj sedan **Ta bort**. 

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du implementerar en VNet-insprutad Databricks-miljö med en tjänstslutpunkt aktiverad för Cosmos DB.
> [!div class="nextstepaction"]
> [Självstudiekurs: Implementera Azure Databricks med en Cosmos DB-slutpunkt](service-endpoint-cosmosdb.md)
