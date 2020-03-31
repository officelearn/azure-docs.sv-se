---
title: Importera virtuella datorer från ett annat labb i Azure DevTest Labs
description: I den här artikeln beskrivs hur du importerar virtuella datorer från ett annat labb till det aktuella labbet i Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 299d5c8758a13edded63b99abb2f12ddf9fa14be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759524"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importera virtuella datorer från ett annat labb i Azure DevTest Labs
Den här artikeln innehåller information om hur du importerar virtuella datorer från ett annat labb till labbet.

## <a name="scenarios"></a>Scenarier
Här är några scenarier där du behöver importera virtuella datorer från ett labb till ett annat labb:

- En person i teamet flyttar till en annan grupp inom företaget och vill ta utvecklarens skrivbord till det nya teamets DevTest Labs.
- Gruppen har nått en [kvot på prenumerationsnivå](../azure-resource-manager/management/azure-subscription-service-limits.md) och vill dela upp lagen i några prenumerationer
- Företaget flyttar till Express Route (eller någon annan ny nätverkstopologi) och teamet vill flytta de virtuella datorerna för att använda den här nya infrastrukturen

## <a name="solution-and-constraints"></a>Lösning och begränsningar
Med den här funktionen kan du importera virtuella datorer i ett labb (källa) till ett annat labb (mål). Du kan också ange ett nytt namn för måldatorn i processen. Importprocessen innehåller alla beroenden som diskar, scheman, nätverksinställningar och så vidare.

Processen tar lite tid och påverkas av följande faktorer:

- Antal/storlek på diskarna som är kopplade till källmaskinen (eftersom det är en kopieringsåtgärd och inte en flyttåtgärd)
- Avstånd till destinationen (till exempel region östra USA till Sydostasien).

När processen är klar förblir den virtuella datorn avstängd och den nya körs i mållabbet.

Det finns två viktiga begränsningar att vara medveten om när du planerar att importera virtuella datorer från ett labb till ett annat labb:

- Import av virtuella datorer över prenumerationer och regioner stöds, men prenumerationerna måste associeras till samma Azure Active Directory-klientorganisation.
- Virtuella datorer får inte vara i ett anspråksbart tillstånd i källlabbet.
- Du är ägare till den virtuella datorn i källlabbet och ägare av labbet i mållabbet.
- För närvarande stöds den här funktionen endast via Powershell och REST API.

## <a name="use-powershell"></a>Använd PowerShell
Ladda ner Filen ImportVirtualMachines.ps1 från [filen GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Du kan använda skriptet för att importera en enda virtuell dator eller alla virtuella datorer i källlabbet till mållabbet.

### <a name="use-powershell-to-import-a-single-vm"></a>Använda PowerShell för att importera en enda virtuell dator
För att köra det här powershell-skriptet måste du identifiera källdatorn och mållabbet och eventuellt ange ett nytt namn som ska användas för måldatorn:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Använda PowerShell för att importera alla virtuella datorer i källlabbet
Om den virtuella källdatorn inte anges importerar skriptet automatiskt alla virtuella datorer i DevTest Labs.  Ett exempel:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Använda HTTP REST för att importera en virtuell dator
REST-samtalet är enkelt. Du ger tillräckligt med information för att identifiera käll- och målresurserna. Kom ihåg att åtgärden sker på mållabbets resurs.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Ange principer för ett labb](devtest-lab-get-started-with-lab-policies.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
