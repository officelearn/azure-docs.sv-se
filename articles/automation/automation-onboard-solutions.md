---
title: Uppdatera uppdateringar, ändrings spårning och inventerings lösningar till Azure Automation
description: Lär dig att publicera uppdateringar och spårning av ändringar i Azure Automation.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81457628"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Uppdatera uppdateringar, ändrings spårning och inventerings lösningar till Azure Automation

I den här självstudien får du lära dig att automatiskt registrera lösningar för uppdatering, ändringsspårning och inventering för virtuella datorer till Azure Automation:

> [!div class="checklist"]
> * Publicera en virtuell Azure-dator
> * Aktivera lösningar
> * Installera och uppdatera moduler
> * Importera runbooken som ska publiceras
> * Starta runbook

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="onboard-an-azure-vm"></a>Publicera en virtuell Azure-dator

Det finns flera sätt att registrera datorer. Du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från bläddring av flera datorer, ](automation-onboard-solutions-from-browse.md) [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md) eller via en runbook. Den här självstudien går igenom aktivering av Uppdateringshantering via en runbook. För att publicera Azure Virtual Machines i skala måste en befintlig virtuell dator publiceras med lösningen Ändringsspårning eller Uppdateringshantering. I det här steget publicerar du en virtuell dator med Uppdateringshantering och Ändringsspårning.

### <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Med Ändringsspårning-och inventerings lösningarna kan du [Spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningar på en virtuell dator.

1. I Azure Portal väljer du **Automation-konton**och väljer sedan ditt Automation-konto i listan.
1. Välj **inventering** under **konfigurations hantering**.
1. Välj en befintlig Log Analytics arbets yta eller skapa en ny. 
1. Klicka på **Aktivera**.

    ![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. I det här steget aktiverar du lösningen för din virtuella datorn.

1. I ditt Automation-konto väljer du **uppdateringshantering** i avsnittet **uppdateringshantering** .
1. Den valda Log Analytics-arbetsytan är den arbets yta som användes i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering. Medan lösningen för uppdateringshantering installeras visas en blå banderoll. 

    ![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Välja virtuella Azure-datorer som ska hanteras

Nu när lösningarna är aktiverade kan du lägga till en virtuell Azure-dator till lösningarna.

1. Från ditt Automation-konto väljer du **ändrings spårning** under **konfigurations hantering**. 
2. På sidan ändrings spårning klickar du på **Lägg till virtuella Azure-datorer** för att lägga till den virtuella datorn.

3. Välj den virtuella datorn i listan och klicka på **Aktivera**. Den här åtgärden aktiverar Ändringsspårning-och inventerings lösningar för den virtuella datorn.

   ![Aktivera uppdateringslösning för virtuell dator](media/automation-onboard-solutions/enable-change-tracking.png)

4. När den virtuella datorns onboarding-meddelande har slutförts väljer du **uppdaterings hantering** under **uppdateringshantering**.

5. Välj **Lägg till virtuella Azure-datorer** för att lägga till den virtuella datorn.

6. Välj din virtuella dator från listan och välj **Aktivera**. Den här åtgärden aktiverar Uppdateringshantering-lösningen för den virtuella datorn.

   ![Aktivera uppdateringslösning för virtuell dator](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Om du inte väntar på att den andra lösningen ska slutföras när du aktiverar nästa lösning får du meddelandet:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Du måste uppdatera till de senaste Azure-modulerna och importera modulen [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) för att kunna automatisera lösnings onboarding.

## <a name="update-azure-modules"></a>Uppdatera Azure-moduler

1. I ditt Automation-konto väljer du **moduler** under **delade resurser**. 
2. Välj **Uppdatera Azure-moduler** för att uppdatera Azure-moduler till den senaste versionen. 
3. Klicka på **Ja** om du vill uppdatera alla befintliga Azure-moduler till den senaste versionen.

![Uppdatera moduler](media/automation-onboard-solutions/update-modules.png) A

### <a name="install-azoperationalinsights-module"></a>Installera AZ. OperationalInsights-modulen

1. I Automation-kontot väljer du **moduler** under **delade resurser**. 
2. Välj **Bläddra i Galleri** för att öppna modulens Galleri. 
3. Sök efter AZ. OperationalInsights och importera modulen till Automation-kontot.

![Importera modulen OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importera runbooken som ska publiceras

1. I ditt Automation-konto väljer du **Runbooks** under **process automatisering**.
1. Välj **Sök i galleri**.
1. Sök efter `update and change tracking`.
3. Välj Runbook och klicka på **Importera** på sidan Visa källa. 
4. Klicka på **OK** för att importera runbooken till Automation-kontot.

   ![Importera runbook som ska publiceras](media/automation-onboard-solutions/import-from-gallery.png)

6. På sidan Runbook klickar du på **Redigera**och väljer sedan **publicera**. 
7. I fönstret publicera Runbook klickar du på **Ja** för att publicera runbooken.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha registrerat antingen ändrings spårning eller uppdaterings lösningar till en virtuell Azure-dator för att starta denna Runbook. Detta kräver en befintlig virtuell dator och en resursgrupp med en lösning som har publicerats för parametrar.

1. Öppna runbooken **Enable-MultipleSolution** .

   ![Runbooks med flera lösningar](media/automation-onboard-solutions/runbook-overview.png)

1. Klicka på startknappen och ange följande värden för parametrar.

   * **VMNAME** – Lämna tomt. Namnet på en befintlig virtuell dator som ska publiceras för lösningen för uppdatering eller ändringsspårning. Om du lämnar det här värdet tomt publiceras alla virtuella datorer i resursgruppen.
   * **VMRESOURCEGROUP** – Namnet på resursgruppen som de virtuella datorerna ska registreras för.
   * **SUBSCRIPTIONID** – Lämna tomt. Prenumerations-ID för den nya virtuella datorn som ska publiceras. Om inget anges används prenumerationen i arbetsytan. När ett annat prenumerations-ID anges ska RunAs-kontot för det här Automation-kontot läggas till som deltagare för den här prenumerationen också.
   * **ALREADYONBOARDEDVM** – Namnet på den virtuella dator som manuellt publicerades till antingen lösningen för uppdateringar eller ändringsspårning.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – namnet på den resurs grupp som den virtuella datorn tillhör.
   * **SOLUTIONTYPE** – ange **uppdateringar** eller **ChangeTracking**.

   ![Runbookparametrarna Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Välj **OK** för att starta runbookjobbet.
1. Övervaka förlopp och eventuella fel på runbookjobbsidan.

## <a name="clean-up-resources"></a>Rensa resurser

Så här tar du bort en virtuell dator från Uppdateringshantering:

1. I arbets ytan Log Analytics tar du bort den virtuella datorn från den sparade sökningen för omfattnings konfigurationen `MicrosoftDefaultScopeConfig-Updates`. Sparade sökningar hittar du under **Allmänt** på arbets ytan.
2. Ta bort [Log Analytics-agenten för Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) eller [log Analyticss agent för Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

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
