---
title: Använd analys av programmets ändringen i Azure Monitor för att hitta web app-problemen | Microsoft Docs
description: Använd analys av programmets ändringen i Azure Monitor för att felsöka programfel på live-webbplatser i Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 2a31131b662d01f9841a3f1c5b0a6c459a117e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075370"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Använda programmet ändringen analys (förhandsversion) i Azure Monitor

När ett problem med live eller avbrott inträffar, är det viktigt att snabbt avgöra den bakomliggande orsaken. Standard övervakningslösningar kan varna dig om ett problem. De kan även tyda komponent som misslyckas. Men den här aviseringen alltid omedelbart beskriver inte orsaken till felet. Du vet att din webbplats arbetat fem minuter sedan, och nu är den bruten. Vad som ändrats under de senaste fem minuterna? Det här är frågan som analys av programmets ändringen har utformats för att besvara i Azure Monitor. 

Att skapa på kraften hos [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), ändra analys ger insikter om dina ändringar för Azure-program att öka observability och minska MTTR (medelvärde tid att reparera).

> [!IMPORTANT]
> Ändra Analysis förhandsvisas just nu. Den här förhandsversionen tillhandahålls utan ett servicenivåavtal. Den här versionen rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte finns stöd eller kan ha begränsad funktionalitet. Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Översikt

Ändra Analysis identifierar olika typer av ändringar från lager för hanteringsinfrastruktur hela vägen till programdistributionen. Det är en prenumerationsnivå Azure-resursprovidern som kontrollerar resursändringar i prenumerationen. Ändra analys ger data för olika diagnostiska verktyg för att hjälpa användarna att förstå vad ändras kan ha orsakat problem.

Följande diagram illustrerar arkitekturen i ändra analys:

![Arkitekturdiagram över hur ändra Analysis hämtar sammanställning och gör det tillgängligt för klientverktyg](./media/change-analysis/overview.png)

För närvarande ändra Analysis är integrerad i den **diagnostisera och lösa problem** användarupplevelsen i App Service-webbapp. Om du vill aktivera identifiering av ändring av och visa ändringar i webbapp, finns det *ändra analys för Web Apps-funktionen* senare i den här artikeln.

### <a name="azure-resource-manager-deployment-changes"></a>Distributionsändringar av Azure Resource Manager

Med hjälp av [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), ändra analys ger en historisk post av hur Azure-resurser som är värdar för ditt program har ändrats över tid. Ändra analys kan identifiera, till exempel ändringar i IP-konfigurationsregler, hanterade identiteter och SSL-inställningar. Så om en tagg läggs till i en webbapp, ändra analys återspeglar ändringen. Den här informationen är tillgänglig så länge som den `Microsoft.ChangeAnalysis` resursprovidern är aktiverat i Azure-prenumeration.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Ändringar i web app-distribution och konfiguration

Ändra Analysis samlar in tillståndet för distribution och konfiguration för ett program var fjärde timme. Det kan till exempel identifiera ändringar i miljövariablerna som programmet. Verktyget beräknar skillnaderna och visar vad som har ändrats. Till skillnad från Resource Manager-ändringar kanske inte kod ändra distributionsinformation tillgängligt direkt i verktyget. Om du vill visa de senaste ändringarna i ändra analys, Välj **genomsökning ändras nu**.

