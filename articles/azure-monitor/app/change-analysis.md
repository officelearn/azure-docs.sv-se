---
title: Använd programändringsanalys i Azure Monitor för att hitta webb-appproblem | Microsoft-dokument
description: Använd programändringsanalys i Azure Monitor för att felsöka programproblem på aktiva webbplatser på Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348734"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Använda programändringsanalys (förhandsversion) i Azure Monitor

När ett problem eller avbrott på en levande plats inträffar är det viktigt att snabbt fastställa orsaken. Standardövervakningslösningar kan varna dig för ett problem. De kan till och med ange vilken komponent som misslyckas. Men den här varningen kommer inte alltid omedelbart att förklara felets orsak. Du vet att din webbplats fungerade för fem minuter sedan, och nu är den trasig. Vad har förändrats de senaste fem minuterna? Det här är frågan som Application Change Analysis är utformad för att svara på i Azure Monitor.

Ändra analys bygger på kraften i [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)och ger insikter om dina Azure-programändringar för att öka observerbarheten och minska MTTR (tid att reparera).

> [!IMPORTANT]
> Ändringsanalysen förhandsgranskas för närvarande. Den här förhandsversionen tillhandahålls utan ett servicenivåavtal. Den här versionen rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsade funktioner. Mer information finns i [Tilläggsvillkor för microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Översikt

Ändringsanalys identifierar olika typer av ändringar, från infrastrukturlagret hela vägen till programdistribution. Det är en Azure-resursleverantör på prenumerationsnivå som kontrollerar resursändringar i prenumerationen. Ändringsanalys innehåller data för olika diagnostikverktyg som hjälper användarna att förstå vilka ändringar som kan ha orsakat problem.

Följande diagram illustrerar arkitekturen i ändringsanalys:

![Arkitekturdiagram över hur ändringsanalys får ändringsdata och tillhandahåller dem till klientverktyg](./media/change-analysis/overview.png)

För närvarande är ändringsanalys integrerad i **diagnostisera och lösa problemupplevelse** i App Service-webbappen, samt tillgänglig som en fristående flik i Azure-portalen.
Se *avsnittet Visa ändringar för alla resurser i Azure* för att komma åt bladet Ändra analys och avsnittet Ändra analys för *webbappar* för att använda det i Web App-portalen senare i den här artikeln.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Spårade ändringar i Azure Resource Manager

Med Hjälp av [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)innehåller ändringsanalys en historisk post om hur De Azure-resurser som är värdar för ditt program har ändrats med tiden. Spårade inställningar som hanterade identiteter, uppgradering av plattformsoperativsystem och värdnamn kan identifieras.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager-proxied-inställningsändringar
Inställningar som IP-konfigurationsregel, TLS-inställningar och tilläggsversioner är ännu inte tillgängliga i ARG, så ändra analysfrågor och beräknar dessa ändringar på ett säkert sätt för att ge mer information i vad som har ändrats i appen. Den här informationen är ännu inte tillgänglig i Azure Resource Graph men kommer snart att vara tillgänglig.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Ändringar i distribution och konfiguration av webbappar (ändringar i gästen)

Ändringsanalys fångar distributions- och konfigurationstillståndet för ett program var fjärde timme. Den kan identifiera till exempel ändringar i programmiljövariablerna. Verktyget beräknar skillnaderna och presenterar vad som har förändrats. Till skillnad från Resource Manager-ändringar kanske information om ändring av koddistribution inte är tillgänglig direkt i verktyget. Om du vill visa de senaste ändringarna i ändringsanalys väljer du **Skanna ändringar nu**.

![Skärmbild av knappen "Skanna ändra nu"](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroendeändringar

Ändringar av resursberoenden kan också orsaka problem i en webbapp. Om till exempel en webbapp anropar en Redis-cache kan Redis-cachen SKU påverka webbappens prestanda. Om du vill identifiera ändringar i beroenden kontrollerar Ändringsanalys webbappens DNS-post. På så sätt identifierar den ändringar i alla appkomponenter som kan orsaka problem.
För närvarande stöds följande beroenden:
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>Aktivering
"Microsoft.ChangeAnalysis"-resursprovidern måste registreras med en prenumeration för azure Resource Manager-spårade egenskaper och avancerade inställningar ändra data för att vara tillgängliga. När du anger webbappdiagnostisera och löser problemverktyget eller tar upp fliken Ändra analys fristående registreras den här resursleverantören automatiskt. Den har inga prestanda- och kostnadsimplementeringar för din prenumeration. När du aktiverar ändringsanalys för webbappar (eller aktiverar i verktyget Diagnostisera och lösa problem) har det försumbar prestandapåverkan på webbappen och ingen faktureringskostnad.
För ändringar i webbappens gäst krävs separat aktivering för att skanna kodfiler i en webbapp. Mer information finns [i Aktivera ändringsanalys i](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) avsnittet Diagnostisera och lösa problemverktyg senare i den här artikeln för mer information.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Visa ändringar för alla resurser i Azure
I Azure Monitor finns det ett fristående blad för ändringsanalys för att visa alla ändringar med insikter och programberoenderesurser.

Sök efter ändringsanalys i sökfältet på Azure-portalen för att starta bladet.

![Skärmbild av sökning av ändringsanalys i Azure-portalen](./media/change-analysis/search-change-analysis.png)

Välj Resursgrupp och resurser för att börja visa ändringar.

![Skärmbild av bladet Ändra analys i Azure-portalen](./media/change-analysis/change-analysis-standalone-blade.png)

Du kan se insikter och relaterade beroenderesurser som är värd för ditt program. Den här vyn är utformad för att vara programcentrerad för utvecklare att felsöka problem.

Resurser som för närvarande stöds omfattar:
- Virtuella datorer
- Skaluppsättning för virtuell dator
- Azure Networking-resurser
- Webbapp med in-guest filspårning och miljövariabler ändras

För all feedback, använd knappen skicka feedback changeanalysisteam@microsoft.comi bladet eller e-postmeddelandet .

![Skärmbild av feedback-knappen i bladet Ändra analys](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>Ändra analys för webbappfunktionen

I Azure Monitor är ändringsanalys också inbyggd i **självbetjäningsdiagnosen och löser problemupplevelsen.** Få tillgång till den här upplevelsen från **sidan Översikt i** ditt App Service-program.

![Skärmbild av knappen "Översikt" och knappen "Diagnostisera och lösa problem"](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Aktivera förändringsanalys i verktyget Diagnostisera och lösa problem

1. Välj **Tillgänglighet och prestanda**.

    ![Skärmbild av felsökningsalternativen "Tillgänglighet och prestanda"](./media/change-analysis/availability-and-performance.png)

1. Välj **Programändringar**. Inte för att funktionen också är tillgänglig i **Programkrascher**.

   ![Skärmbild av knappen "Programkrascher"](./media/change-analysis/application-changes.png)

1. Om du vill aktivera ändringsanalys väljer du **Aktivera nu**.

   ![Skärmbild av alternativen "Programkrascher"](./media/change-analysis/enable-changeanalysis.png)

1. Aktivera **Ändra analys** och välj **Spara**. Verktyget visar alla webbappar under en App Service-plan. Du kan använda bytet på plannivå för att aktivera Ändra analys för alla webbappar under ett abonnemang.

    ![Skärmbild av användargränssnittet "Aktivera ändringsanalys"](./media/change-analysis/change-analysis-on.png)


1. Om du vill komma åt ändringsanalys väljer du **Diagnostisera och löser problem** > Tillgänglighet och**prestandaprogramkrascher****Availability and Performance** > . Du ser ett diagram som sammanfattar typen av ändringar över tid tillsammans med information om dessa ändringar. Som standard visas ändringar under de senaste 24 timmarna för att hjälpa till med omedelbara problem.

     ![Skärmbild av ändringsdiffvyn](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>Aktivera ändringsanalys i stor skala

Om din prenumeration innehåller många webbappar är det ineffektivt att aktivera tjänsten på webbappens nivå. Kör följande skript för att aktivera alla webbappar i din prenumeration.

Förutsättningar:
* PowerShell Az-modul. Följ instruktionerna vid [Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0)

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

- Aktivera programstatistik för [Azure App Services-appar](azure-web-apps.md).
- Aktivera Application Insights för [Azure VM och Azure virtual machine scale set IIS-värdappar](azure-vm-vmss-apps.md).
- Läs mer om [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview), som hjälper till att driva förändringsanalys.
