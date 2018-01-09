---
title: "Tillägget för anpassat skript på en virtuell Windows-dator | Microsoft Docs"
description: "Automatisera åtgärder för konfiguration av virtuell Azure-dator med hjälp av tillägget för anpassat skript för att köra PowerShell-skript på en fjärransluten Windows VM"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: danis
ms.openlocfilehash: 5de2949561ed5f64582898006de7818e81f79f5b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>Anpassade skript tillägget för Windows med hjälp av den klassiska distributionsmodellen

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](../extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Tillägget för anpassat skript hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för post distributionskonfiguration, Programvaruinstallation eller någon annan konfiguration / hanteringsaktivitet. Skript kan hämtas från Azure storage eller GitHub eller tillhandahålls på Azure-portalen på tillägget körtiden. Tillägget för anpassat skript kan integreras med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller Azure Virtual Machine REST API.

Det här dokumentet beskriver hur du använder den tillägget för anpassat skript med hjälp av Azure PowerShell-modulen, Azure Resource Manager-mallar och information om felsökning i Windows-System.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Tillägget för anpassat skript utgåvor för Windows kan köras mot Windows Server 2008 R2, 2012 och 2012 R2 2016.

### <a name="script-location"></a>Skriptets placering

Skriptet måste lagras i Azure storage eller någon annan plats som är tillgängligt via en giltig URL.

### <a name="internet-connectivity"></a>Internet-anslutning

Anpassade skript tillägget för Windows kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget för anpassat skript. Tillägget kräver en skript-plats (Azure Storage eller någon annan plats med en giltig URL) och ett kommando som ska köras. Om du använder Azure Storage som skriptkälla krävs ett Azure storage-konto namn och åtkomstnyckel. De här objekten ska behandlas som känsliga data och angetts i konfigurationen för skyddade inställningen tillägg. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Publisher | Microsoft.Compute |
| Tillägg | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (t.ex.) | https://Raw.githubusercontent.com/Microsoft/DotNet-Core-Sample-Templates/Master/DotNet-Core-Music-Windows/Scripts/Configure-Music-App.ps1 |
| commandToExecute (t.ex.) | PowerShell - ExecutionPolicy obegränsad - filen konfigurera-musik-app.ps1 |

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en Azure Resource Manager för malldistribution. En exempelmall som innehåller tillägget för anpassat skript hittar du här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>PowerShell-distribution

Den `Set-AzureVMCustomScriptExtension` kommando kan användas för att lägga till tillägget för anpassat skript till en befintlig virtuell dator. Mer information finns i [Set AzureRmVMCustomScriptExtension ](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure PowerShell-modulen. Kör följande kommando för att se distributionsstatusen för tillägg för en viss virtuell dator.

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Tillägget körning utdata oss inloggade filerna i följande katalog på den virtuella måldatorn.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Själva skriptet hämtas till följande katalog på den virtuella måldatorn.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/en-us/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/en-us/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/en-us/support/faq/).