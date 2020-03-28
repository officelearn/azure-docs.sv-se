---
title: Självstudiekurs - anpassade åtgärder & resurser
description: Den här självstudien beskriver hur du skapar ett Azure-hanterat program med en Azure Custom Provider.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650081"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Självstudiekurs: Skapa hanterade program med anpassade åtgärder och resurser

I den här självstudien skapar du ett eget hanterat program med anpassade åtgärder och resurser. Det hanterade programmet innehåller en `Overview` anpassad åtgärd på sidan, en anpassad resurstyp som visas som ett separat menyalternativ i `Table of Content` och en anpassad kontextåtgärd på den anpassade resurssidan.

Den här självstudien innehåller följande steg:

> [!div class="checklist"]
> * Författare användargränssnitt definition fil för att skapa en hanterad programinstans
> * Mall för författaresdistribution med [Azure Custom Provider,](../custom-providers/overview.md)Azure Storage Account och Azure-funktion
> * Författare vy definition artefakt med anpassade åtgärder och resurser
> * Distribuera en hanterad programdefinition
> * Distribuera en instans av hanterade program
> * Utföra anpassade åtgärder och skapa anpassade resurser

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien måste du veta:

* Skapa [och publicera en hanterad programdefinition](publish-service-catalog-app.md).
* Distribuera [servicekatalogappen via Azure portal](deploy-service-catalog-quickstart.md).
* Skapa [Azure Portal-användargränssnitt för ditt hanterade program](create-uidefinition-overview.md).
* [Visa definitionsartefaktkapacitet.](concepts-view-definition.md)
* [Azure Custom](../custom-providers/overview.md) Provider-funktioner.

## <a name="user-interface-definition"></a>Definition av användargränssnitt

I den här självstudien skapar du ett hanterat program och dess hanterade resursgrupp innehåller anpassad providerinstans, lagringskonto och funktion. Azure-funktionen som används i det här exemplet implementerar ett API som hanterar anpassade provideråtgärder för åtgärder och resurser. Azure Storage-konto används som grundläggande lagring för dina anpassade providerresurser.

Användargränssnittsdefinitionen för att skapa `funcname` `storagename` en hanterad programinstans innehåller och indataelement. Namn och funktionsnamn för lagringskontot måste vara globalt unika. Som standard kommer funktionsfiler att distribueras från [exempelfunktionspaketet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), men du kan ändra det genom att lägga till ett indataelement för en paketlänk i *createUIDefinition.json:*

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

