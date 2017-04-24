---
title: Verifiera konfiguration av Azure Automation-konto | Microsoft Docs
description: "Den här artikeln beskriver hur du bekräftar att konfigurationen av ditt Automation-konto har gjorts på korrekt sätt."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/14/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 5bed2616e15a2e5f52e79d0c28159b568e7a1de3
ms.lasthandoff: 04/15/2017

---

# <a name="test-azure-automation-run-as-account-authentication"></a>Testa Kör som-kontoautentisering för Azure Automation
När ett Automation-konto har skapats kan du utföra ett enkelt test för att bekräfta att du kan autentisera i Azure Resource Manager eller den klassiska Azure-distributionen med ditt nyskapade eller uppdaterade Automation Kör som-konto.    

## <a name="automation-run-as-authentication"></a>Automation Kör som-autentisering

1. Öppna Automation-kontot som du skapade tidigare på Azure Portal.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Välj **AzureAutomationTutorialScript**-runbooken och klicka sedan på **Starta** för att starta runbooken.  Ett meddelande visas där du bekräftar att du vill starta runbooken.
4. Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet för jobbet öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**.  
5. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
6. När runbook-jobbet har slutförts bör du se statusen **Slutfört**.<br> ![Runbook-test för säkerhetsobjekt](media/automation-verify-runas-authentication/job-summary-automationtutorialscript.png)<br>
7. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.
8. På bladet **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla tillgängliga resurser i resursgruppen.
9. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.
10. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureAutomationTutorialScript**-runbooken.

## <a name="classic-run-as-authentication"></a>Klassisk Kör som-autentisering
Utför följande steg om du ska hantera resurser i den klassiska distributionsmodellen och vill bekräfta att du kan autentisera med det nya klassiska Kör som-kontot.     

1. Öppna Automation-kontot som du skapade tidigare på Azure Portal.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Välj **AzureAutomationTutorialScript**-runbooken och klicka på **Starta** för att starta runbooken.  Ett meddelande visas där du bekräftar att du vill starta runbooken.
4. Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet för jobbet öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**.  
5. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
6. När runbook-jobbet har slutförts bör du se statusen **Slutfört**.<br><br> ![Runbook-test för säkerhetsobjekt](media/automation-verify-runas-authentication/job-summary-automationclassictutorialscript.png)<br>  
7. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.
8. På bladet **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla klassiska virtuella datorer i prenumerationen.
9. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.
10. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureClassicAutomationTutorialScript**-runbooken.

## <a name="next-steps"></a>Nästa steg
* Information om hur du kommer igång med PowerShell-runbooks finns i [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
* Läs mer om grafisk redigering i [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

