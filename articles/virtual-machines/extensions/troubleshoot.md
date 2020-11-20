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
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bca826cda8dfe47c341886faaf4a0d66f09d37d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966351"
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

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Utlös en ny GoalState för den virtuella datorn
Du kanske märker att ett tillägg inte har körts eller inte kan köras på grund av ett saknat "Windows Azure CRP Certificate Generator" (det certifikatet används för att skydda transporten av tilläggets skyddade inställningar).
Certifikatet kommer automatiskt att återskapas genom att starta om Windows gäst agenten från den virtuella datorn:
- Öppna aktivitets hanteraren
- Gå till fliken information
- Hitta WindowsAzureGuestAgent.exes processen
- Högerklicka på och välj "Avsluta aktivitet". Processen kommer att startas om automatiskt


Du kan också utlösa en ny GoalState för den virtuella datorn genom att köra en "Tom uppdatering":

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Azure CLI:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Om en "Tom uppdatering" inte fungerade kan du lägga till en ny tom datadisk till den virtuella datorn från Azure-Hanteringsportal och sedan ta bort den senare när certifikatet har lagts till igen.
