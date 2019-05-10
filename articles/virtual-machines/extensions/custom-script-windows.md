---
title: Azure anpassade skripttillägg för Windows | Microsoft Docs
description: Automatisera åtgärder för Windows-VM-konfigurationen genom att använda tillägget för anpassat skript
services: virtual-machines-windows
manager: carmonm
author: georgewallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: gwallace
ms.openlocfilehash: b71ba69bcf4965ea607e097c392573e77aab6865
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408281"
---
# <a name="custom-script-extension-for-windows"></a>Tillägget för anpassat skript för Windows

Det anpassade Skripttillägget laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, Programvaruinstallation, eller några andra konfiguration eller hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Det anpassade Skripttillägget integreras med Azure Resource Manager-mallar och kan köras med Azure CLI, PowerShell, Azure-portalen eller REST-API i Azure VM.

Det här dokumentet beskriver hur du använder det anpassade Skripttillägget med Azure PowerShell-modulen, Azure Resource Manager-mallar och information om felsökning i Windows-System.

## <a name="prerequisites"></a>Nödvändiga komponenter

> [!NOTE]  
> Använd inte tillägget för anpassat skript för att köra Update-AzVM med samma virtuella dator som dess parameter, eftersom det väntar på sig självt.  

### <a name="operating-system"></a>Operativsystem

