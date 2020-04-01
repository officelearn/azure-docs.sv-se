---
title: Azure Custom Script-tillägg för Windows
description: Automatisera konfigurationsuppgifter för Windows VM med tillägget Anpassat skript
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: robreed
ms.openlocfilehash: 2c7cad2dfdcd55073a1cf09d79e5223b666ced5f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478146"
---
# <a name="custom-script-extension-for-windows"></a>Anpassat skripttillägg för Windows

Custom Script Extension hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurations- eller hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Det anpassade skripttillägget integreras med Azure Resource Manager-mallar och kan köras med Azure CLI, PowerShell, Azure-portalen eller AZURE Virtual Machine REST API.

Det här dokumentet beskriver hur du använder det anpassade skripttillägget med Azure PowerShell-modulen, Azure Resource Manager-mallar och informationsfelsökningssteg på Windows-system.

## <a name="prerequisites"></a>Krav

> [!NOTE]  
> Använd inte anpassat skripttillägg för att köra Update-AzVM med samma virtuella dator som parametern, eftersom den väntar på sig själv.  

### <a name="operating-system"></a>Operativsystem

Det anpassade skripttillägget för Windows körs på tilläggstilläggets operativsystem som stöds, för mer information, se de [operativsystem som stöds av Azure Extension](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Skriptplats

Du kan konfigurera tillägget för att använda dina Azure Blob-lagringsbehörighetsbehörighet för att komma åt Azure Blob-lagring. Skriptplatsen kan finnas var som helst, så länge den virtuella datorn kan dirigeras till den slutpunkten, till exempel GitHub eller en intern filserver.

### <a name="internet-connectivity"></a>Internetanslutning

Om du behöver hämta ett skript externt, till exempel från GitHub eller Azure Storage, måste ytterligare brandväggs- och nätverkssäkerhetsgruppportar öppnas. Om skriptet till exempel finns i Azure Storage kan du tillåta åtkomst med Azure NSG-tjänsttaggar för [lagring](../../virtual-network/security-overview.md#service-tags).

Om skriptet finns på en lokal server kan du fortfarande behöva ytterligare brandväggs- och nätverkssäkerhetsgruppsportar måste öppnas.

### <a name="tips-and-tricks"></a>Tips

* Den högsta felfrekvensen för det här tillägget är på grund av syntaxfel i skriptet, testa skriptet körs utan fel och även lägga in ytterligare loggning i skriptet för att göra det lättare att hitta där det misslyckades.
* Skriv skript som är idempotenta. Detta säkerställer att om de körs igen av misstag, kommer det inte att orsaka systemändringar.
* Se till att skripten inte kräver indata från användaren när de körs.
* Skripten kan köra i 90 minuter. Längre körningar gör att etableringen av tillägget misslyckas.
* Lägg inte in omstarter i skriptet eftersom det leder till problem med andra tillägg som installeras. Tillägget fortsätter inte efter omstarten.
* Om du har ett skript som orsakar en omstart och sedan installerar program och kör skript kan du schemalägga omstarten med en schemalagd aktivitet i Windows eller använda verktyg som DSC-, Chef- eller Puppet-tillägg.
* Tillägget kör bara ett skript en gång. Om du vill köra ett skript vid varje start måste du använda tillägget för att skapa en schemalagd uppgift i Windows.
* Om du vill schemalägga när ett skript ska köras använder du tillägget för att skapa en schemalagd uppgift i Windows.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du behöver mer frekventa statusuppdateringar för ett skript som körs måste du skapa en egen lösning.
* Custom Script-tillägget stöder inte proxyservrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet, till exempel *Curl*
* Om dina skript eller kommandon använder andra katalogplatser än standardplatserna krävs logik som kan hantera den situationen.
* Anpassat skripttillägg körs under LocalSystem-kontot

## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för anpassat skripttillägg anger saker som skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange den på kommandoraden eller ange den i en Azure Resource Manager-mall.

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och endast dekrypteras inuti den virtuella datorn. Den skyddade konfigurationen är användbar när körningskommandot innehåller hemligheter som ett lösenord.

Dessa objekt ska behandlas som känsliga data och anges i den skyddade inställningskonfigurationen för tillägg. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
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
        "typeHandlerVersion": "1.10",
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
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> egenskapen managedIdentity **får inte** användas tillsammans med egenskaperna storageAccountName eller storageAccountKey

> [!NOTE]
> Det går inte att installera endast en version av ett tillägg på en virtuell dator vid en tidpunkt, och det går inte att ange anpassade skript två gånger i samma Resource Manager-mall för samma virtuella dator.

> [!NOTE]
> Vi kan använda det här schemat i VirtualMachine-resursen eller som en fristående resurs. Namnet på resursen måste vara i det här formatet "virtualMachineName/extensionName", om det här tillägget används som en fristående resurs i ARM-mallen.

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.Compute | sträng |
| typ | Anpassad Beskrivning | sträng |
| typHandlerVersion | 1,10 | int |
| fileUris (t.ex. ) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matris |
| tidsstämpel (t.ex.) | 123456789 | 32-bitars heltal |
| commandToExecute (t.ex.) | powershell -ExecutionPolicy Obegränsad -Fil konfigurera-musik-app.ps1 | sträng |
| storageAccountName (t.ex.) | exempelstoracket | sträng |
| lagringAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sträng |
| hanteradidentitet (t.ex. | { } eller { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } eller { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json-objekt |

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Använd namnen som visas här för att undvika distributionsproblem.

#### <a name="property-value-details"></a>Information om egenskapsvärde

* `commandToExecute`:**(obligatorisk**, sträng) startpunkten skriptet för att köra. Använd det här fältet i stället om kommandot innehåller hemligheter som lösenord eller om filenUris är känsliga.
* `fileUris`: (valfritt, strängmatris) url:erna för filer som ska hämtas.
* `timestamp`(valfritt, 32-bitars heltal) använd det här fältet endast för att utlösa en repris av skriptet genom att ändra värdet för det här fältet.  Alla heltalsvärde är godtagbara. Den får bara vara annorlunda än det tidigare värdet.
* `storageAccountName`: (valfritt, sträng) namnet på lagringskontot. Om du anger lagringsautentiseringsuppgifter måste alla `fileUris` vara URL:er för Azure Blobbar.
* `storageAccountKey`: (valfritt, sträng) åtkomstnyckeln för lagringskontot
* `managedIdentity`: (valfritt, json-objekt) den [hanterade identiteten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för nedladdning av filer
  * `clientId`: (valfritt, sträng) klient-ID för den hanterade identiteten
  * `objectId`: (valfritt, sträng) objekt-ID för den hanterade identiteten

Följande värden kan anges i offentliga eller skyddade inställningar, tillägget avvisar alla konfigurationer där värdena nedan anges i både offentliga och skyddade inställningar.

* `commandToExecute`

Använda offentliga inställningar kanske användbart för felsökning, men vi rekommenderar att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella datorn där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som bara är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, det vill än om inställningarna krypterades de sparas krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (om det behövs) vid körning.

####  <a name="property-managedidentity"></a>Fastighetsförmedling: hanteradIdentity
> [!NOTE]
> Den här egenskapen **får** endast anges i skyddade inställningar.

CustomScript (version 1.10 och framåt) stöder [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för nedladdning av filer från webbadresser som anges i inställningen "fileUris". Det gör att CustomScript kan komma åt privata azure storage-blobbar eller behållare utan att användaren behöver skicka hemligheter som SAS-token eller lagringskontonycklar.

Om du vill använda den här funktionen måste användaren lägga till en [systemtilldelad](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) eller [användartilldelad](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identitet till den virtuella datorn eller VMSS där CustomScript förväntas köras och [bevilja den hanterade identitetsåtkomsten till Azure Storage-behållaren eller bloben](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Om du vill använda den systemtilldelade identiteten på mål-VM/VMSS anger du fältet "managedidentity" till ett tomt json-objekt. 

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Om du vill använda den användartilldelade identiteten på mål-VM/VMSS konfigurerar du fältet "managedidentity" med klient-ID eller objekt-ID för den hanterade identiteten.

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> egenskapen managedIdentity **får inte** användas tillsammans med egenskaperna storageAccountName eller storageAccountKey

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat, som beskrivs i föregående avsnitt, kan användas i en Azure Resource Manager-mall för att köra det anpassade skripttillägget under distributionen. Följande exempel visar hur du använder tillägget Anpassat skript:

* [Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Distribuera tvånivåprogram i Windows och Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-distribution

Kommandot `Set-AzVMCustomScriptExtension` kan användas för att lägga till tillägget Anpassat skript på en befintlig virtuell dator. Mer information finns i [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

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

I det här exemplet har du tre skript som används för att skapa servern. **KommandotToExecute anropar** det första skriptet, då har du alternativ för hur de andra kallas. Du kan till exempel ha ett huvudskript som styr körningen, med rätt felhantering, loggning och tillståndshantering. Skripten hämtas till den lokala datorn för körning. Till exempel `1_Add_Tools.ps1` i `2_Add_Features.ps1` du `.\2_Add_Features.ps1` skulle ringa genom att lägga till skriptet och `$settings`upprepa denna process för andra skript som du definierar i .

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
    -TypeHandlerVersion "1.10" `
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Köra skript från en lokal resurs

I det här exemplet kanske du vill använda en lokal SMB-server för din skriptplats. På så sätt behöver du inte ange några andra inställningar, förutom **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Så här kör du anpassade skript mer än en gång med CLI

Om du vill köra det anpassade skripttillägget mer än en gång kan du bara göra den här åtgärden under följande villkor:

* Parametern **tilläggsnamn** är samma som den tidigare distributionen av tillägget.
* Uppdatera konfigurationen annars kommandot inte kommer att köras igen. Du kan lägga till en dynamisk egenskap i kommandot, till exempel en tidsstämpel.

Du kan också ange egenskapen [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) till **true**.

### <a name="using-invoke-webrequest"></a>Använda Invoke-WebRequest

Om du använder [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) i skriptet måste `-UseBasicParsing` du ange parametern annars får du följande felmeddelande när du kontrollerar den detaljerade statusen:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Information om hur du distribuerar det anpassade skripttillägget i en skalningsuppsättning finns [i Add-AzVmssExtension](https://docs.microsoft.com/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>Klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Om du vill distribuera det anpassade skripttillägget på klassiska virtuella datorer kan du använda Azure-portalen eller de klassiska Azure PowerShell-cmdletsen.

### <a name="azure-portal"></a>Azure Portal

Navigera till din klassiska VM-resurs. Välj **Tillägg** under **Inställningar**.

Klicka på **+ Lägg till** och välj Anpassat **skripttillägg**i listan över resurser .

På sidan **Installera tillägg** markerar du den lokala PowerShell-filen och fyller i eventuella argument och klickar på **Ok**.

### <a name="powershell"></a>PowerShell

Använd [cmdleten Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure/set-azurevmcustomscriptextension) kan användas för att lägga till tillägget Anpassat skript på en befintlig virtuell dator.

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

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure PowerShell-modulen. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Tilläggsutdata loggas till filer som hittas under följande mapp på den virtuella måldatorn.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De angivna filerna hämtas till följande mapp på den virtuella måldatorn.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

var `<n>` är ett decimalt heltal, som kan ändras mellan körningar av tillägget.  Värdet `1.*` matchar det faktiska, aktuella `typeHandlerVersion` värdet för tillägget.  Den faktiska katalogen kan `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2`till exempel vara .  

När `commandToExecute` du kör kommandot anger tillägget den här `...\Downloads\2`katalogen (till exempel ) som den aktuella arbetskatalogen. Den här processen gör det möjligt att använda relativa `fileURIs` sökvägar för att hitta de filer som hämtats via egenskapen. Se tabellen nedan för exempel.

Eftersom den absoluta hämtningssökvägen kan variera över tiden är det `commandToExecute` bättre att välja relativa skript-/filsökvägar i strängen, när det är möjligt. Ett exempel:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Sökvägsinformation efter det första URI-segmentet `fileUris` sparas för filer som hämtas via egenskapslistan.  Som visas i tabellen nedan mappas nedladdade filer till nedladdningsunderkataloger för att återspegla `fileUris` värdenas struktur.  

#### <a name="examples-of-downloaded-files"></a>Exempel på nedladdade filer

| URI i fileUris | Relativ nedladdad plats | Absolut nedladdad plats <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> De absoluta katalogsökvägarna ändras under den virtuella datorns livstid, men inte inom en enda körning av CustomScript-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
