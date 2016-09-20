<properties
    pageTitle="Anpassa en exporterad Resource Manager-mall | Microsoft Azure"
    description="Lägg till parametrar i en exporterad Azure Resource Manager-mall och distribuera den igen via Azure PowerShell eller Azure CLI."
    services="azure-resource-manager"
    documentationCenter=""
    authors="tfitzmac"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="azure-resource-manager"
    ms.workload="multiple"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/01/2016"
    ms.author="tomfitz"/>

# Anpassa en exporterad Azure Resource Manager-mall.

Den här artikeln visar hur du ändrar en exporterad mall så att du kan skicka in ytterligare värden som parametrar. Den bygger på stegen i artikeln [Exportera en Resource Manager-mall](resource-manager-export-template.md), men det är inget krav att du har gått igenom stegen i den artikeln först. Du hittar den nödvändiga mallen och skripten i den här artikeln.

## Visa en exporterad mall

Om du har följt stegen i [Exportera en Resource Manager-mall](resource-manager-export-template.md) öppnar du mallen som du laddade ned. Mallen heter **template.json**. Om du har Visual Studio eller Visual Studio Code kan du använda något av dem för att redigera mallen. Annars kan du använda valfri JSON-redigerare eller textredigerare.

Om du inte har gått igenom den tidigare genomgången kan du skapa en fil med namnet **template.json** och lägga till följande innehåll från den exporterade mallen i filen.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
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
    "variables": {},
    "resources": [
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworks_VNET_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "default",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24"
                        }
                    }
                ]
            },
            "dependsOn": []
        },
        {
            "comments": "Generalized from resource in walkthrough.",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccounts_storagetf05092016_name')]",
            "apiVersion": "2015-06-15",
            "location": "northeurope",
            "tags": {},
            "properties": {
                "accountType": "Standard_RAGRS"
            },
            "dependsOn": []
        }
    ]
}
```

Mallen template.json fungerar bra om du vill skapa samma typ av lagringskonto i samma region med ett virtuellt nätverk som använder samma adressprefix och samma undernätsprefix för varje distribution. Resource Manager innehåller dock alternativ som gör att du kan distribuera mallar med mycket bättre flexibilitet än så. Under distributionen kanske du vill ange vilken typ av lagringskonto som ska skapas eller vilka värden som ska användas för adressprefixen och undernätsprefixen för det virtuella nätverket.

## Anpassa mallen

I det här avsnittet ska du lägga till parametrar till den exporterade mallen så att du kan återanvända mallen när du distribuerar dessa resurser till andra miljöer. Du kan också lägga till vissa funktioner till mallen för att minska risken för fel när du distribuerar mallen. Du behöver inte längre gissa dig fram till ett unikt namn för ditt lagringskonto. I stället skapar mallen ett unikt namn. Du kan begränsa de värden som kan anges för lagringskontotypen till endast giltiga alternativ.

1. För att kunna skicka de värden som du eventuellt vill ange under distributionen ersätter du avsnittet **parameters** med följande parameterdefinitioner. Observera värdena för **allowedValues** för **storageAccount_accountType**. Om du råkar ange ett ogiltigt värde upptäcks felet innan distributionen startar. Observera också att du bara anger ett prefix för lagringskontonamnet och att prefixet är begränsat till 11 tecken. Genom att begränsa prefixet till 11 tecken kan du vara säker på att det fullständiga namnet inte överskrider det högsta antalet tillåtna tecken för ett lagringskonto. Prefixet gör att du kan använda en namngivningskonvention för dina lagringskonton. Du ser hur du skapar ett unikt namn i nästa steg.

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

2. Avsnittet **variables** i mallen är tomt för närvarande. Ersätt det här avsnittet med följande kod. I avsnittet **variables** kan du, som mallens skapare, skapa värden som förenklar syntaxen för resten av mallen. Variabeln **storageAccount_name** sammanfogar prefixet från parametern till en unik sträng som genereras baserat på resursgruppens identifierare.

        "variables": {
          "storageAccount_name": "[concat(parameters('storageAccount_prefix'), uniqueString(resourceGroup().id))]"
        },

3. Om du vill använda parametrarna och variabeln i resursdefinitionerna ersätter du avsnittet **resources** med följande definitioner. Observera att mycket lite faktiskt har ändrats i resursdefinitionerna förutom värdet som tilldelats till resursegenskapen. Egenskaperna är samma som egenskaperna från den exporterade mallen. Du tilldelar bara parametervärden egenskaper i stället för hårdkodade värden. Platsen för resurserna är konfigurerad att använda samma plats som resursgruppen via uttrycket **resourceGroup().location**. **Variabeluttrycket** refererar till variabeln som du skapade för lagringskontonamnet.

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

## Åtgärda parameterfilen

Den nedladdade parameterfilen stämmer inte längre med parametrarna i mallen. Du behöver inte använda en parameterfil, men det kan förenkla processen när du distribuerar en miljö. Eftersom du ska använda standardvärdena som är definierade i mallen för många av parametrarna behöver parameterfilen bara två värden.

Ersätt innehållet i filen parameters.json med:

```
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

## Distribuera mallen

Du kan använda antingen Azure PowerShell eller Azure CLI (Command-Line-Interface) för att distribuera den anpassade mallen och parameterfilerna. Om det behövs installerar du antingen [Azure PowerShell](powershell-install-configure.md) eller [Azure CLI](xplat-cli-install.md). Du kan använda skripten som du laddade ned med mallen när du exporterade den ursprungliga mallen. Du kan också skriva egna skript för att distribuera mallen.
Båda alternativen visas i den här artikeln.

2. Använd något av följande om du vill distribuera mallen med hjälp av ett eget skript.

     Om du använder PowerShell kör du:

        # login
        Add-AzureRmAccount

        # create a new resource group
        New-AzureRmResourceGroup -Name ExportGroup -Location "West Europe"

        # deploy the template to the resource group
        New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExportGroup -TemplateFile {path-to-file}\template.json -TemplateParameterFile {path-to-file}\parameters.json

     Om du använder Azure CLI kör du:

        azure login

        azure group create -n ExportGroup -l "West Europe"

        azure group deployment create -f {path-to-file}\azuredeploy.json -e {path-to-file}\parameters.json -g ExportGroup -n ExampleDeployment

3. Om du har laddat ned den exporterade mallen och skripten letar du upp filen **deploy.ps1** (för PowerShell) eller filen **deploy.sh** (för Azure CLI).

     Om du använder PowerShell kör du:

        Get-Item deploy.ps1 | Unblock-File

        .\deploy.ps1

     Om du använder Azure CLI kör du:

        .\deploy.sh

## Nästa steg

- [Genomgång av en Resource Manager-mall](resource-manager-template-walkthrough.md) bygger vidare på det du lärt dig i den här artikeln och visar hur du skapar en mall för en mer komplicerad lösning. I artikeln får du lära dig mer om de resurser som är tillgängliga och hur du avgör vilka värden du ska använda.
- Information om hur du exporterar en mall med PowerShell finns i [Använda Azure PowerShell med Azure Resource Manager](powershell-azure-resource-manager.md).
- Information om hur du exporterar en mall med Azure CLI finns i [Använda Azure CLI för Mac, Linux och Windows med Azure Resource Manager](xplat-cli-azure-resource-manager.md).
- Information om hur mallar är strukturerade finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).



<!--HONumber=sep16_HO1-->


