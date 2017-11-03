---
title: Uppdatera Azure moduler i Azure Automation | Microsoft Docs
description: "Den här artikeln beskriver hur du kan nu uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2017
ms.author: magoedte
ms.openlocfilehash: 6bd259f3da1005228b8137415c30660221507909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

De vanligaste Azure PowerShell-modulerna som tillhandahålls som standard i varje Automation-kontot.  Azure-teamet uppdaterar regelbundet, Azure moduler så i Automation-konto får ett sätt att uppdatera modulerna i kontot när nya versioner är tillgängliga från portalen.  

Eftersom moduler uppdateras regelbundet med produktgruppen, kan förändringar uppstå med inkluderade cmdlets som kan påverka dina runbooks beroende på vilken typ av ändring, till exempel byta namn på en parameter eller sluta en cmdlet helt. För att undvika att påverka dina runbooks och processer som de automatisera, rekommenderas du att testa och verifiera innan du fortsätter.  Överväg att skapa en så att du kan testa många olika scenarier och permutationer under utvecklingen av din runbooks, förutom iterativ ändringarna, som uppdatering PowerShell-moduler om du inte har ett särskilt Automation-konto som är avsedda för detta ändamål.  När resultatet verifieras och du har gjort några ändringar krävs, fortsätta med samordning av migreringen av alla runbooks som krävs för ändring och utföra uppdateringen som beskrivs nedan i produktion.     

## <a name="updating-azure-modules"></a>Uppdatera Azure moduler

1. Modulerna bladet för Automation-kontot det är ett alternativ som kallas **Update Azure moduler**.  Det är alltid aktiverat.<br><br> ![Uppdatera Azure moduler alternativ i bladet för moduler](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Klicka på **Update Azure moduler** och du kommer att visas ett meddelande om bekräftelse som frågar om du vill fortsätta.<br><br> ![Uppdatera Azure moduler meddelande](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klicka på **Ja** och börjar uppdateringsprocessen modulen.  Uppdateringen tar ungefär 15-20 minuter för att uppdatera följande moduler:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Om modulerna som är redan uppdaterade, Slutför processen i några sekunder.  När uppdateringen är slutförd meddelas du.<br><br> ![Uppdatera uppdateringsstatus för Azure-moduler](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.    

Om du använder cmdlet: ar från dessa Azure PowerShell-moduler i runbooks för att hantera Azure-resurser, kommer sedan du att utföra den här uppdateringen varje månad eller så att garantera att du har de senaste modulerna.

## <a name="next-steps"></a>Nästa steg

* Läs mer om integreringsmoduler och hur du skapar anpassade moduler för att ytterligare integrera Automation med andra system, tjänster eller lösningar i [integreringsmoduler](automation-integration-modules.md).

* Överväg källa kontrollen integrering med [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) eller [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) centralt hantera och styra versioner av ditt Automation-runbook och konfiguration portfölj.  