---
title: Använd program ändrings analys i Azure Monitor för att hitta problem med webb program | Microsoft Docs
description: Använd program ändrings analys i Azure Monitor för att felsöka program problem på Live-webbplatser på Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: c287a2315f2b2319a6873ce84ee0e4e48bec8444
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82836821"
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

## <a name="data-sources"></a>Datakällor

Frågor om program ändrings analys för Azure Resource Manager spårade egenskaper, proxy-konfigurationer och webbappar i gäst ändringar. Dessutom spårar tjänsten resurs beroende ändringar för att diagnostisera och övervaka ett program från slut punkt till slut punkt.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Ändringar i Azure Resource Manager spårade egenskaper

Med hjälp av [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)får du en historisk förteckning över hur Azure-resurser som är värdar för ditt program har ändrats över tid. Spårade inställningar, till exempel hanterade identiteter, plattforms-OS-uppgradering och värdnamn kan identifieras.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Inställnings ändringar för Azure Resource Manager-proxy

Inställningar som IP-konfigurationsfil, TLS-inställningar och tilläggs versioner är ännu inte tillgängliga i Azures resurs diagram, så du kan ändra analys frågor och beräkna dessa ändringar på ett säkert sätt för att ge mer information om vad som har ändrats i appen.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Ändringar i distribution och konfiguration av webbappar (ändringar i gästen)

Med ändrings analys samlas distributions-och konfigurations status för ett program var fjärde timme. Den kan till exempel identifiera ändringar i program miljöns variabler. Verktyget beräknar skillnaderna och visar vad som har ändrats. Till skillnad från i Resource Manager-ändringar kanske kod distributionens ändrings information inte är tillgänglig direkt i verktyget. Om du vill visa de senaste ändringarna i ändrings analysen väljer du **Uppdatera**.

