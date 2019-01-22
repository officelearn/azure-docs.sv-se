---
title: Uppdatera Azure-moduler i Azure Automation
description: Den här artikeln beskrivs hur du kan nu uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c0636a3e1fa50f90c68393aea910f36d38d8eaf5
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437275"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

De vanligaste Azure PowerShell-moduler som tillhandahålls som standard i varje Automation-konto. Azure-teamet uppdaterar regelbundet Azure-moduler. I ditt Automation-konto får du ett sätt att uppdatera moduler i kontot när nya versioner är tillgängliga från portalen.

> [!NOTE]
> Den nya [Az för Azure PowerShell-modulen](/powershell/azure/new-azureps-module-az?view=azurermps-6.13.0) stöds inte i Azure Automation.

Eftersom modulerna uppdateras regelbundet av produktgruppen och ändringar som kan uppstå med cmdletarna som ingår. Den här åtgärden kan negativt påverka dina runbooks beroende på vilken typ av ändring, till exempel byta namn på en parameter eller avvecklar en cmdlet helt och hållet. För att undvika att påverka dina runbooks och de processer som de automatisera, testa och verifiera innan du fortsätter. Om du inte har ett särskilt Automation-konto som är avsedd för detta ändamål kan du skapa en så att du kan testa många olika scenarier under utvecklingen av dina runbooks. Detta test bör innehålla iterativ ändringar, till exempel uppdaterar PowerShell-moduler. Om du utvecklar dina skript lokalt bör ha samma modulversioner lokalt som du har i ditt Automation-konto när testning för att se till att du får samma resultat. När resultaten verifieras och du har använt alla ändringar som krävs, kan du flytta ändringarna till produktion.

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

    Om modulerna som redan är uppdaterad, Slutför processen i några sekunder. När uppdateringen har slutförts meddelas du.<br><br> ![Uppdatera status för Azure-moduler](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

    .NET core AzureRm moduler (AzureRm.*. Kärnor) stöds inte i Azure Automation och kan inte importeras.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  

Om du använder cmdlets från dessa Azure PowerShell-moduler i dina runbooks du vill köra den här uppdateringen varje månad eller så se till att du har de senaste modulerna. Azure Automation används den `AzureRunAsConnection` anslutning för autentisering när du uppdaterar moduler. Om tjänstens huvudnamn har upphört att gälla eller finns inte längre på prenumerationsnivån, misslyckas uppdateringen modulen.

## <a name="alternative-ways-to-update-your-modules"></a>Olika sätt att uppdatera dina moduler

Som tidigare nämnts kan den **uppdatera Azure-moduler** knappen är inte tillgängliga i suveräna moln, det är endast tillgängligt i globala Azure-molnet. Detta beror på att den senaste versionen av Azure PowerShell-moduler från PowerShell-galleriet inte fungerar med Resource Manager-resurser som för närvarande har distribuerats i dessa moln.

Du kan importera och köra den [uppdatering AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook att uppdatera Azure-moduler i ditt Automation-konto. Det är vanligtvis en bra idé att uppdatera alla Azure-moduler på samma gång. Men den här processen kan misslyckas om de versioner som du försöker importera från galleriet inte är kompatibla med Azure-tjänsterna för närvarande har distribuerats till mål-Azure-miljön. Du kan behöva du kan verifiera kompatibla versioner av moduler har angetts i runbook-parametrar.

Använd den `AzureRmEnvironment` parameter för att skicka rätt miljö till runbooken.  Godkända värden är **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, och **azureusgovernment eller**. Dessa värden kan hämtas från att använda `Get-AzureRmEnvironment | select Name`. Om du inte skickar ett värde för den här parametern, runbooken som standard i Azures offentliga moln **AzureCloud**

Om du vill använda en specifik version av Azure PowerShell-modulen i stället för den senaste tillgängliga på PowerShell-galleriet, skicka dessa versioner till den valfria `ModuleVersionOverrides` -parametern för den **uppdatering AzureModule** runbook. Exempel finns i den [uppdatering AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook. Azure PowerShell-moduler som inte nämns i den `ModuleVersionOverrides` parametern uppdateras med de senaste modulversionerna på PowerShell-galleriet. Om du skickar något till den `ModuleVersionOverrides` parameter, alla moduler som uppdateras med de senaste modulversionerna på PowerShell-galleriet. Det här beteendet är samma som den **uppdatera Azure-moduler** knappen.

## <a name="next-steps"></a>Nästa steg

* Läs mer om integreringsmoduler och hur du skapar anpassade moduler för att ytterligare integrera Automation med andra system, tjänster eller lösningar i [integreringsmoduler](automation-integration-modules.md).


