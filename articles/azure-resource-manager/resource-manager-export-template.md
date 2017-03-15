---
title: Exportera en Azure Resource Manager-mall | Microsoft Docs
description: "Använd Azure Resource Manager om du vill exportera en mall från en befintlig resursgrupp."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 5f5ca940-eef8-4125-b6a0-f44ba04ab5ab
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: f8512229ee30fee6315d8ba167f1716e40f79b3e
ms.lasthandoff: 03/06/2017


---
# <a name="export-an-azure-resource-manager-template-from-existing-resources"></a>Exportera en Azure Resource Manager-mall från befintliga resurser
Med Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs.

Observera att du kan exportera en mall på två sätt:

* Du kan exportera själva mallen som du använde för en distribution. Den exporterade mallen innehåller alla parametrar och variabler exakt som de visas i den ursprungliga mallen. Den här metoden är användbar när du har distribuerat resurser via portalen. I detta fall behöver du veta hur du bygger mallen för att skapa dessa resurser.
* Du kan exportera en mall som representerar resursgruppens aktuella tillstånd. Den exporterade mallen baseras inte på en mall som du har använt för distribution. I stället skapar den en mall som är en ögonblicksbild av resursgruppen. Den exporterade mallen har många hårdkodade värden och troligen inte så många parametrar som du vanligtvis definierar. Den här metoden är användbar när du har ändrat resursgruppen via portalen eller med hjälp av skript. I detta fall behöver du avbilda resursgruppen som en mall.

Båda metoderna beskrivs i det här avsnittet.

I den här självstudiekursen loggar du in på Azure-portalen, skapar ett lagringskonto och exporterar mallen för det lagringskontot. Du lägger till ett virtuellt nätverk för att ändra resursgruppen. Slutligen exporterar du en ny mall som representerar det aktuella tillståndet. Även om den här artikeln fokuserar på en förenklad infrastruktur kan du följa samma steg för att exportera en mall för en mer komplicerad lösning.

## <a name="create-a-storage-account"></a>skapar ett lagringskonto
1. På [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Lagring** > **Lagringskonto**.
   
      ![Skapa lagring](./media/resource-manager-export-template/create-storage.png)
2. Skapa ett lagringskonto med namnet **lagring**, din initialer och datumet. Namnet på lagringskontot måste vara unikt i Azure. Om namnet redan används, visas ett felmeddelande som anger att namnet används. Försök med en annan variant. Välj **Skapa ny** för resursgruppen och ge den namnet **ExportGroup**. Du kan använda standardvärden för de andra egenskaperna. Välj **Skapa**.
   
      ![Ange värden för lagring](./media/resource-manager-export-template/provide-storage-values.png)

Distributionen kan ta någon minut. När distributionen är klar innehåller din prenumeration lagringskontot.

## <a name="view-a-template-from-deployment-history"></a>Visa en mall från distributionshistoriken
1. Gå till resursgruppsbladet för din nya resursgrupp. Observera att bladet visar resultatet från den senaste distributionen. Välj den här länken.
   
      ![blad för resursgrupp](./media/resource-manager-export-template/resource-group-blade.png)
2. Du ser distributionshistoriken för gruppen. I ditt fall visas antagligen bara en distribution på bladet. Välj den här distributionen.
   
     ![den senaste distributionen](./media/resource-manager-export-template/last-deployment.png)
3. Bladet visar en sammanfattning av distributionen. Sammanfattningen innehåller statusen för distributionen och dess åtgärder samt de värden som du angav för parametrarna. Om du vill visa mallen som du använde för distributionen väljer du **Visa mall**.
   
     ![visa distributionssammanfattning](./media/resource-manager-export-template/deployment-summary.png)
4. Resource Manager hämtar följande sex filer åt dig:
   
   1. **Mall**– Mallen som definierar infrastrukturen för lösningen. När du skapade lagringskontot på portalen använde Resource Manager en mall för att distribuera det och sparade mallen för framtida bruk.
   2. **Parametrar** – En parameterfil som du kan använda för att skicka in värden under distributionen. Den innehåller de värden som du angav under den första distributionen, men du kan ändra dessa värden när du distribuerar om mallen.
   3. **CLI** – En Azure CLI-skriptfil (Command-Line-Interface) som du kan använda för att distribuera mallen.
   3. **CLI 2.0** – En Azure CLI-skriptfil (Command-Line-Interface) som du kan använda för att distribuera mallen.
   4. **PowerShell** – En Azure PowerShell-skriptfil som du kan använda för att distribuera mallen.
   5. **.NET** – En .NET-klass som du kan använda för att distribuera mallen.
   6. **Ruby**– En Ruby-klass som du kan använda för att distribuera mallen.
      
      Filerna är tillgängliga via länkar i bladet. Som standard visas mallen på bladet.
      
       ![Visa mall](./media/resource-manager-export-template/view-template.png)
      
      Vi ska titta närmare på mallen. Mallen bör se ut ungefär så här:
      
      ```json
      {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "name": {
            "type": "String"
          },
          "accountType": {
            "type": "String"
          },
          "location": {
            "type": "String"
          },
          "encryptionEnabled": {
            "defaultValue": false,
            "type": "Bool"
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
              "name": "[parameters('accountType')]"
            },
            "kind": "Storage",
            "name": "[parameters('name')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "properties": {
              "encryption": {
                "services": {
                  "blob": {
                    "enabled": "[parameters('encryptionEnabled')]"
                  }
                },
                "keySource": "Microsoft.Storage"
              }
            }
          }
        ]
      }
      ```

Den här mallen är den mall som används för att skapa ditt lagringskonto. Observera att den innehåller parametrar som gör att du kan distribuera olika typer av lagringskonton. Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md). En fullständig lista över de funktioner som du kan använda i en mall finns i [Funktioner i en Azure Resource Manager-mall](resource-group-template-functions.md).

