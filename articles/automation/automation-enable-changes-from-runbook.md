---
title: Aktivera Azure Automation Ändringsspårning och inventering från en Runbook
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning och inventering från en Runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 014442dee1be23a189e22a505abf86050601b2aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826750"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Aktivera Ändringsspårning och inventering från en runbook

I den här artikeln beskrivs hur du kan använda en Runbook för att aktivera [ändringsspårning-och inventerings](change-tracking.md) funktionen för virtuella datorer i din miljö. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering. 

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering 

1. I Azure Portal väljer du **Automation-konton**och väljer sedan ditt Automation-konto i listan.
1. Välj **inventering** under **konfigurations hantering**.
1. Välj en befintlig Log Analytics arbets yta eller skapa en ny. 
1. Klicka på **Aktivera**.

    ![Aktivera Ändringsspårning och inventering](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Välj den virtuella Azure-dator som ska hanteras

Med Ändringsspårning och inventering aktive rad kan du lägga till en virtuell Azure-dator för hantering via funktionen.

1. Från ditt Automation-konto väljer du **ändrings spårning** eller **inventering** under **konfigurations hantering**.

2. Klicka på **Lägg till virtuella Azure-datorer** för att lägga till den virtuella datorn.

3. Välj den virtuella datorn i listan och klicka på **Aktivera**. Den här åtgärden aktiverar Ändringsspårning och inventering för den virtuella datorn.

   ![Aktivera Ändringsspårning och inventering för virtuell dator](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Om du försöker aktivera en annan funktion innan installationen av Ändringsspårning och inventeringen har slutförts visas följande meddelande:`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Du måste uppdatera till de senaste Azure-modulerna och importera modulen [AZ. OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) för att kunna aktivera ändringsspårning och inventering för den virtuella datorn.

1. I ditt Automation-konto väljer du **moduler** under **delade resurser**. 
2. Välj **Uppdatera Azure-moduler** för att uppdatera Azure-moduler till den senaste versionen. 
3. Klicka på **Ja** om du vill uppdatera alla befintliga Azure-moduler till den senaste versionen.

    ![Uppdatera moduler](media/automation-enable-changes-from-runbook/update-modules.png)

4. Återgå till **moduler** under **delade resurser**. 
5. Välj **Bläddra i Galleri** för att öppna modulens Galleri. 
6. Sök efter AZ. OperationalInsights och importera modulen till Automation-kontot.

    ![Importera modulen OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importera en Runbook för att aktivera Ändringsspårning och inventering

1. I ditt Automation-konto väljer du **Runbooks** under **process automatisering**.
2. Välj **Sök i galleri**.
3. Sök efter `update and change tracking`.
4. Välj Runbook och klicka på **Importera** på sidan Visa källa. 
5. Klicka på **OK** för att importera runbooken till Automation-kontot.

   ![Importera Runbook för installation](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. På sidan Runbook klickar du på **Redigera**och väljer sedan **publicera**. 
7. I fönstret publicera Runbook klickar du på **Ja** för att publicera runbooken.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha aktiverat Ändringsspårning och inventering för att en virtuell Azure-dator ska kunna starta denna Runbook. Den kräver en befintlig virtuell dator och resurs grupp med funktionen aktive rad för parametrar.

1. Öppna runbooken **Enable-MultipleSolution** .

   ![Runbooks med flera lösningar](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Klicka på Start-knappen och ange parameter värden i följande fält:

   * **VMNAME** – namnet på en befintlig virtuell dator som ska läggas till i ändringsspårning och inventering. Lämna fältet tomt om du vill lägga till alla virtuella datorer i resurs gruppen.
   * **VMRESOURCEGROUP** – namnet på resurs gruppen för de virtuella datorer som ska aktive ras.
   * **SUBSCRIPTIONID** -PRENUMERATIONS-ID för den nya virtuella dator som ska aktive ras. Lämna fältet tomt om du vill använda arbets ytans prenumeration. När du använder ett annat prenumerations-ID lägger du till kör som-kontot för ditt Automation-konto som deltagare för prenumerationen.
   * **ALREADYONBOARDEDVM** – namnet på den virtuella dator som redan har Aktiver ATS manuellt för ändringar.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – namnet på den resurs grupp som den virtuella datorn tillhör.
   * **SOLUTIONTYPE** – ange **ChangeTracking**.

   ![Runbookparametrarna Enable-MultipleSolution](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Välj **OK** för att starta runbookjobbet.
1. Övervaka förlopp och eventuella fel på runbookjobbsidan.

## <a name="next-steps"></a>Nästa steg

* Information om hur du schemalägger en Runbook finns i [hantera scheman i Azure Automation](shared-resources/schedules.md).
* Information om hur du arbetar med funktionen finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om konfiguration av omfattningar finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).
* Information om hur du använder funktionen för att identifiera program vara som är installerad i din miljö finns i [identifiera vilken program vara som är installerad på dina virtuella datorer](automation-tutorial-installed-software.md).
* Om du inte vill integrera ditt Automation-konto med en Log Analytics arbets yta när du aktiverar funktionen, se [ta bort länken till arbets ytan från Automation-kontot](automation-unlink-workspace-change-tracking.md).
* När du har distribuerat ändringar av virtuella datorer kan du ta bort dem enligt beskrivningen i [ta bort virtuella datorer från ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md).
* Information om hur du felsöker allmänna problem med funktionen finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).