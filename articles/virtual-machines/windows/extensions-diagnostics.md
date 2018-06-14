---
title: Azure Diagnostics tillägget för Windows | Microsoft Docs
description: Övervaka Azure virtuella Windows-datorer med Windows Azure-diagnostik
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
ms.openlocfilehash: a9621f6f93d8e14e53cfe05ca4a714e88b9d8572
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928720"
---
# <a name="windows-azure-diagnostics-extension"></a>Tillägget för Windows Azure-diagnostik

## <a name="overview"></a>Översikt

Windows Azure-diagnostik VM-tillägget kan du samla in övervakningsdata, till exempel prestandaräknare och händelseloggar, från Windows-VM. Granularly kan du ange vilka data du vill samla in och där du vill att data ska gå, till exempel ett Azure Storage-konto eller ett Azure-Händelsehubb. Du kan också använda dessa data för att skapa diagram i Azure-portalen eller skapa mått aviseringar.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Windows Azure-diagnostik tillägget kan köras mot Windows 10-klient, Windows Server 2008 R2, 2012 och 2012 R2 2016.

### <a name="internet-connectivity"></a>Internetanslutning

Windows Azure-diagnostik tillägget kräver att den virtuella måldatorn är ansluten till internet. 

## <a name="extension-schema"></a>Tilläggsschema

[Värdena för Windows Azure-diagnostik tillägget schemat och egenskapen beskrivs i det här dokumentet.](../../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md)

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för Windows Azure-diagnostik under en Azure Resource Manager för malldistribution. Se [Använd övervakning och diagnostik med en virtuell Windows-dator och Azure Resource Manager-mallar](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Windows Azure-diagnostik tillägget till en befintlig virtuell dator. Ersätt skyddade inställningar och inställningar med giltig JSON från tillägget schemat ovan. 

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

Den `Set-AzureRmVMDiagnosticsExtension` kommando kan användas för att lägga till Windows Azure-diagnostik-tillägg till en befintlig virtuell dator. Se även [Använd PowerShell för att aktivera Azure-diagnostik i en virtuell dator som kör Windows](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Finns den här artikeln](../../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) för en mer omfattande felsökningsguiden för tillägg för Windows Azure-diagnostik.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nästa steg
* [Mer information om Windows Azure-diagnostik tillägg](../../monitoring-and-diagnostics/azure-diagnostics.md)
* [Granska tillägget schemat och versioner](../../monitoring-and-diagnostics/azure-diagnostics-schema.md)
