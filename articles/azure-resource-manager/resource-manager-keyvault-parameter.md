---
title: Key Vault hemligheten med Azure Resource Manager-mall | Microsoft Docs
description: "Visar hur du skickar en hemlighet från ett nyckelvalv som en parameter under distributionen."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: tomfitz
ms.openlocfilehash: e789a234979be877d990665902fd6219ae7ec40b
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använda Azure Key Vault för att skicka säkra parametervärdet under distributionen

När du vill skicka ett säkert värde (till exempel ett lösenord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-whatis.md). Du kan hämta värdet genom att referera till nyckelvalvet och hemlighet i parameterfilen. Värdet exponeras aldrig eftersom du endast referera till ett nyckelvalv-ID. Du behöver inte manuellt ange ett värde för hemligheten som varje gång du distribuerar resurserna. Nyckelvalvet kan finnas i en annan prenumeration än den resursgrupp som du distribuerar till. När du refererar till nyckelvalvet inkludera du prenumerations-ID.

Ange när du skapar nyckelvalvet i *enabledForTemplateDeployment* egenskapen *SANT*. Genom att ange värdet till true kan bevilja du åtkomst från Resource Manager-mallar under distributionen.

## <a name="deploy-a-key-vault-and-secret"></a>Distribuera ett nyckelvalv och hemlighet

Om du vill skapa ett nyckelvalv och hemlighet, använder du Azure CLI eller PowerShell. Observera att nyckelvalvet är aktiverat för malldistribution. 

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

## <a name="enable-access-to-the-secret"></a>Aktivera åtkomst till hemligheten

Se till att distribuera mallen användaren kan komma åt hemligheten som om du använder ett nytt nyckelvalv eller en befintlig. Användaren som distribuerar en mall som refererar till en hemlighet måste ha den `Microsoft.KeyVault/vaults/deploy/action` för nyckelvalvet. Den [ägare](../active-directory/role-based-access-built-in-roles.md#owner) och [deltagare](../active-directory/role-based-access-built-in-roles.md#contributor) roller båda bevilja åtkomst.

## <a name="reference-a-secret-with-static-id"></a>Referera till en hemlighet med statiska ID

Den mall som tar emot en hemlighet i nyckelvalvet är precis som alla andra mallar. Det beror på **du refererar till nyckelvalvet i parameterfilen inte mall.** Följande mall distribuerar till exempel en SQL-databas som innehåller ett administratörslösenord. Parametern password anges till en säker sträng. Men mallen anger inte där värdet kommer från.

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

Nu skapa en parameterfil för föregående mallen. Ange en parameter som matchar namnet på parametern i mallen i parameterfilen. Parametervärde, referera till hemligheten från nyckelvalvet. Du kan referera hemligheten genom att skicka resurs-ID för nyckelvalvet och namnet på hemligheten. I följande exempel nyckelvalv hemlighet måste redan finnas och du kan ange ett statiskt värde för resurs-ID.

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

Nu kan distribuera mallen och ange parameterfilen. Om du använder Azure CLI använder du:

```azurecli-interactive
az group create --name datagroup --location "Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-file sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateFile sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Referera till en hemlighet med dynamiska ID

I föregående avsnitt visades hur du skickar en statisk resurs-ID för nyckelvalvet hemlighet. Dock i vissa fall kan behöva du referera en nyckelvalv hemlighet som varierar baserat på den aktuella distributionen. I så fall, det går inte att hårdkoda resurs-ID i filen parametrar. Tyvärr kan generera du inte dynamiskt resurs-ID i parameterfilen eftersom malluttryck inte är tillåtna i parameterfilen.

För att dynamiskt generera resurs-ID för en hemlighet i nyckelvalvet, måste du flytta resursen som behöver hemligheten i en kapslad mall. I mallen master du lägger till den kapslade mallen och ange en parameter som innehåller dynamiskt genererade resurs-ID. Mallen för kapslade måste vara tillgänglig via en extern URI. Resten av den här artikeln förutsätter att du har lagt till föregående mallen till ett lagringskonto och är tillgänglig via URI - `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

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
          "uri": "https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json",
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

Distribuera föregående mallen och ange värden för parametrarna.

## <a name="next-steps"></a>Nästa steg
* Allmän information om nyckelvalv finns [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).
* Komplett exempel på refererar till viktiga hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
