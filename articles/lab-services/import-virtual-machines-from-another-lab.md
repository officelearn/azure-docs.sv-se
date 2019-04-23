---
title: Importera virtuella datorer från en annan labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att importera virtuella datorer från en annan lab till den aktuella testmiljön.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: ca6ed58cfabb5027830828812c4820c1b586875c
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60148908"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Importera virtuella datorer från en annan labb i Azure DevTest Labs
Den här artikeln innehåller information om hur du importerar virtuella datorer från en annan lab till labbet.

## <a name="scenarios"></a>Scenarier
Här följer några scenarier där du behöver importera virtuella datorer från en labb till en annan Labb:

- En person i gruppen flyttas till en annan grupp i företaget och vill ta developer skrivbordet till det nya teamet DevTest Labs.
- Gruppen har nått en [prenumerationsnivå kvot](../azure-subscription-service-limits.md) och vill dela upp team i några prenumerationer
- Företaget flyttar till Express Route (eller några andra nya nätverkets topologi) och teamet vill flytta virtuella datorer att använda den här nya infrastruktur

## <a name="solution-and-constraints"></a>Lösningen och begränsningar
Den här funktionen kan du importera virtuella datorer i en labb (källa) till en annan lab (mål). Du kan också ge ett nytt namn för mål VM i processen. Importen inkluderar alla beroenden som diskar, scheman, nätverksinställningar och så vidare.

Processen tar lite tid och påverkas av följande faktorer:

- Antal/storlek på diskarna som är kopplade till källdatorn (eftersom det är en kopieringsåtgärd och inte en flyttningsåtgärd)
- Avståndet till mål (till exempel USA, östra region till Sydostasien).

När processen är klar, förblir den virtuella källdatorn avstängning och den nya en körs i laboratoriet mål.

Det finns två viktiga begränsningar vara medvetna om när du planerar att importera virtuella datorer från ett labb i till en annan Labb:

- Import för virtuell dator mellan prenumerationer och mellan regioner som stöds, men prenumerationerna måste vara kopplade till samma Azure Active Directory-klient.
- Virtuella datorer får inte vara i ett tillstånd som är tillgängliga i laboratoriet källa.
- Du är ägare av den virtuella datorn i källan lab och ägare av övningen i laboratoriet mål.
- Den här funktionen stöds för närvarande endast via Powershell och REST API.

## <a name="use-powershell"></a>Använd PowerShell
Ladda ned ImportVirtualMachines.ps1 filen från den [GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). Du kan använda skriptet för att importera en enskild virtuell dator eller alla virtuella datorer i labbet källa till mål-labbet.

### <a name="use-powershell-to-import-a-single-vm"></a>Använd PowerShell för att importera en enskild virtuell dator
Kör det här powershell-skriptet måste identifiera den Virtuella källdatorn och målet labbet och du kan också ange ett nytt namn för måldatorn:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Använd PowerShell för att importera alla virtuella datorer i labbet källa
Om den virtuella källdatorn inte anges importerar skriptet automatiskt alla virtuella datorer i DevTest Labs.  Exempel:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>Importera en virtuell dator med hjälp av HTTP-REST
REST-anrop är enkelt. Du ger tillräcklig information för att identifiera resurserna som källa och mål. Kom ihåg att åtgärden sker på lab målresursen.

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
