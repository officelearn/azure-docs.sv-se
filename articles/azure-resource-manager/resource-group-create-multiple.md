---
title: Distribuera flera instanser av Azure-resurser | Microsoft Docs
description: "Använd kopieringen och matriser i en Azure Resource Manager-mall för att iterera flera gånger när du distribuerar resurser."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 8e6d68612be4b7d4e1d6cea13e0f29636931abd8
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Distribuera flera instanser av en resurs eller en egenskap i Azure Resource Manager-mallar
Det här avsnittet visar hur du iterera i Azure Resource Manager-mall för att skapa flera instanser av en resurs, eller flera instanser av en egenskap för en resurs.

Om du behöver lägga till logik i mallen som du kan ange om en resurs har distribuerats, se [villkorligt distribuera resurs](#conditionally-deploy-resource).

Ett exempel på hur du skapar flera element i en array-variabel, se [variabler](resource-group-authoring-templates.md#variables).

## <a name="resource-iteration"></a>Resursen upprepning
När du skapar flera instanser av en resurstyp, till en `copy` elementet för resurstypen. I elementet kopia anger du antalet upprepningar och ett namn för den här loop. Värdet för antal måste vara ett positivt heltal och får inte överskrida 800. Hanteraren för filserverresurser skapar resurserna parallellt. Den ordning som de har skapats kan därför inte garanteras. För att skapa hävdade resurser i följd, se [seriella kopiera](#serial-copy). 

Resursen ska skapa flera gånger tar följande format:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Observera att varje resurs ingår i `copyIndex()` som returnerar den aktuella upprepningen i en slinga. `copyIndex()`är nollbaserade. Det, i följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar dessa namn:

* storage0
* storage1
* storage2.

Om du vill förskjuta indexvärdet skickar du ett värde i funktionen copyIndex(). Antal iterationer för att utföra fortfarande har angetts i elementet kopia, men värdet för copyIndex förskjutas av det angivna värdet. Det, i följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar dessa namn:

* storage1
* storage2
* storage3

Kopieringen är användbart när du arbetar med matriser eftersom du kan söka igenom varje element i matrisen. Använd den `length` funktion på matrisen för att ange antalet för iterationer, och `copyIndex` att hämta det aktuella indexet i matrisen. Det, i följande exempel:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Skapar dessa namn:

* storagecontoso
* storagefabrikam
* storagecoho

## <a name="serial-copy"></a>Seriell kopia

När du använder elementet kopiera för att skapa flera instanser av en resurstyp, hanteraren för filserverresurser, som standard distribuerar dessa instanser parallellt. Du kanske vill ange att resurserna som distribueras i följd. Till exempel när du uppdaterar en produktionsmiljö måste du kanske vill sprida uppdateringar så bara ett visst antal uppdateras samtidigt.

Resource Manager tillhandahåller egenskaper i elementet kopia som gör det möjligt att distribuera seriellt flera instanser. I elementet kopiera ange `mode` till **seriella** och `batchSize` med antalet instanser som ska distribueras i taget. Med seriella läge skapar Resource Manager ett beroende på tidigare instanser i en slinga så att den inte startar en batch tills den föregående batchen har slutförts.

```json
"copy": {
    "name": "iterator",
    "count": "[parameters('numberToDeploy')]",
    "mode": "serial",
    "batchSize": 2
},
```

Egenskapen läget accepterar också **parallella**, vilket är standardvärdet.

Använd följande mall som distribuerar tom kapslade mallar för att testa seriella kopia utan att skapa verkliga resurser:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberToDeploy": {
      "type": "int",
      "minValue": 2,
      "defaultValue": 5
    }
  },
  "resources": [
    {
      "apiVersion": "2015-01-01",
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('loop-', copyIndex())]",
      "copy": {
        "name": "iterator",
        "count": "[parameters('numberToDeploy')]",
        "mode": "serial",
        "batchSize": 1
      },
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [],
          "outputs": {
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Observera att kapslade distributioner bearbetas i följd i distributionshistoriken för.

![seriell distribution](./media/resource-group-create-multiple/serial-copy.png)

För en mer realistisk scenario distribuerar i följande exempel två instanser samtidigt för en Linux-VM från en kapslad mall:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "User name for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "dnsLabelPrefix": {
            "type": "string",
            "metadata": {
                "description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "ubuntuOSVersion": {
            "type": "string",
            "defaultValue": "16.04.0-LTS",
            "allowedValues": [
                "12.04.5-LTS",
                "14.04.5-LTS",
                "15.10",
                "16.04.0-LTS"
            ],
            "metadata": {
                "description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version."
            }
        }
    },
    "variables": {
        "templatelink": "https://raw.githubusercontent.com/rjmax/Build2017/master/Act1.TemplateEnhancements/Chapter03.LinuxVM.json"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "[concat('nestedDeployment',copyIndex())]",
            "type": "Microsoft.Resources/deployments",
            "copy": {
                "name": "myCopySet",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            },
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "adminUsername": {
                        "value": "[parameters('adminUsername')]"
                    },
                    "adminPassword": {
                        "value": "[parameters('adminPassword')]"
                    },
                    "dnsLabelPrefix": {
                        "value": "[parameters('dnsLabelPrefix')]"
                    },
                    "ubuntuOSVersion": {
                        "value": "[parameters('ubuntuOSVersion')]"
                    },
                    "index":{
                        "value": "[copyIndex()]"
                    }
                }
            }
        }
    ]
}
```

## <a name="property-iteration"></a>Egenskapen upprepning

Om du vill skapa flera värden för en egenskap för en resurs lägger du till en `copy` matris i elementet egenskaper. Denna matris innehåller objekt och alla objekt som har följande egenskaper:

* namn – namnet på egenskapen för att skapa flera värden för
* Antal - värden för att skapa
* indata - ett objekt som innehåller värdena som att tilldela egenskapen  

I följande exempel visas hur du använder `copy` dataDisks-egenskapen på en virtuell dator:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Observera att när du använder `copyIndex` inuti en egenskapen iteration måste du ange namnet på upprepning. Du behöver inte ange namnet när det används med resurs iteration.

Hanteraren för filserverresurser expanderar den `copy` matris under distributionen. Namnet på matrisen blir namnet på egenskapen. Indatavärdena bli objektets egenskaper. Den distribuerade mallen blir:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
          {
              "lun": 0,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Du kan använda resursen och egenskapen iteration tillsammans. Referens egenskapen iteration efter namn.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
    "copy":{
        "count": 2,
        "name": "vnetloop"
    },
    "location": "[resourceGroup().location]",
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "copy": [
            {
                "name": "subnets",
                "count": 2,
                "input": {
                    "name": "[concat('subnet-', copyIndex('subnets'))]",
                    "properties": {
                        "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
                    }
                }
            }
        ]
    }
}
```

