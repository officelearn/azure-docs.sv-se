---
title: Skapa och publicera ett program för katalogen som hanteras av Azure-tjänst | Microsoft Docs
description: Visar hur du skapar ett Azure-hanterat program som är avsett för medlemmar i din organisation.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: 57821e9c7ed1ca04aa7442f089268c5e89a017c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>Publicera ett hanterat program för internt bruk

Du kan skapa och publicera Azure [hanterade program](overview.md) som är avsedda för medlemmar i din organisation. Exempelvis kan en IT-avdelning publicera hanterade program som säkerställa efterlevnad med organisationens normer. Dessa hanterade program är tillgängliga via tjänstkatalog, inte på Azure marketplace.

Om du vill publicera ett hanterat program för tjänstkatalogen, måste du:

* Skapa en mall som definierar resurserna som ska distribuera med hanterade program.
* Definiera användargränssnittselement för portalen när du distribuerar det hanterade programmet.
* Skapa en ZIP-paketet som innehåller mallfilerna som krävs för.
* Bestäm vilka användare, grupp eller ett program behöver åtkomst till resursgruppen i användarens prenumeration.
* Skapa definition för hanterade program som pekar på ZIP-paketet och begär åtkomst för identiteten.

För den här artikeln innehåller det hanterade programmet bara ett lagringskonto. Den är avsedd att illustrera stegen för att publicera ett hanterade program. Komplett exempel finns [exempelprojekten för Azure hanterade program](sample-projects.md).

## <a name="create-the-resource-template"></a>Skapa resursmall för

Varje definition för hanterade program innehåller en fil med namnet **mainTemplate.json**. I det definierar du Azure-resurser för att etablera. Mallen skiljer sig inte från en vanlig Resource Manager-mall.

Skapa en fil med namnet **mainTemplate.json**. Namnet är skiftlägeskänsligt.

Lägg till följande JSON i din fil. Den definierar parametrar för att skapa ett lagringskonto och anger egenskaperna för lagringskontot.

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

## <a name="create-the-user-interface-definition"></a>Skapa gränssnittsdefinition användare

Azure-portalen använder den **createUiDefinition.json** filen för att generera användargränssnittet för användare som skapar det hanterade programmet. Du definierar hur användare ange indata för varje parameter. Du kan använda alternativ som en listrutan, textrutan, lösenord och andra indata verktyg. Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.

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

## <a name="package-the-files"></a>Paketet filerna

Lägga till de två filerna i en .zip-fil med namnet app.zip. De två filerna måste vara på rotnivå på ZIP-filen. Om du placerar dem i en mapp, felmeddelande ett när du skapar definition för hanterade program om filerna som krävs inte finns. 

Överför paketet till en tillgänglig plats från där den kan användas. 

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

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Skapa ett Azure Active Directory-användargrupp eller ett program

Nästa steg är att välja en användargrupp eller ett program för att hantera resurserna för kundens räkning. Den här användargruppen eller program du har behörighet på hanterade resursgruppen enligt den roll som är tilldelad. Rollen kan vara en inbyggd roll rollbaserad åtkomstkontroll (RBAC) som ägare eller deltagare. Du kan också ge en användarbehörighet att hantera resurser, men vanligtvis du tilldelar behörighet till en användargrupp. Om du vill skapa en ny Active Directory-användargrupp finns [skapar en grupp och lägga till medlemmar i Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Du måste objekt-ID i gruppen du använder för att hantera resurser. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Hämta rolldefinitions-ID:

Sedan måste roll definition-ID för den inbyggda RBAC-rollen som du vill bevilja åtkomst till användare, grupp eller programmet. Normalt använder du rollen ägare eller deltagare eller läsare. Följande kommando visar hur du hämtar rolldefinitions-ID:t för ägarrollen:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

Om du inte redan har en resursgrupp för att lagra dina definition för hanterade program ska du skapa en nu:

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

## <a name="create-the-managed-application"></a>Skapa det hanterade programmet

Du kan distribuera hanterade program via portalen, PowerShell eller Azure CLI.

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

Hanterade programmet och hanterad infrastruktur finns nu i prenumerationen.

### <a name="portal"></a>Portalen

Nu ska vi använda portalen för att distribuera det hanterade programmet. Du kan se användargränssnittet som du skapade i paketet.

1. Gå till Azure-portalen. Välj **+ skapa en resurs** och Sök efter **tjänstkatalogen**.

   ![Sök tjänstkatalogen](./media/publish-service-catalog-app/create-new.png)

1. Välj **Tjänstkatalogen hanterat program**.

   ![Välj tjänstkatalogen](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Välj **Skapa**.

   ![Välj Skapa](./media/publish-service-catalog-app/select-create.png)

1. Hitta det hanterade programmet som du vill skapa i listan över tillgängliga lösningar och markera den. Välj **Skapa**.

   ![Hitta det hanterade programmet](./media/publish-service-catalog-app/find-application.png)

1. Ange grundläggande information som krävs för det hanterade programmet. Ange prenumerationen och en ny resursgrupp som innehåller det hanterade programmet. Välj **Väst centrala oss** för platsen. När du är klar väljer du **OK**.

   ![Ange parametrar för hanterade program](./media/publish-service-catalog-app/add-basics.png)

1. Ange värden som är specifika för resurser i det hanterade programmet. När du är klar väljer du **OK**.

   ![Ange Resursparametrar för](./media/publish-service-catalog-app/add-storage-settings.png)

1. Mallen verifierar de värden du angav. Om verifieringen lyckas, väljer **OK** att starta distributionen.

   ![Validera hanterade program](./media/publish-service-catalog-app/view-summary.png)

När distributionen är klar finns det hanterade programmet i en resursgrupp med namnet applicationGroup. Storage-konto finns i en resursgrupp med namnet applicationGroup plus ett strängvärde som hashformaterats.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Till exempel projekt, se [exempelprojekten för Azure hanterade program](sample-projects.md).
* Se [Kom igång med CreateUiDefinition](create-uidefinition-overview.md) för att lära dig om hur du skapar en UI-definitionsfil för ett hanterat program.
