---
title: Key Vault - referenser i Azure App Service | Microsoft Docs
description: Konceptuell guiden för referens och installationsprogrammet för Azure Key Vault-referenser i Azure App Service och Azure Functions
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 662260c3cf37f8f8a675c522f3d3dea41153e485
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663579"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Använd Key Vault-referenser för App Service och Azure Functions (förhandsversion)

> [!NOTE] 
> Referenser för Key Vault finns för närvarande i förhandsversion.

Det här avsnittet visar hur du arbetar med hemligheter från Azure Key Vault i ditt program med App Service eller Azure Functions utan några kodändringar. [Azure Key Vault](../key-vault/key-vault-overview.md) är en tjänst som tillhandahåller centraliserad hemligheter, med fullständig kontroll över åtkomst principer och granska historiken.

## <a name="granting-your-app-access-to-key-vault"></a>Ge din appåtkomst till Key Vault

För att kunna läsa hemligheter från Nyckelvalvet, måste du ha ett valv som skapats och ge ditt appbehörighet att komma åt den.

1. Skapa ett nyckelvalv genom att följa den [Snabbstart för Key Vault](../key-vault/quick-create-cli.md).

1. Skapa en [systemtilldelade hanterad identitet](overview-managed-identity.md) för ditt program.

   > [!NOTE] 
   > Key Vault refererar till för närvarande endast stöd för system tilldelade hanterade identiteter. Användartilldelade identiteter kan inte användas.

1. Skapa en [åtkomstprincipen i Nyckelvalvet](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) för applikationsidentitet som du skapade tidigare. Aktivera hemliga behörighet ”Get” för den här principen. Konfigurera inte ”åtkomsträttigheter program” eller `appliationId` inställningar, eftersom det är inte kompatibel med en hanterad identitet.

## <a name="reference-syntax"></a>Referens-syntax

En referens för Key Vault är i formatet `@Microsoft.KeyVault({referenceString})`, där `{referenceString}` ersätts av något av följande alternativ:

> [!div class="mx-tdBreakAll"]
> | Referenssträng                                                            | Beskrivning                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | Den **SecretUri** ska vara fullständig dataplanet URI för en hemlighet i Key Vault, inklusive en version, t.ex. https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | Den **VaultName** ska namnet på Key Vault-resursen. Den **SecretName** bör vara namnet på mål-hemlighet. Den **SecretVersion** ska vara version av hemligheten att använda. |

> [!NOTE] 
> I den aktuella förhandsversionen krävs versioner. När du roterar hemligheter, kommer du behöva uppdatera versionen i konfigurationen av programmet.

En fullständig referens skulle se ut så här:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Du kan också:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Inställningar för datakälla program från Key Vault

Referenser för Key Vault kan användas som värden för [programinställningar](web-sites-configure.md#app-settings), så att du kan hålla hemligheter i Key Vault i stället för platsens konfiguration. Programinställningar krypteras på ett säkert sätt på plats, men om du behöver hemliga hanteringsfunktioner, de bör ingå i Key Vault.

Om du vill använda en Key Vault-referens för en programinställning, ange referensen som värde för inställningen. Din app kan referera till hemligheten via dess nyckel som vanligt. Inga kodändringar krävs.

> [!TIP]
> De flesta inställningar för program som använder Key Vault referenser bör markeras som platsinställningar, som du bör ha separat valv för varje miljö.

### <a name="azure-resource-manager-deployment"></a>Azure Resource Manager-distribution

Vid automatisering av resource-distributioner via Azure Resource Manager-mallar, kan du behöva ordna dina beroenden i en viss ordning till den här funktionen fungerar. Notera, behöver du definiera inställningar för program som sina egna resursen, i stället för en `siteConfig` -egenskapen i platsdefinitionen. Det beror på att platsen måste definieras först så att den systemtilldelade identiteten skapas med det och kan användas i åtkomstprincipen.

Ett exempel psuedo-mall för en funktionsapp kan se ut så här:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> I det här exemplet är källan kontroll distributionen beroende programinställningarna. Detta är normalt osäkra beteende, eftersom inställningen appuppdatering fungerar asynkront. Men eftersom vi har inkluderat i `WEBSITE_ENABLE_SYNC_UPDATE_SITE` programinställningen, uppdateringen är synkron. Det innebär att källan kontroll distribution endast börjar när programinställningarna har uppdaterats helt.
