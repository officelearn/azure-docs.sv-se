---
title: Uppdatera Azure-moduler i Azure Automation
description: Den här artikeln beskrivs hur du kan nu uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f968bd75c7aee4ad390f9206bb1fa210e7fcd8ff
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903157"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

De vanligaste Azure PowerShell-moduler som tillhandahålls som standard i varje Automation-konto. Azure-teamet uppdaterar regelbundet, Azure-moduler så att du får ett sätt att uppdatera moduler i kontot när nya versioner är tillgängliga från portalen i Automation-kontot.

Eftersom modulerna uppdateras regelbundet av produktgruppen, kan ändras med inkluderade cmdlets som kan negativt påverka dina runbooks beroende på vilken typ av ändring, till exempel byta namn på en parameter eller avvecklar en cmdlet helt och hållet. För att undvika att påverka dina runbooks och de processer som de automatisera, bör du testa och verifiera innan du fortsätter. Om du inte har ett särskilt Automation-konto som är avsedd för detta ändamål kan du skapa en så att du kan testa många olika scenarier och permutationer under utvecklingen av dina runbooks dessutom till iterativ ändringar, till exempel uppdaterar de PowerShell-moduler. När resultaten verifieras och du har gjort några ändringar som krävs, Fortsätt med samordna migrering av alla runbooks som krävs för ändring av och utföra följande uppdatering som beskrivs i produktion.

> [!NOTE]
> Ett nytt Automation-konto kan inte innehålla de senaste modulerna.

## <a name="updating-azure-modules"></a>Uppdatera Azure-moduler

1. På sidan moduler i ditt Automation-konto är ett alternativ som heter **uppdatera Azure-moduler**. Det är alltid aktiverat.<br><br> ![Uppdatera Azure-moduler alternativ i moduler sidan](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Klicka på **uppdatera Azure-moduler**, ett meddelande om bekräftelse visas som frågar om du vill fortsätta.<br><br> ![Uppdatera Azure-moduler meddelande](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Klicka på **Ja** och uppdateringsprocessen modulen börjar. Uppdateringen tar ungefär 15-20 minuter för att uppdatera följande moduler:

  * Azure
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Om modulerna som redan är uppdaterad, Slutför processen i några sekunder. När uppdateringen är klar visas ett meddelande.<br><br> ![Uppdatera status för Azure-moduler](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.    

Om du använder cmdlets från dessa Azure PowerShell-moduler i dina runbooks du vill köra den här uppdateringen varje månad eller så se till att du har de senaste modulerna.

## <a name="next-steps"></a>Nästa steg

* Läs mer om integreringsmoduler och hur du skapar anpassade moduler för att ytterligare integrera Automation med andra system, tjänster eller lösningar i [integreringsmoduler](automation-integration-modules.md).

* Överväg att datakällan kontroll integrering med [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) eller [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) att centralt hantera och styra versioner av ditt Automation-runbook och konfiguration portfölj.  
