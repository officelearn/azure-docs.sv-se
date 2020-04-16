---
title: Hanterad tjänstkatalog hanterad app
description: Visar hur du skapar ett Azure-hanterat program som är avsett för medlemmar i din organisation.
author: tfitzmac
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: 48aaca64949aafecff27c76ad7572b3c2fa44732
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391509"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Snabbstart: Skapa och publicera en hanterad programdefinition

Den här snabbstarten ger en introduktion till hur du arbetar med [Azure Managed Applications](overview.md). Du kan skapa och publicera ett hanterat program som är avsett för medlemmar i organisationen.

Om du vill publicera ett hanterat program i tjänstkatalogen måste du:

* Skapa en mall som definierar resurserna som ska distribueras med det hanterade programmet.
* Definiera användargränssnittselementen för portalen när du distribuerar det hanterade programmet.
* Skapa ett ZIP-paket som innehåller nödvändiga mallfiler.
* Bestämma vilken användare, vilken grupp eller vilket program som behöver åtkomst till resursgruppen i användarens prenumeration.
* Skapa definitionen för det hanterade programmet som pekar på ZIP-paketet och begär åtkomst för identiteten.

## <a name="create-the-arm-template"></a>Skapa ARM-mallen

Alla definitioner för hanterade program innehåller en fil med namnet **mainTemplate.json**. I den filen definierar du Azure-resurserna som ska distribueras. Mallen skiljer sig inte från en vanlig AZURE Resource Manager -mall (ARM).

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
            "apiVersion": "2019-06-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
        }
    }
}
```

Spara filen mainTemplate.json.

## <a name="define-your-create-experience"></a>Definiera din skapa upplevelse

Som utgivare definierar du portalupplevelsen för att skapa det hanterade programmet. Filen **createUiDefinition.json** genererar portalgränssnittet. Du definierar hur användare anger indata för varje parameter med hjälp av [kontrollelement,](create-uidefinition-elements.md) inklusive listrutor, textrutor och lösenordsrutor.

Skapa en fil med namnet **createUiDefinition.json** (Det här namnet är skiftlägeskänsligt)

Lägg till följande startspelare JSON i filen och spara den.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
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

Mer information finns i [Kom igång med CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Paketera filerna

Lägg till de två filerna i en ZIP-fil med namnet app.zip. Båda filerna måste ligga på rotnivå i ZIP-filen. Om du lägger dem i en mapp får du ett felmeddelande när du skapar definitionen för det hanterade programmet, som anger att de nödvändiga filerna saknas.

Ladda upp paketet till en tillgänglig plats som det kan användas från. Du måste ange ett unikt namn för lagringskontot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Skapa en Azure Active Directory-användargrupp eller ett Azure Active Directory-program

Nästa steg är att välja en användargrupp, användare eller ett program för att hantera resurserna för kunden. Den här identiteten har behörigheter på den hanterade resursgruppen enligt den roll som är tilldelad. Rollen kan vara en inbyggd roll för rollbaserad åtkomstkontroll (RBAC) som Ägare eller Deltagare. Information om hur du skapar en ny Active Directory-användargrupp finns i [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Du behöver objekt-ID:t för den användargrupp som du vill använda för att hantera resurser. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>Hämta rolldefinitions-ID:t

Du behöver också rolldefinitions-ID:t för den inbyggda roll med rollbaserad åtkomstkontroll som du vill använda för att ge åtkomst till användaren, användargruppen eller programmet. Normalt använder du rollen Ägare, Deltagare eller Läsare. Följande kommando visar hur du hämtar rolldefinitions-ID:t för ägarrollen:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>Skapa definitionen för det hanterade programmet

Om du inte redan har en resursgrupp där det hanterade programmets definition kan lagras skapar du en nu:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Skapa nu definitionsresursen för det hanterade programmet.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

När kommandot har slutförts har du en definition för det hanterade programmet i resursgruppen.

Några av de parametrar som användes i det föregående exemplet är:

* **resursgrupp**: Namnet på den resursgrupp där den hanterade programdefinitionen skapas.
* **låsnivå**: Den typ av lås som placeras på den hanterade resursgruppen. Det förhindrar kunden från att utföra oönskade åtgärder på den här resursgruppen. ReadOnly stöds för närvarande endast på låsnivå. När ReadOnly har angivits kan kunden endast läsa resurser som finns i den hanterade resursgruppen. Utgivaridentiteter som beviljas åtkomst till den hanterade resursgruppen är undantagna från låset.
* **auktorisering**: Beskriver huvudkontots ID och rolldefinitions-ID som används för att ge behörigheter till den hanterade resursgruppen. Det anges i formatet `<principalId>:<roleDefinitionId>`. Om det krävs fler än ett värde anger du dem i formatet `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Värdena avgränsas med ett blanksteg.
* **paketfil URI**: Platsen för ett ZIP-paket som innehåller de nödvändiga filerna.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Ta med egen lagring för den hanterade programdefinitionen

