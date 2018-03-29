---
title: Publicera uppdateringar och spårning av ändringar i Azure Automation
description: Lär dig att publicera uppdateringar och spårning av ändringar i Azure Automation.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.topic: tutorial
ms.date: 03/16/2018
ms.custom: mvc
ms.openlocfilehash: 97db493b77483d68860c6124f1063bc4a743c3be
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Publicera uppdateringar och spårning av ändringar i Azure Automation

I den här självstudien får du lära dig att automatiskt registrera lösningar för uppdatering, ändringsspårning och inventering för virtuella datorer till Azure Automation:

> [!div class="checklist"]
> * Publicera en virtuell Azure-dator
> * Aktivera lösningar
> * Installera och uppdatera moduler
> * Importera runbooken som ska publiceras
> * Starta runbook

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md) som du vill publicera.

## <a name="onboard-an-azure-vm"></a>Publicera en virtuell Azure-dator

Det finns flera sätt att registrera datorer. Du kan registrera lösningen [från en virtuell dator](automation-onboard-solutions-from-vm.md), [från ditt Automation-konto](automation-onboard-solutions-from-automation-account.md) eller via en runbook. Den här självstudien går igenom aktivering av Uppdateringshantering via en runbook. För att publicera Azure Virtual Machines i skala måste en befintlig virtuell dator publiceras med lösningen Ändringsspårning eller Uppdateringshantering. I det här steget publicerar du en virtuell dator med Uppdateringshantering och Ändringsspårning.

### <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

Lösningen Ändringsspårning och inventering ger dig möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [inventering](automation-vm-inventory.md) på dina virtuella datorer. I det här steget aktiverar du lösningen för en virtuell dator.

1. På menyn till vänster väljer du **Automation-konton** och sedan ditt Automation-konto på listan.
1. Välj **Inventering** under **Konfigurationshantering**.
1. Välj en befintlig Log Analytics-arbetsyta eller skapa en ny. Klicka på knappen **Aktivera**.

![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions/inventory-onboard.png)

När meddelandet för lösning för publicering av ändringsspårning och inventering har slutförts klickar du på **Uppdateringshantering** under **Konfigurationshantering**.

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Med uppdateringshanteringslösningen kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer i Windows. Du kan utvärdera statusen för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att verifiera att uppdateringarna har tillämpats på den virtuella datorn. I det här steget aktiverar du lösningen för din virtuella datorn.

1. Från ditt Automation-konto väljer du **Hantering av uppdateringar** under **Hantering av uppdateringar**.
1. Log Analytics-arbetsytan som är vald är samma arbetsyta som används i föregående steg. Klicka på **Aktivera** för att publicera lösningen för uppdateringshantering.

![Publicera lösning för uppdateringshantering](media/automation-onboard-solutions/update-onboard.png)

Medan lösningen för uppdateringshantering installeras visas en blå banderoll. När lösningen är aktiverad väljer du **Ändringsspårning** under **Konfigurationshantering** för att gå till nästa steg.

### <a name="select-azure-vm-to-be-managed"></a>Välja virtuella Azure-datorer som ska hanteras

Nu när lösningarna är aktiverade kan du lägga till en virtuell Azure-dator till lösningarna.

1. Från ditt Automation-konto går du till sidan **Ändringsspårning**, väljer **+ Lägg till virtuell Azure-dator** för att lägga till din virtuella dator.

1. Välj din virtuella dator från listan och välj **Aktivera**. Den här åtgärden aktiverar lösningen Ändringsspårning och inventering för den virtuella datorn.

   ![Aktivera uppdateringslösning för virtuell dator](media/automation-onboard-solutions/enable-change-tracking.png)

1. När meddelandet om VM-publiceringen är klart väljer du **Hantering av uppdateringar** under **Hantering av uppdateringar** från ditt Automation-konto.

1. Välj **+ Lägg till virtuell Azure-dator** för att lägga till dina virtuella datorer.

1. Välj din virtuella dator från listan och välj **Aktivera**. Den här åtgärden aktiverar uppdateringshanteringslösningen för den virtuella datorn.

   ![Aktivera uppdateringslösning för virtuell dator](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Om du inte väntar tills den andra lösningen slutförs och aktiverar nästa lösning ser du meddelandet: *Installation of another solution is in progress on this or a different virtual machine (Installation av en annan lösning pågår på den här eller någon annan virtuell dator). When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine (När installationen är klar aktiveras knappen Aktivera, och du kan begära installation av lösningen på den här virtuella datorn).*

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Det krävs att du uppdaterar till de senaste Azure-modulerna och importerar `AzureRM.OperationalInsights` för att automatisera publiceringen av lösningen.

## <a name="update-azure-modules"></a>Uppdatera Azure-moduler

Från ditt Automation-konto väljer du **Moduler** under **Delade resurser**. Välj **Uppdatera Azure-moduler** för att uppdatera Azure-moduler till den senaste versionen. Välj **Ja** i uppmaningen för att uppdatera alla befintliga Azure-moduler till den senaste versionen.

![Uppdatera moduler](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Installera modulen AzureRM.OperationalInsights

På sidan **Moduler** väljer du **Sök i galleri** för att öppna modulgalleriet. Sök efter AzureRM.OperationalInsights och importera modulen till Automation-kontot.

![Importera modulen OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importera runbooken som ska publiceras

1. Från ditt Automation-konto väljer du **Runbooks** under **Processautomatisering**.
1. Välj **Sök i galleri**.
1. Sök efter **uppdateringar och ändringsspårning**, klicka på runbooken och välj **Importera** på sidan **Visa källa**. Välj **OK** för att importera runbooken till Automation-kontot.

  ![Importera runbook som ska publiceras](media/automation-onboard-solutions/import-from-gallery.png)

1. På sidan **Runbook** väljer du **Redigera** och sedan **Publicera**. I dialogrutan **Publicera runbook** väljer du **Ja** för att publicera runbooken.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha publicerat antingen ändringsspårning eller uppdateringslösningar till en virtuell Azure-dator för att starta denna runbook. Detta kräver en befintlig virtuell dator och en resursgrupp med en lösning som har publicerats för parametrar.

1. Öppna runbooken Enable-MultipleSolution.

   ![Runbooks med flera lösningar](media/automation-onboard-solutions/runbook-overview.png)

1. Klicka på startknappen och ange följande värden för parametrar.

   * **VMNAME** – Lämna tomt. Namnet på en befintlig virtuell dator som ska publiceras för lösningen för uppdatering eller ändringsspårning. Om du lämnar det här värdet tomt publiceras alla virtuella datorer i resursgruppen.
   * **VMRESOURCEGROUP** – Namnet på resursgruppen som de virtuella datorerna ska registreras för.
   * **SUBSCRIPTIONID** – Lämna tomt. Prenumerations-ID för den nya virtuella datorn som ska publiceras. Om inget anges används prenumerationen i arbetsytan. När ett annat prenumerations-ID anges ska RunAs-kontot för det här Automation-kontot läggas till som deltagare för den här prenumerationen också.
   * **ALREADYONBOARDEDVM** – Namnet på den virtuella dator som manuellt publicerades till antingen lösningen för uppdateringar eller ändringsspårning.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – Namnet på resursgruppen som den virtuella datorn är medlem av.
   * **SOLUTIONTYPE** – Ange **Uppdateringar** eller **ChangeTracking**

   ![Runbookparametrarna Enable-MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Välj **OK** för att starta runbookjobbet.
1. Övervaka förlopp och eventuella fel på runbookjobbsidan.

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
