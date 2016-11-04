---
title: Exportera en Azure Resource Manager-mall | Microsoft Docs
description: Använd Azure Resource Manager om du vill exportera en mall från en befintlig resursgrupp.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2016
ms.author: tomfitz

---
# Exportera en Azure Resource Manager-mall från befintliga resurser
Med Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs.

Observera att du kan exportera en mall på två sätt:

* Du kan exportera själva mallen som du använde för en distribution. Den exporterade mallen innehåller alla parametrar och variabler exakt som de visas i den ursprungliga mallen. Den här metoden är användbar när du har distribuerat resurser via portalen. I detta fall behöver du veta hur du bygger mallen för att skapa dessa resurser.
* Du kan exportera en mall som representerar resursgruppens aktuella tillstånd. Den exporterade mallen baseras inte på en mall som du har använt för distribution. I stället skapar den en mall som är en ögonblicksbild av resursgruppen. Den exporterade mallen har många hårdkodade värden och troligen inte så många parametrar som du vanligtvis definierar. Den här metoden är användbar när du har ändrat resursgruppen via portalen eller med hjälp av skript. I detta fall behöver du avbilda resursgruppen som en mall.

Båda metoderna beskrivs i det här avsnittet. I artikeln [Anpassa en exporterad Azure Resource Manager-mall](resource-manager-customize-template.md) ser du hur du använder en mall som du genererat från resursgruppens aktuella tillstånd och gör den mer användbar för omdistribution av din lösning.

I den här självstudiekursen loggar du in på Azure-portalen, skapar ett lagringskonto och exporterar mallen för det lagringskontot. Du lägger till ett virtuellt nätverk för att ändra resursgruppen. Slutligen exporterar du en ny mall som representerar det aktuella tillståndet. Även om den här artikeln fokuserar på en förenklad infrastruktur kan du följa samma steg för att exportera en mall för en mer komplicerad lösning.