och utdata i *createUIDefinition.json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Det fullständiga *exemplet createUIDefinition.json* finns på [Referens: Artefakter för användargränssnittselement](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Mall med anpassad leverantör

Om du vill skapa en hanterad programinstans med anpassad provider måste du definiera anpassad leverantörsresurs med namn **offentligt** och skriva **Microsoft.CustomProviders/resourceProviders** i **mainTemplate.json**. I den resursen definierar du resurstyper och åtgärder för tjänsten. Om du vill distribuera Azure-funktions- `Microsoft.Web/sites` och `Microsoft.Storage/storageAccounts` Azure Storage-kontoinstanser definierar du resurser av typen respektive.

I den här självstudien skapar `ping` du en `users/contextAction` `users` resurstyp, anpassad åtgärd och anpassad `users` åtgärd som ska utföras i en kontext av en anpassad resurs. För varje resurstyp och åtgärd finns en slutpunkt som pekar på funktionen med namn som anges i [createUIDefinition.json](#user-interface-definition). Ange **routingType** `Proxy,Cache` som för `Proxy` resurstyper och åtgärder:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

Det fullständiga *exemplet mainTemplate.json* finns på [Referens: Distributionsmallsartefakt](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Visa definitionsartefakt

Om du vill definiera användargränssnitt som innehåller anpassade åtgärder och anpassade resurser i det hanterade programmet måste du skapa **artefakten viewDefinition.json.** Mer information om vydefinitionsartefakt finns [i Visa definitionsartefakt i Azure Managed Applications](concepts-view-definition.md).

I den här självstudien definierar du:
* En *översiktssida* med knapp i `TestAction` verktygsfältet som representerar en anpassad åtgärd med grundläggande textinmatning.
* En *sida användare* som representerar en anpassad resurstyp `users`.
* En anpassad `users/contextAction` resursåtgärd på sidan *Användare* som ska utföras i `users`en kontext av anpassad resurs av typen .

I följande exempel visas vykonfiguration för en "Översikt"-sida:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Exemplet nedan innehåller konfigurationen av resurssidan "Användare" med anpassad resursåtgärd:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Exemplet complete *viewDefinition.json* finns på [Referens: Visa definitionsartefakt](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definition av hanterat program

Paketera följande hanterade programartefakter för att zip-arkiv och ladda upp det till lagring:

* createUiDefinition.json
* mainTemplate.json
* visaDefinition.json

Alla filer måste vara på rotnivå. Paketet med artefakter kan lagras i alla lagringar, till exempel GitHub-blob eller Azure Storage Account blob. Här är ett skript för att ladda upp programpaketet till lagringskonto: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Kör Azure CLI-skriptet nedan eller följ stegen i Azure-portalen för att distribuera en tjänstkataloghanterad programdefinition:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj **Alla tjänster** i Azure-portalen. Skriv och välj **Managed Applications Center i**listan över resurser .
2. I **Managed Applications Center**väljer du **Programdefinition för Service Catalog** och klickar på Lägg **till**. 
    
    ![Lägg till servicekatalog](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Ange värden för att skapa en tjänstkatalogdefinition:

    * Ange ett unikt **namn** för definition av servicekatalog, **visningsnamn** och *beskrivning*(valfritt).
    * Välj gruppen **Prenumeration,** **Resurs**och **Plats** där programdefinitionen ska skapas. Du kan använda samma resursgrupp som används för zip-paket eller skapa en ny resursgrupp.
    * För en **paketfil Uri**anger du sökvägen till zip-filen som du skapade i föregående steg.

    ![Ange värden](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. När du kommer till avsnittet Autentisering och låsnivå väljer du **Lägg till auktorisering**.

    ![Lägg till auktorisering](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Välj en Azure Active Directory-grupp för att hantera resurserna och välj **OK**.

   ![Lägga till auktoriseringsgrupp](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. När du har angett alla värden väljer du **Skapa**.

   ![Skapa definierad av hanterade program](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Hanterad programinstans

När den hanterade programdefinitionen distribueras kör du skriptet nedan eller följer stegen i Azure-portalen för att distribuera din hanterade programinstans med anpassad provider:

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Välj **Alla tjänster** i Azure-portalen. Skriv och välj **Managed Applications Center i**listan över resurser .
2. I **Managed Applications Center**väljer du **Service catalog-program** och klickar på **Lägg till**. 

    ![Lägg till hanterat program](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. På sidan **Tjänstkatalogprogram** skriver du Tjänstkatalogdefinitionsnamn i sökrutan. Markera den definition som skapades i föregående steg och klicka på **Skapa**.

    ![Välj tjänstkatalog](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Ange värden för att skapa en hanterad programinstans från definition av tjänstkatalog:

    * Välj gruppen **Prenumeration,** **Resurs**och **Plats** där programinstansen ska skapas.
    * Ange ett unikt Azure-funktionsnamn och Azure Storage-kontonamn.

    ![Programinställningar](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. När valideringen skickades klickar du på **OK** för att distribuera en instans av ett hanterat program. 
    
    ![Distribuera hanterade program](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Anpassade åtgärder och resurser

När programinstansen för tjänstkatalogen har distribuerats har du två nya resursgrupper. Den första `applicationGroup` resursgruppen innehåller en instans av `managedResourceGroup` det hanterade programmet, den andra resursgruppen innehåller resurserna för det hanterade programmet, inklusive **anpassad provider**.

![Resursgrupper för program](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

Du kan gå till hanterad programinstans och utföra **anpassad åtgärd** på sidan Översikt, skapa **anpassade resurser** för användare på sidan Användare och köra **anpassad kontextåtgärd** på anpassad resurs.

* Gå till sidan "Översikt" och klicka på knappen "Ping åtgärd":

![Utför anpassad åtgärd](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Gå till sidan "Användare" och klicka på knappen "Lägg till". Ange indata för att skapa en resurs och skicka formuläret:

![Skapa anpassad resurs](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Gå till sidan "Användare", välj en "användare"-resurs och klicka på "Anpassad kontextåtgärd":

![Skapa anpassad resurs](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Söker hjälp

Om du har frågor om Azure Managed Applications kan du prova att fråga på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). En liknande fråga kan redan ha ställts och besvarats, så kontrollera först innan du postar. Lägg till `azure-managedapps` taggen för att få ett snabbt svar!

## <a name="next-steps"></a>Nästa steg

Information om hur du publicerar ditt hanterade program till Azure Marketplace finns i [Azure-hanterade program på Marketplace](publish-marketplace-app.md).

Läs mer om [Azure Custom Providers](../custom-providers/overview.md).
