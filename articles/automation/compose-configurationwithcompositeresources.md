---
title: Komponera DSC-konfigurationer i DSC (Azure Automation State Configuration) med sammansatta resurser
description: Lär dig hur du skriver konfigurationer med sammansatta resurser i DSC (Azure Automation State Configuration)
keywords: powershell dsc, önskad tillståndskonfiguration, powershell dsc azure, sammansatta resurser
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370663"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Komponera DSC-konfigurationer i DSC (Azure Automation State Configuration) med sammansatta resurser

När en resurs måste hanteras med mer än en DSC-konfiguration (Desired State Configuration) är den bästa sökvägen att använda [sammansatta resurser](/powershell/scripting/dsc/resources/authoringresourcecomposite). En sammansatt resurs är en kapslad och parameteriserad konfiguration som används som en DSC-resurs i en annan konfiguration. Detta gör det möjligt att skapa komplexa konfigurationer samtidigt som de underliggande sammansatta resurserna (parameteriserade konfigurationer) kan hanteras och byggas individuellt.

Azure Automation möjliggör [import och kompilering av sammansatta resurser](automation-dsc-compile.md).
När sammansatta resurser har importerats till ditt Automation-konto kan du använda **compose-konfigurationsupplevelsen** på sidan **Tillståndskonfiguration (DSC).**

## <a name="composing-a-configuration-from-composite-resources"></a>Skriva en konfiguration från sammansatta resurser

Innan du kan tilldela en konfiguration gjord av sammansatta resurser i Azure-portalen måste du skriva den. Detta kan göras med hjälp av **Compose-konfiguration** på sidan **Tillståndskonfiguration (DSC)** på **flikarna Konfigurationer** eller **Kompilerade konfigurationer.**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Till vänster klickar du på **Alla resurser** och sedan namnet på ditt Automation-konto.
1. På sidan **Automation-konto** väljer du **Tillståndskonfiguration (DSC)** under **Konfigurationshantering**.
1. På sidan **Tillståndskonfiguration (DSC)** klickar du antingen på fliken **Konfigurationer** eller **Kompilerade konfigurationer** och sedan på **Komponera konfiguration** på menyn högst upp på sidan.
1. I steget **Grunderna** anger du det nya konfigurationsnamnet (obligatoriskt) och klickar var som helst på raden för varje sammansatt resurs som du vill inkludera i den nya konfigurationen och klickar sedan på **Nästa** eller klickar på **steg för källkod.** För följande steg valde vi **PSExecutionPolicy** och **Byt namn påOchDomänHjutningskompositresurser.**
   ![Skärmbild av grundsteget på konfigurationssidan för komponera](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. **Stegvisa källkod** visar hur den sammansatta konfigurationen av de valda sammansatta resurserna ser ut. Du kan se sammanslagningen av alla parametrar och hur de skickas till den sammansatta resursen. När du är klar med granskningen av den nya källkoden klickar du på **Nästa** eller klickar på steget **Parametrar.**
   ![Skärmbild av källkodssteget på konfigurationssidan för komponera](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. I steget **Parametrar** visas parametern som varje sammansatt resurs har så att de kan tillhandahållas. Om en parameter har en beskrivning visas den bredvid parameterfältet. Om ett fält är en **PSCredential-typparameter** innehåller listrutan för att konfigurera en lista över **autentiseringsobjekt** i det aktuella automationskontot. A **+ Lägg till ett autentiseringsuppgifter** alternativ är också tillgänglig. När alla nödvändiga parametrar har angetts klickar du på **Spara och kompilerar**.
   ![Skärmbild av parametersteget på konfigurationssidan för komponera](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

När den nya konfigurationen har sparats skickas den för kompilering. Status för kompileringsjobbet kan visas som vilken importerad konfiguration som helst. Mer information finns i [Visa ett kompileringsjobb](automation-dsc-getting-started.md#viewing-a-compilation-job).

När kompileringen har slutförts visas den nya konfigurationen på fliken **Kompilerade konfigurationer.** När den visas på den här fliken kan den tilldelas en hanterad nod med hjälp av stegen för [att tilldela om en nod till en annan nodkonfiguration](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Mer information om hur du 2019:er kontrollerar [Onboarding-datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till målnoder finns [i Kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- Cmdlet-referens för PowerShell finns i [cmdlets för Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Prisinformation finns i [prissättningen för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Information om hur du använder Azure Automation State Configuration i en pipeline för kontinuerlig distribution finns i [Kontinuerlig distribution med Azure Automation State Configuration och Chocolatey](automation-dsc-cd-chocolatey.md)