![Skärm bild av knappen "Skanna ändringar nu"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar

Ändringar i resurs beroenden kan också orsaka problem i en webbapp. Om en webbapp till exempel anropar en Redis cache kan Redis cache-SKU: n påverka webbappens prestanda. Om du vill identifiera ändringar i beroenden kontrollerar ändrings analysen webbappens DNS-post. På så sätt identifieras ändringar i alla app-komponenter som kan orsaka problem.
För närvarande stöds följande beroenden:
- Web Apps
- Azure Storage
- Azure SQL

## <a name="application-change-analysis-service"></a>Analys tjänst för program ändring

Beräknings-och agg regeringar för program ändrings analys gör ändrings data från de data källor som anges ovan. Den innehåller en uppsättning analyser för användare som enkelt kan navigera bland alla resurs ändringar och identifiera vilken ändring som är relevant i fel söknings-eller övervaknings kontexten.
Resurs leverantören Microsoft. ChangeAnalysis måste registreras med en prenumeration för Azure Resource Manager spårade egenskaper och proxy-inställningar ändra att data ska vara tillgängliga. När du anger ett verktyg för att diagnostisera och lösa problem, eller ta fram fliken för att skapa ändrings analyser, registreras denna resurs leverantör automatiskt. Det finns inga prestanda-eller kostnads implementeringar för din prenumeration. När du aktiverar ändrings analys för webbappar (eller aktiverar verktyget diagnostisera och lösa problem) har den försumbar prestanda påverkan på webbappen och ingen fakturerings kostnad.
För att webbappen ska ändras i gästen krävs separat aktivering för att genomsöka filer i en webbapp. Mer information finns i avsnittet om att [ändra analyser i verktyget diagnostisera och lösa problem](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#application-change-analysis-in-the-diagnose-and-solve-problems-tool) längre fram i den här artikeln.

## <a name="visualizations-for-application-change-analysis"></a>Visualiseringar för program ändrings analys

### <a name="standalone-ui"></a>Fristående gränssnitt

I Azure Monitor finns det ett fristående fönster för ändrings analys som visar alla ändringar med insikter i program beroenden och resurser.

Sök efter ändrings analys i Sök fältet på Azure Portal för att starta upplevelsen.

![Skärm bild av sökning av ändrings analys i Azure Portal](./media/change-analysis/search-change-analysis.png)

Alla resurser under en vald prenumeration visas med ändringar från de senaste 24 timmarna. För att optimera prestanda för sid inläsning visar tjänsten 10 resurser i taget. Klicka på nästa sidor om du vill visa fler resurser. Vi arbetar med att ta bort den här begränsningen.

![Skärm bild av bladet för ändrings analys i Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Visa alla ändringar genom att klicka på en resurs. Vid behov kan du öka detalj nivån i en ändring om du vill visa JSON-formaterad ändrings information och insikter.

![Skärm bild av ändrings information](./media/change-analysis/change-details.png)

Använd knappen Skicka feedback i bladet eller e-postmeddelandet för all feedback changeanalysisteam@microsoft.com .

![Skärm bild av knappen feedback i bladet ändra analys](./media/change-analysis/change-analysis-feedback.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Webbappen diagnostisera och lösa problem

I Azure Monitor är även ändrings analys inbyggd i självbetjänings **diagnos och lösa problem** . Få åtkomst till den här upplevelsen från sidan **Översikt** i ditt App Service-program.

![Skärm bild av knappen "Översikt" och "diagnosticera och lösa problem"](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Program ändrings analys i verktyget diagnostisera och lösa problem

Program ändrings analys är en fristående detektor i webbappen diagnostisera och lösa problem verktyg. Den sammanställs också i **program krascher** och i **webb program**. När du anger verktyget diagnostisera och lösa problem registreras **Microsoft. ChangeAnalysis** Resource Provider automatiskt. Följ de här instruktionerna för att aktivera webb program i gäst ändrings spårning.

1. Välj **tillgänglighet och prestanda**.

    ![Skärm bild av fel söknings alternativen "tillgänglighet och prestanda"](./media/change-analysis/availability-and-performance.png)

2. Välj **program ändringar**. Funktionen är också tillgänglig i **program krascher**.

   ![Skärm bild av knappen "program krascher"](./media/change-analysis/application-changes.png)

3. Om du vill aktivera ändrings analys väljer du **Aktivera nu**.

   ![Skärm bild av alternativen för "program krascher"](./media/change-analysis/enable-changeanalysis.png)

4. Aktivera **ändrings analys** och välj **Spara**. Verktyget visar alla webbappar under en App Service plan. Du kan använda plan nivå växeln för att aktivera ändrings analyser för alla webbappar under en plan.

    ![Skärm bild av användar gränssnittet "Aktivera ändrings analys"](./media/change-analysis/change-analysis-on.png)

5. Om du vill komma åt ändrings analyser väljer du **diagnostisera och lösa problem**  >  **tillgänglighet och prestanda**  >  **program krascher**. Du ser en graf som sammanfattar typen av ändringar över tid tillsammans med information om dessa ändringar. Som standard visas ändringar under de senaste 24 timmarna för att hjälpa till med omedelbara problem.

     ![Skärm bild av vyn ändra diff](./media/change-analysis/change-view.png)

### <a name="enable-change-analysis-at-scale"></a>Aktivera ändrings analys i skala

Om din prenumeration innehåller flera webbappar är det inte effektivt att aktivera tjänsten på nivån för webbappen. Kör följande skript för att aktivera alla webb program i din prenumeration.

Förutsättningar:

- PowerShell-modulen AZ. Följ anvisningarna i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

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

### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnostisera och lösa problem med den virtuella datorn

Gå till diagnostisera och lösa problem verktyg för en virtuell dator.  Gå till **fel söknings verktyg**, bläddra nedåt på sidan och välj **analysera nya ändringar** för att visa ändringar på den virtuella datorn.

![Skärm bild av den virtuella datorn diagnostisera och lösa problem](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Skärm bild av den virtuella datorn diagnostisera och lösa problem](./media/change-analysis/analyze-recent-changes.png)

## <a name="next-steps"></a>Nästa steg

- Aktivera Application Insights för [Azure App Services-appar](azure-web-apps.md).
- Aktivera Application Insights för [virtuella Azure-datorer och skalnings uppsättningar för IIS-värdbaserade appar i Azure](azure-vm-vmss-apps.md).
- Lär dig mer om [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), som hjälper dig att analysera energi ändringar.
