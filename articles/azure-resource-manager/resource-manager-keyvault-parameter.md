---
title: Key Vault hemligheten med Resource Manager-mall | Microsoft Docs
description: "Visar hur du skickar en hemlighet från ett nyckelvalv som en parameter under distributionen."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 1ca72599e67e79d42a3d430dbb13e89ea7265334
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Använda Nyckelvalv för att skicka säkra parametervärdet under distributionen

När du vill skicka ett säkert värde (till exempel ett lösenord) som en parameter under distributionen kan du hämta värdet från en [Azure Key Vault](../key-vault/key-vault-whatis.md). Du kan hämta värdet genom att referera till nyckelvalvet och hemlighet i parameterfilen. Värdet exponeras aldrig eftersom du endast referera till ett nyckelvalv-ID. Du behöver inte manuellt ange ett värde för hemligheten som varje gång du distribuerar resurserna. Nyckelvalvet kan finnas i en annan prenumeration än den resursgrupp som du distribuerar till. När du refererar till nyckelvalvet inkludera du prenumerations-ID.

Ange när du skapar nyckelvalvet i *enabledForTemplateDeployment* egenskapen *SANT*. Genom att ange värdet till true kan bevilja du åtkomst från Resource Manager-mallar under distributionen.  

## <a name="deploy-a-key-vault-and-secret"></a>Distribuera ett nyckelvalv och hemlighet

Om du vill skapa ett nyckelvalv och hemlighet, använder du Azure CLI eller PowerShell. Observera att nyckelvalvet är aktiverat för malldistribution. 

Om du använder Azure CLI använder du:

```azurecli
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create --name $vaultname --resource-group examplegroup --location 'South Central US' --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Om du använder PowerShell använder du:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault -VaultName $vaultname -ResourceGroupName examplegroup -Location "South Central US" -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Aktivera åtkomst till hemligheten

Se till att distribuera mallen användaren kan komma åt hemligheten som om du använder ett nytt nyckelvalv eller en befintlig. Användaren som distribuerar en mall som refererar till en hemlighet måste ha den `Microsoft.KeyVault/vaults/deploy/action` för nyckelvalvet. Den [ägare](../active-directory/role-based-access-built-in-roles.md#owner) och [deltagare](../active-directory/role-based-access-built-in-roles.md#contributor) roller båda bevilja åtkomst. Du kan också skapa en [anpassad roll](../active-directory/role-based-access-control-custom-roles.md) som beviljar behörighet och lägga till användaren i rollen. Information om att lägga till en användare till en roll finns [Tilldela administratörsroller i Azure Active Directory användare](../active-directory/active-directory-users-assign-role-azure-portal.md).


## <a name="reference-a-secret-with-static-id"></a>Referera till en hemlighet med statiska ID

Den mall som tar emot en hemlighet i nyckelvalvet är precis som alla andra mallar. Det beror på **du refererar till nyckelvalvet i parameterfilen inte mall.** Följande mall distribuerar till exempel en SQL-databas som innehåller ett administratörslösenord. Parametern password anges till en säker sträng. Men mallen anger inte där värdet kommer från.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "string"
        },
        "administratorLoginPassword": {
            "type": "securestring"
        },
        "collation": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        },
        "edition": {
            "type": "string"
        },
        "requestedServiceObjectiveId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "maxSizeBytes": {
            "type": "string"
        },
        "serverName": {
            "type": "string"
        },
        "sampleName": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "name": "[parameters('serverName')]",
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "version": "12.0"
            },
            "resources": [
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "[parameters('databaseName')]",
                    "properties": {
                        "collation": "[parameters('collation')]",
                        "edition": "[parameters('edition')]",
                        "maxSizeBytes": "[parameters('maxSizeBytes')]",
                        "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]",
                        "sampleName": "[parameters('sampleName')]"
                    },
                    "type": "databases"
                },
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "AllowAllWindowsAzureIps",
                    "properties": {
                        "endIpAddress": "0.0.0.0",
                        "startIpAddress": "0.0.0.0"
                    },
                    "type": "firewallrules"
                }
            ],
            "type": "Microsoft.Sql/servers"
        }
    ]
}
```

Nu skapa en parameterfil för föregående mallen. Ange en parameter som matchar namnet på parametern i mallen i parameterfilen. Parametervärde, referera till hemligheten från nyckelvalvet. Du kan referera hemligheten genom att skicka resurs-ID för nyckelvalvet och namnet på hemligheten. I följande exempel nyckelvalv hemlighet måste redan finnas och du kan ange ett statiskt värde för resurs-ID.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "value": "exampleadmin"
        },
        "administratorLoginPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/{guid}/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/{vault-name}"
              },
              "secretName": "examplesecret"
            }
        },
        "collation": {
            "value": "SQL_Latin1_General_CP1_CI_AS"
        },
        "databaseName": {
            "value": "exampledb"
        },
        "edition": {
            "value": "Standard"
        },
        "location": {
            "value": "southcentralus"
        },
        "maxSizeBytes": {
            "value": "268435456000"
        },
        "requestedServiceObjectiveId": {
            "value": "455330e1-00cd-488b-b5fa-177c226f28b7"
        },
        "sampleName": {
            "value": ""
        },
        "serverName": {
            "value": "exampleserver"
        }
    }
}
```

## <a name="reference-a-secret-with-dynamic-id"></a>Referera till en hemlighet med dynamiska ID

I föregående avsnitt visades hur du skickar en statisk resurs-ID för nyckelvalvet hemlighet. Dock i vissa fall kan behöva du referera en nyckelvalv hemlighet som varierar baserat på den aktuella distributionen. I så fall, det går inte att hårdkoda resurs-ID i filen parametrar. Tyvärr kan generera du inte dynamiskt resurs-ID i parameterfilen eftersom malluttryck inte är tillåtna i parameterfilen.

För att dynamiskt generera resurs-ID för en hemlighet i nyckelvalvet, måste du flytta resursen som behöver hemligheten i en kapslad mall. I mallen master du lägger till den kapslade mallen och ange en parameter som innehåller dynamiskt genererade resurs-ID.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
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
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Nästa steg
* Allmän information om nyckelvalv finns [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md).
* Komplett exempel på refererar till viktiga hemligheter finns [Key Vault exempel](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

