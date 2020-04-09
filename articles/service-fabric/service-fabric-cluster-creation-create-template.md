---
title: Skapa en klustermall för Azure Service Fabric
description: Lär dig hur du skapar en Resource Manager-mall för ett Service Fabric-kluster. Konfigurera säkerhet, Azure Key Vault och Azure Active Directory (Azure AD) för klientautentisering.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 6cf0f9c3b8b54db7bd27ec8dd9c9d59d849c74cc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985379"
---
# <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Skapa en Resurshanterarens servermall för Service Fabric-kluster

Ett [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella datorer som dina mikrotjänster distribueras och hanteras till. Ett Service Fabric-kluster som körs i Azure är en Azure-resurs och distribueras, hanteras och övervakas med hjälp av Resource Manager.  I den här artikeln beskrivs hur du skapar en Resource Manager-mall för ett Service Fabric-kluster som körs i Azure.  När mallen är klar kan du [distribuera klustret på Azure](service-fabric-cluster-creation-via-arm.md).

Klustersäkerhet konfigureras när klustret först konfigureras och kan inte ändras senare. Läs [säkerhetsscenarier för Service Fabric-kluster][service-fabric-cluster-security]innan du konfigurerar ett kluster . I Azure använder Service Fabric x509-certifikat för att skydda klustret och dess slutpunkter, autentisera klienter och kryptera data. Azure Active Directory rekommenderas också för att skydda åtkomst till hanteringsslutpunkter. Azure AD-klienter och användare måste skapas innan klustret skapas.  Mer information finns i [Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

Innan du distribuerar ett produktionskluster för att köra produktionsarbetsbelastningar måste du först läsa [checklistan för produktionsberedskap](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen
Exempel på Resource Manager-mallar är tillgängliga i [Azure-exemplen på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångspunkt för klustermallen.

I den här artikeln används den säkra klusterextpelsmallen och mallparametrarna med [fem noder.][service-fabric-secure-cluster-5-node-1-nodetype] Hämta *azuredeploy.json* och *azuredeploy.parameters.json* till din dator och öppna båda filerna i din favorittextredigerare.

> [!NOTE]
> För nationella moln (Azure Government, Azure China, Azure `fabricSettings` Germany) bör `AADLoginEndpoint` `AADTokenEndpointFormat` du `AADCertEndpointFormat`också lägga till följande i mallen: och .

## <a name="add-certificates"></a>Lägga till certifikat
Du lägger till certifikat i en klusterresurshanteraresmall genom att referera till nyckelvalvet som innehåller certifikatnycklarna. Lägg till dessa nyckelvalvsparametrar och värden i en Resource Manager-mallparametrarfil (*azuredeploy.parameters.json*).

### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Lägga till alla certifikat i den virtuella datorns skalningsuppsättning osProfile
Alla certifikat som installeras i klustret måste konfigureras i avsnittet **osProfile** i skalningsuppsättningsresursen (Microsoft.Compute/virtualMachineScaleSets). Den här åtgärden instruerar resursprovidern att installera certifikatet på de virtuella datorerna. Den här installationen innehåller både klustercertifikat och alla programsäkerhetscertifikat som du planerar att använda för dina program:

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

### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurera klustercertifikatet Service Fabric

Klusterautentiseringscertifikatet måste konfigureras både i klusterresursen Service Fabric (Microsoft.ServiceFabric/clusters) och tillägget Service Fabric för skalningsuppsättningar för virtuella datorer i skaluppsättningsresursen för den virtuella datorn. Med den här ordningen kan resursprovidern för Service Fabric konfigurera den för användning för klusterautentisering och serverautentisering för hanteringsslutpunkter.

#### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Lägga till certifikatinformationen för skalningsuppsättningen för virtuell dator

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
            "name": "[concat('ServiceFabricNodeVmExt_',variables('vmNodeType0Name'))]",
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

#### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Lägga till certifikatinformationen i klusterresursen Service Fabric

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

Du lägger till Azure AD-konfigurationen i en klusterresurshanteraren-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna. Lägg till dessa Azure AD-parametrar och värden i en Resource Manager-mallparametrarfil (*azuredeploy.parameters.json*). 

> [!NOTE]
> På Linux måste Azure AD-klienter och användare skapas innan klustret skapas.  Mer information finns i [Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

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

## <a name="populate-the-parameter-file-with-the-values"></a>Fylla parameterfilen med värdena

Slutligen använder du utdatavärdena från nyckelvalvet och Azure AD PowerShell-kommandona för att fylla i parameterfilen.

Om du planerar att använda Azure-tjänstinfrastruktur RM PowerShell-modulerna behöver du inte fylla i klustercertifikatinformationen. Om du vill att systemet ska generera det självsignerade certifikatet för klustersäkerhet, behåll dem bara som null. 

> [!NOTE]
> För rm-modulerna att plocka upp och fylla i dessa tomma parametervärden matchar parametrarna mycket namnen nedan

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

Om du använder programcerter eller använder ett befintligt kluster som du har överfört till nyckelvalvet måste du hämta den här informationen och fylla i den.

RM-modulerna har inte möjlighet att generera Azure AD-konfigurationen åt dig, så om du planerar att använda Azure AD för klientåtkomst måste du fylla i den.

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

## <a name="test-your-template"></a>Testa mallen
Använd följande PowerShell-kommando för att testa Resource Manager-mallen med en parameterfil:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och får kryptiska meddelanden, använd sedan "-Debug" som ett alternativ.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Följande diagram visar var din nyckelvalv och Azure AD-konfiguration passar in i din Resource Manager-mall.

![Beroendekarta för Resurshanteraren][cluster-security-arm-dependency-map]

## <a name="next-steps"></a>Nästa steg
Nu när du har en mall för klustret kan du läsa om hur du [distribuerar klustret till Azure](service-fabric-cluster-creation-via-arm.md).  Om du inte redan har gjort det läser du [checklistan för produktionsberedskap](service-fabric-production-readiness-checklist.md) innan du distribuerar ett produktionskluster.

Mer information om JSON-syntaxen och egenskaperna för de resurser som distribueras i den här artikeln finns i:

* [Microsoft.ServiceFabric/kluster](/azure/templates/microsoft.servicefabric/clusters)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAdresser](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)

<!-- Links -->
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-create-template/cluster-security-arm-dependency-map.png
