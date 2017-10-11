---
title: "Autoskala Linux virtuella Skalningsuppsättningarna | Microsoft Docs"
description: "Ställa in autoskalning för en Linux Skalningsuppsättning virtuell dator med hjälp av Azure CLI"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: eff4add1cb16fe25022787668dc1d2277845dd95
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Skala automatiskt Linux-datorer i en skaluppsättning för virtuell dator
Skaluppsättningar för den virtuella datorn gör det enkelt att distribuera och hantera identiska virtuella datorer som en uppsättning. Skaluppsättningar ger en skalbar och anpassningsbara beräkningslager för storskaliga program och stöd för avbildningar för Windows-plattformen, Linux-plattformen bilder, anpassade avbildningar och tillägg. Läs mer i [översikt över virtuella datorer skala anger](virtual-machine-scale-sets-overview.md).

Den här kursen visar hur du skapar en skaluppsättning för Linux virtuella datorer med den senaste versionen av Ubuntu Linux. Kursen visar även hur du skalar automatiskt på datorer i uppsättningen. Du kan skapa skala och skalning genom att skapa en Azure Resource Manager-mall och distribuera den med hjälp av Azure CLI. Mer information om mallar finns i [Redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md). Läs mer om automatisk skalning av skalningsuppsättningar i [automatisk skalning och Skalningsuppsättningarna för virtuella](virtual-machine-scale-sets-autoscale-overview.md).

I kursen får distribuera du följande resurser och tillägg:

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

Mer information om Resource Manager-resurser finns [Azure Resource Manager och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md).

Innan du börjar med stegen i den här självstudiekursen [installerar Azure CLI](../cli-install-nodejs.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Steg 1: Skapa en resursgrupp och ett lagringskonto

1. **Logga in på Microsoft Azure**  
Växla till Resource Manager-läge i din kommandoradsgränssnittet (Bash, Terminal, Kommandotolken) och sedan [logga in med ditt arbets- eller skolkonto id](../xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login). Följ anvisningarna för en interaktiv inloggning upplevelse på Azure-konto.

    ```cli   
    azure config mode arm

    azure login
    ```
   
    > [!NOTE]
    > Om du har ett arbets- eller skolkonto ID och du inte har tvåfaktorsautentisering aktiverad, använda `azure login -u` med att logga in utan en interaktiv session-ID. Om du inte har ett arbets- eller skolkonto ID, kan du [skapar ett arbets- eller skolkonto id från ditt personliga Microsoft-konto](../active-directory/active-directory-users-create-azure-portal.md).
    
2. **Skapa en resursgrupp**  
Alla resurser måste distribueras till en resursgrupp. Den här självstudiekursen kommer du kalla resursgruppen **vmsstest1**.
   
    ```cli
    azure group create vmsstestrg1 centralus
    ```

3. **Distribuera ett lagringskonto i den nya resursgruppen**  
Det här lagringskontot är där mallen lagras. Skapa ett lagringskonto med namnet **vmsstestsa**.
   
    ```cli
    azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa
    ```

## <a name="step-2-create-the-template"></a>Steg 2: Skapa mallen
En mall för Azure Resource Manager gör det möjligt för dig att distribuera och hantera Azure-resurser tillsammans med hjälp av en JSON-beskrivning av resurser och associerade distributionen parametrar.

1. Skapa filen VMSSTemplate.json i din favorit-redigeraren och Lägg till den ursprungliga JSON-strukturen för att stödja mallen.

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. Parametrar krävs inte alltid, men de ger ett sätt att ange värden när mallen distribueras. Lägg till de här parametrarna under det överordnade elementet parametrar som du lagt till mallen.

    ```json
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
   * Ange ett namn för den separata virtuella dator som används för att få åtkomst till datorer i skalan.
   * Ett namn för det lagringskonto där mallen ska lagras.
   * Ange antalet instanser av virtuella datorer för att skapa först i skalan.
   * Ett namn och lösenord för administratörskontot på de virtuella datorerna.
   * Ange ett namnprefix för de resurser som skapas för att stödja skalningen.

3. Variabler kan användas i en mall för att ange värden som ändras ofta eller värden som måste skapas från en kombination av parametervärden. Lägg till dessa variabler under det överordnade elementet för variabler som du lagt till mallen.

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```

   * DNS-namn som används av nätverksgränssnitt.
   * IP-adressnamn och prefix för virtuellt nätverk och undernät.
   * Namn och identifierare för det virtuella nätverket att läsa in belastningsutjämning och nätverksgränssnitt.
   * Ange lagringskontonamn för konton som är kopplade till datorer i skalan.
   * Inställningar för diagnostik-tillägg som är installerad på de virtuella datorerna. Läs mer om diagnostik [skapa en Windows virtuell dator med övervakning och diagnostik med Azure Resource Manager-mall](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Lägg till konto lagringsresurs under det överordnade elementet för resurser som du lagt till mallen. Den här mallen använder en loop för att skapa de rekommenderade fem storage-kontona där operativsystemet diskar och diagnostiska data lagras. Denna uppsättning konton stöder upp till 100 virtuella datorer i en skaluppsättning, vilket är största. Varje lagringskonto heter med en bokstav enhetsbeteckning som definierades i variablerna kombineras med suffixet som du anger i parametrarna för mallen.
   
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Lägg till resursen virtuellt nätverk. Mer information finns i [Nätverksresursprovidern](../virtual-network/resource-groups-networking.md).

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. Lägg till de offentliga IP-adress-resurser som används av belastningsutjämnare och nätverksgränssnittet.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. Lägg till belastningsutjämningsresurs som används av skaluppsättning. Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnaren](../load-balancer/load-balancer-arm.md).

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 22
            }
          }
        ]
      }
    },
    ```

8. Lägg till gränssnittet nätverksresurs som används av en separat virtuell dator. Eftersom datorer i en skaluppsättning inte är tillgängligt via en offentlig IP-adress, skapas en separat virtuell dator i samma virtuella nätverk via fjärranslutning åtkomst på datorer.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. Lägg till en separat virtuell dator i samma nätverk som skaluppsättning.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "14.04.4-LTS",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    ```