## skapar ett lagringskonto
1. På [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Data + Lagring** > **Lagringskonto**.
   
      ![Skapa lagring](./media/resource-manager-export-template/create-storage.png)
2. Skapa ett lagringskonto med namnet **lagring**, din initialer och datumet. Namnet på lagringskontot måste vara unikt i Azure. Om du försöker med ett namn som redan används kan du pröva en variant. För resursgruppen använder du **ExportGroup**. Du kan använda standardvärden för de andra egenskaperna. Välj **Skapa**.
   
      ![Ange värden för lagring](./media/resource-manager-export-template/provide-storage-values.png)

När distributionen är klar innehåller din prenumeration lagringskontot.

## Exportera mallen från distributionshistoriken
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
   4. **PowerShell** – En Azure PowerShell-skriptfil som du kan använda för att distribuera mallen.
   5. **.NET** – En .NET-klass som du kan använda för att distribuera mallen.
   6. **Ruby**– En Ruby-klass som du kan använda för att distribuera mallen.
      
      Filerna är tillgängliga via länkar i bladet. Som standard visas mallen på bladet.
      
       ![Visa mall](./media/resource-manager-export-template/view-template.png)
      
      Vi ska titta närmare på mallen. Mallen bör se ut ungefär så här:
      
        {     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",     "contentVersion": "1.0.0.0",     "parameters": {       "name": {         "type": "String"       },       "accountType": {         "type": "String"       },       "location": {         "type": "String"       },       "encryptionEnabled": {         "defaultValue": false,         "type": "Bool"       }     },     "resources": [       {         "type": "Microsoft.Storage/storageAccounts",         "sku": {           "name": "[parameters('accountType')]"         },         "kind": "Storage",         "name": "[parameters('name')]",         "apiVersion": "2016-01-01",         "location": "[parameters('location')]",         "properties": {           "encryption": {             "services": {               "blob": {                 "enabled": "[parameters('encryptionEnabled')]"               }             },             "keySource": "Microsoft.Storage"           }         }       }     ]   }

Den här mallen är den mall som används för att skapa ditt lagringskonto. Observera att den innehåller parametrar som gör att du kan distribuera olika typer av lagringskonton. Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md). En fullständig lista över de funktioner som du kan använda i en mall finns i [Funktioner i en Azure Resource Manager-mall](resource-group-template-functions.md).

## Lägga till ett virtuellt nätverk
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
5. Titta på mallen för den distributionen. Observera att den bara definierar de ändringar som du har gjort för att lägga till det virtuella nätverket.

Det är oftast en bra idé att arbeta med en mall som distribuerar hela infrastrukturen för din lösning i en enda åtgärd. Den här metoden är mer tillförlitlig än att behöva komma ihåg många olika mallar som ska distribueras.

## Exportera mallen från resursgruppen
Även om varje distribution endast visar de ändringar som du har gjort i resursgruppen kan du när som helst exportera en mall för att visa attributen för hela resursgruppen.  

> [!NOTE]
> Du kan inte exportera en mall för en resursgrupp som har fler än 200 resurser.
> 
> 

1. Du visar mallen för en resursgrupp genom att välja **Automationsskript**.
   
      ![Exportera resursgrupp](./media/resource-manager-export-template/export-resource-group.png)
   
     Alla resurstyper stöder inte funktionen för mallexport. Om din resursgrupp bara innehåller lagringskontot och det virtuella nätverket som visas i den här artikeln ser du inget fel. Om du har skapat andra resurstyper kan du dock se ett fel som anger att det har uppstått problem med exporten. Du lär dig hur du hanterar dessa problem i avsnittet [Åtgärda exportproblem](#fix-export-issues).
2. Återigen ser du de sex filer som du kan använda för att distribuera lösningen igen, men nu är mallen lite annorlunda. Den här mallen har bara två parametrar: en för lagringskontots namn och en för det virtuella nätverkets namn.
   
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
   
     Resource Manager hämtade inte mallarna som du använde under distributionen. I stället skapades en ny mall som baseras på resursernas aktuella konfiguration. Exempelvis anger mallen lagringskontots plats och replikeringsvärdet till:
   
        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },
3. Ladda ned mallen så att du kan arbeta med den lokalt.
   
      ![Ladda ned mall](./media/resource-manager-export-template/download-template.png)
4. Leta upp ZIP-filen som du laddade ned och extrahera innehållet. Du kan använda den nedladdade mallen för att distribuera om din infrastruktur.

## Åtgärda exportproblem
Alla resurstyper stöder inte funktionen för mallexport. Resource Manager exporterar inte vissa resurstyper för att förhindra att känsliga data exponeras. Om det till exempel finns en anslutningssträng i platsens config-fil vill du förmodligen inte att den ska visas uttryckligen i en exporterad mall. Du kan komma runt det här problemet genom att manuellt lägga till de resurser som saknas i mallen.

> [!NOTE]
> Exportproblem uppstår bara när du exporterar från en resursgrupp i stället för från distributionshistoriken. Om din senaste distribution korrekt representerar resursgruppens aktuella tillstånd exporterar du mallen från distributionshistoriken i stället för från resursgruppen. Exportera bara från en resursgrupp om du har gjort ändringar i resursgruppen som inte är definierade i samma mall.
> 
> 

Om du till exempel exporterar en mall för en resursgrupp som innehåller en webbapp, SQL Database och en anslutningssträng i platsens config-fil visas följande meddelande.

![visa fel](./media/resource-manager-export-template/show-error.png)

Markera meddelandet så ser du exakt vilka resurstyper som inte exporterades. 

![visa fel](./media/resource-manager-export-template/show-error-details.png)

Det här avsnittet innehåller följande vanliga korrigeringar. Du måste lägga till parametrar i mallen för att implementera dessa resurser. Mer information finns i [Anpassa och distribuera om en exporterad mall](resource-manager-customize-template.md).

### Anslutningssträng
Lägg till en definition för anslutningssträngen till databasen i webbplatsresursen:

```
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

### Tillägg för webbplats
I webbplatsresursen lägger du till en definition för koden för att installera:

```
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

### Tillägg för virtuell dator
Exempel på tillägg för virtuella datorer finns i [Konfigurationsexempel med Windows VM-tillägget för Azure](virtual-machines/virtual-machines-windows-extensions-configuration-samples.md).

### Virtuell nätverksgateway
Lägg till en resurstyp för virtuella nätverksgateways.

```
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

### Lokal nätverksgateway
Lägg till en resurstyp för lokala nätverksgateways.

```
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

### Anslutning
Lägg till en resurstyp för anslutningar.

```
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


## Nästa steg
Grattis! Nu vet du hur du exporterar en mall från resurser som du har skapat på portalen.

* I den andra delen av den här självstudiekursen ska du anpassa mallen som du precis har laddat ned genom att lägga till fler parametrar och distribuera om den med hjälp av ett skript. Mer information finns i [Anpassa och distribuera om en exporterad mall](resource-manager-customize-template.md).
* Information om hur du exporterar en mall med PowerShell finns i [Använda Azure PowerShell med Azure Resource Manager](powershell-azure-resource-manager.md).
* Information om hur du exporterar en mall med Azure CLI finns i [Använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).

<!--HONumber=Sep16_HO4-->


