---
title: Runbook-inställningar i Azure Automation
description: Beskriver konfigurationsinställningarna för en runbook i Azure Automation och hur du ändrar dem med hjälp av både Azure-portalen och Windows PowerShell.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 548a168055a63ee8cb5147b56fb77ef0bc801e17
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="runbook-settings"></a>Runbook-inställningar
Varje runbook i Azure Automation har flera inställningar som gör att den kan identifieras och ändra loggningsbeteende. Dessa inställningar beskrivs nedan följs av procedurer för hur du ändrar dem.

## <a name="settings"></a>Inställningar
### <a name="name-and-description"></a>Namn och beskrivning
Du kan inte ändra namnet på en runbook när den har skapats. Beskrivningen är valfri och kan vara upp till 512 tecken.

### <a name="tags"></a>Taggar
Taggar kan du tilldela distinkta ord och fraser för att identifiera en runbook. Till exempel när du skickar en runbook och den [PowerShell-galleriet](https://www.powershellgallery.com/), anger du viss taggar för att identifiera vilka kategorier som ska visas i runbook. Du kan ange flera etiketter för en runbook genom att avgränsa dem med kommatecken.

### <a name="logging"></a>Loggning
Som standard skrivs utförlig och förlopp poster inte till jobbhistoriken. Du kan ändra inställningarna för en viss runbook för att logga dessa poster. Mer information om dessa poster finns [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Ändra runbook-inställningar

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Ändra runbook-inställningar med Azure-portalen
Du kan ändra inställningarna för en runbook i Azure-portalen från den **inställningar** bladet för runbook.

1. Välj i Azure-portalen **Automation** och klicka sedan på namnet på ett automation-konto.
2. Välj den **Runbooks** fliken.
3. Klicka på namnet på en runbook och dirigeras du till inställningsbladet för runbook. Härifrån du ange eller ändra taggar, runbook-beskrivning, konfigurera loggning och spårningsinställningar och komma åt supportverktyg som hjälper dig att lösa problem.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Ändra runbook-inställningar med Windows PowerShell
Du kan använda den [Set AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) för att ändra inställningarna för en runbook. Om du vill ange flera etiketter kan ange du antingen en matris eller en sträng med kommateckenavgränsade värden att parametern taggar. Du kan hämta de aktuella taggarna med den [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Följande exempelkommandon visar hur du ställer in egenskaperna för en runbook. Det här exemplet lägger till tre taggar i de befintliga taggarna och anger att utförliga poster ska loggas.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Nästa steg
* Information om hur du skapar och hämta utdata och felmeddelanden från runbooks finns [Runbook-utdata och meddelanden](automation-runbook-output-and-messages.md) 
* Att förstå hur du lägger till en runbook som redan har utvecklats av gemenskapen eller någon annan källa eller skapa egna runbook finns [skapa eller importera en Runbook](automation-creating-importing-runbook.md) 

