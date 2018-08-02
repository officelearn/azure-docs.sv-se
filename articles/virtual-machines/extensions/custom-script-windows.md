---
title: Azure anpassade skripttillägg för Windows | Microsoft Docs
description: Automatisera åtgärder för Windows-VM-konfigurationen genom att använda tillägget för anpassat skript
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
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
ms.author: roiyz
ms.openlocfilehash: 5c105c6adba1a5fca52a85f6d54751e1b54fd721
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412839"
---
# <a name="custom-script-extension-for-windows"></a>Tillägget för anpassat skript för Windows

Det anpassade Skripttillägget laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Det anpassade skripttillägget är integrerat med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller REST API:n för virtuella Azure-datorer.

Det här dokumentet beskriver hur du använder det anpassade Skripttillägget med Azure PowerShell-modulen, Azure Resource Manager-mallar och information om felsökning i Windows-System.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]  
> Använd inte tillägget för anpassat skript för att köra Update-AzureRmVM med samma virtuella dator som dess parameter, eftersom det väntar på sig självt.  
>   
> 

### <a name="operating-system"></a>Operativsystem

Tillägget för anpassat skript för Linux ska köras på tillägget tillägget stöds OS, mer information finns i det här [artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Skriptplats

Du kan använda tillägget för att använda Azure Blob storage-autentiseringsuppgifter för att få åtkomst till Azure Blob storage. Du kan också kan skriptets placering vara någon where, så länge som den virtuella datorn kan vidarebefordra till den slutpunkten, till exempel GitHub, interna filserver osv.


### <a name="internet-connectivity"></a>Internetanslutning
Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage kan sedan ytterligare Brandväggsnätverk säkerhetsgrupp-portar måste du öppna. Till exempel om skriptet finns i Azure Storage, du kan tillåta åtkomst till med hjälp av Azure NSG-Tjänsttaggar för [Storage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Om skriptet finns på en lokal server så kan du fortfarande behöver ytterligare brandvägg/Network Security Group portar måste du öppna.

### <a name="tips-and-tricks"></a>Tips
* Högsta Felfrekvensen för det här tillägget är på grund av ett syntaxfel i skriptet test som skriptet körs utan fel, och även placera i ytterligare loggning till skriptet att göra det enklare att hitta där det misslyckades.
* Skriva skript som är idempotenta, så om de få kör igen mer än en gång av misstag kan den inte orsakar ändringarna.
* Se till att skript som inte kräver indata från användaren när de körs.
* Det är 90 minuter som tillåts för skriptet att köra, något längre resulterar i en misslyckad tillhandahållande av tillägget.
* Placera inte omstarter i skriptet och detta kan orsaka problem med andra tillägg installeras efter omstarten tillägget kommer inte att fortsätta efter omstarten. 
* Om du har ett skript som gör att en omstart kan installera program och kör skript osv. Du bör schemalägga omstarten med en schemalagd uppgift i Windows eller med verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget körs bara ett skript en gång, om du vill köra ett skript på varje start måste du använda tillägget för att skapa en schemalagd uppgift i Windows.
* Om du vill schemalägga när ett skript som körs ska du använda tillägget för att skapa en schemalagd uppgift i Windows. 
* När skriptet körs, visas bara statusen ”transitioning' tillägg från Azure-portalen eller CLI. Om du vill mer frekventa statusuppdateringar ett skript som körs, behöver du skapa en egen lösning.
* Anpassat skripttillägg internt stöder inte proxy-servrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet som *Curl* 
* Tänk på inte är standard directory-platser som dina skript eller kommandon kan förlita sig på, har logik för att hantera det.


## <a name="extension-schema"></a>Tilläggsschema

Tillägget för anpassat skript konfigurationen anger till exempel skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange det på kommandoraden eller anger den i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras bara på den virtuella datorn. Konfigurationen av skyddade är användbart när körningskommandot innefattar hemligheter som lösenord.

De här objekten ska behandlas som känsliga data och anges i den skyddade Konfigurationsinställningen för tillägg. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn.

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
**Obs** -endast en version av ett tillägg kan installeras på en virtuell dator vid en tidpunkt i tid, anpassat skript för att ange två gånger i samma Resource Manager-mallen för samma virtuella dator kommer att misslyckas. 

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| utgivare | Microsoft.Compute | sträng |
| typ | CustomScriptExtension | sträng |
| typeHandlerVersion | 1.9 | int |
| fileUris (t.ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matris |
| commandToExecute (t.ex.) | PowerShell - ExecutionPolicy obegränsad - filen konfigurera-musik-app.ps1 | sträng |
| storageAccountName (t.ex.) | examplestorageacct | sträng |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg == | sträng |

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Undvik distributionsproblem genom att använda namnen som visas här.

#### <a name="property-value-details"></a>Information om egenskapen
 * `commandToExecute`: (**krävs**, string) startpunktsskriptet att köra. Använd det här fältet i stället om kommandot innehåller hemligheter som lösenord, eller din fileUris är känsliga.
* `fileUris`: (valfritt, Strängmatrisen) URL: er för filer som ska hämtas.
* `storageAccountName`: (valfritt, string) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagringskontot, alla `fileUris` måste vara URL: er för Azure-BLOB.
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot

Följande värden kan anges i antingen offentliga eller skyddade inställningar, tillägget kommer att avslå alla konfigurationer där värdena nedan anges i inställningarna för både offentliga och skyddade.
* `commandToExecute`

Med offentliga inställningar som kan vara användbart för felsökning, men det rekommenderas starkt att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Skyddade inställningarna är krypterat med en nyckel som bara du känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, dvs. Om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera krypterade värden lagras på den virtuella datorn, och används för att dekryptera inställningar (vid behov) vid körning.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en malldistribution för Azure Resource Manager. En exempelmall som innehåller det anpassade Skripttillägget finns här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell-distribution

Den `Set-AzureRmVMCustomScriptExtension` kommando kan användas för att lägga till tillägget för anpassat skript i en befintlig virtuell dator. Mer information finns i [Set-AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).
```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location myLocation `
    -FileUri myURL `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```
## <a name="further-examples"></a>Ytterligare exempel

### <a name="using-multiple-script"></a>Med hjälp av flera skript
Du har tre skript som används för att skapa din server, 'commandToExecute'-anrop som det första skriptet har du tillgång alternativ på hur de andra kallas i det här exemplet, du kan till exempel ha ett master-skript som styr körningen, med rätt fel hantering, loggning och tillståndshantering.

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

### <a name="running-scripts-from-a-local-share"></a>Köra skript från en lokal resurs
I det här exemplet kan du använda en lokal SMB-server för din skriptplats Observera att du inte behöver skicka in en annan andra inställningar, utom *commandToExecute*.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Så här kör du anpassat skript för mer än en gång med CLI
Om du vill köra det anpassade skripttillägget mer än en gång kan du bara göra detta under dessa förhållanden:
1. Tillägget ”Name”-parametern är samma som föregående distributionen av tillägget.
2. Du måste uppdatera konfigurationen annars kommandot kommer utförs inte igen, t.ex, du kan lägga till i en dynamisk egenskap i kommandot, till exempel en tidsstämpel. 

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure PowerShell-modulen. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando:

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
där `<n>` är ett heltal som kan ändras mellan körningar av tillägget.  Den `1.*` värde matchar det faktiska, aktuellt `typeHandlerVersion` värdet för tillägget.  Den faktiska katalogen kan till exempel vara `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

När du kör den `commandToExecute` kommandot tillägget anger den här katalogen (t.ex. `...\Downloads\2`) som den aktuella arbetskatalogen. Detta kan du använda relativa sökvägar för att hitta filer som hämtas den `fileURIs` egenskapen. Se tabellen nedan exempel.

Eftersom de absolut sökvägen kan variera över tid, är det bättre att välja relativa skriptfilen sökvägar i den `commandToExecute` string, när det är möjligt. Exempel:
```json
    "commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Information om sökvägen när det första segmentet URI sparas i filer som hämtas den `fileUris` egenskapslistan.  I tabellen nedan visas nedladdade filer ska mappas till download underkataloger att återspegla strukturen för de `fileUris` värden.  

#### <a name="examples-of-downloaded-files"></a>Exempel på hämtade filer

| URI: N i fileUris | Relativ hämtade plats | Absoluta laddade ned * |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

\* Som ändras ovan är de absoluta sökvägarna under livslängden för den virtuella datorn, men inte i en enda körning av CustomScript-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
