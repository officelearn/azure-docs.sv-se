---
title: Metodtips för mallar
description: Beskriver rekommenderade metoder för att skapa Azure Resource Manager-mallar. Innehåller förslag för att undvika vanliga problem när du använder mallar.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 870636d6457d842c89f261c2537644c17a335294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156420"
---
# <a name="arm-template-best-practices"></a>Metodtips för ARM-mall

Den här artikeln innehåller rekommendationer om hur du skapar din AZURE Resource Manager-mall (ARM). Dessa rekommendationer hjälper dig att undvika vanliga problem när du använder en ARM-mall för att distribuera en lösning.

Rekommendationer om hur du styr dina Azure-prenumerationer finns i [Azure enterprise scaffold: Prescriptive subscription governance](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Rekommendationer om hur du skapar mallar som fungerar i alla Azure-molnmiljöer finns i [Utveckla Azure Resource Manager-mallar för molnkonsekvens](templates-cloud-consistency.md).

## <a name="template-limits"></a>Mallgränser

Begränsa storleken på mallen till 4 MB och varje parameterfil till 64 kB. Gränsen på 4 MB gäller för mallens slutliga tillstånd när den har utökats med iterativa resursdefinitioner och värden för variabler och parametrar. 

Du är också begränsad till:

* 256 parametrar
* 256 variabler
* 800 resurser (inklusive antal kopior)
* 64 utdatavärden
* 24 576 tecken i ett malluttryck

Du kan överskrida vissa mallgränser med hjälp av en kapslad mall. Mer information finns i [Använda länkade mallar när du distribuerar Azure-resurser](linked-templates.md). Om du vill minska antalet parametrar, variabler eller utdata kan du kombinera flera värden till ett objekt. Mer information finns i [Objekt som parametrar](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

## <a name="resource-group"></a>Resursgrupp

När du distribuerar resurser till en resursgrupp lagrar resursgruppen metadata om resurserna. Metadata lagras på plats för resursgruppen.

Om resursgruppens region inte är tillgänglig för tillfället kan du inte uppdatera resurser i resursgruppen eftersom metadata inte är tillgängliga. Resurserna i andra regioner fungerar fortfarande som förväntat, men du kan inte uppdatera dem. För att minimera risken letar du reda på resursgruppen och resurserna i samma region.

## <a name="parameters"></a>Parametrar

Informationen i det här avsnittet kan vara användbar när du arbetar med [parametrar](template-parameters.md).

### <a name="general-recommendations-for-parameters"></a>Allmänna rekommendationer för parametrar

* Minimera din användning av parametrar. Använd i stället variabler eller litterala värden för egenskaper som inte behöver anges under distributionen.

* Använd kamelfodral för parameternamn.

* Använd parametrar för inställningar som varierar beroende på miljö, till exempel SKU, storlek eller kapacitet.

* Använd parametrar för resursnamn som du vill ange för enkel identifiering.

* Ge en beskrivning av varje parameter i metadata:

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

* Definiera standardvärden för parametrar som inte är känsliga. Genom att ange ett standardvärde är det enklare att distribuera mallen och användarna av mallen ser ett exempel på ett lämpligt värde. Alla standardvärden för en parameter måste vara giltiga för alla användare i standarddistributionskonfigurationen. 
   
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

* Om du vill ange en valfri parameter ska du inte använda en tom sträng som standardvärde. Använd i stället ett litteralt värde eller ett språkuttryck för att konstruera ett värde.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Använd inte en parameter för API-versionen för en resurstyp. Resursegenskaper och resursvärden kan variera beroende på versionsnummer. IntelliSense i en kodredigerare kan inte avgöra rätt schema när API-versionen är inställd på en parameter. I stället hårdkoda API-versionen i mallen.

* Använd `allowedValues` sparsamt. Använd den bara när du måste se till att vissa värden inte ingår i de tillåtna alternativen. Om du `allowedValues` använder för brett kan du blockera giltiga distributioner genom att inte hålla listan uppdaterad.

* När ett parameternamn i mallen matchar en parameter i PowerShell-distributionskommandot löser Resource Manager den här namngivningskonflikten genom att lägga till **postfixet FromTemplate** i mallparametern. Om du till exempel inkluderar en parameter med namnet **ResourceGroupName** i mallen står det i konflikt med parametern **ResourceGroupName** i cmdleten [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Under distributionen uppmanas du att ange ett värde för **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Säkerhetsrekommendationer för parametrar

* Använd alltid parametrar för användarnamn och lösenord (eller hemligheter).

* Använd `securestring` för alla lösenord och hemligheter. Om du skickar känsliga data i ett `secureObject` JSON-objekt använder du typen. Mallparametrar med säker sträng eller säkra objekttyper kan inte läsas efter resursdistributionen. 
   
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

* Ange inte standardvärden för användarnamn, lösenord eller något värde `secureString` som kräver en typ.

* Ange inte standardvärden för egenskaper som ökar programmets angreppsyta.

### <a name="location-recommendations-for-parameters"></a>Platsrekommendationer för parametrar

* Använd en parameter för att ange plats för `resourceGroup().location`resurser och ange standardvärdet till . Om du anger en platsparameter kan användare av mallen ange en plats som de har behörighet att distribuera till.

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

* Ange inte `allowedValues` för platsparametern. De platser du anger kanske inte är tillgängliga i alla moln.

* Använd platsparametervärdet för resurser som sannolikt finns på samma plats. Den här metoden minimerar antalet gånger användare uppmanas att ange platsinformation.

* För resurser som inte är tillgängliga på alla platser använder du en separat parameter eller anger ett litteralt platsvärde.

## <a name="variables"></a>Variabler

Följande information kan vara användbar när du arbetar med [variabler:](template-variables.md)

* Använd kamelfodral för variabelnamn.

* Använd variabler för värden som du behöver använda mer än en gång i en mall. Om ett värde bara används en gång gör ett hårt kodat värde mallen lättare att läsa.

* Använd variabler för värden som du skapar från ett komplext arrangemang av mallfunktioner. Mallen är lättare att läsa när det komplexa uttrycket bara visas i variabler.

* Använd inte variabler `apiVersion` för på en resurs. API-versionen bestämmer resursens schema. Ofta kan du inte ändra versionen utan att ändra egenskaperna för resursen.

* Du kan inte använda [referensfunktionen](template-functions-resource.md#reference) i **variabelavsnittet** i mallen. **Referensfunktionen** härleder sitt värde från resursens körningstillstånd. Variabler matchas dock under den första tolkningen av mallen. Konstruera värden som behöver **referensfunktionen** direkt i avsnittet **resurser** eller **utdata** i mallen.

* Inkludera variabler för resursnamn som måste vara unika.

* Använd en [kopieringsloop i variabler](copy-variables.md) för att skapa ett upprepat mönster av JSON-objekt.

* Ta bort oanvända variabler.

## <a name="resource-dependencies"></a>Resursberoenden

När du bestämmer vilka [beroenden](define-resource-dependency.md) som ska ställas in använder du följande riktlinjer:

* Använd **referensfunktionen** och skicka resursnamnet för att ange ett implicit beroende mellan resurser som behöver dela en egenskap. Lägg inte till `dependsOn` ett explicit element när du redan har definierat ett implicit beroende. Detta tillvägagångssätt minskar risken för onödiga beroenden.

* Ange en underordnad resurs som beroende av den överordnade resursen.

* Resurser med [villkorselementet](conditional-resource-deployment.md) inställt på false tas automatiskt bort från beroendeordningen. Ange beroenden som om resursen alltid distribueras.

* Låt beroenden kaskad utan att ange dem uttryckligen. Den virtuella datorn är till exempel beroende av ett virtuellt nätverksgränssnitt och det virtuella nätverksgränssnittet är beroende av ett virtuellt nätverk och offentliga IP-adresser. Därför distribueras den virtuella datorn efter alla tre resurserna, men ange inte uttryckligen den virtuella datorn som beroende av alla tre resurserna. Den här metoden förtydligar beroendeordningen och gör det enklare att ändra mallen senare.

* Om ett värde kan fastställas före distributionen kan du prova att distribuera resursen utan beroende. Om ett konfigurationsvärde till exempel behöver namnet på en annan resurs kanske du inte behöver ett beroende. Den här vägledningen fungerar inte alltid eftersom vissa resurser verifierar att det finns en annan resurs. Om du får ett felmeddelande lägger du till ett beroende.

## <a name="resources"></a>Resurser

Följande information kan vara användbar när du arbetar med [resurser:](template-syntax.md#resources)

* Om du vill hjälpa andra deltagare att förstå syftet med resursen anger du **kommentarer** för varje resurs i mallen:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Om du använder en *offentlig slutpunkt* i mallen (till exempel en offentlig slutpunkt för Azure Blob *Storage) ska du inte hårdkoda* namnområdet. Använd **referensfunktionen** för att dynamiskt hämta namnområdet. Du kan använda den här metoden för att distribuera mallen till olika offentliga namnområdesmiljöer utan att manuellt ändra slutpunkten i mallen. Ange API-versionen till samma version som du använder för lagringskontot i mallen:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   Om lagringskontot distribueras i samma mall som du skapar och namnet på lagringskontot inte delas med en annan resurs i mallen behöver du inte ange leverantörens namnområde eller apiVersion när du refererar till resursen. I följande exempel visas den förenklade syntaxen:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   Du kan också referera till ett befintligt lagringskonto som finns i en annan resursgrupp:

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* Tilldela offentliga IP-adresser till en virtuell dator endast när ett program kräver det. Om du vill ansluta till en virtuell dator (VM) för felsökning, eller för hantering eller administrativa ändamål, använder du inkommande NAT-regler, en virtuell nätverksgateway eller en jumpbox.
   
     Mer information om hur du ansluter till virtuella datorer finns i:
   
   * [Köra virtuella datorer för en N-nivåarkitektur i Azure](../../guidance/guidance-compute-n-tier-vm.md)
   * [Konfigurera WinRM-åtkomst för virtuella datorer i Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Tillåt extern åtkomst till den virtuella datorn med hjälp av Azure-portalen](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Tillåt extern åtkomst till den virtuella datorn med powershell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Tillåt extern åtkomst till din virtuella Linux-dator med hjälp av Azure CLI](../../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* Egenskapen **domainNameLabel** för offentliga IP-adresser måste vara unik. **Värdet domainNameLabel** måste vara mellan 3 och 63 tecken långt och följa `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`de regler som anges i det här reguljära uttrycket: . Eftersom funktionen **uniqueString** genererar en sträng som är 13 tecken lång, är **parametern dnsPrefixString** begränsad till 50 tecken:

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

* När du lägger till ett lösenord i ett anpassat skripttillägg använder du egenskapen **commandToExecute** i egenskapen **protectedSettings:**
   
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
   > Om du vill vara säkra på att hemligheter krypteras när de skickas som parametrar till virtuella datorer och tillägg använder du egenskapen **protectedSettings** för de relevanta tilläggen.
   > 
   > 

## <a name="next-steps"></a>Nästa steg

* Information om mallfilens struktur finns i [Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Rekommendationer om hur du skapar mallar som fungerar i alla Azure-molnmiljöer finns i [Utveckla ARM-mallar för molnkonsekvens](templates-cloud-consistency.md).