Anpassade skript-tillägget för Windows körs på tillägget tillägget stöds OSs, mer information, se den här [operativsystem som stöds av Azure-tillägget](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Skriptplats

Du kan konfigurera tillägget om du vill använda din Azure Blob storage-autentiseringsuppgifter för att få åtkomst till Azure Blob storage. Skriptets placering kan vara var som helst, så länge som den virtuella datorn kan vidarebefordra till den slutpunkten, t.ex GitHub eller en intern filserver.

### <a name="internet-connectivity"></a>Internetanslutning

Om du behöver hämta ett skript som externt till exempel från GitHub eller Azure Storage sedan ytterligare brandvägg och Nätverkssäkerhetsgruppen portar måste öppnas. Om skriptet finns i Azure Storage kan du till exempel tillåter åtkomst med hjälp av Azure NSG-Tjänsttaggar för [Storage](../../virtual-network/security-overview.md#service-tags).

Om skriptet finns på en lokal server, måste du kanske fortfarande ytterligare en brandvägg och Nätverkssäkerhetsgruppen portar måste du öppna.

### <a name="tips-and-tricks"></a>Tips

* Högsta Felfrekvensen för det här tillägget är på grund av ett syntaxfel i skriptet test som skriptet körs utan fel, och även placera i ytterligare loggning till skriptet att göra det enklare att hitta där det misslyckades.
* Skriva skript som är idempotenta. Detta säkerställer att om de kör igen av misstag kan den inte orsakar ändringarna.
* Se till att skripten inte kräver indata från användaren när de körs.
* Skripten kan köra i 90 minuter. Längre körningar gör att etableringen av tillägget misslyckas.
* Lägg inte in omstarter i skriptet eftersom det leder till problem med andra tillägg som installeras. Tillägget fortsätter inte efter omstarten.
* Om du har ett skript som orsakar en omstart, sedan installera program och köra skript kan du schemalägga omstart med hjälp av en schemalagd uppgift i Windows eller Använd verktyg som DSC, Chef eller Puppet-tillägg.
* Tillägget kör bara ett skript en gång. Om du vill köra ett skript vid varje start måste du använda tillägget för att skapa en schemalagd uppgift i Windows.
* Om du vill schemalägga när ett skript ska köras använder du tillägget för att skapa en schemalagd uppgift i Windows.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du behöver mer frekventa statusuppdateringar för ett skript som körs måste du skapa en egen lösning.
* Anpassat skripttillägg internt stöder inte proxy-servrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet som *Curl*
* Om dina skript eller kommandon använder andra katalogplatser än standardplatserna krävs logik som kan hantera den situationen.

## <a name="extension-schema"></a>Tilläggsschema

Tillägget för anpassat skript konfigurationen anger till exempel skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange det på kommandoraden eller anger den i en Azure Resource Manager-mall.

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras bara på den virtuella datorn. Konfigurationen av skyddade är användbart när körningskommandot innefattar hemligheter som lösenord.

De här objekten ska behandlas som känsliga data och anges i den skyddade Konfigurationsinställningen för tillägg. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn.

```json
{
    "apiVersion": "2018-06-01",
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
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey"
        }
    }
}
```

> [!NOTE]
> Endast en version av ett tillägg kan installeras på en virtuell dator vid en tidpunkt i tid, ange anpassat skript två gånger i samma Resource Manager-mallen för samma virtuella dator misslyckas.

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.Compute | string |
| typ | CustomScriptExtension | string |
| typeHandlerVersion | 1.9 | int |
| fileUris (t.ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matris |
| tidsstämpel (t.ex.) | 123456789 | 32-bitars heltal |
| commandToExecute (t.ex.) | PowerShell - ExecutionPolicy obegränsad - filen konfigurera-musik-app.ps1 | string |
| storageAccountName (t.ex.) | examplestorageacct | string |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Undvik distributionsproblem genom att använda namnen som visas här.

#### <a name="property-value-details"></a>Information om egenskapen

* `commandToExecute`: (**krävs**, string) startpunktsskriptet att köra. Använd det här fältet i stället om kommandot innehåller hemligheter som lösenord, eller din fileUris är känsliga.
* `fileUris`: (valfritt, Strängmatrisen) URL: er för filer som ska hämtas.
* `timestamp` (valfritt, 32-bitars heltal) Använd det här fältet bara för att utlösa en kör av skriptet genom att ändra värdet för det här fältet.  Alla heltalsvärde är godtagbara. Det får endast vara skiljer sig från det tidigare värdet.
* `storageAccountName`: (valfritt, string) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagringskontot, alla `fileUris` måste vara URL: er för Azure-BLOB.
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot

Följande värden kan anges i antingen offentliga eller skyddade inställningar, tillägget kommer att avslå alla konfigurationer där värdena nedan anges i inställningarna för både offentliga och skyddade.

* `commandToExecute`

Med offentliga inställningar som kan vara användbart för felsökning, men det rekommenderas att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Skyddade inställningarna är krypterat med en nyckel som bara du känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, det vill säga om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera krypterade värden lagras på den virtuella datorn, och används för att dekryptera inställningar (vid behov) vid körning.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema, som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under distributionen. Följande exempel visar hur du använder tillägget för anpassat skript:

* [Självstudie: Distribuera virtual machine-tillägg med Azure Resource Manager-mallar](../../azure-resource-manager/resource-manager-tutorial-deploy-vm-extensions.md)
* [Distribuera två Nivåprogram på Windows och Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-distribution

Den `Set-AzVMCustomScriptExtension` kommando kan användas för att lägga till tillägget för anpassat skript i en befintlig virtuell dator. Mer information finns i [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Ytterligare exempel

### <a name="using-multiple-scripts"></a>Använda flera skript

I det här exemplet har du tre skript som används för att skapa din server. Den **commandToExecute** anropar det första skriptet och sedan har du alternativ på hur de andra anropas. Du kan till exempel har ett huvud skript som styr körningen, med rätt felhantering, loggning och tillståndshantering. Skript som laddas ned till den lokala datorn för att köra. Till exempel i `1_Add_Tools.ps1` du anropa `2_Add_Features.ps1` genom att lägga till `.\2_Add_Features.ps1` till skript och upprepa processen för de andra skript du definierar i `$settings`.

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Köra skript från en lokal resurs

I det här exemplet kan du använda en lokal SMB-server för din skriptplats. Genom att göra detta behöver du inte ange eventuella andra inställningar utom **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.9" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Så här kör du anpassat skript för mer än en gång med CLI

Om du vill köra det anpassade skripttillägget mer än en gång kan du bara utföra den här åtgärden under dessa förhållanden:

* Tillägget **namn** parametern är samma som föregående distributionen av tillägget.
* Uppdatera konfigurationen annars kommandot inte köras igen. Du kan lägga till i en dynamisk egenskap i kommandot, till exempel en tidsstämpel.

Du kan också ange den [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) egenskap **SANT**.

### <a name="using-invoke-webrequest"></a>Med hjälp av Invoke-WebRequest

Om du använder [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) i skriptet, måste du ange parametern `-UseBasicParsing` eller annan du får följande fel när kontrollerar detaljerad status:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```

## <a name="classic-vms"></a>Klassiska virtuella datorer

Du kan använda Azure portal eller den klassiska Azure PowerShell-cmdlets för att distribuera det anpassade Skripttillägget på klassiska virtuella datorer.

### <a name="azure-portal"></a>Azure Portal

Gå till din klassiska VM-resurs. Välj **tillägg** under **inställningar**.

Klicka på **+ Lägg till** och i listan över resurser väljer **tillägget för anpassat skript**.

På den **installera tillägget** väljer du den lokala PowerShell-filen och fylla i några argument och klicka på **Ok**.

### <a name="powershell"></a>PowerShell

Använd den [Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) cmdlet kan användas för att lägga till tillägget för anpassat skript i en befintlig virtuell dator.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure PowerShell-modulen. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Tillägget utdata loggas till filer som finns i följande mapp på den virtuella måldatorn.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De angivna filerna laddas ned till följande mapp på den virtuella måldatorn.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

där `<n>` är ett heltal som kan ändras mellan körningar av tillägget.  Den `1.*` värde matchar det faktiska, aktuellt `typeHandlerVersion` värdet för tillägget.  Den faktiska katalogen kan till exempel vara `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`.  

När du kör den `commandToExecute` kommandot tillägget anger den här katalogen (till exempel `...\Downloads\2`) som den aktuella arbetskatalogen. Den här processen kan du använda relativa sökvägar för att hitta filer som hämtas den `fileURIs` egenskapen. Se tabellen nedan exempel.

Eftersom de absolut sökvägen kan variera över tid, är det bättre att välja relativa skriptfilen sökvägar i den `commandToExecute` string, när det är möjligt. Exempel:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Information om sökvägen när det första segmentet URI sparas för filer som hämtas den `fileUris` egenskapslistan.  I tabellen nedan visas nedladdade filer ska mappas till download underkataloger att återspegla strukturen för de `fileUris` värden.  

#### <a name="examples-of-downloaded-files"></a>Exempel på hämtade filer

| URI: N i fileUris | Relativ hämtade plats | Absoluta laddade ned <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> de absoluta sökvägarna ändras under livslängden för den virtuella datorn, men inte i en enda körning av CustomScript-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Du kan även skicka en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