Du får bara innehålla ett kopiera element i egenskaperna för varje resurs. Definiera flera objekt i matrisen kopia om du vill ange en iteration loop för mer än en egenskap. Varje objekt är hävdade separat. Till exempel för att skapa flera instanser av både den `frontendIPConfigurations` egenskapen och `loadBalancingRules` egenskapen för en belastningsutjämnare definiera både objekt i en enda kopia-elementet: 

```json
{
    "name": "[variables('loadBalancerName')]",
    "type": "Microsoft.Network/loadBalancers",
    "properties": {
        "copy": [
          {
              "name": "frontendIPConfigurations",
              "count": 2,
              "input": {
                  "name": "[concat('loadBalancerFrontEnd', copyIndex('frontendIPConfigurations', 1))]",
                  "properties": {
                      "publicIPAddress": {
                          "id": "[variables(concat('publicIPAddressID', copyIndex('frontendIPConfigurations', 1)))]"
                      }
                  }
              }
          },
          {
              "name": "loadBalancingRules",
              "count": 2,
              "input": {
                  "name": "[concat('LBRuleForVIP', copyIndex('loadBalancingRules', 1))]",
                  "properties": {
                      "frontendIPConfiguration": {
                          "id": "[variables(concat('frontEndIPConfigID', copyIndex('loadBalancingRules', 1)))]"
                      },
                      "backendAddressPool": {
                          "id": "[variables('lbBackendPoolID')]"
                      },
                      "protocol": "tcp",
                      "frontendPort": "[variables(concat('frontEndPort' copyIndex('loadBalancingRules', 1))]",
                      "backendPort": "[variables(concat('backEndPort' copyIndex('loadBalancingRules', 1))]",
                      "probe": {
                          "id": "[variables('lbProbeID')]"
                      }
                  }
              }
          }
        ],
        ...
    }
}
```

## <a name="depend-on-resources-in-a-loop"></a>Beroende av resurser i en loop
Du anger att en resurs distribueras efter en annan resurs med hjälp av den `dependsOn` element. Ange namnet på kopian loop i dependsOn-elementet för att distribuera en resurs som är beroende av mängd resurser i en slinga. I följande exempel visas hur du distribuerar tre storage-konton innan du distribuerar den virtuella datorn. Fullständig definitionen för virtuell dator visas inte. Observera att kopiera elementet har name angivet till `storagecopy` och dependsOn-elementet för virtuella datorer har också `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="create-multiple-instances-of-a-child-resource"></a>Skapa flera instanser av en underordnad-resurs
Du kan inte använda en kopia skapas för en underordnad resurs. Du måste i stället skapa resursen som översta resurs för att skapa flera instanser av en resurs som du vanligtvis definiera som kapslad i en annan resurs. Du definierar relationen med den överordnade resursen via egenskaperna typ och namn.

Anta exempelvis att du definierar en datamängd vanligtvis som en underordnad resurs i en datafabrik.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Om du vill skapa flera instanser av datauppsättningar flyttas utanför datafabriken. Dataset måste vara på samma nivå som data factory, men det är fortfarande en underordnad resurs i data factory. Du bevara relationen mellan datauppsättning och datafabriken via egenskaperna typ och namn. Eftersom typen inte kan härledas från dess position i mallen, måste du ange typen av fullständigt kvalificerat i formatet: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Ange ett namn för den datamängd som innehåller namnet på överordnade resurs för att upprätta en överordnad-underordnad relation med en instans av datafabriken. Använd formatet: `{parent-resource-name}/{child-resource-name}`.  

I följande exempel visas implementeringen:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="conditionally-deploy-resource"></a>Villkorligt distribuera resurs

Anger om en resurs har distribuerats på `condition` element. Värdet för det här elementet matchar true eller false. När värdet är true, distribueras resursen. Om värdet är FALSKT har resursen inte distribuerats. Till exempel vill ange om ett nytt lagringskonto distribueras eller ett befintligt lagringskonto används, använder du:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Ett exempel på hur du använder en ny eller befintlig resurs finns [ny eller befintlig mall för villkoret](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json).

Ett exempel på med ett lösenord eller SSH-nyckel för att distribuera den virtuella datorn, se [användarnamn eller SSH villkoret mallen](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json).

## <a name="next-steps"></a>Nästa steg
* Om du vill veta om avsnitt i en mall finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Information om hur du distribuerar mallen finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

