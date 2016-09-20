<properties
   pageTitle="Genomgång av en Resource Manager-mall | Microsoft Azure"
   description="En stegvis genomgång av en Resource Manager-mall som etablerar en grundläggande Azure IaaS-arkitektur."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="navale;tomfitz"/>
   
# Genomgång av en Resource Manager-mall

En av de första frågorna du ställer dig när du ska skapa en mall är ”hur börjar jag?”. Du kan börja från en tom mall och följa den grundläggande strukturen som beskrivs i artikeln [Redigera en mall](resource-group-authoring-templates.md#template-format) och lägga till resurser, parametrar och variabler. Ett annat alternativ är att börja med att gå igenom [snabbstartsgalleriet](https://github.com/Azure/azure-quickstart-templates) och leta efter liknande scenarier som det som du vill skapa. Du kan slå samman flera mallar eller redigera en befintlig mall så att den passar ditt specifika scenario. 

Låt oss ta en titt på en vanlig infrastruktur:

* Två virtuella datorer som använder samma lagringskonto, som finns i samma tillgänglighetsuppsättning och i samma undernät i ett virtuellt nätverk.
* Ett nätverkskort och en VM-IP-adress för varje virtuell dator.
* En belastningsutjämnare med en regel för belastningsutjämning på port 80

![Arkitektur](./media/resource-group-overview/arm_arch.png)

Den här artikeln beskriver steg för steg hur du skapar en Resource Manager-mall för den här infrastrukturen. Den slutliga mallen som du skapar baseras på en snabbstartsmall för [två virtuella datorer i Load Balancer och belastningsutjämningsregler](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

Det är mycket att bygga på en gång. Vi börjar med att skapa och sedan distribuera ett lagringskonto. När du har skapat lagringskontot lägger du till de andra resurserna och distribuerar mallen igen för att göra klart infrastrukturen.

>[AZURE.NOTE] Du kan använda valfri typ av redigerare när du skapar mallen. Visual Studio innehåller verktyg som gör det lättare att skapa mallar, men du måste inte ha Visual Studio för att slutföra den här kursen. En självstudiekurs om hur du skapar en distribution för en webbapp och SQL Database med hjälp av Visual Studio finns i [Skapa och distribuera Azure-resursgrupper via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md). 

## Skapa Resource Manager-mallen

Mallen är en JSON-fil som definierar alla resurser som ska distribueras. Du kan också definiera parametrar som anges under distributionen, variabler som skapas från andra värden och uttryck samt utdata från distributionen. 

Låt oss börja med den enklaste mallen:

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }
 ```

Spara den här filen som **azuredeploy.json**. (Mallen kan ha vilket namn som helst, men det måste vara en JSON-fil).

## skapar ett lagringskonto
I avsnittet **resources** lägger du till ett objekt som definierar lagringskontot, som du ser nedan. 

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Du kanske undrar var de här egenskaperna och värdena kommer från. Egenskaperna **type**, **name**, **apiVersion** och **location** är standardelement som är tillgängliga för alla resurstyper. Mer information om vanliga element finns i [Resurser](resource-group-authoring-templates.md#resources). **name** är konfigurerat till ett parametervärde som du anger under distributionen och **location** som platsen som används av resursgruppen. Vi ska titta på hur du bestämmer **type** och **apiVersion** i avsnitten nedan.

Avsnittet **properties** innehåller alla egenskaper som är unika för en viss resurstyp. De värden som du anger i det här avsnittet matchar exakt PUT-åtgärden i REST-API:et för att skapa den resurstypen. När du skapar ett lagringskonto måste du ange en **accountType**. Observera i [REST-API:et för att skapa ett lagringskonto](https://msdn.microsoft.com/library/azure/mt163564.aspx) att properties-avsnittet för REST-åtgärden även innehåller en **accountType**-egenskap, och tillåtna värden är dokumenterade. I det här exemplet har kontotypen angetts till **Standard_LRS**, men du kan ange ett annat värde eller tillåta användare att skicka in kontotypen som en parameter.

Nu ska vi gå tillbaka till avsnittet **parameters** och se hur du definierar namnet på lagringskontot. Du kan lära dig mer om hur du använder parametrar i [Parametrar](resource-group-authoring-templates.md#parameters). 

```json
"parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
}
```
Här definierade du en parameter av typen sträng som innehåller namnet på lagringskontot. Värdet för den här parametern anges när mallen distribueras.

## Distribuera mallen
Vi har en fullständig mall för att skapa ett nytt lagringskonto. Som du vet sparades mallen i filen **azuredeploy.json**:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters" : {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Storage Account Name"
      }
    }
  },  
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[parameters('storageAccountName')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "accountType": "Standard_LRS"
      }
    }
  ]
}
```

