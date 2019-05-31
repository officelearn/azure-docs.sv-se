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
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226281"
---
# <a name="application-change-analysis-public-preview"></a>Analys av programmets ändring (offentlig förhandsversion)

När ett live-webbplatsen problem/strömavbrott uppstår, är det viktigt att fastställa rotorsak snabbt. Standard övervakningslösningar kan hjälpa dig att snabbt identifiera ett problem har uppstått, och ofta även vilka komponenten inte. Men detta alltid leda inte till en omedelbar förklaring till varför felet inträffar. Webbplatsen arbetat fem minuter sedan, nu är den bruten. Vad som ändrats under de senaste fem minuterna? Det här är den fråga som den nya funktionen Azure Monitor analys av programmets ändringen har utformats för att besvara. Med kraften hos den [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) analys av programmets ändringen ger insikt i dina Azure-programändringar att öka observability och minska MTTR (Mean Time att reparera).

> [!IMPORTANT]
> Azure Monitor ändra programanalys är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Översikt över ändra Analysis service
Ändra analystjänst identifierar olika typer av ändringar från lager för hanteringsinfrastruktur hela vägen till programdistributionen. Det är en prenumeration på Azure-resursprovidern som ser ut i resursändringar i prenumerationen och tillhandahåller data för olika diagnostik-verktyg som hjälper användarna att förstå vad ändras kan ha orsakat problem.

Följande diagram illustrerar arkitekturen i ändra analysis Services: ![Arkitekturdiagram för hur ändringen analystjänst hämtar ändra data och skicka data till klientverktyg](./media/change-analysis/overview.png)

Verktyget ingår för närvarande i App Service webbapp diagnostisera och lösa problem upplevelse. Se *ändra Analysis service för App Services Web App* avsnittet om hur du aktiverar och visa ändringar som gjorts i en webbapp.

### <a name="azure-resource-manager-deployment-changes"></a>Distributionsändringar av Azure Resource Manager
Utnyttja [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) analysverktyget ändringen ger en historisk post av hur Azure-resurser som är värdar för ditt program har ändrats över tid. Till exempel återspeglas en webbapp har haft en tagg som har lagts till, ändringen i analysverktyget ändringen.
Den här informationen alltid är tillgängligt så länge som den `Microsoft.ChangeAnalysis` resursprovidern har publicerats till Azure-prenumerationen.

### <a name="web-application-deployment-and-configuration-changes"></a>Web Application distribution och konfiguration av ändringar
Ändra analysverktyget samlar in status för distribution och konfiguration för ett program var fjärde timme för att beräkna skillnaderna och presentera vad som har ändrats. Exempel på sådana ändringar är variabeln programändringar för miljön, IP-konfigurationsändringar för regeln, hanterad tjänstidentitet ändringar, ändringar för SSL-inställningar och så vidare.
Till skillnad från Resource Manager-ändringar kanske inte den här typen av information om malländring tillgängligt direkt i verktyget. Använd knappen ”genomsökning ändras nu” om du vill visa de senaste ändringarna i verktyget.

![Skärmbild av genomsökning efter ändringar nu i diagnostisera och lösa problem med verktyget med ändringen analysis-integrering för app service-webbapp](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Beroende ändringar
Beroenden resurs kan också vara orsaken till problem. Till exempel om en webbapp anrop till en Redis-cache, kan web app prestanda påverkas av Redis-cache SKU. Ändra analysis service även presentera beroenden ändra information för att identifiera ändringar i alla komponenter i en app som kan ha orsakat problem genom att titta i web app DNS-post.


## <a name="change-analysis-service-for-app-services-web-app"></a>Ändra Analysis service för App Services Web App

Azure Monitor ändra programanalys för närvarande är inbyggd i självbetjäningen **diagnostisera och lösa problem** får, som kan nås från den **översikt** avsnittet i Azure App Service program:

![Skärmbild av Azure App Service-översikt med röda rutor runt översikt-knappen och diagnostisera och lösa problem med knappen](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Aktivera ändring analyser i diagnostisera och lösa problem som verktyget

1. Välj **tillgänglighet och prestanda**

    ![Skärmbild av tillgänglighet och prestanda som felsökningsalternativ](./media/change-analysis/availability-and-performance.png)

2. Klicka på den **programmet kraschar** panelen.

   ![Skärmbild med programmet kraschar panel](./media/change-analysis/application-crashes-tile.png)

3. Aktivera **ändra Analysis** Välj **aktivera nu**.

   ![Skärmbild av tillgänglighet och prestanda som felsökningsalternativ](./media/change-analysis/application-crashes.png)

4. För att dra nytta av fullständiga ändra analysis funktioner **ändra Analysis**, **söka efter ändringar i koden**, och **alltid på** till **på** Välj **spara**.

    ![Skärmbild av Azure App Service-aktivera ändra analysis-användargränssnittet](./media/change-analysis/change-analysis-on.png)

    Om **ändra Analysis** är aktiverad, du kommer att kunna identifiera resursen ändras. Om **söka efter ändringar i koden** är aktiverat kan du också se filer för distribution och ändringar i platskonfigurationen. Aktivera **alltid på** optimerar ändringen genomsökning prestanda, men kan medföra ytterligare kostnader från ett.

5.  När allt är aktiverat kan du välja **diagnostisera och lösa problem** > **tillgänglighet och prestanda** > **programmet kraschar** kan du komma åt ändra analysis-upplevelse. I diagrammet visas en sammanställning av typ av ändringar som har hänt med tiden tillsammans med information om dessa ändringar:

     ![Skärmbild av ändringen diff-vy](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Aktivera ändring analystjänst i stor skala
Om du har en massa webbappar i din prenumeration, kommer aktivera tjänsten på per web app-nivå att ineffektiv. Här följer några alternativ registreringsanvisningar.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Registrera ändringen Analysis resource provider för din prenumeration

1. Registrera ändringen Analysis funktionsflagga för förhandsversion

    Eftersom den här funktionen är i förhandsversion, som du behöver registrera för det första funktionsflagga att vara synliga för din prenumeration.
    - Öppna [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Skärmbild av ändringen Azure Cloud Shell](./media/change-analysis/cloud-shell.png)

    - Ändra shell till PowerShell:

    ![Skärmbild av ändringen Azure Cloud Shell](./media/change-analysis/choose-powershell.png)

    - Kör följande PowerShell-kommando:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Ändra Analysis Registerresursleverantören för prenumerationen

    - Gå till prenumerationer, Välj den prenumeration du vill att integrera tjänsten ändringen och sedan på resursprovidrar:

        ![Skärmbild för att registrera ändringen Analysis RP från prenumerationsbladet](./media/change-analysis/register-rp.png)

    - Välj *Microsoft.ChangeAnalysis* och klicka på *registrera* överst på sidan.

    - När Resursprovidern har publicerats, följer du anvisningarna i *det gick inte att hämta ändra analysinformation* nedan för att ange dolda taggen för webbappen distribueras ändra nivå identifiering på webbappen.

3. Alternativt kan du registrera för Resursprovidern via PowerShell-skript till steg 2 ovan:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Om du vill ange en tagg för dolda på en webbapp med hjälp av PowerShell kör du följande kommando:

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
