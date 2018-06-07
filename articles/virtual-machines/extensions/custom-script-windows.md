---
title: Anpassat skript för Azure-tillägget för Windows | Microsoft Docs
description: Automatisera åtgärder för Windows VM-konfigurationen genom att använda tillägget för anpassat skript
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/24/2018
ms.author: danis
ms.openlocfilehash: 80f9ecd40c5b9504a6554b95bf374046d8253933
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809785"
---
# <a name="custom-script-extension-for-windows"></a>Tillägget för anpassat skript för Windows

Tillägget för anpassat skript hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Det anpassade skripttillägget är integrerat med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller REST API:n för virtuella Azure-datorer.

Det här dokumentet beskriver hur du använder den tillägget för anpassat skript med hjälp av Azure PowerShell-modulen, Azure Resource Manager-mallar och information om felsökning i Windows-System.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]  
> Använd inte tillägget för anpassat skript för att köra Update AzureRmVM med samma virtuella dator som dess parameter, eftersom det väntar på sig själv.  
>   
> 

### <a name="operating-system"></a>Operativsystem

Tillägget för anpassat skript för Linux ska köras på tillägget tillägg stöds OS, mer information finns i det här [artikel](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Skriptets placering

Du kan använda tillägget för att använda Azure Blob storage-autentiseringsuppgifter för att komma åt Azure Blob storage. Du kan också kan skriptets placering vara någon where, förutsatt att den virtuella datorn kan vidarebefordra till den slutpunkten som GitHub, interna filserver osv.


### <a name="internet-connectivity"></a>Internet-anslutning
Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage sedan ytterligare Brandväggsnätverk säkerhetsgruppen portar måste öppnas. Till exempel om skriptet finns i Azure Storage, du kan tillåta åtkomst med hjälp av Azure NSG Service taggar för [lagring](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Om skriptet finns på en lokal server, så du kan behöva ytterligare brandvägg/Network Security Group portar måste öppnas.

### <a name="tips-and-tricks"></a>Tips
* Det högsta antalet misslyckade för det här tillägget är på grund av syntaxfel i skriptet test skriptet körs utan fel, och också för ytterligare loggning i skript för att göra det lättare att hitta där det misslyckades.
* Skriva skript som är idempotent, så om de får köras igen mer än en gång av misstag, inte får den systemändringar.
* Se till att skripten inte kräver indata från användaren när de körs.
* Det finns 90 minuter som tillåts att köra skript, något längre resulterar i en misslyckad tillhandahållande av tillägget.
* Placera inte omstarter i skriptet och detta medför problem med andra filnamnstillägg som installeras efter omstart, tillägget kan inte fortsätta efter omstart. 
* Om du har ett skript som gör att en omstart kan installera program och köra skript osv. Du bör schemalägga omstart med hjälp av Windows schemalagd aktivitet eller använda verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget kommer bara köra ett skript en gång, om du vill köra ett skript på varje start måste du använda tillägget för att skapa en schemalagd aktivitet i Windows.
* Om du vill schemalägga när ett skript ska du använda tillägget för att skapa en schemalagd aktivitet i Windows. 
* När skriptet körs, visas bara en 'transitioning' tillståndets status från Azure-portalen eller CLI. Om du vill oftare statusuppdateringar ett skript som körs, behöver du skapa din egen lösning.
* Tillägget för anpassat skript internt stöder inte proxy-servrar, men du kan använda ett verktyg för dataöverföring som stöder proxyservrar i skriptet som *Curl* 
* Tänk på att icke directory standardplatser som ditt skript eller kommandon kan förlita sig på har logik för att hantera detta.


## <a name="extension-schema"></a>Tilläggsschema

Tillägget för anpassat skript konfigurationen anger sådant som skriptets placering och kommandot ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange på kommandoraden eller ange i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras endast inuti den virtuella datorn. Skyddade konfigurationen är användbar när kommandot körningen innehåller hemligheter, till exempel ett lösenord.

De här objekten ska behandlas som känsliga data och angetts i konfigurationen för skyddade inställningen tillägg. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

```json
{
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.9",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "fileUris": [
                "script location"
            ]
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```
**Obs** -endast en version av ett tillägg kan installeras på en virtuell dator i en punkt i tid, ange anpassat skript för två gånger i samma Resource Manager-mall för samma virtuella dator misslyckas. 

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Publisher | Microsoft.Compute | sträng |
| typ | CustomScriptExtension | sträng |
| typeHandlerVersion | 1.9 | int |
| fileUris (t.ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matris |
| commandToExecute (t.ex.) | PowerShell - ExecutionPolicy obegränsad - filen konfigurera-musik-app.ps1 | sträng |
| storageAccountName (t.ex.) | examplestorageacct | sträng |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg == | sträng |

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Du undviker problem med distribution genom att använda namnen som visas här.

#### <a name="property-value-details"></a>Värdet för egenskapsinformationen
 * `commandToExecute`: (**krävs**, string) att posten punkt skriptet körs. Använd det här fältet i stället om kommandot innehåller hemligheter, till exempel lösenord eller din fileUris är känsliga.
* `fileUris`: (valfritt, Strängmatrisen) URL: er för filer som ska hämtas.
* `storageAccountName`: (valfritt, string) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagring, alla `fileUris` måste vara URL: er för Azure-BLOB.
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot

Följande värden kan anges i inställningarna för offentliga eller skyddade, tillägget avvisar konfiguration där värdena nedan anges i inställningarna för både offentliga och skyddade.
* `commandToExecute`

Med inställningar för offentliga är användbart för felsökning, men rekommenderas starkt att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Inställningar för skyddade krypteras med en nyckel som känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, dvs. Om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn, och används för att dekryptera inställningar (om det behövs) vid körning.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en Azure Resource Manager för malldistribution. En exempelmall som innehåller tillägget för anpassat skript hittar du här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell-distribution

Den `Set-AzureRmVMCustomScriptExtension` kommando kan användas för att lägga till tillägget för anpassat skript till en befintlig virtuell dator. Mer information finns i [Set AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Ytterligare exempel

### <a name="using-multiple-script"></a>Använda flera skript
I det här exemplet är tre skript som används för att skapa servern 'commandToExecute'-anrop som första skriptet och sedan har alternativ för hur de andra kallas, du kan till exempel ha ett master skript som styr körningen med rätt fel hantering, loggning och tillståndshantering av.

```powershell

$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$Settings = @{"fileUris" = $fileUri};

$storageaccname = "xxxxxxx"
$storagekey = "1234ABCD"
$ProtectedSettings = @{"storageAccountName" = $storageaccname; "storageAccountKey" = $storagekey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzureRmVMExtension -ResourceGroupName myRG `
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "buildserver1" ` 
    -Publisher "Microsoft.Compute" ` 
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" ` 
    -Settings $Settings ` 
    -ProtectedSettings $ProtectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Kör skript från en lokal resurs
I det här exemplet som du kanske vill använda en lokal SMB-server för skriptet plats, Observera att du inte behöver ange en annan andra inställningar, utom *commandToExecute*.

```powershell
$ProtectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};
 
Set-AzureRmVMExtension -ResourceGroupName myRG 
    -Location myLocation ` 
    -VMName myVM ` 
    -Name "serverUpdate" 
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" ` 
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $ProtectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Hur du kör skriptet mer än en gång med CLI
Om du vill köra tillägget för anpassat skript mer än en gång, kan du bara göra det under dessa förhållanden:
1. Parametern ”namn” tillägget är samma som den tidigare distribueringen av tillägget.
2. Du måste uppdatera konfigurationen annars kommandot kommer inte att köra t.ex, du kan lägga till i en dynamisk egenskap i till kommandot, till exempel en tidsstämpel. 

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure PowerShell-modulen. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Tillägget utförande-utdatan loggas till filer som finns i följande katalog på den virtuella måldatorn.
```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De angivna filerna laddas ned till följande katalog på den virtuella måldatorn.
```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
där `<n>` är ett heltal som kan förändras mellan körningar av tillägget.  Den `1.*` värdet matchar den faktiska, aktuellt `typeHandlerVersion` värdet för tillägget.  Den faktiska katalogen kan till exempel vara `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

När du kör den `commandToExecute` kommandot tillägget anger den här katalogen (t.ex. `...\Downloads\2`) som den aktuella arbetskatalogen. Detta kan du använda relativa sökvägar för filer som hämtas den `fileURIs` egenskapen. Se tabellen nedan som exempel.

Eftersom absoluta sökvägen kan variera över tid, är det bättre att välja relativa skriptfilen sökvägarna i den `commandToExecute` sträng, när det är möjligt. Exempel:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Information om sökvägen efter det första segmentet i URI: N finns kvar för filer som hämtas den `fileUris` egenskapslistan.  I tabellen nedan visas nedladdade filer ska mappas till hämta underkataloger återspeglar strukturen för de `fileUris` värden.  

#### <a name="examples-of-downloaded-files"></a>Exempel på filer som hämtas

| URI i fileUris | Relativa hämtade plats | Absolut ned * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Som ändras ovan, de absoluta sökvägarna under livslängden för den virtuella datorn, men inte i en enda körning av CustomScript-tillägg.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
