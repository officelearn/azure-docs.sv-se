---
title: Använd program ändrings analys i Azure Monitor för att hitta problem med webb program | Microsoft Docs
description: Använd program ändrings analys i Azure Monitor för att felsöka program problem på Live-webbplatser på Azure App Service.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 3efa26a1eaea8f522d9717efb0de0ec8e1682e0e
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875117"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Använda program ändrings analys (för hands version) i Azure Monitor

När ett problem med en aktiv webbplats eller ett avbrott inträffar kan du snabbt avgöra rotor saken är kritiskt. Vanliga övervaknings lösningar kan varna dig för problem. De kan till och med Visa vilken komponent som Miss fungerar. Men aviseringen kan inte alltid direkt förklara orsaken till felet. Du vet att webbplatsen arbetade fem minuter sedan och att den nu är bruten. Vad har ändrats under de senaste fem minuterna? Detta är den fråga som program ändrings analysen har utformats för att svara i Azure Monitor.

Genom att bygga vidare på kraften i [Azures resurs diagram](https://docs.microsoft.com/azure/governance/resource-graph/overview)får du insikter om dina Azure-programändringar för att öka den observerade och minska MTTR (genomsnittlig tid för reparation).

> [!IMPORTANT]
> Ändrings analys är för närvarande en för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal. Den här versionen rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsade funktioner. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="overview"></a>Översikt

Ändrings analysen identifierar olika typer av ändringar, från infrastruktur skiktet hela vägen till program distributionen. Det är en Azure-adressresurs på prenumerations nivå som kontrollerar resurs ändringar i prenumerationen. Med ändrings analys får du data för olika diagnostiska verktyg som hjälper användarna att förstå vilka ändringar som kan ha orsakat problem.

Följande diagram illustrerar arkitekturen för ändrings analys:

![Arkitektur diagram över hur ändrings analys får ändra data och ger den till klient verktyg](./media/change-analysis/overview.png)

För närvarande är ändrings analys integrerad i lösningen för att **diagnostisera och lösa problem** i App Service-webbappen. Om du vill aktivera ändrings identifiering och Visa ändringar i webbappen läser du avsnittet *om ändrings analyser för Web Apps funktion* längre fram i den här artikeln.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager distributions ändringar

Med hjälp av [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)får du en historisk förteckning över hur Azure-resurser som är värdar för ditt program har ändrats över tid. Ändrings analys kan identifiera exempelvis ändringar i IP-konfigurationsinställningar, hanterade identiteter och SSL-inställningar. Så om en tagg läggs till i en webbapp, visar ändrings analysen ändringen. Den här informationen är tillgänglig så länge `Microsoft.ChangeAnalysis` resurs leverantören är aktive rad i Azure-prenumerationen.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Ändringar i distribution och konfiguration av webbapp

Med ändrings analys samlas distributions-och konfigurations status för ett program var fjärde timme. Den kan till exempel identifiera ändringar i program miljöns variabler. Verktyget beräknar skillnaderna och visar vad som har ändrats. Till skillnad från i Resource Manager-ändringar kanske kod distributionens ändrings information inte är tillgänglig direkt i verktyget. Om du vill visa de senaste ändringarna i ändrings analysen väljer du **Genomsök ändringar nu**.

![Skärm bild av knappen "Skanna ändringar nu"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar

Ändringar i resurs beroenden kan också orsaka problem i en webbapp. Om en webbapp till exempel anropar en Redis cache kan Redis cache-SKU: n påverka webbappens prestanda. Om du vill identifiera ändringar i beroenden kontrollerar ändrings analysen webbappens DNS-post. På så sätt identifieras ändringar i alla app-komponenter som kan orsaka problem.

## <a name="change-analysis-for-the-web-apps-feature"></a>Ändrings analys för funktionen Web Apps

I Azure Monitor är ändrings analys för närvarande inbyggd i självbetjänings **diagnos och lösa problem** . Få åtkomst till den här upplevelsen från sidan **Översikt** i ditt App Service-program.

![Skärm bild av knappen "Översikt" och "diagnosticera och lösa problem"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Aktivera ändrings analys i verktyget diagnostisera och lösa problem

1. Välj **tillgänglighet och prestanda**.

    ![Skärm bild av fel söknings alternativen "tillgänglighet och prestanda"](./media/change-analysis/availability-and-performance.png)

1. Välj **program ändringar**. Inte att funktionen också är tillgänglig i **program krascher**.

   ![Skärm bild av knappen "program krascher"](./media/change-analysis/application-changes.png)

1. Om du vill aktivera ändrings analys väljer du **Aktivera nu**.

   ![Skärm bild av alternativen för "program krascher"](./media/change-analysis/enable-changeanalysis.png)

1. Aktivera **ändrings analys** och välj **Spara**.

    ![Skärm bild av användar gränssnittet "Aktivera ändrings analys"](./media/change-analysis/change-analysis-on.png)


1. Om du vill komma åt ändrings analyser väljer du **diagnostisera och lösa problem** > **tillgänglighet och prestanda** > **program krascher**. Du ser en graf som sammanfattar typen av ändringar över tid tillsammans med information om ändringarna:

     ![Skärm bild av vyn ändra diff](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Aktivera ändrings analys i skala

Om din prenumeration innehåller flera webbappar är det inte effektivt att aktivera tjänsten på nivån för webbappen. I det här fallet följer du de här alternativa anvisningarna.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>Registrera resurs leverantören för ändrings analys för din prenumeration

1. Registrera funktions flaggan för ändrings analys (för hands version). Eftersom funktions flaggan är i för hands version måste du registrera den så att den blir synlig för din prenumeration:

   1. Öppna [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

      ![Skärm bild av ändrings Cloud Shell](./media/change-analysis/cloud-shell.png)

   1. Ändra Shell-typen till **PowerShell**.

      ![Skärm bild av ändrings Cloud Shell](./media/change-analysis/choose-powershell.png)

   1. Kör följande PowerShell-kommando:

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. Registrera resurs leverantören för ändrings analys för prenumerationen.

   - Gå till **prenumerationer**och välj den prenumeration som du vill aktivera i ändrings tjänsten. Välj sedan resurs leverantörer:

        ![Skärm bild som visar hur du registrerar resurs leverantören för ändrings analys](./media/change-analysis/register-rp.png)

       - Välj **Microsoft. ChangeAnalysis**. Välj **Registrera**längst upp på sidan.

       - När resurs leverantören har Aktiver ATS kan du ange en dold tagg i webbappen för att identifiera ändringar på distributions nivån. Om du vill ange en dold tagg följer du anvisningarna under **det går inte att hämta information om ändrings analys**.

   - Du kan också använda ett PowerShell-skript för att registrera resurs leverantören:

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        Om du vill använda PowerShell för att ange en dold tagg i en webbapp kör du följande kommando:

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > När du har lagt till den dolda taggen kan du fortfarande vänta i upp till fyra timmar innan du börjar se ändringarna. Resultatet är försenat eftersom ändrings analys bara söker igenom din webbapp var fjärde timme. Schemat på 4 timmar begränsar genomsökningens prestanda påverkan.

## <a name="next-steps"></a>Nästa steg

- Aktivera Application Insights för [Azure App Services-appar](azure-web-apps.md).
- Aktivera Application Insights för [virtuella Azure-datorer och skalnings uppsättningar för IIS-värdbaserade appar i Azure](azure-vm-vmss-apps.md).
- Lär dig mer om [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), som hjälper dig att analysera energi ändringar.
