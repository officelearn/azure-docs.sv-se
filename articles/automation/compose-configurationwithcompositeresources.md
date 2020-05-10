---
title: Skriver DSC-konfigurationer i Azure Automation tillstånds konfiguration med sammansatta resurser
description: Lär dig hur du skapar konfigurationer med sammansatta resurser i Azure Automation tillstånds konfiguration.
keywords: PowerShell DSC, önskad tillstånds konfiguration, PowerShell DSC Azure, sammansatta resurser
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1840f4049f8450295e179a89b472d7710c5f61a0
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993766"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-using-composite-resources"></a>Skriver DSC-konfigurationer i Azure Automation tillstånds konfiguration med sammansatta resurser

När du behöver hantera resurser med mer än en enda önskad tillstånds konfiguration (DSC) är den bästa vägen att använda [sammansatta resurser](/powershell/scripting/dsc/resources/authoringresourcecomposite). En sammansatt resurs är en kapslad och parameterstyrda konfiguration som används som en DSC-resurs i en annan konfiguration. Med hjälp av sammansatta resurser kan du skapa komplexa konfigurationer samtidigt som de underliggande sammansatta resurserna individuellt hanteras och skapas.

Azure Automation aktiverar [import och kompilering av sammansatta resurser](automation-dsc-compile.md). När du har importerat sammansatta resurser till ditt Automation-konto kan du använda Azure Automation tillstånds konfiguration via funktionen **tillstånds konfiguration (DSC** i Azure Portal.

## <a name="composing-a-configuration-from-composite-resources"></a>Skapa en konfiguration från sammansatta resurser

Innan du kan tilldela en konfiguration som görs från sammansatta resurser i Azure Portal måste du skapa konfigurationen. Sammansättningen använder- **konfiguration** på sidan för tillstånds konfiguration (DSC) på fliken **konfigurationer** eller **kompilerade konfigurationer** .

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Klicka på **alla resurser** till vänster och sedan på namnet på ditt Automation-konto.
1. På sidan Automation-konto väljer du **tillstånds konfiguration (DSC)** under **konfigurations hantering**.
1. På sidan tillstånds konfiguration (DSC) klickar du på fliken **konfigurationer** eller **kompilerade konfigurationer** och sedan på **skapa konfiguration** på menyn längst upp på sidan.
1. I **grundläggande** steg anger du det nya konfigurations namnet (obligatoriskt) och klickar på var som helst på raden i varje sammansatt resurs som du vill inkludera i den nya konfigurationen. Klicka sedan på **Nästa** eller klicka på **käll kods** steget. För följande steg har vi valt `PSExecutionPolicy` och `RenameAndDomainJoin` sammansatta resurser.
   ![Skärm bild av grundläggande steg på sidan Skriv konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. I **käll kods** steget visas hur den sammanställda konfigurationen av de valda sammansatta resurserna ser ut. Du kan se sammanslagningen av alla parametrar och hur de skickas till den sammansatta resursen. När du är färdig med att granska den nya käll koden klickar du på **Nästa** eller klickar på **parameter** steget.
   ![Skärm bild av käll kods steget på sidan Skriv konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. I steget **Parameters** visas parametern för varje sammansatt resurs så att du kan ange värden. Om en parameter har en beskrivning visas den bredvid parameter fältet. Om en parameter är av `PSCredential` typen, innehåller List rutan en lista över **Credential** -objekt i det aktuella Automation-kontot. Det finns även ett alternativ för att **lägga till en autentiseringsuppgift** . När alla obligatoriska parametrar har angetts klickar du på **Spara och kompilera**.
   ![Skärm bild av steget Parameters på sidan Skriv konfiguration](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

När den nya konfigurationen har sparats skickas den för kompilering. Status för Compilation-jobbet kan visas som alla importerade konfigurationer. Mer information finns i [Visa ett Compilation-jobb](automation-dsc-getting-started.md#view-a-compilation-job).

När kompileringen har slutförts visas den nya konfigurationen på fliken **kompilerade konfigurationer** . Sedan kan du tilldela konfigurationen till en hanterad nod med hjälp av stegen i [omtilldela en nod till en annan nod-konfiguration](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns i [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md).
- Information om hur du kan publicera noder finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md).
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md).
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/azurerm.automation/#automation).
- Pris information finns i pris information för [Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/).
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md).
