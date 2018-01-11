---
title: Azure Resource Manager mallstruktur och syntax | Microsoft Docs
description: "Beskriver strukturen och egenskaperna för Azure Resource Manager-mallar med deklarativ JSON-syntax."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2017
ms.author: tomfitz
ms.openlocfilehash: 89e4b52e7d306bd495c426bcf775f59d0f30eb55
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="resources-section-of-azure-resource-manager-templates"></a>Avsnittet resurser i Azure Resource Manager-mallar

I avsnittet resurser kan du definiera de resurser som distribueras eller uppdateras. Det här avsnittet får komplicerad, eftersom du måste förstå vilka typer som du distribuerar för att tillhandahålla rätt värden.

## <a name="available-properties"></a>Tillgängliga egenskaper

Du kan definiera resurser med följande struktur:

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elementnamn | Krävs | Beskrivning |
|:--- |:--- |:--- |
| tillstånd | Nej | Booleskt värde som anger om resursen har distribuerats. |
| apiVersion |Ja |Version av REST-API för att använda för att skapa resursen. |
| typ |Ja |Typ av resursen. Det här värdet är en kombination av namnområde med resursprovidern och resurstypen (exempelvis **Microsoft.Storage/storageAccounts**). |
| namn |Ja |Namnet på resursen. Namnet måste följa URI komponenten begränsningar som definierats i RFC3986. Dessutom Azure-tjänster som exponerar resursnamnet att externa parter verifiera namnet så att den är inte ett försök att imitera en annan identitet. |
| location |Det varierar |Geo-platser som stöds av den angivna resursen. Du kan välja någon av de tillgängliga platserna, men oftast är det klokt att välja ett som är nära dina användare. Vanligtvis gör det också bra att placera resurser som samverkar med varandra i samma region. De flesta resurstyper kräver en plats, men vissa typer (till exempel en rolltilldelning) kräver inte en plats. |
| tags |Nej |Taggar som är kopplade till resursen. Lägga till taggar för att organisera logiskt resurser i din prenumeration. |
| Kommentarer |Nej |Anteckningar för dokumentation resurserna i mallen |
| kopiera |Nej |Om mer än en instans krävs antalet resurser för att skapa. Standardläget är parallell. Ange seriell läge när du inte vill att alla eller resurser som ska distribueras på samma gång. Mer information finns i [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nej |Resurser som måste distribueras innan den här resursen har distribuerats. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i rätt ordning. Om resurserna inte är beroende av varandra kan distribueras de parallellt. Värdet kan vara en kommaavgränsad lista över en resurs namn eller resurs unika identifierare. Endast lista över resurser som distribueras i den här mallen. Resurser som inte har definierats i denna mall måste redan finnas. Undvik att lägga till onödiga beroenden som de långsamma distributionen och skapa Cirkelberoenden. Information om inställningen beroenden finns [definiera beroenden i Azure Resource Manager-mallar](resource-group-define-dependencies.md). |
| properties |Nej |Resurs-specifika konfigurationsinställningar. Värdena för egenskaperna är samma värden som du anger i begärandetexten för REST API-åtgärd (PUT-metoden) att skapa resursen. Du kan också ange en kopia matris om du vill skapa flera instanser av en egenskap. |
| resurser |Nej |Underordnade resurser som är beroende av resursen som definieras. Ange endast resurstyper som tillåts enligt schemat för den överordnade resursen. Den fullständigt kvalificerade typ av underordnade resursen innehåller resurstypen överordnade **Microsoft.Web/sites/extensions**. Beroende på den överordnade resursen är inte underförstådd. Du måste uttryckligen definiera sambandet. |

## <a name="resource-specific-values"></a>Resurs-specifika värden

Den **apiVersion**, **typen**, och **egenskaper** är olika för varje resurstyp av. För att fastställa värden för dessa egenskaper finns [mallreferensen](/azure/templates/).

## <a name="resource-names"></a>Resursnamn
I allmänhet kan arbeta du med tre typer av resursnamn i Resource Manager:

* Resursnamn som måste vara unika.
* Resursnamn som inte behöver vara unika, men välja att ange ett namn som hjälper dig att identifiera resursen.
* Resursnamn som kan vara generiska.

### <a name="unique-resource-names"></a>Unikt namn
Du måste ange ett unikt resursnamn för någon resurstyp av som har en slutpunkt för åtkomst av data. Några vanliga typer av resurser som kräver ett unikt namn är:

* Azure Storage<sup>1</sup> 
* Funktionen Web Apps i Azure App Service
* SQL Server
* Azure Key Vault
* Azure Redis Cache
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> lagringskontonamn måste också vara gemener, 24 tecken eller mindre, och inte har någon bindestreck.

När du anger namnet, du kan manuellt skapa ett unikt namn eller använda den [uniqueString()](resource-group-template-functions-string.md#uniquestring) funktion för att generera ett namn. Du kan också lägga till ett prefix eller suffix till den **uniqueString** resultat. Ändra det unika namnet hjälper dig att enkelt identifiera resurstypen från namn. Du kan till exempel generera ett unikt namn för ett lagringskonto med hjälp av följande variabel:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Resursnamn för identifiering
Vissa typer av resurser som du kanske vill namn, men deras namn behöver inte vara unika. Du kan ange ett namn som identifierar både resurs-kontexten och resurstypen för dessa typer av resurser.

```json
"parameters": {
    "vmName": { 
        "type": "string",
        "defaultValue": "demoLinuxVM",
        "metadata": {
            "description": "The name of the VM to create."
        }
    }
}
```

### <a name="generic-resource-names"></a>Allmänt namn
Du kan använda ett allmänt namn som är hårdkodat i mallen för resurstyper som främst öppnas från en annan resurs. Du kan till exempel ange ett standard, allmän namn på brandväggsregler på en SQLServer:

```json
{
    "type": "firewallrules",
    "name": "AllowAllWindowsAzureIps",
    ...
}
```

## <a name="location"></a>Plats
När du distribuerar en mall måste du ange en plats för varje resurs. Olika typer stöds i olika platser. Använda Azure PowerShell eller Azure CLI om du vill se en lista över platser som är tillgängliga för din prenumeration för en viss resurstyp. 

I följande exempel använder PowerShell för att hämta platser för den `Microsoft.Web\sites` resurstyp:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

I följande exempel använder Azure CLI 2.0 för att hämta platser för den `Microsoft.Web\sites` resurstyp:

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

Ange platsen efter att placeringar som stöds för dina resurser i mallen. Det enklaste sättet att ange det här värdet är att skapa en resursgrupp i en plats som har stöd för resurstyperna och ange varje plats som `[resourceGroup().location]`. Du kan omdistribuera mallen till resursgrupper på olika platser och inte att ändra alla värden i mallen eller parametrar. 

I följande exempel visas ett lagringskonto som har distribuerats till samma plats som resursgruppen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Om du behöver att hårdkoda platsen i mallen kan du ange namnet på en av regionerna som stöds. I följande exempel visas ett lagringskonto som alltid har distribuerats till norra centrala USA:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="tags"></a>Taggar
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

### <a name="add-tags-to-your-template"></a>Lägga till taggar i mallen

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="child-resources"></a>Underordnade resurser

Du kan också definiera en matris med underordnade resurser inom vissa typer av resurser. Underordnade är resurser som finns bara i samband med en annan resurs. Till exempel kan inte en SQL-databas finnas utan en SQLServer så att databasen är en underordnad till servern. Du kan definiera databasen i definitionen för servern.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  ...
  "resources": [
    {
      "name": "exampledatabase",
      "type": "databases",
      "apiVersion": "2014-04-01",
      ...
    }
  ]
}
```

När kapslade, typen är värdet `databases` men dess fullständiga resurstypen är `Microsoft.Sql/servers/databases`. Du inte anger `Microsoft.Sql/servers/` eftersom det antas från den överordnade resurstypen. Namnet på underordnade resursen har angetts till `exampledatabase` men det fullständiga namnet innehåller namnet på överordnade. Du inte anger `exampleserver` eftersom det antas från den överordnade resursen.

Formatet för resurstypen underordnade är:`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`

Formatet på underordnade resursnamnet är:`{parent-resource-name}/{child-resource-name}`

Men du behöver inte definiera databas på servern. Du kan definiera den underordnade resursen på den översta nivån. Du kan använda den här metoden om den överordnade resursen inte är distribuerat i samma mall, eller om vill använda `copy` att skapa flera underordnade resurser. Med den här metoden måste du ange fullständig resurstypen och inkludera namnet på överordnade resursen i namnet på underordnade resursen.

```json
{
  "name": "exampleserver",
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2014-04-01",
  "resources": [ 
  ],
  ...
},
{
  "name": "exampleserver/exampledatabase",
  "type": "Microsoft.Sql/servers/databases",
  "apiVersion": "2014-04-01",
  ...
}
```

När man skapar en fullständiga referens till en resurs, är ordningen att kombinera segment från typ och namn inte bara en sammanfogning av två.  Använd i stället efter namnområde, en sekvens med *typnamn/* par från minst specifika mest specifika:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`stämmer `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` är inte korrekt

## <a name="recommendations"></a>Rekommendationer
Följande information kan vara användbart när du arbetar med resurser:

* För att hjälpa andra deltagare förstå syftet med resursen, ange **kommentarer** för varje resurs i mallen:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Om du använder en *offentlig slutpunkt* i mallen (till exempel Azure Blob storage offentlig slutpunkt), *gör inte hårdkoda* namnområdet. Använd den **referens** funktion för att dynamiskt hämta namnområdet. Du kan använda den här metoden för att distribuera mallen till olika namnområdes-offentliga miljöer utan att manuellt ändra slutpunkten i mallen. Ange API-versionen till samma version som du använder för lagringskontot i mallen:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Om lagringskontot distribueras i samma mall som du skapar, behöver du inte ange leverantörens namnrymd när du refererar till resursen. I följande exempel visas förenklad syntax:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Om du har andra värden i mallen som är konfigurerade för att använda en offentlig namnrymd kan ändra dessa värden för att återspegla samma **referens** funktion. Du kan till exempel ange det **storageUri** -egenskapen för den virtuella dator diagnostikprofilen:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Du kan också referera till ett befintligt lagringskonto i en annan resursgrupp:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Tilldela offentliga IP-adresser till en virtuell dator endast när ett program kräver. Använd ingående NAT-regler, en virtuell nätverksgateway eller en jumpbox för att ansluta till en virtuell dator (VM) för felsökning eller för att hantera administrativa syften.
   
     Mer information om hur du ansluter till virtuella datorer finns:
   
   * [Köra virtuella datorer för en arkitektur med flera nivåer i Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurera WinRM-åtkomst för virtuella datorer i Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Ge extern åtkomst till din Linux VM med hjälp av Azure CLI](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)
* Den **domainNameLabel** -egenskapen för offentliga IP-adresser måste vara unika. Den **domainNameLabel** värdet måste vara mellan 3 och 63 tecken långt och följa de regler som anges av den här reguljärt uttryck: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Eftersom den **uniqueString** funktionen genererar en sträng som är 13 tecken lång, den **dnsPrefixString** parameter är begränsad till 50 tecken:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* När du lägger till ett lösenord för tillägget för anpassat skript kan använda den **commandToExecute** egenskap i den **protectedSettings** egenskapen:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Använd för att säkerställa att hemligheter krypteras när de skickas som parametrar till virtuella datorer och tillägg i **protectedSettings** -egenskapen för de relevanta tillägg.
   > 
   > 


## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner som du kan använda från i en mall finns [Azure Resource Manager mallen Functions](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Du kan behöva använda resurser som finns i en annan resursgrupp. Det här scenariot är vanligt när du arbetar med lagringskonton eller virtuella nätverk som delas mellan flera resursgrupper. Mer information finns i [resourceId funktionen](resource-group-template-functions-resource.md#resourceid).
* Information om begränsningar för resursen finns [rekommenderas namnkonventionerna för Azure-resurser](../guidance/guidance-naming-conventions.md).