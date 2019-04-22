---
title: Skapa en mall för Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du skapar en Resource Manager-mall för Service Fabric-kluster. Konfigurera säkerhet, Azure Key Vault och Azure Active Directory (Azure AD) för klientautentisering.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 2fdea1f088dd6eabdf7d72342c837d976133a1bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046200"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Skapa ett Service Fabric-kluster Resource Manager-mall

En [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella datorer som dina mikrotjänster distribueras och hanteras. Service Fabric-kluster som körs i Azure är en Azure-resurs och har distribuerats, hanterad och övervakas med Resource Manager.  Den här artikeln beskrivs hur skapa Resource Manager-mall för Service Fabric-kluster som körs i Azure.  När mallen är klar, kan du [Skapa kluster på Azure](service-fabric-cluster-creation-via-arm.md).

Klustersäkerhet konfigureras när klustret skapas först och kan inte ändras senare. Innan du konfigurerar ett kluster, läsa [säkerhetsscenarier för Service Fabric-kluster][service-fabric-cluster-security]. I Azure, Service Fabric använder x509 certifikat till säkra ditt kluster och dess slutpunkter, autentisera klienter och kryptera data. Azure Active Directory rekommenderas för säker åtkomst till av hanteringsslutpunkter. Azure AD-klienter och användare måste skapas innan du skapar klustret.  Mer information finns i [ställa in Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

Innan du distribuerar ett produktionskluster för att köra arbetsbelastningar i produktion, bör du först läsa den [produktion beredskap checklista](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen
Exemplet Resource Manager-mallar är tillgängliga i den [Azure-exempel på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångspunkt för mallen för klustret.

Den här artikeln används den [säkra kluster med fem noder] [ service-fabric-secure-cluster-5-node-1-nodetype] exempelmall och mallparametrar. Ladda ned *azuredeploy.json* och *azuredeploy.parameters.json* till din dator och öppna båda filerna i valfri textredigerare.

> [!NOTE]
> För nationella moln (Azure Government, Azure Kina Azure Tyskland), bör du också lägga till följande `fabricSettings` i mallen: `AADLoginEndpoint`, `AADTokenEndpointFormat` och `AADCertEndpointFormat`.

## <a name="add-certificates"></a>Lägg till certifikat
Du lägger till certifikat i en Resource Manager-mall för klustret genom att referera till nyckelvalvet som innehåller certifikatnycklarna som. Lägg till dessa key vault-parametrar och värden i en Resource Manager-mallparametrarfil (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Lägg till alla certifikat i VM scale set osProfile
Alla certifikat som installeras i klustret måste konfigureras i den **osProfile** avsnittet skalans ange resurs (Microsoft.Compute/virtualMachineScaleSets). Den här åtgärden instruerar resursprovidern att installera certifikatet på de virtuella datorerna. Den här installationen innehåller både klustercertifikatet och alla application security-certifikat som du planerar att använda för dina program:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurera certifikat för Service Fabric-kluster

Certifikatet för autentisering av klustret måste konfigureras i båda Service Fabric klusterresursen (Microsoft.ServiceFabric/clusters) och Service Fabric-tillägget för virtuell datorskalning som anger i VM scale set resursen. Den här ordningen kan konfigureras för användning för autentisering för klustret och serverautentisering för hanteringsslutpunkter Service Fabric-resursprovidern.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Lägg till certifikatinformation VM-skalningsuppsättningen ange resurs

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Lägg till certifikatinformationen till resursen i Service Fabric-kluster

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

## <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägga till Azure AD-konfiguration för att använda Azure AD för klientåtkomst

Du lägger till Azure AD-konfigurationen till ett kluster Resource Manager-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna som. Lägg till de Azure AD-parametrar och värden i en Resource Manager-mallparametrarfil (*azuredeploy.parameters.json*). 

> [!NOTE]
> Azure AD-klienter och användare måste skapas innan du skapar klustret.  Mer information finns i [ställa in Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

## <a name="populate-the-parameter-file-with-the-values"></a>Fyll i parameterfilen med värden

Använd slutligen utdatavärden från nyckelvalvet och Azure AD PowerShell-kommandon för att fylla i parameterfilen.

Om du planerar att använda Azure service fabric RM PowerShell-moduler, behöver du inte fylla i certifikatinformationen för klustret. Om du vill systemet för att generera självsignerat signerat certifikat för Klustersäkerhet, bara låta dem vara null. 

> [!NOTE]
> Att hämta och fylla i de här tom parametervärden RM-moduler matchar de parametrar som mycket namnen nedan

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Om du använder certifikat för programmet eller använder ett befintligt kluster som du har överfört till nyckelvalvet måste du hämta den här informationen och fyller den.

RM-moduler har inte möjlighet att skapa Azure AD-konfiguration för dig, så om du planerar att använda Azure AD för klientåtkomst, måste du fylla i den.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

## <a name="test-your-template"></a>Testa din mall
Använd följande PowerShell-kommando för att testa Resource Manager-mallen med en parameterfil:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och få kryptisk meddelanden, Använd sedan ”-Debug” som ett alternativ.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Följande diagram illustrerar där dina nyckelvalv och Azure AD-konfiguration passar i Resource Manager-mallen.

![Resource Manager-beroendekarta][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Nästa steg
Nu när du har en mall för ditt kluster kan lära dig hur du [distribuera klustret till Azure](service-fabric-cluster-creation-via-arm.md).  Läs om du inte redan gjort det [produktion beredskap checklista](service-fabric-production-readiness-checklist.md) innan du distribuerar ett produktionskluster.

Mer information om JSON-syntax och egenskaper för resurser som har distribuerats i den här artikeln finns i:

* [Microsoft.ServiceFabric/clusters](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
