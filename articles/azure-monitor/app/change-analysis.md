---
title: Azure Monitor-program ändrar analys – identifiera ändringar som kan påverka live-webbplatsen problem/avbrott med Azure Monitor program ändrar analys | Microsoft Docs
description: Felsökning av problem med live-webbplatsen på Azure App Services med Azure Monitor ändra programanalys
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: b04bd57c66b52e9a2c14d43c9e89d7c54fc48ae2
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415859"
---
# <a name="azure-monitor-application-change-analysis-public-preview"></a>Azure Monitor ändra programanalys (offentlig förhandsversion)

När ett live-webbplatsen problem/strömavbrott uppstår, är det viktigt att fastställa rotorsak snabbt. Standard övervakningslösningar kan hjälpa dig att snabbt identifiera ett problem har uppstått, och ofta även vilka komponenten inte. Men detta alltid leda inte till en omedelbar förklaring till varför felet inträffar. Webbplatsen arbetat fem minuter sedan, nu är den bruten. Vad som ändrats under de senaste fem minuterna? Det här är den fråga som den nya funktionen Azure Monitor analys av programmets ändringen har utformats för att besvara. Med kraften hos den [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) analys av programmets ändringen ger insikt i din Azure App Service-programändringar att öka observability och minska MTTR (Mean Time att reparera).

> [!IMPORTANT]
> Azure Monitor ändra programanalys är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-do-i-use-application-change-analysis"></a>Hur kan jag använda analys av programmets ändringen?

Azure Monitor ändra programanalys för närvarande är inbyggd i självbetjäningen **diagnostisera och lösa problem** får, som kan nås från den **översikt** avsnittet i Azure App Service program:

![Skärmbild av Azure App Service-översikt med röda rutor runt översikt-knappen och diagnostisera och lösa problem med knappen](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis"></a>Aktivera ändring analys

1. Välj **tillgänglighet och prestanda**

    ![Skärmbild av tillgänglighet och prestanda som felsökningsalternativ](./media/change-analysis/availability-and-performance.png)

2. Klicka på den **programmet kraschar** panelen.

   ![Skärmbild med programmet kraschar panel](./media/change-analysis/application-crashes-tile.png)

3. Aktivera **ändra Analysis** Välj **aktivera nu**.

   ![Skärmbild av tillgänglighet och prestanda som felsökningsalternativ](./media/change-analysis/application-crashes.png)

4. För att dra nytta av fullständiga ändra analysis funktioner **ändra Analysis**, **söka efter ändringar i koden**, och **alltid på** till **på** Välj **spara**.

    ![Skärmbild av Azure App Service-aktivera ändra analysis-användargränssnittet](./media/change-analysis/change-analysis-on.png)

    Om **ändra Analysis** är aktiverad, du kommer att kunna identifiera resursen ändras. Om **söka efter ändringar i koden** är aktiverat kan du också se filer för distribution och ändringar i platskonfigurationen. Aktivera **alltid på** optimerar ändringen genomsökning prestanda, men kan medföra ytterligare kostnader från ett.

5.  När allt är aktiverat kan du välja **diagnostisera och lösa problem** > **tillgänglighet och prestanda** > **programmet kraschar** kan du komma åt ändra analysis-upplevelse. Diagrammet kommer summerize vilken typ av ändringar som har hänt med tiden tillsammans med information om dessa ändringar:

     ![Skärmbild av ändringen diff-vy](./media/change-analysis/change-view.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="unable-to-fetch-change-analysis-information"></a>Det går inte att hämta ändringen analysinformation.

Det här är ett tillfälligt problem med aktuella förhandsversion publiceringsupplevelsen. Lösningen består av att en dold tagg på din webbapp och sedan uppdatera sidan:

   ![Skärmbild av ändringen dolda tagg](./media/change-analysis/hidden-tag.png)

Så här dolda taggen med hjälp av PowerShell:

1. Öppna Azure Cloudshell:

    ![Skärmbild av ändringen Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

2. Ändra shell till PowerShell:

   ![Skärmbild av ändringen Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

3. Kör följande kommando:

```powershell
$webapp=Get-AzWebApp -Name <name_of_your_webapp>
$tags = $webapp.Tags
$tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
```

> [!NOTE]
> När dolda taggen har lagts till, kan fortfarande måste du först vänta upp till fyra timmar för att kunna visa ändringar först. Detta beror på 4 timmars freqeuncy som ändra analysis Services använder för att söka igenom dina webbappar och begränsa prestandapåverkan från återställning av sökningen.

## <a name="next-steps"></a>Nästa steg

- Förbättra din övervakning av Azure App Services [genom att aktivera funktioner för Application Insights](azure-web-apps.md) över Azure Monitor.
- Åka din förståelse för de [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) som bidrar till att power Azure Monitor program ändrar analys. 
