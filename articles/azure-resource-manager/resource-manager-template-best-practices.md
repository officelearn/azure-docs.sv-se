---
title: "Bästa praxis för att skapa Resource Manager-mallar | Microsoft Docs"
description: "Riktlinjer för att förenkla Azure Resource Manager-mallar."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 31b10deb-0183-47ce-a5ba-6d0ff2ae8ab3
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: tomfitz
ms.openlocfilehash: a23301ba88279af3f7bf4d353ae808e9eeb0900d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-azure-resource-manager-templates"></a>Metodtips för att skapa Azure Resource Manager-mallar
Dessa riktlinjer kan hjälpa dig att skapa Azure Resource Manager-mallar som är tillförlitliga och enkla att använda. Riktlinjerna är bara förslag. De är inte krav, om inget annat anges. Ditt scenario kan kräva en variant av en av följande metoder eller exempel.

## <a name="resource-names"></a>Resursnamn
I allmänhet kan arbeta du med tre typer av resursnamn i Resource Manager:

* Resursnamn som måste vara unika.
* Resursnamn som inte behöver vara unika, men välja att ange ett namn som hjälper dig att identifiera en resurs baserat på kontext.
* Resursnamn som kan vara generiska.

 Information om begränsningar för resursen finns [rekommenderas namnkonventionerna för Azure-resurser](../guidance/guidance-naming-conventions.md).

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

