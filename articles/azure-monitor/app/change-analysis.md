---
title: Använd program ändrings analys i Azure Monitor för att hitta problem med webb program | Microsoft Docs
description: Använd program ändrings analys i Azure Monitor för att felsöka program problem på Live-webbplatser på Azure App Service.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: ed297a1005f67a14db1da15aba2c47c98e83df9c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885012"
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

För närvarande är ändrings analys integrerad i lösningen för att **diagnostisera och lösa problem** i App Service webbapp, samt vara tillgängligt som ett fristående blad i Azure Portal.
Se avsnittet *Visa ändringar för alla resurser i Azure* för att få åtkomst till ändrings analys bladet och *ändrings analysen för avsnittet Web Apps funktion* för att använda den i Web App-portalen senare i den här artikeln.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Ändringar i Azure Resource Manager spårade egenskaper

Med hjälp av [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)får du en historisk förteckning över hur Azure-resurser som är värdar för ditt program har ändrats över tid. Spårade inställningar, till exempel hanterade identiteter, plattforms-OS-uppgradering och värdnamn kan identifieras.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Inställnings ändringar för Azure Resource Manager-proxy
Inställningar som IP-konfigurationsinformation, SSL-inställningar och tilläggs versioner är ännu inte tillgängliga i ARG, så ändra analys frågor och beräkna dessa ändringar på ett säkert sätt för att ge mer information om vad som har ändrats i appen. Informationen är inte tillgänglig ännu i Azure Resource Graph men kommer snart att vara tillgänglig.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Ändringar i distribution och konfiguration av webbappar (ändringar i gästen)

Med ändrings analys samlas distributions-och konfigurations status för ett program var fjärde timme. Den kan till exempel identifiera ändringar i program miljöns variabler. Verktyget beräknar skillnaderna och visar vad som har ändrats. Till skillnad från i Resource Manager-ändringar kanske kod distributionens ändrings information inte är tillgänglig direkt i verktyget. Om du vill visa de senaste ändringarna i ändrings analysen väljer du **Genomsök ändringar nu**.

![Skärm bild av knappen "Skanna ändringar nu"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar

Ändringar i resurs beroenden kan också orsaka problem i en webbapp. Om en webbapp till exempel anropar en Redis cache kan Redis cache-SKU: n påverka webbappens prestanda. Om du vill identifiera ändringar i beroenden kontrollerar ändrings analysen webbappens DNS-post. På så sätt identifieras ändringar i alla app-komponenter som kan orsaka problem.
För närvarande stöds följande beroenden:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Aktivering
Resurs leverantören Microsoft. ChangeAnalysis måste registreras med en prenumeration för Azure Resource Manager spårade egenskaper och proxy-inställningar ändra att data ska vara tillgängliga. När du anger webbappen diagnostiserar och löser problem eller hämtar det fristående ändrings analys bladet registreras denna resurs leverantör automatiskt. Det finns inga prestanda-och kostnads implementeringar för din prenumeration.
För att webbappen ska ändras i gästen krävs separat aktivering för att genomsöka filer i en webbapp. Se *Aktivera ändrings analys i avsnittet diagnosticera och lösa problem* i den här artikeln längre fram i den här artikeln för mer information.

## <a name="viewing-changes-for-all-resources-in-azure"></a>Visa ändringar för alla resurser i Azure
I Azure Monitor finns det ett fristående blad för ändrings analys för att visa alla ändringar med insikter och resurser för program beroenden.

Sök efter ändrings analys i Sök fältet på Azure Portal för att starta bladet.

![Skärm bild av sökning av ändrings analys i Azure Portal](./media/change-analysis/search-change-analysis.png)

Välj resurs grupp och resurser för att starta visning av ändringar.

![Skärm bild av bladet för ändrings analys i Azure Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Du kan se insikter och relaterade beroende resurser som är värdar för ditt program. Den här vyn är utformad för att vara programinriktad för utvecklare för att felsöka problem.

Resurser som stöds för närvarande är:
- Virtuella datorer
- Skalnings uppsättning för virtuell dator
- Nätverks resurser för Azure
- Webbapp med ändringar i gäst fil spårning och miljövariabler

Använd knappen Skicka feedback i bladet eller e-postchangeanalysisteam@microsoft.comför all feedback.

![Skärm bild av knappen feedback i bladet ändra analys](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Ändrings analys för funktionen Web Apps

I Azure Monitor är även ändrings analys inbyggd i självbetjänings **diagnos och lösa problem** . Få åtkomst till den här upplevelsen från sidan **Översikt** i ditt App Service-program.

![Skärm bild av knappen "Översikt" och "diagnosticera och lösa problem"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Aktivera ändrings analys i verktyget diagnostisera och lösa problem

1. Välj **tillgänglighet och prestanda**.

    ![Skärm bild av fel söknings alternativen "tillgänglighet och prestanda"](./media/change-analysis/availability-and-performance.png)

1. Välj **program ändringar**. Inte att funktionen också är tillgänglig i **program krascher**.

   ![Skärm bild av knappen "program krascher"](./media/change-analysis/application-changes.png)

1. Om du vill aktivera ändrings analys väljer du **Aktivera nu**.

   ![Skärm bild av alternativen för "program krascher"](./media/change-analysis/enable-changeanalysis.png)

1. Aktivera **ändrings analys** och välj **Spara**. Verktyget visar alla webbappar under ett App Services abonnemang. Du kan använda plan nivå växeln för att aktivera ändrings analyser för alla webbappar under en plan.

    ![Skärm bild av användar gränssnittet "Aktivera ändrings analys"](./media/change-analysis/change-analysis-on.png)


1. Om du vill komma åt ändrings analyser väljer du **diagnostisera och löser problem** > **tillgänglighet och prestanda** > **program krascher**. Du ser en graf som sammanfattar typen av ändringar över tid tillsammans med information om dessa ändringar. Som standard visas ändringar under de senaste 24 timmarna för att hjälpa till med omedelbara problem.

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