10. Lägga till virtuella datorns skaluppsättning resurs och ange diagnostik-tillägget som är installerad på alla virtuella datorer i skaluppsättning. Många av inställningarna för den här resursen är liknande med den virtuella datorresursen. De viktigaste skillnaderna är kapacitet elementet som anger hur många virtuella datorer i skalningsuppsättningen och upgradePolicy som anger hur uppdateringar görs i virtuella datorer. Skaluppsättning skapas inte förrän alla lagringskonton skapas som anges med dependsOn-element.

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "Canonical",
              "offer": "UbuntuServer",
              "sku": "14.04.4-LTS",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name":"LinuxDiagnostic",
                "properties": {
                  "publisher":"Microsoft.OSTCExtensions",
                  "type":"LinuxDiagnostic",
                  "typeHandlerVersion":"2.1",
                  "autoUpgradeMinorVersion":false,
                  "settings": {
                    "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint":"https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Lägg till resursen autoscaleSettings som definierar hur skaluppsättning justerar baserat på processoranvändning på datorer i uppsättningen.

    ```json
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Processor\\PercentProcessorTime",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```
    
    Dessa värden är viktiga för den här självstudiekursen:
    
    * **metricName**  
    Det här värdet är samma som de prestandaräknare som vi har definierat i variabeln wadperfcounter. Med hjälp av variabeln, samlar in diagnostik-tillägget i **Processor\PercentProcessorTime** räknaren.
    
    * **metricResourceUri**  
    Det här värdet är resursidentifieraren för virtuella datorns skaluppsättning.
    
    * **Tidskorn**  
    Det här värdet är Granulariteten för de mätvärden som samlats in. I den här mallen är den inställd på en minut.
    
    * **statistik**  
    Det här värdet avgör hur mätvärdena kombineras för automatisk skalning åtgärd. Möjliga värden är: medel, Min, Max. Genomsnittlig totala CPU-användningen av virtuella datorer samlas in i den här mallen.

    * **värdet timeWindow**  
    Det här värdet är tidsintervallet där instansens data samlas in. Det måste vara mellan 5 minuter och 12 timmar.
    
    * **timeAggregation**  
    hans värdet avgör hur data som samlas in ska kombineras med tiden. Standardvärdet är medelvärde. Möjliga värden är:, lägsta, högsta, senaste, totalt antal, räknas.
    
    * **operatorn**  
    Det här värdet är den operator som används för att jämföra måttinformationen och tröskelvärdet. Möjliga värden är: är lika med, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    
    * **Tröskelvärde**  
    Det här värdet utlöser skalningsåtgärden. I den här mallen läggs datorer till skaluppsättningen när den genomsnittliga processoranvändningen mellan datorer i uppsättningen är över 50%.
    
    * **riktning**  
    Det här värdet anger den åtgärd som utförs när ett tröskelvärde uppnås. Möjliga värden är ökning eller minskning. I den här mallen ökar antalet virtuella datorer i skaluppsättning om tröskelvärdet för över 50% under den definierade tidsperioden.

    * **typ**  
    Det här värdet är typ av åtgärd som ska ske och måste anges till ChangeCount.
    
    * **värdet**  
    Det här värdet är antalet virtuella datorer som läggs till eller tas bort från skaluppsättning. Det här värdet måste vara 1 eller högre. Standardvärdet är 1. I den här mallen ange antalet datorer i skalan ökar med 1 när tröskelvärdet är uppfyllda.

    * **cooldown**  
    Det här värdet är hur lång tid att vänta sedan den senaste skalning åtgärden innan nästa åtgärd inträffar. Det här värdet måste vara mellan en minut och en vecka.

12. Spara filen med mallen.    

## <a name="step-3-upload-the-template-to-storage"></a>Steg 3: Överför mallen till lagring
Mallen kan överföras så länge som du känner till namn och en primär nyckel för lagringskontot som du skapade i steg 1.

1. Kör dessa kommandon för att ställa in miljövariabler som behövs för att komma åt lagringskontot i din kommandoradsgränssnittet (Bash, Terminal, Kommandotolken):

    ```cli   
    export AZURE_STORAGE_ACCOUNT={account_name}
    export AZURE_STORAGE_ACCESS_KEY={key}
    ```
    
    Du kan hämta nyckeln genom att klicka på nyckelikonen när du visar konto lagringsresurs i Azure-portalen. När du använder en Windows kommandotolk, Skriv **ange** i stället för export.

2. Skapa behållare för lagring av mallen.
   
    ```cli
    azure storage container create -p Blob templates
    ```

3. Överför mallfilen till den nya behållaren.
   
    ```cli
    azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json
    ```

## <a name="step-4-deploy-the-template"></a>Steg 4: Distribuera mallen
Nu när du har skapat mallen kan du börja distribuera resurserna. Använd följande kommando för att starta processen:

```cli
azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1
```

När du trycker på Ange, du uppmanas att ange värden för variabler som du tilldelat. Ange dessa värden:

```cli
vmName: vmsstestvm1
vmSSName: vmsstest1
instanceCount: 5
adminUserName: vmadmin1
adminPassword: VMpass1
resourcePrefix: vmsstest
```

Det bör ta ungefär 15 minuter för alla resurser som har ska distribueras.

> [!NOTE]
> Du kan också använda portalens möjlighet för att distribuera resurserna. Använd den här länken: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>


## <a name="step-5-monitor-resources"></a>Steg 5: Övervaka resurser
Du kan få information om skalningsuppsättningar i virtuella datorer på följande sätt:

* Azure portal – du får för närvarande en begränsad mängd information med hjälp av portalen.

* Den [resursutforskaren Azure](https://resources.azure.com/) – det här verktyget är bäst för att utforska det aktuella tillståndet för din skaluppsättning. Följ den här sökvägen och du bör se Instansvy för skaluppsättning som du skapade:
  
    ```cli
    subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines
    ```

* Azure CLI - Använd detta kommando för att hämta viss information:

    ```cli  
    azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1
    ```

* Ansluta till den virtuella datorn jumpbox precis som du skulle någon annan dator och sedan kan du fjärransluta till de virtuella datorerna i skaluppsättningen att övervaka enskilda processer.

> [!NOTE]
> En fullständig REST-API för att få information om skaluppsättningar finns i [Skalningsuppsättningarna för virtuella](https://msdn.microsoft.com/library/mt589023.aspx).

## <a name="step-6-remove-the-resources"></a>Steg 6: Ta bort resurserna
Eftersom du debiteras för de resurser som används i Azure, men det är alltid en bra idé att ta bort resurser som inte längre behövs. Du behöver inte ta bort varje resurs separat från en resursgrupp. Du kan ta bort resursgruppen och alla dess resurser tas bort automatiskt.

```cli
azure group delete vmsstestrg1
```

## <a name="next-steps"></a>Nästa steg
* Hitta exempel på Azure-Monitor övervakningsfunktionerna i [Azure-Monitor plattformsoberoende CLI snabb start prover](../monitoring-and-diagnostics/insights-cli-samples.md)
* Lär dig mer om meddelandefunktioner i [använda automatiska åtgärder för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
* Lär dig hur du [Använd granskningsloggarna för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
* Kolla in den [Autoskala demo-appen på Ubuntu 16.04](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) mall som ställer in en Python/bottle app att utöva automatisk skalning funktionerna i Skalningsuppsättningarna för virtuella datorer.

