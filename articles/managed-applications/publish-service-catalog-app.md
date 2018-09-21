---
title: Skapa och publicera ett Azure-hanterat program för tjänstkatalogen | Microsoft Docs
description: Visar hur du skapar ett Azure-hanterat program som är avsett för medlemmar i din organisation.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 06/08/2018
ms.author: tomfitz
ms.openlocfilehash: 3b1da6e9068be3c96cce5973f29344fe7e4b4872
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35764067"
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicera ett hanterat program för internt bruk

Du kan skapa och publicera Azure-[hanterade program](overview.md) som är avsedda för medlemmar i din organisation. En IT-avdelning kan exempelvis publicera hanterade program som säkerställer efterlevnaden av organisationens standarder. Dessa hanterade program är tillgängliga via tjänstkatalogen, inte på Azure Marketplace.

Om du vill publicera ett hanterat program för tjänstkatalogen måste du:

* Skapa en mall som definierar resurserna som ska distribueras med det hanterade programmet.
* Definiera användargränssnittselementen för portalen när du distribuerar det hanterade programmet.
* Skapa ett ZIP-paket som innehåller nödvändiga mallfiler.
* Bestämma vilken användare, vilken grupp eller vilket program som behöver åtkomst till resursgruppen i användarens prenumeration.
* Skapa definitionen för det hanterade programmet som pekar på ZIP-paketet och begär åtkomst för identiteten.

I den här artikeln innehåller det hanterade programmet bara ett lagringskonto. Artikeln beskriver steg för steg hur du publicerar ett hanterat program. Fullständiga exempel hittar du i [exempelprojekten för Azure-hanterade program](sample-projects.md).

PowerShell-exemplen i den här artikeln kräver Azure PowerShell 6.2 eller senare. Om det behövs, [uppdatera din version](/powershell/azure/install-azurerm-ps).

## <a name="create-the-resource-template"></a>Skapa resursmallen

Alla definitioner för hanterade program innehåller en fil med namnet **mainTemplate.json**. I den filen definierar du Azure-resurserna som ska distribueras. Mallen skiljer sig inte från en vanlig Resource Manager-mall.

Skapa en fil med namnet **mainTemplate.json**. Namnet är skiftlägeskänsligt.

Lägg till följande JSON i filen. Den definierar parametrarna för att skapa ett lagringskonto och anger egenskaperna för lagringskontot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Spara filen mainTemplate.json.

## <a name="create-the-user-interface-definition"></a>Skapa definitionen för användargränssnittet

Azure Portal använder filen **createUiDefinition.json** för att generera användargränssnittet för användare som skapar det hanterade programmet. Du definierar hur användare anger indata för varje parameter. Du kan använda alternativ som en listruta, textruta, lösenordsruta och andra inmatningsverktyg. Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.

Skapa en fil med namnet **createUiDefinition.json**. Namnet är skiftlägeskänsligt.

Lägg till följande JSON i filen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Spara filen createUiDefinition.json.

## <a name="package-the-files"></a>Paketera filerna

Lägg till de två filerna i en ZIP-fil med namnet app.zip. Båda filerna måste ligga på rotnivå i ZIP-filen. Om du lägger dem i en mapp får du ett felmeddelande när du skapar definitionen för det hanterade programmet, som anger att de nödvändiga filerna saknas. 

Ladda upp paketet till en tillgänglig plats som det kan användas från. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Skapa en Azure Active Directory-användargrupp eller ett Azure Active Directory-program

