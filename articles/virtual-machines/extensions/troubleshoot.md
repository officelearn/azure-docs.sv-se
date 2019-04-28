---
title: Felsökning av problem med Windows Virtuella datortillägg | Microsoft Docs
description: Läs om hur du felsöker problem med Azure Windows Virtuella datortillägg
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
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107252"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Felsöka Azure Windows VM-tillägg-fel
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visa Tilläggsstatus för
Azure Resource Manager-mallar kan köras från Azure PowerShell. När mallen körs, kan du visa tilläggets status från Azure Resource Explorer eller kommandoradsverktygen.

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

## <a name="troubleshooting-extension-failures"></a>Felsöka datortillägg
### <a name="rerun-the-extension-on-the-vm"></a>Kör tillägget på den virtuella datorn
Om du kör skript på den virtuella datorn med hjälp av tillägget för anpassat skript, kan du ibland stöter på ett fel där virtuella datorn har skapats men har skriptet misslyckats. Med dessa villkor sker är det rekommenderade sättet att komma tillrätta med det här felet att ta bort tillägget och köra mallen igen.
Obs! Den här funktionen skulle i framtiden förbättras för att ta bort behovet av att avinstallera tillägget.

#### <a name="remove-the-extension-from-azure-powershell"></a>Ta bort tillägget från Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

När tillägget har tagits bort går kan mallen köras igen att köra skripten på den virtuella datorn.

