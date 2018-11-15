---
title: Konfigurera mellanlagringsmiljöer för webbappar i Azure App Service | Microsoft Docs
description: Lär dig hur du använder mellanlagrad publicering för web apps i Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: cephalin
ms.openlocfilehash: b5a06cff653007568b4ab2b44624b6314413f8a6
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636075"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurera mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

När du distribuerar din webbapp, web Apps i Linux-, mobil serverdel och API-app till [Apptjänst](https://go.microsoft.com/fwlink/?LinkId=529714), du kan distribuera till en separat distributionsplats i stället för standard-produktionsplatsen när de körs i den **Standard**, **Premium**, eller **isolerad** nivå för App Service-plan. Distributionsplatser är faktiskt liveappar med egna värddatornamn. App webbappsinnehåll och konfigurationselement kan bytas mellan två distributionsfack, inklusive produktionsplatsen. Distribuera programmet till en distributionsplats har följande fördelar:

* Du kan validera appändringar i en mellanlagringsplats för distributionen innan du växlar med produktionsplatsen.
* Distribuera en app till en plats först och växlar den till produktionen säkerställer du att alla instanser av facket värmas upp innan du håller på att växlas till produktion. Detta eliminerar avbrott när du distribuerar din app. Trafik-omdirigering sker sömlös och inga förfrågningar ignoreras på grund av swap-åtgärder. Den här hela arbetsflödet kan automatiseras genom att konfigurera [automatiskt växla](#Auto-Swap) när före swap-verifiering krävs inte.
* Efter en växling har facket med tidigare mellanlagrad app nu appen tidigare produktion. Om ändringarna växlas över till produktionsplatsen är inte som väntat, du kan utföra samma växlingen direkt för att få igång ”senaste kända bra webbplatsen” tillbaka.

Varje nivå för App Service-plan har stöd för ett annat antal distributionsplatser. Att ta reda på hur många platser stöder din app, se [App tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Om du vill skala din app till en annan nivå måste på mål-nivån stödja antalet platser som din app redan använder. Exempel: om din app har fler än 5 platser, kan du inte skala den ned till **Standard** tier, eftersom **Standard** nivån stöder endast 5 distributionsplatser.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Lägg till ett distributionsfack
Appen måste köras i den **Standard**, **Premium**, eller **isolerad* nivå för att du kan aktivera flera distributionsplatser.

1. I den [Azure-portalen](https://portal.azure.com/), öppna appens [resursbladet](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Välj den **distributionsfack** och sedan klicka på **Lägg till plats**.
   
    ![Lägg till en ny distributionsplats][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Om appen inte redan är i den **Standard**, **Premium**, eller **isolerad* nivå, du får ett meddelande om de stödda nivåerna för att aktivera mellanlagrad publicering. Nu har du möjlighet att välja **uppgradera** och navigera till den **skala** fliken i din app innan du fortsätter.
   > 
   > 
3. I den **Lägg till en plats** bladet namnge facket och välja om du vill klona appkonfiguration från en annan befintlig distributionsplats. Klicka på bockmarkeringen för att fortsätta.
   
    ![Konfigurationskälla][ConfigurationSource1]
   
    Första gången du lägger till en plats, endast har du två alternativ: klona konfiguration från standard uttag i produktion eller inte alls.
    När du har skapat flera platser, kommer du att kunna klona konfigurationen från en plats än den i produktion:
   
    ![Konfigurationen källor][MultipleConfigurationSources]
4. Appens resource-bladet klickar du på **distributionsfack**, klickar på en distributionsplats för att öppna resursbladet för att platsen med en uppsättning mått och konfiguration precis som andra appar. Namnet på platsen visas högst upp på bladet för att påminna dig om att du visar distributionsplatsen.
   
    ![Distribution fack rubrik][StagingTitle]
5. Klicka på app-URL i bladet för den platsen. Observera att distributionsplatsen har sin egen värdnamn och är också en live-app. För att begränsa offentlig åtkomst till distributionsplatsen, se [App Service Web App – blockera Webbåtkomst till icke-produktion distributionsfack](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Det finns inget innehåll efter distribution fack har skapats. Du kan distribuera till facket från en annan databas gren eller en helt annan databas. Du kan också ändra platsens konfiguration. Använd Publicera profil eller distribution av autentiseringsuppgifter som är associerade med distributionsfack för uppdateringar av innehållet.  Du kan till exempel [publicera till den här platsen med git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Vilka inställningar växlas ut?
När du klonar konfigurationen från en annan distributionsplats kan klonade konfigurationen redigeras. Dessutom följer vissa konfigurationselement innehållet i en växling (inte port specifika) medan andra konfigurationselement förblir på samma plats efter en växling (specifika-port). I listan nedan visas de inställningar som ändras när du växlar mellan platser.

**Inställningar som växlas ut**:

* Allmänna inställningar – till exempel framework-version, 32/64-bitars Web sockets
* App-inställningar (kan konfigureras för att fästs mot en plats)
* Anslutningssträngar (kan konfigureras för att fästs mot en plats)
* Hanterarmappningar
* Inställningar för övervakning och diagnostik
* WebJobs-innehåll

**Inställningar som inte växlas ut**:

* Publiceringsslutpunkterna
* Anpassade domännamn
* SSL-certifikat och bindningar
* Skalningsinställningar
* WebJobs-schemaläggare

Om du vill konfigurera en app-inställningen eller anslutningssträng till fästs mot en plats (inte växlas) att komma åt den **programinställningar** bladet för en specifik plats, välj sedan den **platsinställning** kryssrutan för konfigurationen element som bör fästs mot facket. Markera en konfigurationselement som fack specifika har effekten av att etablera det elementet som inte under drift över alla distributionsplatser som är associerat med appen.

![Platsinställningar][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Växla distributionsfack 
Du kan växla distributionsfack i den **översikt** eller **distributionsfack** vy över resursbladet för din app.

> [!IMPORTANT]
> Innan du växla en app från ett distributionsfack till produktion, se till att alla icke-specifika platsinställningar konfigureras exakt som du vill ha i mål-växling.
> 
> 

1. Om du vill växla distributionsfack, klickar du på den **växling** knappen i kommandofältet i appen eller i kommandofältet för en distributionsplats.
   
    ![Växla, knapp][SwapButtonBar]

2. Se till att växla käll- och växling är korrekt inställda. Swap-målet är vanligtvis produktionsplatsen. Klicka på **OK** att slutföra åtgärden. När åtgärden har slutförts har Distributionsplatserna bytts.

    ![Slutför växling](./media/web-sites-staged-publishing/SwapImmediately.png)

    För den **växling med förhandsgranskning** växlingstyp, se [växling med förhandsgranskning (med swap)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Växla med förhandsgranskning (med swap)

Förenkla verifieringen av plats-konfiguration-element, till exempel anslutningssträngar växling med förhandsgranskning eller med bytet.
För verksamhetskritiska arbetsbelastningar du vill validera som appen fungerar som förväntat när den produktionsplatsen konfigurationen tillämpas och du måste utföra sådana verifiering *innan* appen växlar till produktion. Växling med förhandsgranskning är vad du behöver.

> [!NOTE]
> Växling med förhandsgranskning stöds inte i web apps på Linux.

När du använder den **växla med förhandsgranskning** alternativet (se [växla distributionsfack](#Swap)), App Service gör följande:

- Behåller målplatsen har inte ändrats så att befintliga arbetsbelastningen på platsen (t.ex produktion) inte påverkas.
- Gäller konfigurationselement för destinationsplatsen till källplatsen, inklusive fack-specifika anslutningssträngar och appinställningar.
- Startar om arbetsprocesser på käll-platsen med hjälp av dessa tidigare nämnda konfigurationselement.
- När du har slutfört växlingen: flyttar plap warmed upp källplatsen till målplatsen. Målplatsen flyttas till käll-fack som i en manuell växling.
- När du avbryter växlingen: återställer konfigurationselement för källplatsen till källplatsen.

Du kan förhandsgranska exakt hur appen kommer att fungera med den destinationsplatsen konfiguration. När du har slutfört verifiering kan slutföra du växling i ett separat steg. Det här steget har vidare fördelen som källplatsen redan värmas upp med önskad konfiguration och klienter att uppleva inte något avbrott.  

Exempel för Azure PowerShell-cmdletarna för flera fas växling ingår i Azure PowerShell-cmdlets för distribution av platser.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling
Autoväxling effektiviserar DevOps-scenarier där du vill distribuera kontinuerligt din app med noll kallstart och utan stilleståndstid för slutkunder av appen. När en distributionsplats konfigureras för Autoväxling till produktion, varje gång som du har överfört din uppdatering till platsen, App Service automatiskt att växla som appen till produktion när den har redan värmas upp på platsen.

> [!IMPORTANT]
> När du aktiverar automatisk växling för en plats, kontrollera att platskonfigurationen är exakt den konfiguration som är avsedd för målplatsen (vanligtvis produktionsplatsen).
> 
> 

> [!NOTE]
> Autoväxling stöds inte i web apps på Linux.

Det är enkelt att konfigurera automatisk växling för en plats. Följ de här stegen:

1. I **Distributionsfack**, Välj en produktionsplatsen och välj **programinställningar** i resursbladet för den platsen.  
   
    ![][Autoswap1]
2. Välj **på** för **automatiskt växla**, Välj önskat mål-plats i **automatiskt växla fack**, och klicka på **spara** i kommandofältet. Kontrollera att konfigurationen för facket är exakt den konfiguration som är avsedd för målplatsen.
   
    Den **meddelanden** fliken blinkar en grön **lyckades** när åtgärden har slutförts.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Om du vill testa automatisk växling för din app, kan du först välja en icke-produktion målplatsen i **automatiskt växla fack** att bekanta dig med funktionen.  
   > 
   > 
3. Köra en kod push till den distributionsplatsen. Autoväxling händer om en stund och uppdateringen återspeglas på URL: en för din målplatsen.

<a name="Rollback"></a>

## <a name="roll-back-a-production-app-after-swap"></a>Återställa en produktionsapp efter växling
Om några fel har identifierats i produktion efter en växling, återställa platserna deras före växling tillstånd genom att byta samma två platserna omedelbart.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Anpassade värma upp innan växling
Vissa appar kan kräva anpassade värma upp åtgärder. Den `applicationInitialization` konfigurationselementet i web.config kan du ange anpassade initieringen åtgärder som ska utföras innan en begäran tas emot. Växlingen väntar för den här anpassade värma upp att slutföra. Här är ett exempel web.config-fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostname="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap-progress"></a>Övervaka förloppet för växling

Ibland tar växlingen lite tid att slutföra, till exempel när den app som växlar har en lång värma upp tid. Du kan få mer information om växling åtgärder i den [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) i den [Azure-portalen](https://portal.azure.com).

I din app-sida på portalen i det vänstra navigeringsfältet väljer **aktivitetsloggen**.

En växlingsåtgärd visas i loggfråga som `Slotsswap`. Du kan expandera den och välj en av katalogtjänstens eller fel att se detaljerna.

![Aktivitetsloggen för växling](media/web-sites-staged-publishing/activity-log.png)

<a name="Delete"></a>

## <a name="delete-a-deployment-slot"></a>Ta bort ett distributionsfack
I bladet för en distributionsplats, öppnar du den distributionsplats bladet, klickar du på **översikt** (standardsidan) och klicka på **ta bort** i kommandofältet.  

![Ta bort ett Distributionsfack][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-azure-powershell"></a>Automatisera med Azure PowerShell

Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera Azure via Windows PowerShell, inklusive stöd för att hantera distributionsplatser i Azure App Service.

* Information om att installera och konfigurera Azure PowerShell och om autentisering av Azure PowerShell med Azure-prenumerationen finns i [hur du installerar och konfigurerar du Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Skapa en webbapp
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Skapa ett distributionsfack
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Initiera en växling med förhandsgranskning (med swap) och tillämpa platskonfigurationen för mål på källplatsen
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Avbryt en väntande växling (växling med granskning) och återställa platskonfigurationen för källa
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Växla distributionsfack
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Övervaka växling händelser i aktivitetsloggen
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-deployment-slot"></a>Ta bort distributionsplats
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-azure-cli"></a>Automatisera med Azure CLI

För [Azure CLI](https://github.com/Azure/azure-cli) kommandon för distributionsplatser, se [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Nästa steg
[Azure App Service Web App – blockera Webbåtkomst till icke-produktion distributionsfack](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)  
[Introduktion till App Service i Linux](../app-service/containers/app-service-linux-intro.md)  
[Kostnadsfri utvärderingsversion av Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

<!-- IMAGES -->
[QGAddNewDeploymentSlot]:  ./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png
[AddNewDeploymentSlotDialog]: ./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png
[ConfigurationSource1]: ./media/web-sites-staged-publishing/ConfigurationSource1.png
[MultipleConfigurationSources]: ./media/web-sites-staged-publishing/MultipleConfigurationSources.png
[SiteListWithStagedSite]: ./media/web-sites-staged-publishing/SiteListWithStagedSite.png
[StagingTitle]: ./media/web-sites-staged-publishing/StagingTitle.png
[SwapButtonBar]: ./media/web-sites-staged-publishing/SwapButtonBar.png
[SwapConfirmationDialog]:  ./media/web-sites-staged-publishing/SwapConfirmationDialog.png
[DeleteStagingSiteButton]: ./media/web-sites-staged-publishing/DeleteStagingSiteButton.png
[SwapDeploymentsDialog]: ./media/web-sites-staged-publishing/SwapDeploymentsDialog.png
[Autoswap1]: ./media/web-sites-staged-publishing/AutoSwap01.png
[Autoswap2]: ./media/web-sites-staged-publishing/AutoSwap02.png
[SlotSettings]: ./media/web-sites-staged-publishing/SlotSetting.png

