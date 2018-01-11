---
title: "Publicera uppdatering och ändringsspårning lösningar till Azure Automation | Microsoft Docs"
description: "Lär dig att publicera uppdatering och ändringsspårning lösningar till Azure Automation."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: eamono
ms.openlocfilehash: 25ad5e04c96aab9dddd39cdb4d33bd8411817e8e
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Publicera uppdatering och ändringsspårning lösningar till Azure Automation

I kursen får du lära dig hur du automatiskt publicera uppdatering, ändringsspårning och lager lösningar för virtuella datorer till Azure Automation:

> [!div class="checklist"]
> * Publicera en Azure VM
> * Aktivera lösningar
> * Installera och uppdatera moduler
> * Importera runbook onboarding
> * Starta runbook

## <a name="prerequisites"></a>Förutsättningar

Den här kursen krävs följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) att hantera datorer.
* En [virtuella](../virtual-machines/windows/quick-create-portal.md) publicera.

## <a name="onboard-an-azure-vm"></a>Publicera en Azure VM

Att publicera Azure Virtual Machines automatiskt, måste en befintlig virtuell dator publiceras med ändringsspårning eller uppdateringshantering. I det här steget kan du publicera en virtuell dator med hantering av uppdateringar och ändringsspårning.

### <a name="enable-change-tracking-and-inventory"></a>Aktivera spårning av ändringar och inventering

Ändringsspårning och lager lösningen ger möjlighet att [spåra ändringar](automation-vm-change-tracking.md) och [lager](automation-vm-inventory.md) på virtuella datorer. I det här steget kan aktivera du lösning på en virtuell dator.

1. I den vänstra menyn, Välj **Automation-konton**, och välj sedan ditt automation-konto i listan.
1. Välj **inventering** under **KONFIGURATIONSHANTERING**.
1. Välj en befintlig logganalys-arbetsyta eller skapa en ny. Klicka på den **aktivera** knappen.

![Publicera Update-lösning](media/automation-onboard-solutions/inventory-onboard.png)

När spårning och lager lösning onboarding ändringsmeddelanden är klar klickar du på **uppdateringshantering** under **KONFIGURATIONSHANTERING**.

### <a name="enable-update-management"></a>Aktivera uppdateringshantering

Uppdatera hanteringslösningen kan du hantera uppdateringar och korrigeringsfiler för virtuella datorerna i Windows Azure. Du kan kontrollera statusen för uppdateringar, schemalägga installationen av obligatoriska uppdateringar och granska agentdistributionresultaten att verifiera uppdateringar har använts på den virtuella datorn. I det här steget kan aktivera du lösningen för den virtuella datorn.

1. Välj ditt Automation-konto **uppdateringshantering** under **UPPDATERINGSHANTERING**.
1. Log analytics-arbetsyta som valts är i samma arbetsyta som används i föregående steg. Klicka på **aktivera** publicera hanteringslösning för uppdateringen.

![Publicera Update-lösning](media/automation-onboard-solutions/update-onboard.png)

Medan hanteringslösning uppdateringen installeras, visas en blå banderoll. När lösningen är aktiverat väljer **ändringsspårning** under **KONFIGURATIONSHANTERING** att gå till nästa steg.

### <a name="select-azure-vm-to-be-managed"></a>Välj Azure VM som ska hanteras

Nu när lösningarna är aktiverade kan du lägga till en Azure VM ska publiceras till dessa lösningar.

1. Från ditt Automation-konto på den **ändringsspårning** väljer **+ Lägg till Azure VM** att lägga till den virtuella datorn.

1. Välj den virtuella datorn i listan och välj **aktivera**. Den här åtgärden gör att ändra tacking och lager lösning för den virtuella datorn.

   ![Aktivera uppdatering lösning för vm](media/automation-onboard-solutions/enable-change-tracking.png)

1. När onboarding-meddelande för virtuell har slutförts ditt Automation-kontot väljer **uppdateringshantering** under **UPPDATERINGSHANTERING**.

1. Välj **+ Lägg till Azure VM** att lägga till den virtuella datorn.