Du kan välja att lagra den hanterade programdefinitionen i ett lagringskonto som du tillhandahåller när du skapar så att dess plats och åtkomst kan hanteras fullständigt av dig för dina lagstadgade behov.

> [!NOTE]
> Ta med din egen lagring stöds endast med ARM-mall eller REST API-distributioner av den hanterade programdefinitionen.

### <a name="select-your-storage-account"></a>Välj ditt lagringskonto

Du måste [skapa ett lagringskonto](../../storage/common/storage-account-create.md) för att innehålla den hanterade programdefinitionen för användning med Service Catalog.

Kopiera lagringskontots resurs-ID. Den kommer att användas senare när du distribuerar definitionen.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Ange rolltilldelning för "Leverantör av apparatresurser" i ditt lagringskonto

Innan den hanterade programdefinitionen kan distribueras till ditt lagringskonto måste du ge deltagarbehörighet till rollen **Resursprovider** för apparat så att den kan skriva definitionsfilerna till lagringskontots behållare.

1. Navigera till ditt lagringskonto i [Azure-portalen.](https://portal.azure.com)
1. Välj **Åtkomstkontroll (IAM)** om du vill visa åtkomstkontrollinställningarna för lagringskontot. Välj fliken **Rolltilldelningar** om du vill visa listan över rolltilldelningar.
1. Välj rollen **Deltagare** i fönstret **Lägg till rolltilldelning.** 
1. Välj **Azure AD-användare, grupp eller tjänsthuvudnamn**i fältet **Tilldela åtkomst till.**
1. Sök efter rollen Resursleverantör för **apparat** under **Välj**och välj den.
1. Spara rolltilldelningen.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Distribuera den hanterade programdefinitionen med en ARM-mall 

Använd följande ARM-mall för att distribuera det paketerade hanterade programmet som en ny hanterad programdefinition i Service Catalog vars definitionsfiler lagras och underhålls i ditt eget lagringskonto:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Vi har lagt till en ny egenskap med namnet **storageAccountId** till din applicationDefintions egenskaper och tillhandahåller lagringskonto-ID som du vill lagra din definition i som värde:

Du kan kontrollera att programdefinitionsfilerna sparas i det angivna lagringskontot i en behållare med namnet **applicationdefinitions**.

> [!NOTE]
> För ökad säkerhet kan du skapa en definition av hanterade program lagra den i en [Azure storage account-blob där kryptering är aktiverad](../../storage/common/storage-service-encryption.md). Definitionsinnehållet krypteras via lagringskontots krypteringsalternativ. Endast användare med behörighet till filen kan se definitionen i Service Catalog.

## <a name="make-sure-users-can-see-your-definition"></a>Kontrollera att användare kan se din definition

Du har åtkomst till definitionen för hanterade program, men du vill kontrollera att andra användare i din organisation kan komma åt den. Ge dem minst rollen Läsare på definitionen. De kan ha ärvt den här åtkomstnivån från prenumerationen eller resursgruppen. För att kontrollera vem som har åtkomst till definitionen och lägga till användare eller grupper, se [Använda rollbaserad behörighet för att hantera åtkomst till resurserna i din Azure-prenumeration](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

Du har publicerat definitionen av det hanterade programmet. Nu ska du lära dig hur du distribuerar en instans av den definitionen.

> [!div class="nextstepaction"]
> [Snabbstart: Distribuera tjänstkatalogapp](deploy-service-catalog-quickstart.md)
