---
title: Redigera textrepresentation runbooks i Azure Automation
description: "Den här artikeln innehåller olika procedurer för att arbeta med PowerShell och PowerShell-arbetsflöde runbooks i Azure Automation med hjälp av textrepresentation editor."
services: automation
documentationcenter: 
author: georgewallace
manager: stevenka
editor: tysonn
ms.assetid: 6f5b48fb-6f30-4e99-9e14-9061b5554b08
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: magoedte;bwren
ms.openlocfilehash: e166700be0ec6b32d40f34bd47f92a7cff1cc7bf
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigera textrepresentation runbooks i Azure Automation
Textrepresentation redigeraren i Azure Automation kan användas för att redigera [PowerShell runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell-arbetsflöde runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Detta har vanliga funktioner i andra koden redigerare, till exempel intellisense och syntax i färger med ytterligare särskilda funktioner som hjälper dig att komma åt resurser som är gemensamma för runbooks.  Den här artikeln innehåller detaljerade anvisningar för att utföra olika funktioner med den här redigeraren.

Textrepresentation redigeraren innehåller en funktion för att infoga kod för aktiviteter, resurser och underordnade runbooks i en runbook. I stället för att skriva i koden själv, kan du välja från en lista över tillgängliga resurser och lämplig kod infogas i runbooken.

Varje runbook i Azure Automation har två versioner: utkast och publicerad. Du redigerar Utkastversionen av runbooken och publicerar det så att den kan köras. Den publicerade versionen kan inte redigeras. Se [publicera en runbook](automation-creating-importing-runbook.md#publishing-a-runbook) för mer information.

Arbeta med [grafiska runbook-flöden](automation-runbook-types.md#graphical-runbooks), se [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Så här redigerar du en runbook med Azure-portalen
Använd följande procedur för att öppna en runbook för redigering i redigeraren textrepresentation.

1. Välj ditt automation-konto i Azure-portalen.
2. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.
3. Klicka på namnet på den runbook som du vill redigera och klicka sedan på den **redigera** knappen.
4. Utför den redigering som krävs.
5. Klicka på **spara** när ändringarna har slutförts.
6. Klicka på **publicera** om du vill att den senaste Utkastversionen av runbooken ska publiceras.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Infoga en cmdlet i en runbook
1. Placera markören där du vill placera cmdlet i arbetsytan textrepresentation redigeraren.
2. Expandera den **Cmdlets** nod i biblioteket kontrollen.
3. Expandera den modul som innehåller den cmdlet som du vill använda.
4. Högerklicka på cmdlet om du vill infoga och välj **Lägg till arbetsytan**.  Om cmdleten har mer än en parameter har angetts, läggs en standarduppsättning.  Du kan också expandera cmdlet om du vill välja en annan parameteruppsättning.
5. Koden för cmdleten infogas med hela listan över parametrar.
6. Ange ett lämpligt värde i stället för datatypen som omges av klammerparenteser <> för alla obligatoriska parametrar.  Ta bort alla parametrar som du behöver.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad runbook i en runbook
1. I arbetsytan redigeraren textrepresentation placera markören där du vill att koden för den [underordnad runbook](automation-child-runbooks.md).
2. Expandera den **Runbooks** nod i biblioteket kontrollen.
3. Högerklicka på runbooken du vill infoga och välj **Lägg till arbetsytan**.
4. Koden för den underordnade runbooken infogas med några platshållare för alla runbookparametrar.
5. Ersätt platshållarna med lämpliga värden för varje parameter.

### <a name="to-insert-an-asset-into-a-runbook"></a>Infoga en tillgång i en runbook
1. Placera markören där du vill att koden för den underordnade runbooken i arbetsytan textrepresentation redigeraren.
2. Expandera den **tillgångar** nod i biblioteket kontrollen.
3. Expandera noden för den tillgång som du vill använda.
4. Högerklicka på tillgång för att infoga och välj **Lägg till arbetsytan**.  För [variabeln tillgångar](automation-variables.md), väljer du antingen **Lägg till ”hämta variabel” på arbetsytan** eller **Lägg till ”ange variabel” på arbetsytan** beroende på om du vill hämta eller ange variabeln.
5. Koden för tillgången infogas i runbook.

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Så här redigerar du en runbook med Azure-portalen
Använd följande procedur för att öppna en runbook för redigering i redigeraren textrepresentation.

1. Välj i Azure-portalen **Automation** och klicka sedan på namnet på ett automation-konto.
2. Välj den **Runbooks** fliken.
3. Klicka på namnet på den runbook som du vill redigera och välj sedan den **författare** fliken.
4. Klicka på den **redigera** längst ned på skärmen.
5. Utför den redigering som krävs.
6. Klicka på **spara** när ändringarna har slutförts.
7. Klicka på **publicera** om du vill att den senaste Utkastversionen av runbooken ska publiceras.

### <a name="to-insert-an-activity-into-a-runbook"></a>Infoga en aktivitet i en Runbook
1. Placera markören där du vill att aktiviteten i arbetsytan textrepresentation redigeraren.
2. Längst ned på skärmen klickar du på **infoga** och sedan **aktiviteten**.
3. I den **Integrationsmodul** kolumn, Välj den modul som innehåller aktiviteten.
4. I den **aktiviteten** väljer du en aktivitet.
5. I den **beskrivning** kolumnen Observera beskrivningen av aktiviteten. Du kan också klicka på Visa detaljerad hjälp om du vill starta Hjälp för aktiviteten i webbläsaren.
6. Klicka på pilen till höger.  Om aktiviteten har parametrar visas de för kännedom.
7. Klicka på knappen Kontrollera.  Koden för att köra aktiviteten kommer att infogas i runbook.
8. Om aktiviteten kräver parametrar anger du ett lämpligt värde i stället för datatypen som omges av klammerparenteser <>.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad runbook i en runbook
1. I arbetsytan redigeraren textrepresentation placera markören där du vill att den [underordnad runbook](automation-child-runbooks.md).
2. Längst ned på skärmen klickar du på **infoga** och sedan **Runbook**.
3. Välj runbook att infoga från den mellersta kolumnen och klicka på högerpilen.
4. Om runbook har parametrar visas de för kännedom.
5. Klicka på knappen Kontrollera.  Koden för att köra den valda runbooken kommer att infogas i den aktuella runbooken.
6. Om runbook kräver parametrar anger du ett lämpligt värde i stället för datatypen som omges av klammerparenteser <>.

### <a name="to-insert-an-asset-into-a-runbook"></a>Infoga en tillgång i en runbook
1. Placera markören där du vill att aktiviteten för att hämta tillgången i arbetsytan textrepresentation redigeraren.
2. Längst ned på skärmen klickar du på **infoga** och sedan **inställningen**.
3. I den **Inställningsåtgärd** kolumn, Välj den åtgärd som du vill.
4. Välj de tillgängliga resurserna i mellersta kolumnen.
5. Klicka på knappen Kontrollera.  Kod för att hämta eller ange tillgången kommer att infogas i runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Så här redigerar du en Azure Automation-runbook med Windows PowerShell
Om du vill redigera en runbook med Windows PowerShell, använda valfri redigerare och spara den till en .ps1-fil. Du kan använda den [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) för att hämta innehållet i runbook och sedan [Set AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) för att ersätta den befintliga utkast runbook med det ändrade.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Att hämta innehållet i en Runbook med Windows PowerShell
Följande exempelkommandon visar hur du hämtar skriptet för en runbook och spara den till en skriptfil. I det här exemplet hämtas utkastversionen. Det är också möjligt att hämta den publicerade versionen av runbooken även om den här versionen inte kan ändras.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    $runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
    $runbookContent = $runbookDefinition.Content

    Out-File -InputObject $runbookContent -FilePath $scriptPath

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Så här ändrar du innehållet i en Runbook med Windows PowerShell
Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en runbook med innehållet i en skriptfil. Observera att det här är exempel samma procedur som i [att importera en runbook från en skriptfil med Windows PowerShell](automation-creating-importing-runbook.md).

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName

## <a name="related-articles"></a>Relaterade artiklar
* [Skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)
* [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md)
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Scheman](automation-schedules.md)
* [Variabler](automation-variables.md)