I nästa steg väljer du en användargrupp eller ett program som ska hantera resurserna för kundens räkning. Användargruppen eller programmet har behörighet till den hanterade resursgruppen baserat på vilken roll som tilldelas. Rollen kan vara en inbyggd roll för rollbaserad åtkomstkontroll (RBAC) som Ägare eller Deltagare. Du kan också ge en enskild användare behörighet att hantera resurser, men vanligtvis tilldelar du den här behörigheten till en användargrupp. Information om hur du skapar en ny Active Directory-användargrupp finns i [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Du behöver objekt-ID:t för den användargrupp som du vill använda för att hantera resurser. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Hämta rolldefinitions-ID:t

Du behöver också rolldefinitions-ID:t för den inbyggda roll med rollbaserad åtkomstkontroll som du vill använda för att ge åtkomst till användaren, användargruppen eller programmet. Normalt använder du rollen Ägare, Deltagare eller Läsare. Följande kommando visar hur du hämtar rolldefinitions-ID:t för ägarrollen:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

Om du inte redan har en resursgrupp där det hanterade programmets definition kan lagras skapar du en nu:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Skapa nu definitionsresursen för det hanterade programmet.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

### <a name="make-sure-users-can-see-your-definition"></a>Kontrollera att användare kan se din definition

Du har åtkomst till definitionen för hanterade program, men du vill kontrollera att andra användare i din organisation kan komma åt den. Ge dem minst rollen Läsare på definitionen. De kan ha ärvt den här åtkomstnivån från prenumerationen eller resursgruppen. För att kontrollera vem som har åtkomst till definitionen och lägga till användare eller grupper, se [Använda rollbaserad behörighet för att hantera åtkomst till resurserna i din Azure-prenumeration](../role-based-access-control/role-assignments-portal.md).

## <a name="create-the-managed-application"></a>Skapa det hanterade programmet

Du kan distribuera det hanterade programmet via portalen, PowerShell eller Azure CLI.

### <a name="powershell"></a>PowerShell

Först ska vi använda PowerShell för att distribuera det hanterade programmet.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

Nu finns det hanterade programmet och den hanterade infrastrukturen i prenumerationen.

### <a name="portal"></a>Portalen

Nu ska vi använda portalen för att distribuera det hanterade programmet. Du kan se användargränssnittet som du skapade i paketet.

1. Gå till Azure Portal. Välj **+ Skapa en resurs** och sök efter **tjänstkatalog**.

   ![Sök efter tjänstkatalog](./media/publish-service-catalog-app/create-new.png)

1. Välj **Tjänstkatalogen för hanterade program**.

   ![Välj tjänstkatalog](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Välj **Skapa**.

   ![Välj Skapa](./media/publish-service-catalog-app/select-create.png)

1. Leta upp det hanterade program som du vill skapa i listan över tillgängliga lösningar och välj det. Välj **Skapa**.

   ![Leta upp det hanterade programmet](./media/publish-service-catalog-app/find-application.png)

   Om du inte ser definition för hanterade program via portalen kan du behöva ändra dina portalinställningar. Välj **Katalog- och prenumerationsfilter**.

   ![Välj prenumerationsfilter](./media/publish-service-catalog-app/select-filter.png)

   Kontrollera att det globala prenumerationsfiltret innehåller den prenumeration som innehåller den hanterade programdefinitionen.

   ![Kontrollera prenumerationsfilter](./media/publish-service-catalog-app/check-global-filter.png)

   När du har valt prenumerationen, börja om från början genom att skapa tjänsten kataloghanterat program. Nu bör du se den.

1. Ange grundläggande information som krävs för det hanterade programmet. Ange prenumerationen och en ny resursgrupp som ska innehålla det hanterade programmet. Välj **USA, västra centrala** för platsen. När du är klar väljer du **OK**.

   ![Ange parametrar för det hanterade programmet](./media/publish-service-catalog-app/add-basics.png)

1. Ange värden som är specifika för resurserna i det hanterade programmet. När du är klar väljer du **OK**.

   ![Ange resursparametrar](./media/publish-service-catalog-app/add-storage-settings.png)

1. Mallen verifierar de värden du angett. Om verifieringen lyckas väljer du **OK** för att starta distributionen.

   ![Verifiera det hanterade programmet](./media/publish-service-catalog-app/view-summary.png)

När distributionen är klar finns det hanterade programmet i en resursgrupp med namnet applicationGroup. Lagringskontot finns i en resursgrupp med namnet applicationGroup, samt ett hash-kodat strängvärde.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Exempelprojekt hittar du i [exempelprojekten för Azure-hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.
