---
title: Fel sökning av problem med Windows VM-tillägg | Microsoft Docs
description: Lär dig mer om fel sökning av problem med Azure Windows VM-tillägg
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 6108b3c8d4f4c5c93bc2543042294bc8167d4dd9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084298"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Felsöka problem med Azure Windows VM-tillägg
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visa tilläggs status
Azure Resource Manager mallar kan köras från Azure PowerShell. När mallen har körts kan tilläggets status visas från Azure Resource Explorer eller kommando rads verktygen.

Här är ett exempel:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

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

## <a name="troubleshooting-extension-failures"></a>Felsöka tilläggs fel
### <a name="rerun-the-extension-on-the-vm"></a>Kör tillägget på den virtuella datorn igen
Om du kör skript på den virtuella datorn med ett anpassat skript tillägg kan du ibland stöta på ett fel där den virtuella datorn skapades men skriptet har misslyckats. Under dessa förhållanden är det rekommenderade sättet att återställa från det här felet att ta bort tillägget och köra mallen igen.
Obs! I framtiden skulle den här funktionen förbättras för att ta bort behovet av att avinstallera tillägget.

#### <a name="remove-the-extension-from-azure-powershell"></a>Ta bort tillägget från Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

När tillägget har tagits bort kan mallen köras igen för att köra skripten på den virtuella datorn.