Om du anger en parameter för en resurs måste ange du ett unikt namn när du distribuerar resursen. Du kan skapa en variabel som använder den [uniqueString()](resource-group-template-functions-string.md#uniquestring) funktion för att generera ett namn. 

Du kan också lägga till ett prefix eller suffix till den **uniqueString** resultat. Ändra det unika namnet hjälper dig att enkelt identifiera resurstypen från namn. Du kan till exempel generera ett unikt namn för ett lagringskonto med hjälp av följande variabel:

```json
"variables": {
    "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

### <a name="resource-names-for-identification"></a>Resursnamn för identifiering
Vissa typer av resurser som du kanske vill namn, men deras namn behöver inte vara unika. Du kan ange ett namn som identifierar både resurs-kontexten och resurstypen för dessa typer av resurser. Ange ett beskrivande namn som hjälper dig att identifiera resursen i en lista över resurser. Om du behöver använda ett annat resursnamn för olika distributioner kan du använda en parameter för namnet:

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

Om du inte behöver ange ett namn under distributionen kan du använda en variabel: 

```json
"variables": {
    "vmName": "demoLinuxVM"
}
```

Du kan också använda ett hårdkodat värde:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "demoLinuxVM",
  ...
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

## <a name="parameters"></a>Parametrar
Följande information kan vara användbart när du arbetar med parametrar:

* Minimera din användning av parametrar. Använd om möjligt en variabel eller ett litteralvärde. Använd parametrarna endast för dessa scenarier:
   
   * Inställningar som du vill använda variationer av enligt miljö (SKU, storlek, kapacitet).
   * Resursnamn som du vill ange för enkel identifiering.
   * Värden som du använder ofta för att utföra andra åtgärder (till exempel en administratörsanvändarnamnet).
   * Hemligheter (till exempel lösenord).
   * Det antal eller matris med-värden ska användas när du skapar flera instanser av en resurstyp.
* Slitbanor användningsfall för parameternamn.
* Ange en beskrivning av varje parameter i metadata:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Ange standardvärden för parametrar (förutom för lösenord och SSH-nycklar):
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Använd **SecureString** för alla lösenord och hemligheter: 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* Använd inte en parameter anger platsen när det är möjligt. Använd i stället de **plats** -egenskapen för resursgruppen. Med hjälp av den **resourceGroup () .location** uttryck för alla dina resurser, resurserna i mallen distribueras på samma plats som resursgruppen:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         ...
     }
   ]
   ```
   
   Om en resurstyp stöds i endast ett begränsat antal platser, kanske du vill ange en giltig plats direkt i mallen. Om du måste använda en **plats** parameter, dela att parametervärdet så mycket som möjligt med resurser som kan förväntas finnas på samma plats. Detta minskar antalet gånger som användare uppmanas att ange platsinformation.
* Undvik att använda en parameter eller variabel för API-version för en resurstyp. Egenskaper för resursen och värdena kan variera efter versionsnummer. IntelliSense i en redigerare kan inte avgöra korrekt schema när API-version anges som en parameter eller variabel. I stället hårdkoda API-version i mallen.

## <a name="variables"></a>Variabler
Följande information kan vara användbart när du arbetar med variabler:

* Använda variabler för värden som du behöver använda mer än en gång i en mall. Om ett värde används bara en gång, ett hårdkodat värde gör mallen lättare att läsa.
* Du kan inte använda den [referens](resource-group-template-functions-resource.md#reference) fungera i den **variabler** avsnitt i mallen. Den **referens** funktionen hämtar sitt värde från resursens runtime-tillståndet. Variabler är dock lösts vid första parsningen av mallen. Konstruktion värden som måste den **referens** fungera direkt i den **resurser** eller **matar ut** avsnitt i mallen.
* Variabler för resursnamn som måste vara unika, enligt beskrivningen i [resursnamn](#resource-names).
* Du kan gruppera variabler i komplexa objekt. Använd den **variable.subentry** format för att referera till ett värde från ett komplext objekt. Gruppera variabler kan hjälpa dig att spåra relaterade variabler. Det förbättrar också läsbarhet för mallen. Här är ett exempel:
   
   ```json
   "variables": {
       "storage": {
           "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
           "type": "Standard_LRS"
       }
   },
   "resources": [
     {
         "type": "Microsoft.Storage/storageAccounts",
         "name": "[variables('storage').name]",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "sku": {
             "name": "[variables('storage').type]"
         },
         ...
     }
   ]
   ```
   
   > [!NOTE]
   > Ett komplext objekt får inte innehålla ett uttryck som refererar till ett värde från ett komplext objekt. Definiera en separat variabel för detta ändamål.
   > 
   > 
   
     Avancerade exempel på hur komplexa objekt som variabler finns i [dela tillstånd i Azure Resource Manager-mallar](best-practices-resource-manager-state.md).

## <a name="resources"></a>Resurser
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

* Du kan använda taggar för att lägga till metadata till resurser. Använda metadata för att lägga till information om dina resurser. Du kan till exempel lägga till metadata för att registrera faktureringsinformation för en resurs. Mer information finns i [med taggar för att organisera dina Azure-resurser](resource-group-using-tags.md).
* Om du använder en *offentlig slutpunkt* i mallen (till exempel Azure Blob storage offentlig slutpunkt), *gör inte hårdkoda* namnområdet. Använd den **referens** funktion för att dynamiskt hämta namnområdet. Du kan använda den här metoden för att distribuera mallen till olika namnområdes-offentliga miljöer utan att manuellt ändra slutpunkten i mallen. Ange API-versionen till samma version som du använder för lagringskontot i mallen:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Om lagringskontot distribueras i samma mall som du skapar, behöver du inte ange leverantörens namnrymd när du refererar till resursen. Detta är förenklad syntax:
   
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

## <a name="outputs"></a>utdata
Om du använder en mall för att skapa offentliga IP-adresser, inkludera en **matar ut** avsnitt som returnerar information om IP-adressen och det fullständigt kvalificerade domännamnet (FQDN). Du kan använda utdatavärden för att lätt kunna hämta information om offentlig IP-adresser och FQDN efter distributionen. När du refererar till resursen, kan du använda API-version som du använde för att skapa den: 

```json
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## <a name="single-template-vs-nested-templates"></a>Samma mall kontra kapslade mallar
För att distribuera lösningen måste använda du en mall eller en Huvudmall med flera kapslade mallar. Kapslade mallar är gemensamma för mer avancerade scenarier. Med hjälp av en kapslad mall ger följande fördelar:

* Du kan dela upp en lösning i riktade komponenter.
* Du kan återanvända kapslade mallar med olika huvudsakliga mallar.

Om du väljer att använda kapslade mallar kan följande riktlinjer hjälpa dig standardisera utformningen mallen. Dessa riktlinjer är baserade på [mönster för att utforma Azure Resource Manager-mallar](best-practices-resource-manager-design-templates.md). Vi rekommenderar en design som har följande mallar:

* **Huvudmall** (azuredeploy.json). Använd för indataparametrarna.
* **Delade resurser mallen**. Använda för att distribuera delade resurser med andra resurser (till exempel virtuella nätverk och tillgänglighet uppsättningar). Använd den **dependsOn** uttrycket så att den här mallen distribueras innan andra mallar.
* **Valfria resurser mallen**. Använda för att distribuera villkorligt resurser baserat på en parameter (till exempel jumpbox).
* **Medlemmen resurser mallen**. Varje instans i en program-nivå har sin egen konfiguration. Du kan definiera olika instans typer i en nivå. (Till exempel om den första instansen skapar ett kluster och ytterligare instanser läggs till det befintliga klustret.) Varje instans har sin egen Distributionsmall.
* **Skript**. Allmänt återanvändbara skript kan användas för varje instanstyp (till exempel initiera och formatera ytterligare diskar). Anpassade skript som du skapar för en viss anpassning syfte är olika, baserat på typen instans.

![Kapslad mall](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

Mer information finns i [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).

## <a name="conditionally-link-to-nested-templates"></a>Villkorligt länk till kapslade mallar
Du kan använda en parameter för att länka villkorligt till kapslade mallar. Parametern blir en del av URI: N för mallen:

```json
"parameters": {
    "newOrExisting": {
        "type": "String",
        "allowedValues": [
            "new",
            "existing"
        ]
    }
},
"variables": {
    "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
},
"resources": [
    {
        "apiVersion": "2015-01-01",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "[variables('templatelink')]",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
            }
        }
    }
]
```

## <a name="template-format"></a>Mallformat
Det är en bra idé att skicka din mall med en JSON-verifieraren. Med hjälp av en systemhälsoverifierare kan du ta bort överflödig kommatecken, parenteser och hakparenteser som kan orsaka fel under distributionen. Försök [JSONLint](http://jsonlint.com/) eller ett linter paket för din favorit redigerar miljö (Visual Studio Code, Atom, Sublime Text, Visual Studio).

Det är också en bra idé att formatera din JSON för bättre läsbarhet. Du kan använda en JSON-Formateraren paketet för din lokala redigeraren. I Visual Studio för att formatera dokumentet trycker du på **Ctrl + K, Ctrl + D**. I Visual Studio-koden trycker du på **Alt + Skift + F**. Om din lokala redigeraren inte formateras dokumentet, kan du använda en [online formaterare](https://www.bing.com/search?q=json+formatter).

## <a name="next-steps"></a>Nästa steg
* Anvisningar om att utforma din lösning för virtuella datorer, se [kör en virtuell Windows-dator i Azure](../guidance/guidance-compute-single-vm.md) och [kör ett Linux-VM i Azure](../guidance/guidance-compute-single-vm-linux.md).
* Anvisningar om hur du skapar ett lagringskonto finns [Azure Storage checklistan för prestanda och skalbarhet](../storage/common/storage-performance-checklist.md).
* Läs om hur företaget kan använda Resource Manager för att effektivt hantera prenumerationer i [Azure enterprise kodskelett: normativ prenumeration styrning](resource-manager-subscription-governance.md).

