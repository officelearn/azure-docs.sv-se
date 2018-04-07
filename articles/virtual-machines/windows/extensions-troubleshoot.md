---
title: Felsöka Windows VM-tillägget fel | Microsoft Docs
description: Lär dig mer om hur du felsöker Azure Windows VM-tillägget fel
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 5c99f4a9209595fd58021fd8da9cd67f1e034748
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Felsöka Azure Windows VM-tillägget fel
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visa Tilläggsstatus för
Azure Resource Manager-mallar kan köras från Azure PowerShell. När mallen körs visas tilläggets status från Azure Resursläsaren eller kommandoradsverktyg.

Här är ett exempel:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

Här är exempel på utdata:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Felsökning av tillägget fel
### <a name="re-running-the-extension-on-the-vm"></a>Köra tillägget på den virtuella datorn
Om du kör skript på den virtuella datorn med hjälp av tillägget för anpassat skript, kan du ibland stöter på ett fel där VM skapades men inte det gick att skriptet. Under dessa villkor är det rekommenderade sättet att åtgärda felet för att tillägget tas bort och kör mallen igen.
Obs: I framtiden kommer den här funktionen skulle förbättras för att ta bort behovet av att avinstallera tillägget.

#### <a name="remove-the-extension-from-azure-powershell"></a>Ta bort tillägget från Azure PowerShell
    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

När tillägget har tagits bort går kan mallen köras igen att köra skript på den virtuella datorn.

