---
title: Använd Azure hanterade tjänstidentiteten i Azure API Management | Microsoft Docs
description: Lär dig hur du använder Azure hanterade tjänstidentiteten i API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 98aa70935a3efbbe2edb07aade85fa3ea17ce786
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Använd Azure hanterade tjänstidentiteten i Azure API Management

Den här artikeln visar hur du skapar en hanterad tjänstidentitet för en instans för API Management-tjänsten och hur du kommer åt andra resurser. En hanterade tjänstidentiteten som genereras av Azure Active Directory (Azure AD) kan din API Management-instans enkel och säker åtkomst till andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Den här hanterade tjänstidentiteten hanteras av Azure och kräver inte att etablera eller rotera alla hemligheter. Mer information om Azure hanterade tjänstidentiteten finns [hanterade tjänstidentiteten för Azure-resurser](../active-directory/msi-overview.md).

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>Skapa en hanterad tjänstidentitet för en instans för API Management

### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att ställa in en hanterad tjänstidentitet i portalen ska du först skapa en API Management-instans som vanligt och aktivera funktionen.

1. Skapa en instans för API Management i portalen som vanligt. Navigera till den i portalen.
2. Välj **hanterade tjänstidentiteten**.
3. Växla registrera med Azure Active Directory till On. Klicka på Spara.

![Aktivera MSI](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Med hjälp av Azure Resource Manager-mall

Du kan skapa en instans för API Management med en identitet genom att inkludera följande egenskapen i resursdefinitionen: 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Detta visar Azure för att skapa och hantera identitet för din API Management-instans. 

En fullständig Azure Resource Manager-mall kan se ut ungefär så här:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Använda hanterade tjänstidentiteten för att komma åt andra resurser

> [!NOTE]
> För närvarande kan hanterade tjänstidentiteten användas för att hämta certifikat från Azure Key Vault för API Management anpassade domännamn. Flera scenarier kommer snart att stödjas.
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Skaffa ett certifikat från Azure Key Vault

#### <a name="prerequisites"></a>Förutsättningar
1. Nyckelvalvet som innehåller pfx-certifikat måste finnas i samma Azure-prenumerationen och samma resursgrupp som API Management-tjänsten. Detta är ett krav för Azure Resource Manager-mallen. 
2. Innehållstypen för hemlighet måste vara *pkcs12-program/x*. Du kan använda följande skript för att ladda upp certifikatet:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Om objektversion av certifikatet inte tillhandahålls, hämta API Management automatiskt den nya versionen av certifikatet när det överförs till Nyckelvalvet. 

I följande exempel visas en Azure Resource Manager-mall som innehåller följande steg:

1. Skapa en API Management-instans med en hanterad tjänst-identitet.
2. Uppdatera principer för åtkomst av en Azure Key Vault-instans och Tillåt API Management-instansen för att erhålla hemligheter från den.
3. Uppdatera API Management-instans genom att ange ett eget domännamn via ett certifikat från Key Vault-instans.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure hanterade tjänstidentiteten:

* [Hanterade tjänstidentiteten för Azure-resurser](../active-directory/msi-overview.md)
* [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates)

