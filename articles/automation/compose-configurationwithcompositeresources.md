---
title: Skapa DSC-konfigurationer i Azure Automation tillstånd Configuration (DSC) med hjälp av sammansatta resurser
description: Lär dig att skapa konfigurationer med sammansatta resurser i Azure Automation tillstånd Configuration (DSC)
keywords: PowerShell dsc, önskad tillståndskonfiguration, powershell dsc azure, sammansatta resurser
services: automation
ms.service: automation
ms.subservice: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b82457d8a7ce70ecfde32ba625ef610a3772213c
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54432199"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Skapa DSC-konfigurationer i Azure Automation tillstånd Configuration (DSC) med hjälp av sammansatta resurser

När en resurs måste hanteras med mer än en enda önskat tillstånd configuration (DSC) konfiguration, den bästa vägen är att använda [sammansatta resurser](/powershell/dsc/authoringresourcecomposite). En sammansatt resurs är en kapslad och parametriserade konfiguration som används som en DSC-resurs i en annan konfiguration. På så sätt kan skapandet av komplexa konfigurationer samtidigt som de underliggande sammansatta resurserna (parametriserade konfigurationer) kan hanteras individuellt och skapats.

Azure Automation gör det [import och sammanställning av sammansatta resurser](automation-dsc-compile.md#composite-resources). När sammansatta resurser har importerats till ditt Automation-konto, du kan använda den **Skriv configuration** användarupplevelsen i den **tillstånd Configuration (DSC)** sidan.

## <a name="composing-a-configuration-from-composite-resources"></a>Skapa en konfiguration från sammansatta resurser

Innan du kan tilldela en konfiguration som görs från sammansatta resurser i Azure-portalen, måste du skapa den. Detta kan göras med hjälp av **Skriv configuration** på den **tillstånd Configuration (DSC)** sidan på antingen den **konfigurationer** eller **Compiled konfigurationer** flikar.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Till vänster, klicka på **alla resurser** och sedan namnet på ditt Automation-konto.
1. På den **automatiseringskontot** väljer **tillståndskonfiguration (DSC)** under **konfigurationshantering**.
1. På den **tillståndskonfiguration (DSC)** klickar du på den **konfigurationer** eller **kompileras konfigurationer** och klicka sedan på **Compose-konfiguration**  på menyn överst på sidan.
1. På den **grunderna** steg, ge den nya namn (krävs) och klicka på var som helst på raden i varje sammansatta resurs som du vill inkludera i den nya konfigurationen och klicka sedan på **nästa** eller klicka på den **Källkod** steg. För följande steg ska vi valt **PSExecutionPolicy** och **RenameAndDomainJoin** sammansatta resurser.
   ![Skärmbild av grunderna steg på sidan för compose-konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. Den **källkod** steg visar hur består konfigurationen av de valda sammansatta resurserna som ser ut. Du kan se sammanslagning av alla parametrar och hur de skickas till den sammansatta resursen. När du är klar granskar nya källkoden, klickar du på **nästa** eller klicka på den **parametrar** steg.
   ![Skärmbild av källa kod steg i konfigurationssidan compose](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. På den **parametrar** steget parametern med varje sammansatta resurs visas så att de kan anges. Om en parameter har en beskrivning, visas bredvid parameterfältet. Om ett fält är en **PSCredential** typparametern, i listrutan som du konfigurerar innehåller en lista över **Credential** objekt i det aktuella Automation-kontot. En **+ Lägg till autentiseringsuppgift för** alternativet är också vara tillgängliga. När alla nödvändiga parametrar har angetts, klickar du på **spara och kompilera**.
   ![Skärmbild av parametrar steg på sidan för compose-konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

När den nya konfigurationen har sparats, skickas den för kompilering. Status för kompileringsjobbet kan ses som en importerad konfiguration. Mer information finns i [visar en kompileringsjobbet](automation-dsc-getting-started.md#viewing-a-compilation-job).

När kompilering har slutförts, den nya konfigurationen visas i den **kompileras konfigurationer** fliken. När den är synlig i den här fliken, den kan tilldelas till en hanterad nod med hjälp av stegen i [omtilldela en nod till en annan nodkonfiguration](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs hur för att publicera noder i [konfigurera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)
