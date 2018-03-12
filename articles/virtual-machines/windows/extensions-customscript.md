---
title: "Anpassat skript för Azure-tillägget för Windows | Microsoft Docs"
description: "Automatisera åtgärder för Windows VM-konfigurationen genom att använda tillägget för anpassat skript"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/16/2017
ms.author: danis
ms.openlocfilehash: 454bfde69248538a92d2fc447c54509afb08129a
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
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

Anpassade skript tillägget för Windows kan köras mot klienten för Windows 10, Windows Server 2008 R2, 2012 och 2012 R2 2016 versioner.

### <a name="script-location"></a>Skriptets placering

Skriptet måste lagras i Azure Blob storage eller någon annan plats som är tillgängligt via en giltig URL.

### <a name="internet-connectivity"></a>Internet-anslutning

Anpassade skript tillägget för Windows kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget för anpassat skript. Tillägget kräver en skript-plats (Azure Storage eller någon annan plats med en giltig URL) och ett kommando som ska köras. Om du använder Azure Storage som skriptkälla krävs ett Azure storage-konto namn och åtkomstnyckel. De här objekten ska behandlas som känsliga data och angetts i konfigurationen för skyddade inställningen tillägg. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

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

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| typ | Tillägg |
| typeHandlerVersion | 1.9 |
| fileUris (t.ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (t.ex.) | PowerShell - ExecutionPolicy obegränsad - filen konfigurera-musik-app.ps1 |
| storageAccountName (t.ex.) | examplestorageacct |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== |

**Obs** -dessa egenskapsnamn är skiftlägeskänsliga. Använda namnen som visas ovan för att undvika distributionsproblem.

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

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure PowerShell-modulen. Kör följande kommando för att se distributionsstatusen för tillägg för en viss virtuell dator.

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

När du kör den `commandToExecute` kommandot tillägget har angett den här katalogen (t.ex. `...\Downloads\2`) som den aktuella arbetskatalogen. Detta kan du använda relativa sökvägar för filer som hämtas den `fileURIs` egenskapen. Se tabellen nedan som exempel.

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

\* Som ändras ovan, de absoluta sökvägarna under livslängd för den virtuella datorn, men inte i en enda körning av CustomScript-tillägg.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure experter på [MSDN Azure och Stack Overflow forumen] (https://azure.microsoft.com/en-us/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/en-us/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/en-us/support/faq/).