## <a name="add-a-virtual-network"></a>Lägga till ett virtuellt nätverk
Den mall som du hämtade i det föregående avsnittet representerade infrastrukturen för den ursprungliga distributionen. Den kommer dock inte att ta hänsyn till eventuella ändringar som du gör efter distributionen.
För att illustrera detta ska vi ändra resursgruppen genom att lägga till ett virtuellt nätverk via portalen.

1. Välj **Lägg till** i resursgruppsbladet.
   
      ![lägga till en resurs](./media/resource-manager-export-template/add-resource.png)
2. Välj **Virtuellt nätverk** från de tillgängliga resurserna.
   
      ![välj virtuellt nätverk](./media/resource-manager-export-template/select-vnet.png)
3. Ge det virtuella nätverket namnet **VNET** och använd standardvärdena för de andra egenskaperna. Välj **Skapa**.
   
      ![ange varning](./media/resource-manager-export-template/create-vnet.png)
4. När det virtuella nätverket har distribuerats till resursgruppen titta du på distributionshistoriken igen. Nu ser du två distributioner. Om du inte ser den andra distributionen kan du behöva stänga resursgruppsbladet och öppna det igen. Välj den nyare distributionen.
   
      ![distributionshistorik](./media/resource-manager-export-template/deployment-history.png)
5. Visa mallen för den distributionen. Observera att den bara definierar det virtuella nätverket. Den omfattar inte lagringskontot som du tidigare har distribuerat. Du har inte längre en mall som representerar alla resurser i en resursgrupp.

## <a name="export-the-template-from-resource-group"></a>Exportera mallen från resursgruppen
Exportera en mall som visar en ögonblicksbild av resursgruppen för att hämta det aktuella tillståndet för en resursgrupp.  

> [!NOTE]
> Du kan inte exportera en mall för en resursgrupp som har fler än 200 resurser.
> 
> 