1. Välj den virtuella datorn i listan och välj **aktivera**. Den här åtgärden aktiverar hanteringslösning uppdatering för den virtuella datorn.

   ![Aktivera uppdatering lösning för vm](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Om du inte vänta tills andra lösningen att slutföra, när du aktiverar nästa lösningen får du ett meddelande om: *Installation av en annan lösning pågår på den här eller en annan virtuell dator. När installationen är klar knappen Aktivera har aktiverats och du kan begära installation av lösningen på denna virtuella dator.*

## <a name="install-and-update-modules"></a>Installera och uppdatera moduler

Det krävs för att uppdatera till de senaste Azure modulerna och importera `AzureRM.OperationalInsights` har automatisera lösning onboarding.

## <a name="update-azure-modules"></a>Uppdatera Azure moduler

Välj ditt Automation-konto **moduler** under **delade resurser**. Välj **uppdatera Azure moduler** att uppdatera Azure moduler till den senaste versionen. Välj **Ja** på uppmaningen att uppdatera alla befintliga Azure moduler till den senaste versionen.

![Uppdatera moduler](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Installera modulen AzureRM.OperationalInsights

Från den **moduler** väljer **Bläddra galleriet** öppna i modulgalleriet. Sök efter AzureRM.OperationalInsights och importera den här modulen till Automation-kontot.

![Importera OperationalInsights-modulen](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importera runbook onboarding

1. Välj på ditt Automation-konto **Runbooks** under den **PROCESSAUTOMATISERING**.
1. Välj **Bläddra galleriet**.
1. Sök efter **uppdatera och ändringsspårning**runbook markerar du och **importera** på den **Visa källa** sidan. Välj **OK** att importera runbook i Automation-kontot.

  ![Importera onboarding runbook](media/automation-onboard-solutions/import-from-gallery.png)

1. På den **Runbook** väljer **redigera**och välj **publicera**. På den **publicera Runbook** markerar **Ja** publicera en runbook.

## <a name="start-the-runbook"></a>Starta runbook

Du måste ha publicerats så ändringsspårning eller uppdatera lösningar till en Azure VM för att starta denna runbook. Detta kräver en befintlig virtuell dator och en resursgrupp med lösning publicerats så för parametrar.

1. Öppna aktivera MultipleSolution runbook.

   ![Flera lösning runbooks](media/automation-onboard-solutions/runbook-overview.png)

1. Klicka på Start och ange följande värden för parametrar.

   * **VMNAME** -lämna tomt. Namnet på en befintlig virtuell dator ska publiceras för update eller ändringsspårning lösning. Alla virtuella datorer i resursgruppen har publicerats så genom att lämna det här värdet tomt.
   * **VMRESOURCEGROUP** -namnet på resursgruppen för de virtuella datorerna till att publicera.
   * **SUBSCRIPTIONID** -lämna tomt. Prenumerations-ID för den nya virtuella datorn till att publicera. Om inget anges används prenumerationen i arbetsytan. När ett annat prenumerations-ID anges ska RunAs-konto för det här automatiseringskontot läggas till som en deltagare för den här prenumerationen också.
   * **ALREADYONBOARDEDVM** -namnet på den virtuella datorn som manuellt har publicerats så att den uppdateringar eller ChangeTracking lösning.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** -namnet på resursgruppen som den virtuella datorn är medlem i.
   * **SOLUTIONTYPE** -ange **uppdateringar** eller **ChangeTracking**

   ![Aktivera MultipleSolution runbook-parametrar](media/automation-onboard-solutions/runbook-parameters.png)

1. Välj **OK** att starta runbook-jobbet.
1. Övervaka förloppet och eventuella fel på sidan för runbook-jobb.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Publicera ett Azure-datorn manuellt.
> * Installera och uppdatera Azure moduler som krävs.
> * Importera en runbook som onboards virtuella Azure-datorer.
> * Starta runbook den onboards virtuella Azure-datorer automatiskt.

Följ länken för mer information om schemaläggning av runbooks.

> [!div class="nextstepaction"]
> [Schemalägga runbooks](automation-schedules.md).
