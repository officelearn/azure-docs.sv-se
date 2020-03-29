---
title: Azure Diagnostics-tillägg för Windows
description: Övervaka virtuella Azure Windows-datorer med Azure Diagnostics Extension
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
ms.openlocfilehash: d4ba3e8bbdad72f10c630a056f988ec83e9b1a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471626"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure Diagnostics-tillägg för virtuella Windows-datorer

## <a name="overview"></a>Översikt

Med Azure Diagnostics VM-tillägget kan du samla in övervakningsdata, till exempel prestandaräknare och händelseloggar, från din Virtuella Windows-dator. Du kan detaljerad ange vilka data du vill samla in och vart du vill att data ska gå, till exempel ett Azure Storage-konto eller en Azure Event Hub. Du kan också använda dessa data för att skapa diagram i Azure-portalen eller skapa måttaviseringar.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem

Azure Diagnostics-tillägget kan köras mot Windows 10 Client, Windows Server 2008 R2, 2012, 2012 R2 och 2016.

### <a name="internet-connectivity"></a>Internetanslutning

Azure Diagnostics Extension kräver att mål virtuell dator är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

[Schema- och egenskapsvärdena för Azure Diagnostics Extension beskrivs i det här dokumentet.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Azure Diagnostics-tillägget under en Azure Resource Manager-malldistribution. Se [Använda övervakning och diagnostik med mallar för Windows VM och Azure Resource Manager](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Azure Diagnostics-tillägget till en befintlig virtuell dator. Ersätt egenskaperna för skyddade inställningar och inställningar med giltig JSON från tilläggsschemat ovan. 

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

Kommandot `Set-AzVMDiagnosticsExtension` kan användas för att lägga till Azure Diagnostics-tillägget till en befintlig virtuell dator. Se även [Använda PowerShell för att aktivera Azure Diagnostics på en virtuell dator som kör Windows](ps-extensions-diagnostics.md).

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Se den här artikeln](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) för en mer omfattande felsökningsguide för Azure Diagnostics-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Efterföljande moment
* [Läs mer om Azure Diagnostics-tillägget](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Granska tilläggsschemat och versionerna](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
