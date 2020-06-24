---
title: Använda hanterade identiteter i Azure API Management | Microsoft Docs
description: Lär dig hur du använder hanterade identiteter i API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 028b26537c9fe8a976dbc68a4776b2ea4101d811
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789450"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Använda hanterade identiteter i Azure API Management

Den här artikeln visar hur du skapar en hanterad identitet för en API Management instans och hur du kommer åt andra resurser. En hanterad identitet som genereras av Azure Active Directory (Azure AD) gör att API Management-instansen enkelt och säkert kan komma åt andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Den här identiteten hanteras av Azure och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

En API Management instans kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är kopplad till din tjänst och tas bort om tjänsten tas bort. Tjänsten kan bara ha en tilldelad identitet.
- En **användardefinierad identitet** är en fristående Azure-resurs som kan tilldelas till din tjänst. Tjänsten kan ha flera användarspecifika identiteter (*).

## <a name="create-a-system-assigned-managed-identity-for-an-api-management-instance"></a>Skapa en systemtilldelad hanterad identitet för en API Management instans

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterad identitet i portalen skapar du först en API Management-instans som normal och aktiverar sedan funktionen.

1. Skapa en API Management-instans i portalen på vanligt sätt. Gå till den i portalen.
2. Välj **hanterade identiteter**.
3. Växla **status** till **på på**fliken **systemtilldelad** . Klicka på **Spara**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Aktivera systemtilldelad hanterad-identitet." border="true":::


### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg vägleder dig genom att skapa en API Management-instans och tilldela den en identitet med hjälp av Azure PowerShell. 

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/install-az-ps)och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

2. Skapa en API Management-instans med hjälp av Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med API Management instans finns i [API Management PowerShell-exempel](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Uppdatera och befintlig instans för att skapa identiteten:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="using-the-azure-resource-manager-template"></a>Använda Azure Resource Manager mall

Du kan skapa en API Management-instans med en identitet genom att inkludera följande egenskap i resurs definitionen:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Detta meddelar Azure att skapa och hantera identiteten för din API Management-instans.

En fullständig Azure Resource Manager mall kan till exempel se ut så här:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
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
    }]
}
```

När instansen skapas har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Egenskapen tenantId identifierar vilken Azure AD-klient identiteten tillhör. PrincipalId är en unik identifierare för instansen ny identitet. I Azure AD har tjänstens huvud namn samma namn som du gav API Management-instansen.


> [!NOTE]
> En API Management instans kan ha både tilldelade och användarspecifika identiteter på samma gång. I det här fallet `type` skulle egenskapen vara`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Scenarier som stöds

#### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Hämta ett anpassat TLS/SSL-certifikat för API Management-instansen från Azure Key Vault
Den systemtilldelade identiteten för en API Management-tjänst kan användas för att hämta anpassade TLS/SSL-certifikat som lagras i Azure Key Vault. Dessa certifikat kan tilldelas till anpassade domäner i API Management instans.

1. Innehålls typen för hemligheten måste vara *Application/x-PKCS12*.
2. Slut punkten för Key Vault certifikatets hemlighet ska användas, som innehåller den faktiska hemligheten.

> [!Important]
> Om objekt versionen av certifikatet inte anges får API Management automatiskt den nyare versionen av certifikatet när den har laddats upp till Key Vault inom 4 timmar

I följande exempel visas en Azure Resource Manager mall som innehåller följande steg:

1. Skapa en API Management-instans med en hanterad identitet.
2. Uppdatera åtkomst principerna för en Azure Key Vault-instans och Tillåt att API Management-instansen hämtar hemligheter från den.
3. Uppdatera API Management-instansen genom att ange ett eget domän namn genom ett certifikat från Key Vault-instansen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
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
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
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

#### <a name="authenticate-using-api-management-identity-to-the-backend"></a>Autentisera med API Management identitet till Server delen

Systemets tilldelade identitet kan användas för att autentisera till Server delen med hjälp av principen för [autentisering med autentisering](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="create-a-user-assigned-managed-identity-for-an-api-management-instance"></a>Skapa en användardefinierad hanterad identitet för en API Management instans

> [!NOTE]
> En API Management instans kan associeras med upp till 10 användare som tilldelats en hanterad identitet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterad identitet i portalen skapar du först en API Management-instans som normal och aktiverar sedan funktionen.

1. Skapa en API Management-instans i portalen på vanligt sätt. Gå till den i portalen.
2. Välj **hanterade identiteter**.
3. Klicka på **Lägg till**i fliken **tilldelade användare** .
4. Sök efter den identitet som du skapade tidigare och markera den. Klicka på **Lägg till**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Aktivera användare som tilldelats hanterad-identitet." border="true":::

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg vägleder dig genom att skapa en API Management-instans och tilldela den en identitet med hjälp av Azure PowerShell. 

1. Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/install-az-ps)och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

2. Skapa en API Management-instans med hjälp av Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med API Management instans finns i [API Management PowerShell-exempel](powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Uppdatera och befintlig tjänst för att tilldela en identitet till tjänsten.

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="using-the-azure-resource-manager-template"></a>Använda Azure Resource Manager mall

Du kan skapa en API Management-instans med en identitet genom att inkludera följande egenskap i resurs definitionen:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Genom att lägga till en användardefinierad typ anger att Azure ska använda den användar tilldelnings identitet som angetts för din instans.

En fullständig Azure Resource Manager mall kan till exempel se ut så här:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
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
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
        "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

När tjänsten skapas har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

PrincipalId är en unik identifierare för den identitet som används för Azure AD-administration. ClientId är en unik identifierare för programmets nya identitet som används för att ange vilken identitet som ska användas vid körnings anrop.

> [!NOTE]
> En API Management instans kan ha både tilldelade och användarspecifika identiteter på samma gång. I det här fallet `type` skulle egenskapen vara`SystemAssigned,UserAssigned`

### <a name="scenarios-supported"></a>Scenarier som stöds

#### <a name="authenticate-using-user-assigned-identity-to-the-backend"></a>Autentisera med hjälp av användardefinierad identitet till Server delen

Användarens tilldelade identitet kan användas för att autentisera till Server delen med hjälp av principen för [autentiserings-hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) .


## <a name="remove-an-identity"></a><a name="remove"></a>Ta bort en identitet

En systemtilldelad identitet kan tas bort genom att inaktivera funktionen med hjälp av portalen eller Azure Resource Manager mallen på samma sätt som den skapades. Användare-tilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter anger du identitets typen till "ingen".

Om du tar bort en tilldelad identitet på det här sättet tas även den bort från Azure AD. Systemtilldelade identiteter tas också automatiskt bort från Azure AD när API Management-instansen tas bort.

Uppdatera det här avsnittet om du vill ta bort alla identiteter med Azure Resource Manager mall:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Om en API Management instans har kon figurer ATS med anpassat SSL-certifikat från ett nyckel valv och försök görs att inaktivera hanterad identitet, Miss lyckas begäran.
> Kunden kan häva blockeringen genom att växla från Azure Key Vault certifikat för att tillhandahålla inline-kodade certifikat och sedan inaktivera hanterad identitet. Se [Konfigurera anpassad domän](configure-custom-domain.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hanterade identiteter för Azure-resurser:

* [Vad är hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates)
* [Autentisera med en hanterad identitet i en princip](./api-management-authentication-policies.md#ManagedIdentity)
