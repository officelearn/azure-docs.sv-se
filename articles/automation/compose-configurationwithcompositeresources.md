---
title: Skriver DSC-konfigurationer i Azure Automation State Configuration (DSC) med sammansatta resurser
description: Lär dig hur du skapar konfigurationer med sammansatta resurser i Azure Automation State Configuration (DSC)
keywords: PowerShell DSC, önskad tillstånds konfiguration, PowerShell DSC Azure, sammansatta resurser
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d9d16d9d9967af5bf8f9e13406e13d5637325f52
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243354"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Skriver DSC-konfigurationer i Azure Automation State Configuration (DSC) med sammansatta resurser

När en resurs behöver hanteras med mer än en enda DSC-konfiguration (Desired State Configuration) är den bästa sökvägen att använda [sammansatta resurser](/powershell/scripting/dsc/resources/authoringresourcecomposite). En sammansatt resurs är en kapslad och parameterstyrda konfiguration som används som en DSC-resurs i en annan konfiguration. På så sätt kan du skapa komplexa konfigurationer samtidigt som de underliggande sammansatta resurserna (parametriserade konfigurationer) kan hanteras individuellt och skapas.

Azure Automation aktiverar [import och kompilering av sammansatta resurser](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
När sammansatta resurser har importer ATS till ditt Automation-konto kan du använda funktionen **Skriv konfiguration** på sidan för **tillstånds konfiguration (DSC)** .

## <a name="composing-a-configuration-from-composite-resources"></a>Skapa en konfiguration från sammansatta resurser

Innan du kan tilldela en konfiguration som görs från sammansatta resurser i Azure Portal måste du skriva den. Detta kan göras med hjälp av **Skriv konfiguration** på sidan för **tillstånds konfiguration (DSC)** på fliken **konfigurationer** eller **kompilerade konfigurationer** .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan **Automation-konto** väljer du **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan **tillstånds konfiguration (DSC)** klickar du på fliken **konfigurationer** eller **kompilerade konfigurationer** och sedan på **skapa konfiguration** på menyn längst upp på sidan.
1. I **grundläggande** steg anger du det nya konfigurations namnet (obligatoriskt) och klickar på var som helst på raden i varje sammansatt resurs som du vill inkludera i den nya konfigurationen. Klicka sedan på **Nästa** eller klicka på **käll kods** steget. För följande steg valde vi **PSExecutionPolicy** -och **RenameAndDomainJoin** -sammansatta resurser.
   ![skärm bild av grundläggande steg på sidan Skriv konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. I **käll kods** steget visas hur den sammanställda konfigurationen av de valda sammansatta resurserna ser ut. Du kan se sammanslagningen av alla parametrar och hur de skickas till den sammansatta resursen. När du är färdig med att granska den nya käll koden klickar du på **Nästa** eller klickar på **parameter** steget.
   ![skärm bild av käll kods steget på sidan Skriv konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. I steget **Parameters** visas den parameter som varje sammansatt resurs har visats så att de kan tillhandahållas. Om en parameter har en beskrivning visas den bredvid parameter fältet. Om ett fält är en **PSCredential** typ parameter innehåller List rutan för att konfigurera en lista över **Credential** -objekt i det aktuella Automation-kontot. Det finns även ett alternativ för att **lägga till en autentiseringsuppgift** . När alla obligatoriska parametrar har angetts klickar du på **Spara och kompilera**.
   ![skärm bild av steget parametrar på sidan Skriv konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

När den nya konfigurationen har sparats skickas den för kompilering. Status för Compilation-jobbet kan visas som alla importerade konfigurationer. Mer information finns i [Visa ett Compilation-jobb](automation-dsc-getting-started.md#viewing-a-compilation-job).

När kompileringen har slutförts visas den nya konfigurationen på fliken **kompilerade konfigurationer** . När den är synlig på den här fliken kan den tilldelas till en hanterad nod med hjälp av stegen i [omtilldela en nod till en annan nod-konfiguration](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Nästa steg

- För att komma igång, se [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md)
- Information om hur du kan publicera noder finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md)
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/azurerm.automation/#automation)
- För pris information, se [priser för Azure Automation tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
