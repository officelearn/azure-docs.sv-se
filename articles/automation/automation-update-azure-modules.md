---
title: Uppdatera Azure-moduler i Azure Automation
description: Den här artikeln beskrivs hur du kan nu uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbb57753117f3c60010fe910616b8d0af5178360
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434831"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

De vanligaste Azure PowerShell-moduler som tillhandahålls som standard i varje Automation-konto. Azure-teamet uppdaterar regelbundet, Azure-moduler så att du får ett sätt att uppdatera moduler i kontot när nya versioner är tillgängliga från portalen i Automation-kontot.

Eftersom modulerna uppdateras regelbundet av produktgruppen, kan ändras med inkluderade cmdlets som kan negativt påverka dina runbooks beroende på vilken typ av ändring, till exempel byta namn på en parameter eller avvecklar en cmdlet helt och hållet. För att undvika att påverka dina runbooks och de processer som de automatisera, bör du testa och verifiera innan du fortsätter. Om du inte har ett särskilt Automation-konto som är avsedd för detta ändamål kan du skapa en så att du kan testa många olika scenarier och permutationer under utvecklingen av dina runbooks dessutom till iterativ ändringar, till exempel uppdaterar de PowerShell-moduler. När resultaten verifieras och du har gjort några ändringar som krävs, Fortsätt med samordna migrering av alla runbooks som krävs för ändring av och utföra följande uppdatering som beskrivs i produktion.

> [!NOTE]
> Ett nytt Automation-konto kan inte innehålla de senaste modulerna.

## <a name="updating-azure-modules"></a>Uppdatera Azure-moduler

1. På sidan moduler i ditt Automation-konto är ett alternativ som heter **uppdatera Azure-moduler**. Det är alltid aktiverat.<br><br> ![Uppdatera Azure-moduler alternativ i moduler sidan](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

  > [!NOTE]
  > Innan du uppdaterar din Azure-moduler rekommenderar vi att du uppdaterar dem i ett test Automation-konto för att se till att fungerar dina befintliga skript som förväntat innan du uppdaterar din Azure-moduler.
  >
  > Den **uppdatera Azure-moduler** knappen är endast tillgänglig i det offentliga molnet. Det är inte tillgänglig i den [landsbaserade regioner](https://azure.microsoft.com/global-infrastructure/). Se [alternativa metoder för att uppdatera dina moduler](#alternative-ways-to-update-your-modules) avsnittet om du vill veta mer.


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

    .NET core AzureRm moduler (AzureRm.*. Kärnor) stöds inte i Azure Automation och kan inte importeras.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  

Om du använder cmdlets från dessa Azure PowerShell-moduler i dina runbooks du vill köra den här uppdateringen varje månad eller så se till att du har de senaste modulerna. Azure Automation använder AzureRunAsConnection anslutningen att autentisera när du uppdaterar moduler, om tjänstens huvudnamn har upphört att gälla eller finns inte längre på prenumerationsnivå modulen uppdateringen misslyckas.

## <a name="alternative-ways-to-update-your-modules"></a>Olika sätt att uppdatera dina moduler

Som tidigare nämnts kan den **uppdatera Azure-moduler** knappen är inte tillgängliga i suveräna moln, det är endast tillgängligt i globala Azure-molnet. Detta beror på att den senaste versionen av Azure PowerShell-moduler från PowerShell-galleriet inte kanske fungerar med Resource Manager-tjänsterna för närvarande har distribuerats i dessa moln.

Uppdatera moduler kan fortfarande göras genom att importera den [uppdatering AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook i ditt Automation-konto och kör den.

Använd den `AzureRmEnvironment` parameter för att skicka rätt miljö till runbooken.  Godkända värden är **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, och **AzureUSGovernmentCloud**. Om du inte skickar ett värde för den här parametern runbook som standard i Azures offentliga moln **AzureCloud**.

Om du vill använda en specifik version av Azure PowerShell-modulen i stället för den senaste tillgängliga på PowerShell-galleriet, skicka dessa versioner till den valfria `ModuleVersionOverrides` -parametern för den **uppdatering AzureModule** runbook. Exempel finns i den [uppdatering AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook. Azure PowerShell-moduler som inte nämns i den `ModuleVersionOverrides` parametern uppdateras med de senaste modulversionerna på PowerShell-galleriet. Om ingenting har överförts till den `ModuleVersionOverrides` parameter, alla moduler som uppdateras med de senaste modulversionerna på PowerShell-galleriet, vilket är beteendet för den **uppdatera Azure-moduler** knappen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om integreringsmoduler och hur du skapar anpassade moduler för att ytterligare integrera Automation med andra system, tjänster eller lösningar i [integreringsmoduler](automation-integration-modules.md).

* Överväg att datakällan kontroll integrering med [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) eller [Azure DevOps](automation-scenario-source-control-integration-with-vsts.md) att centralt hantera och styra versioner av ditt Automation-runbook och konfiguration portfölj.  