![Skärmbild av knappen ”genomsökning ändras nu”](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar

Ändringar av resursberoenden kan också orsaka problem i en webbapp. Till exempel om anrop till en webbapp i en Redis-cache kan påverka Redis-cache SKU web app prestanda. För att identifiera ändringar i beroenden kontrollerar ändra Analysis webbappens DNS-post. På så sätt kan identifierar den ändringar i alla appkomponenter som kan orsaka problem.

## <a name="change-analysis-for-the-web-apps-feature"></a>Ändra analys för funktionen Web Apps

I Azure Monitor kan ändra analys för närvarande är inbyggd i självbetjäningen **diagnostisera och lösa problem** upplevelse. Få åtkomst till den här funktionen från den **översikt** för App Service-programmet.

![Skärmbild av knappen ”översikt” och ”diagnostisera och lösa problem” knappen](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Aktivera ändring analyser i diagnostisera och lösa problem som verktyget

1. Välj **tillgänglighet och prestanda**.

    ![Skärmbild av ”tillgänglighet och prestanda” felsökningsalternativ](./media/change-analysis/availability-and-performance.png)

1. Välj **programkrascher**.

   ![Skärmbild av knappen ”programmet kraschar”](./media/change-analysis/application-crashes-tile.png)

1. Välj för att aktivera ändringen Analysis **aktivera nu**.

   ![Skärmbild av ”programmet kraschar” alternativ](./media/change-analysis/application-crashes.png)

1. Om du vill dra nytta av den fullständiga funktionaliteten för analys av ändringen, aktivera **ändra Analysis**, **söka efter ändringar i koden**, och **alltid på**. Välj sedan **Spara**.

    ![Skärmbild av användargränssnittet ”aktivera ändringen Analysis”](./media/change-analysis/change-analysis-on.png)

    - Aktivera **ändra Analysis** att identifiera resursnivå ändringar. 
    - Aktivera **söka efter ändringar i koden** Se filer för distribution och ändringar i platskonfigurationen. 
    - Aktivera **alltid på** optimera prestandan för genomsökning av ändringen. Men tänk på att den här inställningen kan resultera i ytterligare debiterade avgifterna.

1. För att komma åt ändringen analys, Välj **diagnostisera och lösa problem** > **tillgänglighet och prestanda** > **programmet kraschar**. Ett diagram som sammanfattar typer av ändringar över tid tillsammans med information om dessa ändringar visas:

     ![Skärmbild av ändringen diff-vy](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Aktivera ändring analyser i stor skala

Om din prenumeration innehåller ett stort antal webbappar, är aktivera tjänsten på nivån för webbappen ineffektiv. I det här fallet, följ instruktionerna för alternativ.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registrera resursprovidern ändra analys för din prenumeration

1. Registrera funktionsflagga ändringen analys (förhandsversion). Eftersom funktionsflagga är i förhandsversion, måste du registrera den så att den visas i din prenumeration:

   1. Öppna [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Skärmbild av ändringen Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Ändra typen till shell **PowerShell**.

      ![Skärmbild av ändringen Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Kör följande PowerShell-kommando:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```
    
1. Registrera resursprovidern ändra analys för prenumerationen.

   - Gå till **prenumerationer**, och välj den prenumeration som du vill aktivera i tjänsten ändras. Välj sedan resursprovidrar:

        ![Skärmbild som visar hur du registrerar resursprovidern ändra analys](./media/change-analysis/register-rp.png)

       - Välj **Microsoft.ChangeAnalysis**. Välj sedan längst ned på sidan **registrera**.

       - När resursprovidern har aktiverats kan ange du en dold tagg på web-app för att identifiera ändringar på nivån för distributionen. Om du vill ange en tagg för dolda, följer du instruktionerna under **det gick inte att hämta ändringen analysinformation**.

   - Du kan också använda ett PowerShell-skript för att registrera resursprovidern:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration
    
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Om du vill använda PowerShell för att ange en dold tagg för en webbapp, kör du följande kommando:
    
        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > När du har lagt till dolda taggen kan du fortfarande behöva vänta upp till fyra timmar innan du börjar ändringar. Resultaten fördröjs eftersom ändringen Analysis söker igenom din webbapp bara var fjärde timme. 4 timmars schemat begränsar genomsökningens prestanda påverkas.

## <a name="next-steps"></a>Nästa steg

- Övervaka App Service effektivare genom [att aktivera Application Insights-funktioner](azure-web-apps.md) i Azure Monitor.
- Läs mer om [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), vilket hjälper till att power ändringen analys.