Du kan distribuera en mall på ganska många sätt, som du ser i [artikeln Resursdistribution](resource-group-template-deploy.md). Om du vill distribuera mallen med hjälp av Azure PowerShell använder du:

```powershell
# create a new resource group
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West Europe"

# deploy the template to the resource group
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile azuredeploy.json
```

Eller, om du vill distribuera mallen med hjälp av Azure CLI, använder du:

```
azure group create -n ExampleResourceGroup -l "West Europe"

azure group deployment create -f azuredeploy.json -g ExampleResourceGroup -n ExampleDeployment
```

Du är nu den stolte ägaren av ett lagringskonto!

Nästa steg är att lägga till alla resurser som krävs för att distribuera arkitekturen som beskrivs i början av den här kursen. Du ska lägga till dessa resurser i samma mall som du har arbetat med.

## Tillgänglighetsuppsättning
När du har definierat lagringskontot lägger du till en tillgänglighetsuppsättning för de virtuella datorerna. I det här fallet finns det inga ytterligare egenskaper som krävs, så dess definition är ganska enkel. Om du vill definiera värdet för antalet uppdateringsdomäner och feldomäner hittar du det fullständiga egenskapsavsnittet i [REST-API:et för att skapa en tillgänglighetsuppsättning](https://msdn.microsoft.com/library/azure/mt163607.aspx).

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Observera att **name** har angetts till värdet för en variabel. I den här mallen behövs namnet på tillgänglighetsuppsättningen på några olika platser. Du kan enkelt underhålla mallen genom att definiera detta värde en gång och sedan använda den på flera platser.

Värdet som du anger för **type** innehåller både resursprovidern och resurstypen. För tillgänglighetsuppsättningar är resursprovidern **Microsoft.Compute** och resurstypen **availabilitySets**. Du kan hämta listan över tillgängliga resursproviders genom att köra följande PowerShell-kommando:

```powershell
    Get-AzureRmResourceProvider -ListAvailable
```

Eller, om du använder Azure CLI, kan du köra följande kommando:
```
    azure provider list
```
Med utgångspunkt i detta använder du följande när du arbetar med lagringskonton, virtuella datorer och virtuella nätverk i det här avsnittet:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Om du vill visa resurstyperna för en viss provider kör du följande PowerShell-kommando:

```powershell
    (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes
```

Eller, för Azure CLI, så returnerar följande kommando de tillgängliga typerna i JSON-format och sparar dem till en fil.

```
    azure provider show Microsoft.Compute --json > c:\temp.json
```

Du bör se **availabilitySets** som en av typerna i **Microsoft.Compute**. Det fullständiga namnet på typen är **Microsoft.Compute/availabilitySets**. Du kan ange resurstypens namn för resurser i mallen.

## Offentlig IP-adress
Definiera en offentlig IP-adress. Som tidigare hittar du alla egenskaper som du kan konfigurera i [REST-API:et för offentliga IP-adresser](https://msdn.microsoft.com/library/azure/mt163590.aspx).

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

Allokeringsmetoden är inställd på **Dynamisk** men du kan ange den till det värde som du behöver eller ange att den ska acceptera ett parametervärde. Du har valt att låta användarna av mallen skicka in ett värde för domännamnets etikett.

Nu ska vi titta på hur du fastställer **apiVersion**. Värdet som du anger matchar bara den version av REST-API:et som du vill använda när du skapar resursen. Därför kan du titta i dokumentationen för REST-API:et för den resurstypen. Eller så kan du köra följande PowerShell-kommando för en viss typ.

```powershell
    ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions
```
Som returnerar följande värden:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Om du vill visa API-versionerna med Azure CLI kör du samma **azure provider show**-kommando som du såg tidigare.

Välj den senaste API-versionen när du skapar en ny mall.

## Virtuellt nätverk och undernät
Skapa ett virtuellt nätverk med ett undernät. Du hittar alla egenskaper som du kan ange i [REST-API:et för virtuella nätverk](https://msdn.microsoft.com/library/azure/mt163661.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## Belastningsutjämnare
Nu ska du skapa en externt riktad belastningsutjämnare. Eftersom den här belastningsutjämnaren använder den offentliga IP-adressen måste du deklarera ett beroende för den offentliga IP-adressen i avsnittet **dependsOn**. Det innebär att belastningsutjämnaren inte distribueras förrän den offentliga IP-adressens distribution har slutförts. Om du inte definierar det här sambandet får du ett felmeddelande eftersom Resource Manager försöker distribuera resurserna parallellt och försöker ange belastningsutjämnaren till en offentlig IP-adress som inte finns än. 

Du ska också skapa en backend-adresspool, ett par ingående NAT-regler till RDP till de virtuella datorerna och en belastningsutjämningsregel med en TCP-avsökning på port 80 i den här resursdefinitionen. Du hittar alla egenskaperna i [REST-API:et för belastningsutjämnaren](https://msdn.microsoft.com/library/azure/mt163574.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## Nätverksgränssnitt
Du ska skapa två nätverksgränssnitt, ett för varje virtuell dator. I stället för att lägga till dubblettposter för nätverksgränssnitten kan du använda [funktionen copyIndex()](resource-group-create-multiple.md) för att iterera i kopieringsloopen (kallas nicLoop) och skapa antalet nätverksgränssnitt som anges i `numberOfInstances`-variablerna. Nätverksgränssnittet är beroende av genereringen av det virtuella nätverket och belastningsutjämnaren. Det använder undernätet som definierades när det virtuella nätverket skapades och belastningsutjämnarens ID för att konfigurera belastningsutjämnarens adresspool och de ingående NAT-reglerna.
Du hittar alla egenskaperna i [REST-API:et för nätverksgränssnitt](https://msdn.microsoft.com/library/azure/mt163668.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## Virtuell dator
Du ska skapa två virtuella datorer med hjälp av funktionen copyIndex() som du gjorde när du skapade [nätverksgränssnitten](#network-interface).
Genereringen av den virtuella datorn är beroende av lagringskontot, nätverksgränssnittet och tillgänglighetsuppsättningen. Den här virtuella datorn skapas från en avbildning från Marketplace och anges i egenskapen `storageProfile` – `imageReference` används för att definiera avbildningens utgivare, erbjudande, SKU och version. Slutligen konfigureras en diagnostikprofil som aktiverar diagnostik för den virtuella datorn. 

Du hittar relevanta egenskaper för en Marketplace-avbildning genom att följa anvisningarna i artikeln [Välja avbildning för en virtuell Linux-dator](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) eller [Välja avbildning för en virtuell Windows-dator](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

>[AZURE.NOTE] För avbildningar publicerade av **tredjepartsleverantörer** måste du ange en till egenskap: `plan`. Du hittar ett exempel i [den här mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) från snabbstartsgalleriet. 

Nu har du definierat resurser för din mall.

## Parametrar

I avsnittet parameters definierar du de värden som kan anges när mallen distribueras. Definiera bara parametrar för värden som du tror kan variera under distributionen. Du kan ange ett standardvärde för en parameter som används om inget anges under distributionen. Du kan också definiera de tillåtna värdena som du ser för **imageSKU**-parametern.

```json
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## Variabler

I avsnittet variables kan du definiera värden som används på mer än en plats i mallen, eller värden som skapas från andra uttryck eller variabler. Variabler används ofta för att förenkla mallens syntax.

```json
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```

Du är klar med mallen! Du kan jämföra mallen med den fullständig mallen i [snabbstartsgalleriet](https://github.com/Azure/azure-quickstart-templates) under mallen för [två virtuella datorer med belastningsutjämnare och belastningsutjämningsregler](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules). Din mall kan avvika något från den fullständiga mallen om olika versionsnummer används. 

Du kan omdistribuera mallen med hjälp av samma kommandon som du använde när du distribuerade lagringskontot. Du behöver inte ta bort lagringskontot innan du distribuerar om mallen eftersom Resource Manager inte återskapar resurser som redan finns och som inte har ändrats.

## Nästa steg

- [Azure Resource Manager Template Visualizer (ARMViz)](http://armviz.io/#/) är ett bra verktyg för att visualisera ARM-mallar eftersom de kan bli för stora för att förstå bara genom att läsa JSON-filen.
- Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
- Information om hur du distribuerar en mall finns i [Distribuera en resursgrupp med en Azure Resource Manager-mall](resource-group-template-deploy.md)



<!--HONumber=sep16_HO1-->


