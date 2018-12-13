---
title: Azure Diagnostics-tillägg för Windows | Microsoft Docs
description: Övervaka virtuella Azure Windows-datorer med hjälp av Azure Diagnostics-tillägg
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: fe24307f7ed01c64217bcb1f9f7645a30bce7f44
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322831"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure Diagnostics-tillägg för virtuella Windows-datorer

## <a name="overview"></a>Översikt

Azure Diagnostics VM-tillägget kan du samla in övervakningsdata, till exempel prestandaräknare och händelseloggar från Windows-VM. Du kan detaljerat ange vilka data du vill samla in och var du vill att data ska användas, till exempel ett Azure Storage-konto eller en Azure-Händelsehubb. Du kan också använda dessa data för att skapa diagram i Azure-portalen eller skapa måttaviseringar.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Azure Diagnostics-tillägget kan köras mot klienten för Windows 10, Windows Server 2008 R2, 2012, 2012 R2 och 2016.

### <a name="internet-connectivity"></a>Internetanslutning

Azure Diagnostics-tillägget kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

[Azure Diagnostics-tillägg-schemat och egenskapen värden beskrivs i det här dokumentet.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Azure-diagnostiktillägget under en malldistribution för Azure Resource Manager. Se [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Azure Diagnostics-tillägget till en befintlig virtuell dator. Ersätt de skyddade inställningarna och egenskaper med giltig JSON från tillägget schemat ovan. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell-distribution

Den `Set-AzureRmVMDiagnosticsExtension` kommando kan användas för att lägga till Azure-diagnostiktillägget i en befintlig virtuell dator. Se även [Använd PowerShell för att aktivera Azure Diagnostics i en virtuell dator med Windows](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Se den här artikeln](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) för en mer omfattande felsökningsguide för Azure Diagnostics-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure Diagnostics-tillägg](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Granska Tilläggsschema och versioner](../../azure-monitor/platform/diagnostics-extension-schema.md)
