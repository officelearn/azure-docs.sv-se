---
title: Skriva DSC-konfigurationer i Azure Automation State Configuration med sammansatta resurser
description: Lär dig hur du skriver konfigurationer med sammansatta resurser i Azure Automation State Configuration.
keywords: powershell dsc, önskad tillståndskonfiguration, powershell dsc azure, sammansatta resurser
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 7e7625ae95d5355ae1122a16ea4828eed5f78c73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682934"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Skriva DSC-konfigurationer i Azure Automation State Configuration med sammansatta resurser

När du behöver hantera resurser med mer än en enda önskad tillståndskonfiguration (DSC) är den bästa sökvägen att använda [sammansatta resurser](/powershell/scripting/dsc/resources/authoringresourcecomposite). En sammansatt resurs är en kapslad och parameteriserad konfiguration som används som en DSC-resurs i en annan konfiguration. Med hjälp av sammansatta resurser kan du skapa komplexa konfigurationer samtidigt som de underliggande sammansatta resurserna kan hanteras och byggas individuellt.

Azure Automation möjliggör [import och kompilering av sammansatta resurser](automation-dsc-compile.md). När du har importerat sammansatta resurser till ditt Automation-konto kan du använda Azure Automation State Configuration via **funktionen Tillståndskonfiguration (DSC-funktionen** i Azure-portalen.

## <a name="composing-a-configuration-from-composite-resources"></a>Skriva en konfiguration från sammansatta resurser

Innan du kan tilldela en konfiguration gjord av sammansatta resurser i Azure-portalen måste du komponera konfigurationen. Komposition använder **Compose-konfiguration** på sidan Tillståndskonfiguration (DSC) på sidan **Konfigurationer** eller **Kompilerade konfigurationer.**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. På sidan Automation-konto väljer du **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration** (DSC) klickar du antingen på fliken Konfigurationer eller **Kompilerade konfigurationer** och sedan på **Komponera konfiguration** på menyn högst upp på sidan.
1. I steget **Grunderna** anger du det nya konfigurationsnamnet (obligatoriskt) och klickar var som helst på raden för varje sammansatt resurs som du vill inkludera i den nya konfigurationen och klickar sedan på **Nästa** eller klickar på **steg för källkod.** För följande steg har `PSExecutionPolicy` `RenameAndDomainJoin` vi valt och sammansatta resurser.
   ![Skärmbild av grundsteget på konfigurationssidan för komponera](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **Stegvisa källkod** visar hur den sammansatta konfigurationen av de valda sammansatta resurserna ser ut. Du kan se sammanslagningen av alla parametrar och hur de skickas till den sammansatta resursen. När du är klar med granskningen av den nya källkoden klickar du på **Nästa** eller klickar på steget **Parametrar.**
   ![Skärmbild av källkodssteget på konfigurationssidan för komponera](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. I steget **Parametrar** visas parametern för varje sammansatt resurs så att värden kan anges. Om en parameter har en beskrivning visas den bredvid parameterfältet. Om en parameter `PSCredential` är av typen innehåller listrutan en lista över **autentiseringsuppgifter** i det aktuella automationskontot. A **+ Lägg till ett autentiseringsuppgifter** alternativ är också tillgänglig. När alla nödvändiga parametrar har angetts klickar du på **Spara och kompilerar**.
   ![Skärmbild av parametersteget på konfigurationssidan för komponera](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

När den nya konfigurationen har sparats skickas den för kompilering. Status för kompileringsjobbet kan visas som vilken importerad konfiguration som helst. Mer information finns i [Visa ett kompileringsjobb](automation-dsc-getting-started.md#viewing-a-compilation-job).

När kompileringen har slutförts visas den nya konfigurationen på fliken **Kompilerade konfigurationer.** Sedan kan du tilldela konfigurationen till en hanterad nod med hjälp av stegen för [att tilldela om en nod till en annan nodkonfiguration](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md).
- Mer information om hur du kontrollerar nas inbyggda noder finns i [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md).
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation).
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md).
