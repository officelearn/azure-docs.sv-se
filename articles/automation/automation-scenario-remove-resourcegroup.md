---
title: Automatiserad borttagning av resursgrupper | Microsoft Docs
description: "PowerShells arbetsflödesversion av ett Azure Automation-scenario, inklusive runbooks för att ta bort alla resursgrupper i din prenumeration."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: 
ms.assetid: b848e345-fd5d-4b9d-bc57-3fe41d2ddb5c
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: magoedte
ms.openlocfilehash: 8b23e55a597f293b17183e80eea6c2763aabe9ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automate-removal-of-resource-groups"></a>Azure Automation-scenario – automatiserad borttagning av resursgrupper
Många kunder skapar fler än en resursgrupp. En del används för att hantera driftsprogram, andra används som utvecklings-, testnings-, eller mellanlagringsmiljöer. Att automatisera distributionen av dessa resurser är en sak, men att kunna ta en resursgrupp ur drift genom att klicka på en knapp är en helt annan sak. Du kan förenkla den här vanliga hanteringsåtgärden med hjälp av Azure Automation. Det är också användbart om du arbetar med en Azure-prenumeration som har en utgiftsgräns via ett medlemserbjudande som till exempel MSDN eller Microsoft Partner Network Cloud Essentials-programmet.

Det här scenariot baseras på en PowerShell-runbook och är utformat för att ta bort en eller flera resursgrupper som du anger i prenumerationshanteringen. Standardinställningen för runbooken är att testa innan du fortsätter. Det säkerställer att du inte råkar ta bort resursgruppen innan du är beredd att slutföra proceduren.   

## <a name="getting-the-scenario"></a>Hämta scenariot
Det här scenariot består av en PowerShell-runbook som du kan hämta från [PowerShell-galleriet](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript). Du kan också importera den direkt från [Runbook-galleriet](automation-runbook-gallery.md) i Azure-portalen.<br><br>

| Runbook | Beskrivning |
| --- | --- |
| Remove-ResourceGroup |Tar bort en eller flera Azure-resursgrupper och dess resurser från prenumerationen. |

<br>
Följande indataparametrar har definierats för denna runbook:

| Parameter | Beskrivning |
| --- | --- |
| NameFilter (obligatorisk) |Anger ett namnfilter för att begränsa resursgrupperna som du vill ta bort. Du kan skicka flera värden med hjälp av en kommaavgränsad lista.<br>Filtret är inte skiftlägeskänsligt och kommer att matcha alla resursgrupper som innehåller strängen. |
| PreviewMode (valfritt) |Kör runbooken för att se vilka resursgrupper som skulle tas bort, men vidtar inga åtgärder.<br>Standardvärdet är **Sant** för att undvika oönskad borttagning av en eller flera resursgrupper som godkänts för en runbook. |

## <a name="install-and-configure-this-scenario"></a>Installera och konfigurera det här scenariot
### <a name="prerequisites"></a>Krav
Denna runbook autentiserar med hjälp av ett [Kör som-konto i Azure](automation-sec-configure-azure-runas-account.md).    

### <a name="install-and-publish-the-runbooks"></a>Installera och publicera runbooks
När du har hämtat runbooken, importerar du den med hjälp av anvisningarna i [Importera runbook-procedurer](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation). Publicera en runbook när den har importerats till ditt Automation-konto.

## <a name="using-the-runbook"></a>Med hjälp av en runbook
Följande steg hjälper dig genom körning av denna runbook och visar dig hur allt fungerar. Du kommer enbart att testa runbooken i det här exemplet, inte ta bort resursgruppen på riktigt.  

1. Öppna ditt Automation-konto i Azure Portal och klicka på **Runbooks**.
2. Välj den runbook som heter **Remove-ResourceGroup** och klicka på **Starta**.
3. När du startar runbooken, öppnas bladet **Starta runbook** och du kan konfigurera parametrarna. Ange namnet på resursgrupperna i din prenumeration som kan använda för testning och som inte skulle ställa till alltför stor skada om du råkar ta bort dem.<br> ![Parametrar för Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)

   > [!NOTE]
   > Kontrollera att alternativet **Förhandsvisningsläge** är ställt till **Sant** för att förhindra att du tar bort de markerade resursgrupperna.  **Observera** att den här runbooken inte tar bort resursgruppen som innehåller Automation-kontot som kör runbooken.  
   >
   >
4. När du har konfigurerat alla parametervärden, klickar du på **Ok** så köas runbooken för körning.  

För att visa informatikon om runbook-jobbet **Remove-ResourceGroup** i Azure Portal, väljer du **Jobb** i runbooken. Jobbsammanfattningen visar indataparametrarna och utdataströmmen samt allmän information om jobbet och eventuella undantag som uppstod.<br> ![Remove-ResourceGroup runbook jobbstatus](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

**Jobbsammanfattning** innehåller meddelanden från strömmarna utdata, varning och fel. Välj **Utdata** för att visa detaljerade resultat från runbook-körningen.<br> ![Remove-ResourceGroup runbook utdataresultat](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## <a name="next-steps"></a>Nästa steg
* Kom igång med att skapa egna runbooks, se [Skapa eller importera en runbook i Azure Automation)](automation-creating-importing-runbook.md).
* Kom igång med PowerShell Workflow-runbook, se [Min första PowerShell Workflow-runbook](automation-first-runbook-textual.md).