1. Du visar mallen för en resursgrupp genom att välja **Automationsskript**.
   
      ![Exportera resursgrupp](./media/resource-manager-export-template/export-resource-group.png)
   
     Alla resurstyper stöder inte funktionen för mallexport. Om din resursgrupp bara innehåller lagringskontot och det virtuella nätverket som visas i den här artikeln ser du inget fel. Om du har skapat andra resurstyper kan du dock se ett fel som anger att det har uppstått problem med exporten. Du lär dig hur du hanterar dessa problem i avsnittet [Åtgärda exportproblem](#fix-export-issues).
2. Återigen ser du de sex filer som du kan använda för att distribuera lösningen igen, men nu är mallen lite annorlunda. Den här mallen har bara två parametrar: en för lagringskontots namn och en för det virtuella nätverkets namn.

  ```json
  "parameters": {
    "virtualNetworks_VNET_name": {
      "defaultValue": "VNET",
      "type": "String"
    },
    "storageAccounts_storagetf05092016_name": {
      "defaultValue": "storagetf05092016",
      "type": "String"
    }
  },
  ```
   
     Resource Manager hämtade inte mallarna som du använde under distributionen. I stället skapades en ny mall som baseras på resursernas aktuella konfiguration. Exempelvis anger mallen lagringskontots plats och replikeringsvärdet till:

  ```json 
  "location": "northeurope",
  "tags": {},
  "properties": {
    "accountType": "Standard_RAGRS"
  },
  ```
3. Du har ett par alternativ för att fortsätta att arbeta med den här mallen. Du kan antingen hämta mallen och arbeta med den lokalt med en JSON-redigerare, eller så kan du spara mallen i biblioteket och arbeta med den via portalen.
   
     Om du är nöjd med en JSON-redigerare såsom [VS-kod](resource-manager-vs-code.md) eller [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), kanske du föredrar att hämta mallen lokalt och använda denna redigerare. Om du inte har en JSON-redigerare kanske du föredrar att redigera mallen via portalen. Resten av det här avsnittet förutsätter att du har sparat mallen i biblioteket i portalen. Du gör dock samma syntaxändringar för mallen oavsett om du arbetar lokalt med en JSON-redigerare eller via portalen.
   
     Om du vill arbeta lokalt väljer du **Hämta**.
   
      ![Ladda ned mall](./media/resource-manager-export-template/download-template.png)
   
     Om du vill arbeta via portalen väljer du **Lägg till i bibliotek**.
   
      ![lägg till i bibliotek](./media/resource-manager-export-template/add-to-library.png)
   
     När du lägger till en mall i biblioteket ska du ge mallen ett namn och en beskrivning. Välj sedan **Spara**.
   
     ![ange mallvärden](./media/resource-manager-export-template/set-template-values.png)
4. Om du vill visa en mall som sparats i biblioteket väljer du **Fler tjänster**, skriv **Mallar** för att filtrera resultaten och välj **Mallar**.
   
      ![hitta mallar](./media/resource-manager-export-template/find-templates.png)
5. Välj mallen med det namn som du sparade.
   
      ![välj mall](./media/resource-manager-export-template/select-library-template.png)

## <a name="customize-the-template"></a>Anpassa mallen
Den exporterade mallen fungerar om du vill skapa samma lagringskonto och virtuella nätverk för varje distribution. Resource Manager innehåller dock alternativ som gör att du kan distribuera mallar med mycket bättre flexibilitet. Under distributionen kanske du vill ange vilken typ av lagringskonto som ska skapas eller vilka värden som ska användas för adressprefixen och undernätsprefixen för det virtuella nätverket.

I det här avsnittet ska du lägga till parametrar till den exporterade mallen så att du kan återanvända mallen när du distribuerar dessa resurser till andra miljöer. Du kan också lägga till vissa funktioner till mallen för att minska risken för fel när du distribuerar mallen. Du behöver inte längre gissa dig fram till ett unikt namn för ditt lagringskonto. I stället skapar mallen ett unikt namn. Du begränsar de värden som kan anges för lagringskontotypen till endast giltiga alternativ.

1. Välj **Redigera** för att anpassa mallen.
   
     ![visa mall](./media/resource-manager-export-template/show-template.png)
2. Välj mallen.
   
     ![redigera mall](./media/resource-manager-export-template/edit-template.png)
3. För att kunna skicka de värden som du eventuellt vill ange under distributionen ersätter du avsnittet **Parametrar** med nya parameterdefinitioner. Observera värdena för **allowedValues** för **storageAccount_accountType**. Om du råkar ange ett ogiltigt värde upptäcks felet innan distributionen startar. Observera också att du bara anger ett prefix för lagringskontonamnet och att prefixet är begränsat till 11 tecken. Genom att begränsa prefixet till 11 tecken kan du vara säker på att det fullständiga namnet inte överskrider det högsta antalet tillåtna tecken för ett lagringskonto. Prefixet gör att du kan använda en namngivningskonvention för dina lagringskonton. Du ser hur du skapar ett unikt namn i nästa steg.

  ```json
  "parameters": {
    "storageAccount_prefix": {
      "type": "string",
      "maxLength": 11
    },
    "storageAccount_accountType": {
      "defaultValue": "Standard_RAGRS",
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ]
    },
    "virtualNetwork_name": {
      "type": "string"
    },
    "addressPrefix": {
      "defaultValue": "10.0.0.0/16",
      "type": "string"
    },
    "subnetName": {
      "defaultValue": "subnet-1",
      "type": "string"
    },
    "subnetAddressPrefix": {
      "defaultValue": "10.0.0.0/24",
      "type": "string"
    }
  },
  ```

4. Avsnittet **variables** i mallen är tomt för närvarande. I avsnittet **Variabler** kan du skapa värden som förenklar syntaxen för resten av mallen. Ersätt det här avsnittet med en ny variabeldefinition. Variabeln **storageAccount_name** sammanfogar prefixet från parametern till en unik sträng som genereras baserat på resursgruppens identifierare. Du behöver inte längre gissa ett unikt namn när du anger ett parametervärde.

  ```json
  "variables": {
    "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
  },
  ```

5. Om du vill använda parametrarna och variabeln i resursdefinitionerna ersätter du avsnittet **Resurser** med nya resursdefinitioner. Observera att det inte är mycket som har ändrats i resursdefinitionerna förutom värdet som tilldelats till resursegenskapen. Egenskaperna är samma som egenskaperna från den exporterade mallen. Du tilldelar bara parametervärden egenskaper i stället för hårdkodade värden. Platsen för resurserna är konfigurerad att använda samma plats som resursgruppen via uttrycket **resourceGroup().location**. **Variabeluttrycket** refererar till variabeln som du skapade för lagringskontonamnet.

  ```json
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[parameters('virtualNetwork_name')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('subnetName')]",
            "properties": {
              "addressPrefix": "[parameters('subnetAddressPrefix')]"
            }
          }
        ]
      },
      "dependsOn": []
    },
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccount_name')]",
      "apiVersion": "2015-06-15",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "accountType": "[parameters('storageAccount_accountType')]"
      },
      "dependsOn": []
    }
  ]
  ```

6. Välj **OK** när du har redigerat klart mallen.
7. Välj **Spara** för att spara ändringarna i mallen.
   
     ![spara mall](./media/resource-manager-export-template/save-template.png)
8. Om du vill distribuera den uppdaterade mallen väljer du **Distribuera**.
   
     ![distribuera mallen](./media/resource-manager-export-template/deploy-template.png)
9. Ange parametervärden och välj en ny resursgrupp att distribuera resurserna till.

## <a name="update-the-downloaded-parameters-file"></a>Uppdatera den hämtade parameterfilen
Om du arbetar med hämtade filer (i stället för portalbiblioteket) måste du uppdatera den hämtade parameterfilen. Den matchar inte längre parametrarna i mallen. Du behöver inte använda en parameterfil, men det kan förenkla processen när du distribuerar en miljö. Eftersom du ska använda standardvärdena som är definierade i mallen för många av parametrarna behöver parameterfilen bara två värden.

Ersätt innehållet i filen parameters.json med:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccount_prefix": {
      "value": "storage"
    },
    "virtualNetwork_name": {
      "value": "VNET"
    }
  }
}
```

Den uppdaterade parameterfilen innehåller bara värden för parametrar som inte har något standardvärde. Du kan ange värden för de andra parametrarna om du vill använda ett annat värde än standardvärdet.

## <a name="fix-export-issues"></a>Åtgärda exportproblem
Alla resurstyper stöder inte funktionen för mallexport. Resource Manager exporterar inte vissa resurstyper för att förhindra att känsliga data exponeras. Om det till exempel finns en anslutningssträng i platsens config-fil vill du förmodligen inte att den ska visas uttryckligen i en exporterad mall. Du kan lösa det här problemet genom att manuellt lägga till de resurser som saknas i mallen.

> [!NOTE]
> Exportproblem uppstår bara när du exporterar från en resursgrupp i stället för från distributionshistoriken. Om din senaste distribution korrekt representerar resursgruppens aktuella tillstånd exporterar du mallen från distributionshistoriken i stället för från resursgruppen. Exportera bara från en resursgrupp om du har gjort ändringar i resursgruppen som inte är definierade i samma mall.
> 
> 

Om du till exempel exporterar en mall för en resursgrupp som innehåller en webbapp, SQL Database och en anslutningssträng i platsens konfiguration visas följande meddelande:

![visa fel](./media/resource-manager-export-template/show-error.png)

Markera meddelandet så ser du exakt vilka resurstyper som inte exporterades. 

![visa fel](./media/resource-manager-export-template/show-error-details.png)

I det här avsnittet beskrivs vanliga korrigeringar.

### <a name="connection-string"></a>Anslutningssträng
Lägg till en definition för anslutningssträngen till databasen i webbplatsresursen:

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "connectionstrings",
      "dependsOn": [
          "[concat('Microsoft.Web/Sites/', parameters('<site-name>'))]"
      ],
      "properties": {
          "DefaultConnection": {
            "value": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('<database-server-name>'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('<database-name>'), ';User Id=', parameters('<admin-login>'), '@', parameters('<database-server-name>'), ';Password=', parameters('<admin-password>'), ';')]",
              "type": "SQLServer"
          }
      }
    }
  ]
}
```    

