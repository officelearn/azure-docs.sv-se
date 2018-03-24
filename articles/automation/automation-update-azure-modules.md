---
title: Uppdatera Azure moduler i Azure Automation
description: Den här artikeln beskriver hur du kan nu uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f1f7068de1781d1cc66a412752f6fd99d603a6be
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

De vanligaste Azure PowerShell-modulerna som tillhandahålls som standard i varje Automation-kontot. Azure-teamet uppdaterar regelbundet, Azure moduler så i Automation-kontot finns ett sätt att uppdatera modulerna i kontot när nya versioner är tillgängliga från portalen.  

Eftersom moduler uppdateras regelbundet med produktgruppen, kan förändringar uppstå med inkluderade cmdlets som kan påverka dina runbooks beroende på vilken typ av ändring, till exempel byta namn på en parameter eller sluta en cmdlet helt. För att undvika att påverka dina runbooks och processer som de automatisera, rekommenderas du att testa och verifiera innan du fortsätter. Överväg att skapa en så att du kan testa många olika scenarier och permutationer under utvecklingen av din runbooks, förutom iterativ ändringarna, som uppdatering PowerShell-moduler om du inte har ett särskilt Automation-konto som är avsedda för detta ändamål. När resultaten verifieras och du har gjort några ändringar krävs kan fortsätta med samordning av migreringen av alla runbooks som krävs för ändring och utför följande uppdatering som beskrivs i produktion.

## <a name="updating-azure-modules"></a>Uppdatera Azure moduler

1. På sidan moduler i ditt Automation-konto är ett alternativ som kallas **Update Azure moduler**. Det är alltid aktiverat.<br><br> ![Uppdatera Azure moduler alternativet moduler på sidan](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Klicka på **Update Azure moduler**, ett meddelande om bekräftelse visas som frågar om du vill fortsätta.<br><br> ![Uppdatera Azure moduler meddelande](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klicka på **Ja** och börjar uppdateringsprocessen modulen. Uppdateringen tar ungefär 15-20 minuter för att uppdatera följande moduler:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Om modulerna som är redan uppdaterade, sedan processen är klar på några sekunder. När uppdateringen är klar visas ett meddelande.<br><br> ![Uppdatera uppdateringsstatus för Azure-moduler](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.    

Om du använder cmdlet: ar från dessa Azure PowerShell-moduler i dina runbooks vill du köra den här uppdateringen varje månad eller så se till att du har de senaste modulerna.

## <a name="next-steps"></a>Nästa steg

* Läs mer om integreringsmoduler och hur du skapar anpassade moduler för att ytterligare integrera Automation med andra system, tjänster eller lösningar i [integreringsmoduler](automation-integration-modules.md).

* Överväg källa kontrollen integrering med [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) eller [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) centralt hantera och styra versioner av ditt Automation-runbook och konfiguration portfölj.  