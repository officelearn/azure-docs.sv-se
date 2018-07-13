---
title: Nyckelvalvshemligheten med Azure Resource Manager-mall | Microsoft Docs
description: Visar hur du skickar en hemlighet från key vault som en parameter under distributionen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/09/2018
ms.author: tomfitz
ms.openlocfilehash: 3a29319a0d478537dfc4905ee77865b8fea64587
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38598415"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använda Azure Key Vault för att skicka säkra parametervärdet under distributionen

När du vill skicka ett säkert värde (t.ex. ett lösenord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-whatis.md). Du kan hämta värdet genom att referera till nyckelvalvet och hemligheten i parameterfilen. Värdet exponeras aldrig eftersom du bara referera till dess nyckelvalv-ID. Nyckelvalvet kan finnas i en annan prenumeration än den resursgrupp som du distribuerar till.

## <a name="enable-access-to-the-secret"></a>Aktivera åtkomst till hemligheten

Det finns två viktiga villkor som måste finnas för att komma åt ett nyckelvalv när mallen distribueras:

1. Egenskapen nyckelvalv `enabledForTemplateDeployment` måste vara `true`.
2. Du distribuerar mallen måste ha åtkomst till hemligheten. Användaren måste ha den `Microsoft.KeyVault/vaults/deploy/action` behörighet för key vault. Den [ägare](../role-based-access-control/built-in-roles.md#owner) och [deltagare](../role-based-access-control/built-in-roles.md#contributor) båda bevilja åtkomst.

När du använder ett Nyckelvalv med hjälp av mallen för en [Managed Application](../managed-applications/overview.md), måste du bevilja åtkomst till den **installation Resource Provider** tjänstens huvudnamn. Mer information finns i [åtkomst till Key Vault-hemlighet när du distribuerar Azure Managed Applications](../managed-applications/key-vault-access.md).


## <a name="deploy-a-key-vault-and-secret"></a>Distribuera ett nyckelvalv och en hemlighet

Använd Azure CLI eller PowerShell för att skapa ett nyckelvalv och hemlighet. Observera att nyckelvalvet är aktiverat för malldistribution. 

Om du använder Azure CLI använder du:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Om du använder PowerShell använder du:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="reference-a-secret-with-static-id"></a>Referera till en hemlighet med statiska ID

Mallen som tar emot en hemlighet i nyckelvalvet är som alla andra mallar. Det beror på **du referera till key vault i parameterfilen, inte i mallen.** Följande bild visar hur parameterfilen refererar till hemligheten och skickar det värdet i mallen.

![Statisk ID](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Till exempel den [följande mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) distribuerar en SQL-databas som innehåller ett administratörslösenord. Lösenordsparametern har angetts till en säker sträng. Men mallen anger inte där värdet kommer från.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Nu skapa en parameterfil för föregående mall. Ange en parameter som matchar namnet på parametern i mallen i parameterfilen. För parametern, referera till hemligheten från nyckelvalvet. Du kan referera hemligheten genom att skicka resurs-ID för nyckelvalvet och namnet på hemligheten. I den [följa parameterfilen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json)key vault-hemlighet måste redan finnas och du kan ange ett statiskt värde för dess resurs-ID. Kopiera filen lokalt och ange prenumerations-ID, valvnamnet och SQL-servernamnet.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Om du vill använda en version av hemligheten än den aktuella versionen kan du använda den `secretVersion` egenskapen.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Distribuera mallen och skicka in parameterfilen. Du kan använda mallen exempel från GitHub, men du måste använda en lokal parameterfil med de värden som anges i miljön.

Om du använder Azure CLI använder du:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Referera till en hemlighet med dynamiska ID

Föregående avsnitt visade hur du skickar en statisk resurs-ID för key vault-hemlighet. Men i vissa fall kan behöva du referera till en key vault-hemlighet som varierar baserat på den aktuella distributionen. I så fall kan du inte hårdkoda resurs-ID i parameterfilen. Dynamiskt Tyvärr kan du kan inte generera resurs-ID i parameterfilen eftersom malluttryck inte tillåts i parameterfilen.

För att dynamiskt generera resurs-ID för en hemlighet i nyckelvalvet, måste du flytta den resurs som behöver hemligheten i en länkad mall. I den överordnade mallen, du lägger till länkad mall och skicka in en parameter som innehåller dynamiskt skapade resurs-ID. Följande bild visar hur en parameter i mallen länkade refererar till hemligheten.

![Dynamisk ID](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

Länkade mallen måste vara tillgänglig via en extern URI. Normalt kan du lägga till mallen till ett lagringskonto och via URI som `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

Den [följande mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) dynamiskt skapar nyckelvalvs-ID och skickar det som en parameter. Den länkar till en [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) i GitHub.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Distribuera föregående mall och ange värden för parametrarna. Du kan använda mallen exempel från GitHub, men du måste ange parametervärden för din miljö.

Om du använder Azure CLI använder du:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>Nästa steg
* Allmän information om viktiga valv finns i [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).
* Komplett exempel på refererar till viktiga hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
