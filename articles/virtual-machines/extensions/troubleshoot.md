---
title: Fel sökning av problem med Windows VM-tillägg
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
ms.openlocfilehash: 2fa87e860d0f5f5117840b9e230e383cdd6aae7c
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187565"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Felsöka problem med Azure Windows VM-tillägg
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Visa tilläggs status
Azure Resource Manager mallar kan köras från Azure PowerShell. När mallen har körts kan tilläggets status visas från Azure Resource Explorer eller kommando rads verktygen.

Här är ett exempel:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Här är exempel på utdata:

```output
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
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Felsöka tilläggs fel
### <a name="rerun-the-extension-on-the-vm"></a>Kör tillägget på den virtuella datorn igen
Om du kör skript på den virtuella datorn med ett anpassat skript tillägg kan du ibland stöta på ett fel där den virtuella datorn skapades men skriptet har misslyckats. Under dessa förhållanden är det rekommenderade sättet att återställa från det här felet att ta bort tillägget och köra mallen igen.
Obs! i framtiden skulle den här funktionen förbättras för att ta bort behovet av att avinstallera tillägget.

#### <a name="remove-the-extension-from-azure-powershell"></a>Ta bort tillägget från Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

När tillägget har tagits bort kan mallen köras igen för att köra skripten på den virtuella datorn.

