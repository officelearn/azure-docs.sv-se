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
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Publicera uppdatering och ändringsspårning lösningar till Azure Automation

I kursen får du lära dig hur du automatiskt publicera uppdatering, ändringsspårning och lager lösningar för virtuella datorer till Azure Automation:

> [!div class="checklist"]
> * Publicera ett Azure-datorn manuellt.
> * Installera och uppdatera Azure moduler som krävs.
> * Importera en runbook som onboards virtuella Azure-datorer.
> * Starta runbook den onboards virtuella Azure-datorer automatiskt.

## <a name="prerequisites"></a>Krav

Den här kursen krävs följande.
+ En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Automation-konto](automation-offering-get-started.md) att hantera datorer.

## <a name="onboard-an-azure-virtual-machine-manually"></a>Publicera en virtuell Azure-dator manuellt

1.  Öppna Automation-kontot.
2.  Klicka på sidan inventering.
![Publicera lager-lösning](media/automation-onboard-solutions/inventory-onboard.png)
3.  Välj en befintlig logganalys-arbetsyta eller skapa en ny. Klicka på Aktivera.
4.  När spårning och lager lösning onboarding ändringsmeddelanden är klar klickar du på sidan hantering av uppdateringar.
![Publicera Update-lösning](media/automation-onboard-solutions/update-onboard.png)
4.  Klicka på Aktivera som onboards update-lösning.
5.  När uppdateringsmeddelande för onboarding av lösningen är klar klickar du på sidan ändringsspårning.
![Publicera ändringsspårning](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Klicka på knappen Lägg till virtuella Azure-datorn.
![Välj VM för ändringsspårning](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Välj en virtuell dator i Azure och klicka på Aktivera för att publicera till ändra inventering och spårning av lösningen.
8.  När onboarding-meddelande för virtuell är klar klickar du på sidan hantering av uppdateringar.
![Publicera virtuell dator för hantering av uppdateringar](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Klicka på knappen Lägg till virtuella Azure-datorn.
![Välj VM för uppdateringshantering](media/automation-onboard-solutions/enable-update.png)
10.  Välj en virtuell dator i Azure och klicka på Aktivera för att publicera till hanteringslösningen uppdateringen.

## <a name="install-and-update-required-azure-modules"></a>Installera och uppdatera Azure moduler som krävs

Det krävs för att uppdatera till de senaste Azure modulerna och importera AzureRM.OperationalInsights för att automatisera har lösningen onboarding.
1.  Klicka på sidan moduler.
![Uppdatera moduler](media/automation-onboard-solutions/update-modules.png)
2.  Klicka på knappen Uppdatera Azure moduler som uppdaterar till den senaste versionen. Vänta tills ska uppdateras.
3.  Klicka på Bläddra galleri om du vill öppna i modulgalleriet.
![Importera OperationalInsights-modulen](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  Sök efter AzureRM.OperationalInsights och importera den här modulen till Automation-kontot.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Importera en runbook som onboards-lösningar till virtuella Azure-datorer

1.  Klicka på sidan Runbooks under kategorin ”PROCESSAUTOMATISERING”.
2.  Klicka på knappen ”Bläddra galleriet”.
3.  Sök efter ”uppdatera och ändringsspårning” och importera runbook i Automation-kontot.
![Importera onboarding runbook](media/automation-onboard-solutions/import-from-gallery.png)
4.  Klicka på ”Edit” visa källan Runbook och klicka på knappen ”Publicera”.
![Importera onboarding runbook](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Starta runbook den onboards virtuella Azure-datorer automatiskt

Du måste ha publicerats så ändringsspårning eller uppdatera lösningar till en Azure VM för att starta denna runbook. Detta kräver en befintlig virtuell dator och en resursgrupp med lösning publicerats så för parametrar.
1.  Öppna aktivera MultipleSolution runbook.
![Flera lösning runbooks](media/automation-onboard-solutions/runbook-overview.png)
2.  Klicka på Start och ange följande värden för parametrar.
    *   VMNAME. Namnet på en befintlig virtuell dator ska publiceras för update eller ändringsspårning lösning. Lämna tomt och alla virtuella datorer i resursgruppen har publicerats så.
    *   VMRESOURCEGROUP. Namnet på resursgruppen som den virtuella datorn är medlem i.
    *   PRENUMERATIONS-ID. Prenumerations-ID som den nya virtuella datorn ska publiceras är placerad. Lämna tomt och prenumeration på arbetsytan används. När ett annat prenumerations-ID anges ska RunAs-konto för det här automatiseringskontot läggas till som en deltagare för den här prenumerationen också.
    *   ALREADYONBOARDEDVM. Namnet på den virtuella datorn som manuellt har publicerats så att den uppdateringar eller ChangeTracking lösning.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. Namnet på resursgruppen som den virtuella datorn är medlem i.
    *   SOLUTIONTYPE. Ange ”uppdaterar” eller ”ChangeTracking”
    
    ![Flera lösning runbook-parametrar](media/automation-onboard-solutions/runbook-parameters.png)
3.  Klicka på OK om du vill starta runbook-jobbet.
4.  Övervaka förloppet och eventuella fel på sidan för runbook-jobb.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [schemaläggning runbooks](automation-schedules.md).