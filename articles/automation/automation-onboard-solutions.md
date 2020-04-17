---
title: Inbyggd uppdatering, ändringsspårning och lagerlösningar till Azure Automation
description: Lär dig att publicera uppdateringar och spårning av ändringar i Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457628"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Inbyggd uppdatering, ändringsspårning och lagerlösningar till Azure Automation

I den här självstudien får du lära dig att automatiskt registrera lösningar för uppdatering, ändringsspårning och inventering för virtuella datorer till Azure Automation:

> [!div class="checklist"]
> * Publicera en virtuell Azure-dator
> * Aktivera lösningar
> * Installera och uppdatera moduler
> * Importera runbooken som ska publiceras
> * Starta runbook

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="onboard-an-azure-vm"></a>Publicera en virtuell Azure-dator

Det finns flera sätt att registrera datorer. Du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från bläddring av flera datorer, ](automation-onboard-solutions-from-browse.md) [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md) eller via en runbook. Den här självstudien går igenom aktivering av Uppdateringshantering via en runbook. För att publicera Azure Virtual Machines i skala måste en befintlig virtuell dator publiceras med lösningen Ändringsspårning eller Uppdateringshantering. I det här steget publicerar du en virtuell dator med Uppdateringshantering och Ändringsspårning.

### <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Med lösningarna Ändra spårning och lager kan du [spåra ändringar](automation-vm-change-tracking.md) och [lager](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningarna på en virtuell dator.

1. I Azure-portalen väljer du **Automation-konton**och väljer sedan ditt automatiseringskonto i listan.
1. Välj **Lager** under **Konfigurationshantering**.
1. Välj en befintlig Log Analytics-arbetsyta eller skapa en ny. 
1. Klicka på **Aktivera**.

    ![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. I det här steget aktiverar du lösningen för din virtuella datorn.

1. I ditt Automation-konto väljer du **Uppdateringshantering** i avsnittet **Uppdateringshantering.**
1. Arbetsytan Logganalys som valts är den arbetsyta som används i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering. Medan lösningen för uppdateringshantering installeras visas en blå banderoll. 

    ![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Välja virtuella Azure-datorer som ska hanteras

Nu när lösningarna är aktiverade kan du lägga till en virtuell Azure-dator till lösningarna.

1. Välj **Ändra spårning** under **Konfigurationshantering**i ditt Automation-konto. 
2. Klicka på **Lägg till virtuella Azure-datorer** på sidan Ändra spårning för att lägga till den virtuella datorn.

3. Välj din virtuella dator i listan och klicka på **Aktivera**. Med den här åtgärden kan lösningarna ändra spårning och lager för den virtuella datorn.

   ![Aktivera uppdateringslösning för virtuell dator](media/automation-onboard-solutions/enable-change-tracking.png)

4. När det virtuella introduktionsmeddelandet är klart väljer du **Uppdatera hantering** under **Uppdateringshantering**.

5. Välj **Lägg till virtuella Azure-datorer** för att lägga till din virtuella dator.

6. Välj din virtuella dator från listan och välj **Aktivera**. Den här åtgärden aktiverar lösningen för uppdateringshantering för den virtuella datorn.

   ![Aktivera uppdateringslösning för virtuell dator](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Om du inte väntar på att den andra lösningen ska slutföras får du meddelandet när du aktiverar nästa lösning:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Det krävs för att uppdatera till de senaste Azure-modulerna och importera modulen [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) för att framgångsrikt automatisera lösningsinboarding.

## <a name="update-azure-modules"></a>Uppdatera Azure-moduler

1. I ditt Automation-konto väljer du **Moduler** under **Delade resurser**. 
2. Välj **Uppdatera Azure-moduler** för att uppdatera Azure-moduler till den senaste versionen. 
3. Klicka på **Ja** om du vill uppdatera alla befintliga Azure-moduler till den senaste versionen.

![Uppdatera moduler](media/automation-onboard-solutions/update-modules.png) A

### <a name="install-azoperationalinsights-module"></a>Installera modulen Az.OperationalInsights

1. I automationskontot väljer du **Moduler** under **Delade resurser**. 
2. Välj **Bläddra i galleriet** för att öppna modulgalleriet. 
3. Sök efter Az.OperationalInsights och importera den här modulen till Automation-kontot.

![Importera modulen OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importera runbooken som ska publiceras

1. I ditt Automation-konto väljer du **Runbooks** under **Process Automation**.
1. Välj **Sök i galleri**.
1. Sök efter `update and change tracking`.
3. Markera runbooken och klicka på **Importera** på sidan Visa källa. 
4. Klicka på **OK** om du vill importera runbooken till Automation-kontot.

   ![Importera runbook som ska publiceras](media/automation-onboard-solutions/import-from-gallery.png)

6. Klicka på **Redigera**på sidan Runbook och välj sedan **Publicera**. 
7. I fönstret Publicera runbook klickar du på **Ja** för att publicera runbooken.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha installerat antingen ändringsspårning eller uppdatera lösningar till en Azure VM för att starta den här runbooken. Detta kräver en befintlig virtuell dator och en resursgrupp med en lösning som har publicerats för parametrar.

1. Öppna runbooken **Enable-MultipleSolution.**

   ![Runbooks med flera lösningar](media/automation-onboard-solutions/runbook-overview.png)

1. Klicka på startknappen och ange följande värden för parametrar.

   * **VMNAME** – Lämna tomt. Namnet på en befintlig virtuell dator som ska publiceras för lösningen för uppdatering eller ändringsspårning. Om du lämnar det här värdet tomt publiceras alla virtuella datorer i resursgruppen.
   * **VMRESOURCEGROUP** – Namnet på resursgruppen som de virtuella datorerna ska registreras för.
   * **SUBSCRIPTIONID** – Lämna tomt. Prenumerations-ID för den nya virtuella datorn som ska publiceras. Om inget anges används prenumerationen i arbetsytan. När ett annat prenumerations-ID anges ska RunAs-kontot för det här Automation-kontot läggas till som deltagare för den här prenumerationen också.
   * **ALREADYONBOARDEDVM** – Namnet på den virtuella dator som manuellt publicerades till antingen lösningen för uppdateringar eller ändringsspårning.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - Namnet på den resursgrupp som den virtuella datorn tillhör.
   * **SOLUTIONTYPE** - Ange **uppdateringar** eller **ChangeTracking**.

   ![Runbookparametrarna Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Välj **OK** för att starta runbookjobbet.
1. Övervaka förlopp och eventuella fel på runbookjobbsidan.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från uppdateringshantering:

1. I logganalysarbetsytan tar du bort den virtuella datorn `MicrosoftDefaultScopeConfig-Updates`från den sparade sökningen efter scopekonfigurationen . Sparade sökningar hittar du under **Allmänt** på arbetsytan.
2. Ta bort [Log Analytics-agenten för Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller Log [Analytics-agenten för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera en virtuell Azure-dator manuellt.
> * Installera och uppdatera Azure-moduler som krävs.
> * Importera en runbook som registrerar virtuella Azure-datorer.
> * Starta runbooken som registrerar virtuella Azure-datorer automatiskt.

Följ den här länken om du vill veta mer om att schemalägga runbooks.

> [!div class="nextstepaction"]
> [Schemalägga runbooks](automation-schedules.md).
