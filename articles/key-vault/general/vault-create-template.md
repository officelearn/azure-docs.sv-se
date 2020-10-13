---
title: Azure-guide – skapa ett Azure Key Vault och en valv åtkomst princip med hjälp av Azure Resource Manager mall | Microsoft Docs
description: Visar hur du skapar Azure Key Vault och valv åtkomst principer med hjälp av Azure Resource Manager mall.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/5/2020
ms.author: mbaldwin
ms.openlocfilehash: cf19561005fe2e98b7b5cf6812ff9224fd9474dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91804400"
---
# <a name="how-to-create-azure-key-vault-and-vault-access-policy-using-a-resource-manager-template"></a>Så här skapar du Azure Key Vault-och valv åtkomst princip med hjälp av en Resource Manager-mall

[Azure Key Vault](../general/overview.md) är en moln tjänst som ger en säker lagring för hemligheter, till exempel nycklar, lösen ord, certifikat och andra hemligheter. Den här guiden fokuserar på processen att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa ett nyckel valv.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här artikeln:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.


## <a name="create-key-vault-resource-manager-template"></a>Skapa Key Vault Resource Manager-mall

Följande mall visar ett enkelt sätt att skapa Key Vault. Vissa värden anges i mallen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    }
  ]
}

```

Mer information om inställningar för Key Vault mal len finns i [referens för Key Vault arm-mall](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> Om mallen omdistribueras åsidosätts eventuella befintliga åtkomst principer i nyckel valvet. Vi rekommenderar att du fyller i en `accessPolicies` egenskap med befintliga åtkomst principer för att undvika återfick åtkomst till nyckel valvet. 

## <a name="add-access-policy-to-key-vault-resource-manager-template"></a>Lägg till åtkomst princip i Key Vault Resource Manager-mall

Du kan distribuera åtkomst principer till befintligt nyckel valv utan att omdistribuera hela Key Vault-mallen. Följande mall visar ett enkelt sätt att skapa åtkomst principer.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": [parameters('certificatesPermissions')]
            }
          }
        ]
      }
    }
  ]
}

```
Mer information om inställningar för Key Vault mal len finns i [referens för Key Vault arm-mall](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="other-available-key-vault-resource-manager-templates"></a>Andra tillgängliga Key Vault Resource Manager-mallar

Det finns andra tillgängliga Resource Manager-mallar för Key Vault objekt:

| Hemligheter | Nycklar | Certifikat |
|--|--|--|
|[Snabbstart](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-template)<br>[Referens](https://docs.microsoft.com/azure/templates/microsoft.keyvault/vaults/secrets)|Saknas|Saknas|

Fler Key Vault mallar som du kan hitta här: [Key Vault Resource Manager-referens](https://docs.microsoft.com/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>Distribuera mallarna

Du kan använda Azure Portal för att distribuera över mallar med hjälp av alternativet "Bygg en egen mall i redigerings programmet" i guiden nedan: [distribuera resurser från anpassad mall](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template)

Du kan också spara över mallar till filer och använda följande kommandon:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) och [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta och arbeta med efterföljande snabb starter och självstudier kan du lämna resurserna på plats. När resurserna inte längre behövs tar du bort resurs gruppen, som tar bort nyckel valvet och relaterade resurser. Om du vill ta bort resurs gruppen med hjälp av Azure CLI eller Azure PowerShell använder du följande steg.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="resources"></a>Resurser

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Granska [Azure Key Vault bästa praxis](../general/best-practices.md)

## <a name="next-steps"></a>Nästa steg

- [Säker åtkomst till ett nyckel valv](secure-your-key-vault.md)
- [Autentisera till ett nyckel valv](authentication.md)
- [Guide för Azure Key Vault utvecklare](developers-guide.md)
