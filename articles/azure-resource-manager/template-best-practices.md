---
title: Metodtips för Azure Resource Manager-mallar
description: Beskriver rekommenderade metoder för att skapa Azure Resource Manager-mallar. Olika sätt att undvika vanliga problem när du använder mallar.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: tomfitz
ms.openlocfilehash: 9b136c73afc08e05694aed99d57139f77466788d
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490389"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure Resource Manager mall Metodtips

Den här artikeln ger rekommendationer om hur du skapar Resource Manager-mall. De här rekommendationerna hjälpa dig att undvika vanliga problem när du använder en mall för att distribuera en lösning.

Rekommendationer om hur du styr dina Azure-prenumerationer finns i [Azure enterprise kodskelett: Förebyggande Prenumerationsåtgärder](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Rekommendationer om hur du skapar mallar som fungerar i alla Azure-molnmiljöer finns i [utveckla Azure Resource Manager-mallar för molnet konsekvens](templates-cloud-consistency.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="parameters"></a>Parametrar
Informationen i det här avsnittet kan vara användbart när du arbetar med [parametrar](resource-manager-templates-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Allmänna rekommendationer för parametrar

* Minimera din användning av parametrar. Använd istället variabler eller litterala värden för egenskaper som inte behöver anges under distributionen.

* Använd kamelnotation för parameternamn.

* Använda parametrar för inställningar som varierar beroende på miljön, som SKU: N, storlek och kapacitet.

* Använda parametrar för resursnamn som du vill ange för enkel identifiering.

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

* Definiera standardvärden för parametrar som inte är känsliga. Det är lättare att distribuera mallen genom att ange ett standardvärde, och användarna av mallen se ett exempel på ett lämpligt värde. Alla standardvärdet för en parameter måste gälla för alla användare i standardkonfigurationen för distribution. 
   
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

* Om du vill ange en valfri parameter, Använd inte en tom sträng som standard. Använd istället ett exakt värde eller ett språk-uttryck för att konstruera ett värde.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Använd inte en parameter för API-version för en resurstyp. Egenskaper för resursen och värdena kan variera efter versionsnummer. IntelliSense i en Kodredigerare kan inte fastställa rätt schema när API-versionen anges som en parameter. I stället hårdkoda API-versionen i mallen.

* Använd `allowedValues` sparsamt. Använda den bara när du måste kontrollera vissa värden inte ingår i de tillåtna alternativ. Om du använder `allowedValues` för brett kan du behöva blockera giltig distributioner av inte hålla listan uppdaterad.

* När ett parameternamn i mallen matchar en parameter i kommandot PowerShell-distribution, Resource Manager löser den här namnkonflikten genom att lägga till postfixen **från mall** mall-parameter. Exempel: Om du inkluderar en parameter med namnet **ResourceGroupName** i mallen, den är i konflikt med den **ResourceGroupName** parametern i den [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet. Under distributionen kan du uppmanas att ange ett värde för **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Säkerhetsrekommendationer för parametrar

* Använd alltid parametrar för användarnamn och lösenord (eller hemligheter).

* Använd `securestring` för alla lösenord och hemligheter. Om du skickar känsliga data i JSON-objektet, använder du den `secureObject` typen. Mallparametrar med säker sträng eller säkra objekt av typen kan inte läsas efter resursdistributionen. 
   
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

* Inte ange standardvärden för användarnamn, lösenord eller ett värde som kräver en `secureString` typen.

* Inte ange standardvärdena för egenskaper som ökar angreppsytan av programmet.

### <a name="location-recommendations-for-parameters"></a>Plats rekommendationer för parametrar

* Använd en parameter för att ange plats för resurser och ange standardvärdet till `resourceGroup().location`. Genom att använda en plats-parameter kan användare av mallen att ange en plats som de har behörighet att distribuera till.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* Ange inte `allowedValues` för parametern plats. De platser som du anger kanske inte är tillgängliga i alla moln.

* Använd värdet för parametern plats för resurser som sannolikt kommer att finnas på samma plats. Denna metod minimerar antalet gånger som användare uppmanas att ange platsinformation.

* Använd en separat parameter för resurser som inte är tillgängliga på alla platser, eller ange ett värde för literal plats.

## <a name="variables"></a>Variabler

Följande information kan vara användbart när du arbetar med [variabler](resource-manager-templates-variables.md):

* Använda variabler för värden som du behöver använda mer än en gång i en mall. Om ett värde används bara en gång, ett hårdkodat värde gör din mall lättare att läsa.

* Använda variabler för värden som du skapar från en komplex ordning med Mallfunktioner. Mallen är lättare att läsa när det komplexa uttrycket visas bara i variablerna.

* Använd inte variabler för `apiVersion` på en resurs. API-versionen avgör schemat för resursen. Ofta kan ändra du inte versionen utan att ändra egenskaper för resursen.

* Du kan inte använda den [referens](resource-group-template-functions-resource.md#reference) fungera i den **variabler** avsnitt i mallen. Den **referens** funktion som hämtar sitt värde från resursens runtime-tillståndet. Variabler är dock lösts vid första parsningen av mallen. Konstruktion värden som behöver den **referens** fungera direkt i den **resurser** eller **matar ut** avsnitt i mallen.

* Inkludera variabler för resursnamn som måste vara unika.

* Använd en [kopiera loop i variabler](resource-group-create-multiple.md#variable-iteration) att skapa ett upprepade mönster av JSON-objekt.

* Ta bort oanvända variabler.

## <a name="resource-dependencies"></a>Resursberoenden

När du bestämmer vilka [beroenden](resource-group-define-dependencies.md) för att ange, Använd följande riktlinjer:

* Använd den **referens** fungerar och ange resursnamnet att ställa in en implicit beroendet mellan resurser som behöver dela en egenskap. Lägg inte till en explicit `dependsOn` elementet när du redan har definierat beroende av ett implicit. Den här metoden minskar risken för onödig beroenden.

* Ange en underordnad resurs som är beroende av dess överordnade.

* Resurser med den [elementet](resource-manager-templates-resources.md#condition) inställt på false tas automatiskt bort från beroendeordningen. Ange beroenden som om resursen distribueras alltid.

* Låt beroenden cascade utan att ange dem explicit. Till exempel den virtuella datorn är beroende av ett virtuellt nätverksgränssnitt och virtuella nätverksgränssnittet är beroende av ett virtuellt nätverk och offentliga IP-adresser. Därför kan den virtuella datorn är distribuerad när alla tre resurser, men uttryckligen ange inte den virtuella datorn som beroende på alla tre resurser. Den här metoden visar beroendeordningen och gör det lättare att ändra mallen senare.

* Om ett värde kan fastställas före distributionen, försök att distribuera resursen utan koppling. Om ett konfigurationsvärde måste namnet på en annan resurs, kan du inte behöver ett beroende. Den här vägledningen fungerar inte alltid eftersom vissa resurser verifiera att den andra resursen. Om du får ett fel, lägger du till ett beroende.

## <a name="resources"></a>Resurser

Följande information kan vara användbart när du arbetar med [resurser](resource-manager-templates-resources.md):

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

* Om du använder en *offentlig slutpunkt* i mallen (till exempel ett Azure Blob storage offentlig slutpunkt), *inte hårdkoda* namnområdet. Använd den **referens** funktionen för att dynamiskt hämta namnområdet. Du kan använda den här metoden för att distribuera mallen till olika offentliga namnrymdsmiljöer utan att manuellt ändra slutpunkten i mallen. Ange API-versionen till samma version som du använder för storage-kontot i mallen:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Om lagringskontot har distribuerats i samma mall som du skapar, behöver du inte ange leverantörens namnområde när du refererar till resursen. I följande exempel visas syntaxen förenklade:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Om du har andra värden i mallen som är konfigurerade för att använda en offentlig namnrymd kan ändra dessa värden för att återspegla samma **referens** funktion. Du kan till exempel ange den **storageUri** egenskapen för diagnostikprofilen för virtuell dator:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Du kan också referera till ett befintligt lagringskonto som tillhör en annan resursgrupp:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Tilldela offentliga IP-adresser till en virtuell dator bara när ett program kräver. Använd inkommande NAT-regler, en virtuell nätverksgateway eller en jumpbox när du ansluter till en virtuell dator (VM) för felsökning eller för att hantera administrativa syften.
   
     Mer information om hur du ansluter till virtuella datorer finns:
   
   * [Köra virtuella datorer för en N-tier-arkitektur i Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurera WinRM-åtkomst för virtuella datorer i Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Tillåt extern åtkomst till din Linux-VM med hjälp av Azure CLI](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Den **domainNameLabel** -egenskapen för offentliga IP-adresser måste vara unikt. Den **domainNameLabel** värdet måste vara mellan 3 och 63 tecken långt och följa de regler som anges av den här reguljärt uttryck: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Eftersom den **uniqueString** funktionen genererar en sträng som är 13 tecken lång, den **dnsPrefixString** parametern är begränsad till 50 tecken:

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

* När du lägger till ett lösenord för ett anpassat skripttillägg använder den **commandToExecute** -egenskapen i den **protectedSettings** egenskapen:
   
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
   > För att säkerställa att hemligheter som krypteras när de skickas som parametrar till virtuella datorer och tillägg, använda den **protectedSettings** egenskapen för de relevanta tillägg.
   > 
   > 

## <a name="outputs"></a>Utdata

Om du använder en mall för att skapa offentliga IP-adresser kan innehålla en [matar ut avsnittet](resource-manager-templates-outputs.md) som returnerar information om IP-adressen och det fullständigt kvalificerade domännamnet (FQDN). Du kan använda utdatavärden för att enkelt hämta information om offentliga IP-adresser och FQDN efter distributionen.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Nästa steg

* Information om strukturen i Resource Manager-mallfilen finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Rekommendationer om hur du skapar mallar som fungerar i alla Azure-molnmiljöer finns i [utveckla Azure Resource Manager-mallar för molnet konsekvens](templates-cloud-consistency.md).
