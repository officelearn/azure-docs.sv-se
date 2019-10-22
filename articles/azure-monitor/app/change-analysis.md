---
title: Använd program ändrings analys i Azure Monitor för att hitta problem med webb program | Microsoft Docs
description: Använd program ändrings analys i Azure Monitor för att felsöka program problem på Live-webbplatser på Azure App Service.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 3805d7b39c25bcb213a1d4f110161dcd00eb3630
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678259"
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

Med hjälp av [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)får du en historisk förteckning över hur Azure-resurser som är värdar för ditt program har ändrats över tid. Ändrings analys kan identifiera exempelvis ändringar i IP-konfigurationsinställningar, hanterade identiteter och SSL-inställningar. Så om en tagg läggs till i en webbapp, visar ändrings analysen ändringen. Den här informationen är tillgänglig så länge `Microsoft.ChangeAnalysis` Resource Provider är aktive rad i Azure-prenumerationen.

### <a name="changes-in-web-app-deployment-and-configuration"></a>Ändringar i distribution och konfiguration av webbapp

Med ändrings analys samlas distributions-och konfigurations status för ett program var fjärde timme. Den kan till exempel identifiera ändringar i program miljöns variabler. Verktyget beräknar skillnaderna och visar vad som har ändrats. Till skillnad från i Resource Manager-ändringar kanske kod distributionens ändrings information inte är tillgänglig direkt i verktyget. Om du vill visa de senaste ändringarna i ändrings analysen väljer du **Genomsök ändringar nu**.

![Skärm bild av knappen "Skanna ändringar nu"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar

Ändringar i resurs beroenden kan också orsaka problem i en webbapp. Om en webbapp till exempel anropar en Redis cache kan Redis cache-SKU: n påverka webbappens prestanda. Om du vill identifiera ändringar i beroenden kontrollerar ändrings analysen webbappens DNS-post. På så sätt identifieras ändringar i alla app-komponenter som kan orsaka problem.
För närvarande stöds följande beroenden:
- Web Apps
- Azure Storage
- Azure SQL


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


1. Om du vill komma åt ändrings analyser väljer du **diagnostisera och löser problem**  > **tillgänglighet och prestanda**  > **program krascher**. Du ser en graf som sammanfattar typen av ändringar över tid tillsammans med information om ändringarna:

     ![Skärm bild av vyn ändra diff](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Aktivera ändrings analys i skala

Om din prenumeration innehåller flera webbappar är det inte effektivt att aktivera tjänsten på nivån för webbappen. Kör följande skript för att aktivera alla webb program i din prenumeration.

Krav:
* PowerShell-modulen AZ. Följ anvisningarna i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

Kör följande skript:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>Nästa steg

- Aktivera Application Insights för [Azure App Services-appar](azure-web-apps.md).
- Aktivera Application Insights för [virtuella Azure-datorer och skalnings uppsättningar för IIS-värdbaserade appar i Azure](azure-vm-vmss-apps.md).
- Lär dig mer om [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), som hjälper dig att analysera energi ändringar.