### <a name="web-site-extension"></a>Tillägg för webbplats
I webbplatsresursen lägger du till en definition för koden för att installera:

```json
{
  "type": "Microsoft.Web/sites",
  ...
  "resources": [
    {
      "name": "MSDeploy",
      "type": "extensions",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('<site-name>'))]"
      ],
      "properties": {
        "packageUri": "[concat(parameters('<artifacts-location>'), '/', parameters('<package-folder>'), '/', parameters('<package-file-name>'), parameters('<sas-token>'))]",
        "dbType": "None",
        "connectionString": "",
        "setParameters": {
          "IIS Web Application Name": "[parameters('<site-name>')]"
        }
      }
    }
  ]
}
```

### <a name="virtual-machine-extension"></a>Tillägg för virtuell dator
Exempel på tillägg för virtuella datorer finns i [Konfigurationsexempel med Windows VM-tillägget för Azure](../virtual-machines/virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="virtual-network-gateway"></a>Virtuell nätverksgateway
Lägg till en resurstyp för virtuella nätverksgateways.

```json
{
  "type": "Microsoft.Network/virtualNetworkGateways",
  "name": "[parameters('<gateway-name>')]",
  "apiVersion": "2015-06-15",
  "location": "[resourceGroup().location]",
  "properties": {
    "gatewayType": "[parameters('<gateway-type>')]",
    "ipConfigurations": [
      {
        "name": "default",
        "properties": {
          "privateIPAllocationMethod": "Dynamic",
          "subnet": {
            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('<vnet-name>'), parameters('<new-subnet-name>'))]"
          },
          "publicIpAddress": {
            "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('<new-public-ip-address-Name>'))]"
          }
        }
      }
    ],
    "enableBgp": false,
    "vpnType": "[parameters('<vpn-type>')]"
  },
  "dependsOn": [
    "Microsoft.Network/virtualNetworks/codegroup4/subnets/GatewaySubnet",
    "[concat('Microsoft.Network/publicIPAddresses/', parameters('<new-public-ip-address-Name>'))]"
  ]
},
```

### <a name="local-network-gateway"></a>Lokal nätverksgateway
Lägg till en resurstyp för lokala nätverksgateways.

```json
{
    "type": "Microsoft.Network/localNetworkGateways",
    "name": "[parameters('<local-network-gateway-name>')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "localNetworkAddressSpace": {
        "addressPrefixes": "[parameters('<address-prefixes>')]"
      }
    }
}
```

### <a name="connection"></a>Anslutning
Lägg till en resurstyp för anslutningar.

```json
{
    "apiVersion": "2015-06-15",
    "name": "[parameters('<connection-name>')]",
    "type": "Microsoft.Network/connections",
    "location": "[resourceGroup().location]",
    "properties": {
        "virtualNetworkGateway1": {
        "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', parameters('<gateway-name>'))]"
      },
      "localNetworkGateway2": {
        "id": "[resourceId('Microsoft.Network/localNetworkGateways', parameters('<local-gateway-name>'))]"
      },
      "connectionType": "IPsec",
      "routingWeight": 10,
      "sharedKey": "[parameters('<shared-key>')]"
    }
},
```


## <a name="next-steps"></a>Nästa steg
Grattis! Nu vet du hur du exporterar en mall från resurser som du har skapat på portalen.

* Du kan distribuera en mall genom [PowerShell](resource-group-template-deploy.md), [Azure CLI](resource-group-template-deploy-cli.md) eller [REST API](resource-group-template-deploy-rest.md).
* Information om hur du exporterar en mall med PowerShell finns i [Använda Azure PowerShell med Azure Resource Manager](powershell-azure-resource-manager.md).
* Information om hur du exporterar en mall med Azure CLI finns i [Använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).


