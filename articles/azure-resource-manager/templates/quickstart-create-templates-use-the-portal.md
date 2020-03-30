---
title: Distribuera mall - Azure-portal
description: Lär dig hur du skapar din första Azure Resource Manager-mall med Azure-portalen och hur du distribuerar den.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: dd3d9caa8184b8637b509fc3318851751b211405
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80131880"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Snabbstart: Skapa och distribuera ARM-mallar med hjälp av Azure-portalen

Lär dig hur du skapar en ARM-mall (Azure Resource Manager) med hjälp av Azure-portalen och processen att redigera och distribuera mallen från portalen. ARM-mallar är JSON-filer som definierar de resurser du behöver distribuera för din lösning. Information om vilka begrepp som är associerade med distribution och hantering av dina Azure-lösningar finns i [översikt över malldistribution](overview.md).

![Snabbstartsportaldiagram för Resource Manager-mall](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

När du har slutfört självstudien kan du distribuera ett Azure Storage-konto. Samma process kan användas till att distribuera andra Azure-resurser.

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="generate-a-template-using-the-portal"></a>Generera en mall med hjälp av portalen

Att skapa en ARM-mall från grunden är inte en lätt uppgift, särskilt om du är ny på Azure-distribution och du inte är bekant med JSON-formatet. Med hjälp av Azure-portalen kan du konfigurera en resurs, till exempel ett Azure Storage-konto. Innan du distribuerar resursen kan du exportera konfigurationen till en mall. Du kan spara mallen och använda den igen senare.

Många erfarna mallutvecklare använder den här metoden för att generera mallar när de försöker distribuera Azure-resurser som de inte är bekanta med. Mer information om hur du exporterar mallar med hjälp av portalen finns i [Exportera resursgrupper till mallar](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Det andra sättet att hitta en fungerande mall är från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/).

1. I en webbläsare går du till [Azure-portalen](https://portal.azure.com) och loggar in.
1. På Portal-menyn i Azure väljer du **Skapa en resurs**.

    ![Välj Skapa en resurs på Azure Portal-menyn](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Välj > **lagringslagringskonto**. **Storage**

    ![Skapa ett Azure-lagringskonto](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Ange följande information:

    |Namn|Värde|
    |----|----|
    |**Resursgrupp**|Välj **Skapa ny** och ge resursgruppen ett namn. På skärmbilden är namnet på resursgruppen *mystorage1016rg*. En resursgrupp är en container för Azure-resurser. Med resursgrupper är det enklare att hantera Azure-resurser. |
    |**Namn**|Ge lagringskontot ett unikt namn. Lagringskontonamnet måste vara unikt i hela Azure och innehåller endast gemener och siffror. Namnet måste vara mellan 3 och 24 tecken. Om du får ett felmeddelande med texten "The storage account name 'mystorage1016' is already taken", prova att använda ** &lt;ditt namn>&lt;lagring Dagens datum i MMDD>**, till exempel **johndolestorage1016**. Mer information finns i [Namngivningsregler och begränsningar](/azure/architecture/best-practices/resource-naming).|

    Du kan använda standardvärdena för resten av egenskaperna.

    ![Skapa en konfiguration för Azure-lagringskonto med hjälp av Azure-portalen](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Vissa av de exporterade mallarna kräver vissa ändringar innan du kan distribuera dem.

1. Välj **Granska + skapa** längst ned på skärmen. Välj inte **Skapa** i nästa steg.
1. Välj **Ladda ned en mall för automatisering** längst ned på skärmen. Portalen visar den genererade mallen:

    ![Generera en mall från portalen](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    Huvudfönsterrutan visar mallen. Det är en JSON-fil med sex element på den översta nivån – `schema`, `contentVersion`, `parameters`, `variables`, `resources` och `output`. Mer information finns [i Förstå strukturen och syntaxen för ARM-mallar](./template-syntax.md)

    Det finns sex parametrar angivna. En av dem heter **storageAccountName**. Den andra markerade delen på den föregående skärmbilden visar hur den här parametern ska anges i mallen. I nästa avsnitt kan du redigera mallen för att använda ett genererat namn för lagringskontot.

    En Azure-resurs har definierats i mallen. Typen är `Microsoft.Storage/storageAccounts`. Ta en titt på hur resursen definieras och definitionsstrukturen.
1. Välj **Hämta** högst upp på skärmen.
1. Öppna den nedladdade zip-filen och spara sedan **template.json** på datorn. I nästa avsnitt använder du ett malldistributionsverktyg för att redigera mallen.
1. Välj fliken **Parameter** för att se de värden som du angav för parametrarna. Anteckna dessa värden, eftersom du behöver dem i nästa avsnitt när du distribuerar mallen.

    ![Generera en mall från portalen](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    Med både mallfilen och parameterfilen kan du skapa en resurs i den här självstudien ett Azure-lagringskonto.

## <a name="edit-and-deploy-the-template"></a>Redigera och distribuera mallen

Du kan använda Azure-portalen för att utföra viss grundläggande redigering av mallen. I den här snabbstarten använder du portalverktyget *Malldistribution*. *Malldistribution* används i den här självstudien, vilket innebär att du kan slutföra hela självstudien med ett gränssnitt – Azure-portalen. Om du vill redigera en mer komplex mall bör du överväga att använda [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), som ger rikare redigeringsfunktioner.

> [!IMPORTANT]
> Malldistribution ger ett gränssnitt för testning av enkla mallar. Det rekommenderas inte att använda den här funktionen i produktionen. Spara i stället dina mallar i ett Azure-lagringskonto eller en källkodsdatabas som GitHub.

Azure kräver att varje Azure-tjänst har ett unikt namn. Distributionen kan misslyckas om du anger namnet på ett lagringskonto som redan finns. För att undvika det här problemet ändrar du mallen så att den använder ett mallfunktionsanrop `uniquestring()` för att generera ett unikt lagringskontonamn.

1. På Azure-portalmenyn eller på **startsidan** väljer du **Skapa en resurs**.
1. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.
1. Välj **Malldistribution**.

    ![Azure Resource Manager-mallbibliotek](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
1. Välj **Skapa**.
1. Välj alternativet för att **skapa din egen mall i redigeringsprogrammet**.
1. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in template.json som du laddade ned i det sista avsnittet.
1. Gör följande tre ändringar i mallen:

    ![Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Ta bort parametern **storageAccountName** som visas i föregående skärmbild.
   - Lägg till en variabel som kallas **storageAccountName** som visas i föregående skärmbild:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       Här används två mallfunktioner: `concat()` och `uniqueString()`.
   - Uppdatera namnelementet för resursen **Microsoft.Storage/storageAccounts** för att använda den nyligen definierade variabeln i stället för parametern:

       ```json
       "name": "[variables('storageAccountName')]",
       ```

     Den slutliga mallen bör se ut så här:

     ```json
     {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "location": {
               "type": "string"
           },
           "accountType": {
               "type": "string"
           },
           "kind": {
               "type": "string"
           },
           "accessTier": {
               "type": "string"
           },
           "supportsHttpsTrafficOnly": {
               "type": "bool"
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       },
       "resources": [
           {
               "name": "[variables('storageAccountName')]",
               "type": "Microsoft.Storage/storageAccounts",
               "apiVersion": "2018-07-01",
               "location": "[parameters('location')]",
               "properties": {
                   "accessTier": "[parameters('accessTier')]",
                   "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
               },
               "dependsOn": [],
               "sku": {
                   "name": "[parameters('accountType')]"
               },
               "kind": "[parameters('kind')]"
           }
       ],
       "outputs": {}
     }
     ```
1. Välj **Spara**.
1. Ange följande värden:

    |Namn|Värde|
    |----|----|
    |**Resursgrupp**|Markera det resursgruppsnamn som du skapade i det sista avsnittet. |
    |**Location**|Välj en plats för lagringskontot. Välj till exempel **USA, centrala**. |
    |**Typ av konto**|Ange **Standard_LRS** för den här snabbstarten. |
    |**Typ**|Ange **StorageV2** för den här snabbstarten. |
    |**Åtkomstnivå**|Ange **Frekvent** för den här snabbstarten. |
    |**Https Endast trafik aktiverad**| Välj **true** för den här snabbstarten. |
    |**Jag godkänner de villkor som anges ovan**|(välj)|

    Här är en skärmbild på en exempeldistribution:

    ![Distribution av Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

1. Välj **Köp**.
1. Välj klockikonen (meddelanden) längst upp på skärmen för att se distributionsstatus. **Distribution pågår** ska nu visas. Vänta tills distributionen är klar.

    ![Distributionsmeddelande för Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Välj **Gå till resursgrupp** i meddelandefönstret. Du bör se en skärm som liknar följande:

    ![Resursgrupp för distribution av Azure Resource Manager-mallar](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Du kan se att distributionen lyckades och att det bara finns ett lagringskonto i resursgruppen. Lagringskontots namn är en unik sträng som genereras av mallen. Mer information om hur du använder Azure Storage-konton finns i [Snabbstart: Ladda upp, ladda ned och lista blobar med Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. I Azure-portalen väljer du **Resursgrupp** på den vänstra menyn.
1. Ange resursgruppens namn i fältet **Filtrera efter namn**.
1. Välj resursgruppens namn.  Du bör se lagringskontot i resursgruppen.
1. Välj **Ta bort resursgrupp** på menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du genererar en mall från Azure-portalen och hur du distribuerar mallen med hjälp av portalen. Den mall som användes i den här snabbstarten är en enkel mall med en Azure-resurs. När mallen är komplex är det enklare att använda Visual Studio Code eller Visual Studio för att utveckla mallen. Mer information om mallutveckling finns i vår nya videoprogramledningsserie:

> [!div class="nextstepaction"]
> [Självstudier för nybörjare](./template-tutorial-create-first-template.md)