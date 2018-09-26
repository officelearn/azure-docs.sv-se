---
title: Importera virtuella datorer från en annan labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du importerar virtuella datorer från en annan lab till det aktuella labbet i Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4585d151e286917c67586a02539a10ade32bdd4c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094552"
---
# <a name="import-vms-from-another-lab-in-azure-devtest-labs"></a>Importera virtuella datorer från en annan labb i Azure DevTest Labs
Tjänsten Azure DevTest Labs avsevärt förbättrar hanteringen av virtuella datorer (VM) för utveckling och testning aktiviteter. Du kan flytta en virtuell dator från en lab till en annan som teamet eller ändra krav på nätverksinfrastruktur. Här följer några vanliga scenarier där du kan behöva göra det: 

- En person i teamet flyttar till en annan grupp i företaget och vill kunna utveckling virtuella datorer till det nya teamet labb.
- Gruppen har nått kvoten prenumerationsnivå och vill dela upp team till flera prenumerationer.
- Företaget planerar att flytta till Express Route (eller några andra nya nätverkets topologi) och teamet vill flytta virtuella datorer att använda den här nya infrastruktur.

## <a name="solution-and-constraints"></a>Lösningen och begränsningar
Azure DevTest Labs kan labbägare att importera virtuella datorer i ett labb för källa till ett mål-labb. Labbägare kan du kan också ange ett nytt namn för mål VM i processen. Importen inkluderar alla beroenden diskar, scheman, nätverksinställningar, t.ex. Processen tar lite tid och påverkas av antalet/storleken på diskar som är kopplade till källdatorn och avståndet till målet (till exempel USA, östra region till regionen Sydostasien). När importen är klar, förblir den Virtuella källdatorn avstängning och den nya en körs i laboratoriet mål.

Det finns två viktiga begränsningar vara medvetna om när du planerar att importera virtuella datorer till en annan Labb:

- Importera virtuella datorer mellan prenumerationer och över regioner som stöds, men prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.
- Virtuella datorer får inte vara i ett tillstånd som är tillgängliga i laboratoriet källa.

Dessutom för att kunna importera en virtuell dator från en labb till en annan, måste vara ägare av den virtuella datorn i källan lab och ägare av övningen i laboratoriet mål.

## <a name="steps-to-import-a-vm-from-another-lab"></a>Steg för att importera en virtuell dator från en annan lab
Du kan för närvarande kan importera en virtuell dator från en labb till en annan endast med hjälp av Azure PowerShell och REST API.

### <a name="use-powershell"></a>Använd PowerShell
Ladda ned PowerShell-skriptet filen ImportVirtualMachines.ps1 från [Azure DevTest Lab Git-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImportVirtualMachines) till din lokala enhet. 

#### <a name="import-a-single-vm"></a>Importera en enskild virtuell dator
Kör skriptet ImportVirtualMachines.ps1 för att importera en enskild virtuell dator från ett labb för källa till ett testlabb för målet. Du kan ange ett nytt namn för den virtuella datorn som ska kopieras med hjälp av DestinationVirtualMachineName paramer. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -SourceVirtualMachineName "<Name of the machine. Optional. If not specified, all VMs are copied>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VM is copied>" `
                            -DestinationVirtualMachineName "<New name for the VM. Optional>"
```


#### <a name="importing-all-vms"></a>Importera alla virtuella datorer
När du kör skriptet ImportVirtualMachines.ps1 om du inte anger en virtuell dator i labbet källa, importerar skriptet alla virtuella datorer i labbet källa till mål-labbet. 

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source VM>" `
                            -SourceDevTestLabName "<Name of the lab that contains the source VM>" `
                            -DestinationSubscriptionId "<ID of the target/destination subscription>" `
                            -DestinationDevTestLabName "<Name of the lab to which the VMs are copied>"
```

### <a name="use-rest-api"></a>Använda REST-API
Anropa REST-API: et mot mål-och mål-labb och skicka i källan lab, prenumeration och information om Virtuellt som parametrar som du ser i följande exempel: 

```json
POST https://management.azure.com/subscriptions/<ID of the target/destination subscription>/resourceGroups/<Name of the resource group that contains the destination lab>/providers/Microsoft.DevTestLab/labs/<Name of the lab to which the VMs are copied>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<ID of the subscription that contains the source VM>/resourcegroups/<Name of the resource group that contains the source lab>/providers/microsoft.devtestlab/labs/<Name of the lab that contains the source VM>/virtualmachines/MyVm",
   destinationVirtualMachineName: "MyVmNew"
}
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar storlek på en virtuell dator finns i [ändra storlek på en virtuell dator](devtest-lab-resize-vm.md).
- Information om hur du distribuerar om en virtuell dator finns i [distribuera om en virtuell dator](devtest-lab-redeploy-vm.md).


