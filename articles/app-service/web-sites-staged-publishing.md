---
title: Skapa mellanlagringsmiljöer för web apps i Azure App Service | Microsoft Docs
description: Lär dig använda stegvis publicering för web apps i Azure App Service.
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
ms.openlocfilehash: c02b7a74eea6973d6ccfbc1cc59d15bfd5cb5b77
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Skapa mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

När du distribuerar ditt webbprogram, webbprogram på Linux, mobila serverdel och API-appen [Apptjänst](http://go.microsoft.com/fwlink/?LinkId=529714), du kan distribuera till en separat distributionsplats i stället för standard produktionsplatsen när den körs i den **Standard** eller **Premium** App plan tjänstnivån. Distributionsplatser är faktiskt live appar med sina egna värdnamn. Appen innehåll och konfigurationer element kan växlas mellan två distributionsplatser, inklusive produktionsplatsen. Distribuera programmet till en distributionsplats har följande fördelar:

* Du kan verifiera app-ändringar i en distribution mellanlagringsplatsen innan växling med produktionsplatsen.
* Först distribuera en app till en plats och växla till produktion säkerställer att alla instanser av facket varmkörts innan som ska växlas över till produktion. Detta eliminerar avbrott när du distribuerar din app. Trafik för omdirigering är sömlös och inga begäranden tas bort på grund av byte åtgärder. Hela arbetsflödet kan automatiseras genom att konfigurera [automatiskt växla](#Auto-Swap) när före växlingen verifiering inte behövs.
* Efter en växling har på plats med tidigare mellanlagrade appen nu tidigare produktionsprogrammet. Om ändringarna växlas över till produktionsplatsen är inte som du förväntade dig, kan du utföra samma växlingen direkt för att få igång ”senaste kända fungerande webbplatsen” tillbaka.

Varje nivå för App Service-plan stöder olika antal distributionsplatser. Ta reda på antalet platser har stöd för din app-nivå, se [App Tjänstbegränsningarna](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#app-service-limits).

* När appen har flera platser, kan du inte ändra nivån.
* Skalning är inte tillgänglig för icke-produktoionsplats.
* Länkade resurshantering stöds inte för icke-produktoionsplats. I den [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) , du kan undvika den här potentiella effekten på en produktionsplatsen genom att tillfälligt flytta icke-produktionsplatsen till ett annat skikt i App Service-plan. Observera att icke-produktionsplatsen måste återigen delar samma nivå med produktionsplatsen innan du kan byta ut de två platserna.

<a name="Add"></a>

## <a name="add-a-deployment-slot"></a>Lägga till en distributionsplats
Appen måste köras den **Standard** eller **Premium** nivån för att du vill aktivera flera distributionsplatser.

1. I den [Azure Portal](https://portal.azure.com/), öppna appens [resursbladet](../azure-resource-manager/resource-group-portal.md#manage-resources).
2. Välj den **distributionsfack** alternativ och klicka sedan på **Lägg till plats**.
   
    ![Lägg till en ny distributionsplats][QGAddNewDeploymentSlot]
   
   > [!NOTE]
   > Om appen inte redan är i den **Standard** eller **Premium** nivån, visas ett meddelande som anger nivåerna som stöds för att aktivera stegvis publicering. Nu har du möjlighet att välja **uppgradera** och navigera till den **skala** fliken i din app innan du fortsätter.
   > 
   > 
3. I den **lägga till en plats** bladet namnge facket och välja om du vill klona app konfigurationen från en annan befintlig distributionsplatsen. Klicka på bockmarkeringen för att fortsätta.
   
    ![Konfigurationskälla][ConfigurationSource1]
   
    Första gången du lägger till en plats kan du bara har två alternativ: klona konfigurationen från standard-plats i produktion eller inte alls.
    När du har skapat flera platser, kommer du att kunna klona konfigurationen från en annan plats än den i produktion:
   
    ![Konfiguration av datakällor][MultipleConfigurationSources]
4. I resursbladet för din app, klickar du på **distributionsfack**, klicka på en distributionsplats för att öppna den platsens resursbladet med en uppsättning mått och konfiguration precis som alla andra appar. Namnet på platsen visas längst upp på bladet för att påminna dig om att du tittar på distributionsplatsen.
   
    ![Distribution fack rubrik][StagingTitle]
5. Klicka på app-URL i bladet för den platsen. Lägg märke till distributionsplatsen har sin egen värdnamn och är också en live-app. Om du vill begränsa offentlig åtkomst till distributionsplatsen, se [App Service Web App – blockera Webbåtkomst till icke-produktion distributionsplatser](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/).

Det finns inget innehåll efter distributionen fack har skapats. Du kan distribuera till facket från en annan databas gren eller en helt annan databas. Du kan också ändra platsens konfiguration. Använd Publicera profil eller distribution av autentiseringsuppgifter som är kopplade till distributionsplatsen för uppdateringar av innehållet.  Du kan till exempel [publicera till den här platsen med git](app-service-deploy-local-git.md).

<a name="AboutConfiguration"></a>

## <a name="configuration-for-deployment-slots"></a>Konfigurationen för distributionsplatser
När du klonar konfigurationen från en annan distributionsplats kan klonade konfigurationen redigeras. Dessutom följer vissa konfigurationselement innehållet över växlingsutrymme (inte port specifika) medan andra konfigurationselement behålls på samma plats efter en växling (specifika-port). Följande tabell visar den konfiguration som ändras när du växlar platser.

**Inställningar som bytts**:

* Allmänna inställningar – till exempel framework-version, 32/64-bitars Web sockets
* App-inställningar (kan konfigureras för att hålla dig till en plats)
* Anslutningssträngar (kan konfigureras för att hålla dig till en plats)
* Hanterarmappningar
* Inställningar för övervakning och diagnostik
* WebJobs innehåll

**Inställningar som inte har bytts**:

* Publishing slutpunkter
* Anpassade domännamn
* SSL-certifikat och bindningar
* Skalinställningarna
* WebJobs planeringsprogram

Om du vill konfigurera en app inställningen eller anslutningssträngen fästa till en plats (inte växlas) att komma åt den **programinställningar** bladet för en viss plats, välj sedan den **fack inställningen** för konfigurationen element som ska behålla på plats. Observera att markera en konfigurationselement som fack specifika har effekten av att etablera att element som inte kan över alla distributionsplatser som är associerat med appen.

![Platsinställningar][SlotSettings]

<a name="Swap"></a>

## <a name="swap-deployment-slots"></a>Växla distributionsplatser 
Du kan växla distributionsplatser i den **översikt** eller **distributionsplatser** vy över resursbladet för din app.

> [!IMPORTANT]
> Kontrollera att alla icke-specifika platsinställningarna konfigureras exakt som du vill ha i mål-växlingen innan du byter en app från en distributionsplats i produktionen.
> 
> 

1. Om du vill växla distributionsplatser, klickar du på den **växlingen** i kommandofältet appen eller i kommandofältet för en distributionsplats.
   
    ![Växla knapp][SwapButtonBar]

2. Kontrollera att växlingen käll- och växling är korrekt inställda. Växlingen målet är vanligtvis produktionsplatsen. Klicka på **OK** att slutföra åtgärden. När åtgärden har slutförts har på distributionsplatser bytts.

    ![Slutför växling](./media/web-sites-staged-publishing/SwapImmediately.png)

    För den **växlingen med preview** växla typ, se [växlingen med preview (flera fasen swap)](#Multi-Phase).  

<a name="Multi-Phase"></a>

## <a name="swap-with-preview-multi-phase-swap"></a>Växla med förhandsgranskning (flera fasen swap)

Växlingen med Förhandsgranska eller flera fasen växlingen förenkla verifieringen av fack konfigurationselement, till exempel anslutningssträngar.
För verksamhetskritiska arbetsbelastningar du vill validera som appen fungerar som förväntat när den produktionsplatsen konfigurationen tillämpas och du måste utföra sådana validering *innan* appen växlas till produktionen. Växlingen med preview är vad du behöver.

> [!NOTE]
> Växlingen med preview stöds inte i web apps i Linux.

När du använder den **växla med förhandsgranskning** alternativet (se [växla distributionsplatser](#Swap)), Apptjänst innehåller följande:

- Behåller destinationsplatsen oförändrade så att befintliga arbetsbelastningen på platsen (t.ex. produktion) inte påverkas.
- Gäller konfigurationselement för destinationsplatsen till källplatsen, inklusive anslutningssträngar för plats-specifika och app-inställningar.
- Startar om arbetsprocesser på källplatsen med hjälp av dessa ovannämnda konfigurationselement.
- När du har slutfört växlingen: flyttar Förproduktion warmed upp källplatsen till destinationsplatsen. Destinationsplatsen flyttas till källplatsen som en manuell växling.
- När du avbryter växlingen: tillämpa konfigurationselement för källplatsen till källplatsen.

Du kan förhandsgranska exakt hur appen fungerar med konfigurationen på destinationsplatsen. När du har slutfört verifieringen slutföra växling i ett separat steg. Det här steget har lagts till fördelen med att källplatsen är redan varmkörts med önskad konfiguration och klienter får inte någon avbrottstid.  

Prover för Azure PowerShell-cmdlets för flera fasen växlingen ingår i Azure PowerShell-cmdlets för distribution av platser.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling
Automatisk växling förenklar DevOps-scenarier där du vill distribuera appen med noll kallstart och driftstopp kontinuerligt för slutkunder av appen. När en distributionsplats konfigureras för automatisk växling till produktion, varje gång du push-kod-uppdatering till platsen, Apptjänst automatiskt att växla som appen till produktion när det redan har varmkörts på plats.

> [!IMPORTANT]
> När du aktiverar automatisk växling för en plats, kontrollera att platskonfigurationen är exakt den konfiguration som är avsedda för mål-plats (vanligtvis produktionsplatsen).
> 
> 

> [!NOTE]
> Automatisk växling stöds inte i web apps i Linux.

Det är enkelt att konfigurera automatisk växling för en plats. Följ stegen nedan:

1. I **Distributionsfack**, Välj en produktionsplatsen och välj **programinställningar** i resursbladet för den platsen.  
   
    ![][Autoswap1]
2. Välj **på** för **automatiskt växla**, väljer önskat mål-plats i **automatiskt växla fack**, och klicka på **spara** i kommandofältet. Kontrollera konfigurationen för platsen är exakt den konfiguration som är avsedda för mål-plats.
   
    Den **meddelanden** fliken blinkar en grön **lyckade** när åtgärden har slutförts.
   
    ![][Autoswap2]
   
   > [!NOTE]
   > Om du vill testa automatisk växling för din app kan du först välja en icke-produktion mål plats i **automatiskt växla fack** att bekanta dig med funktionen.  
   > 
   > 
3. Köra en kod push till den distributionsplatsen. Automatisk växling sker efter en kort tid och uppdateringen avspeglas på mål-platsens URL.

<a name="Rollback"></a>

## <a name="to-rollback-a-production-app-after-swap"></a>Att återställa en produktionsapp efter växling
Om eventuella fel identifieras i produktion efter en växling fack återställa uttagen deras före växlingen tillstånd genom att byta samma två platser direkt.

<a name="Warm-up"></a>

## <a name="custom-warm-up-before-swap"></a>Anpassade värmts före växlingen
Vissa appar kan kräva anpassade värmts åtgärder. Den `applicationInitialization` konfigurationselementet i web.config kan du ange anpassade initieringen åtgärder kan utföras innan en begäran tas emot. Byte väntar på den här anpassade värmts ska slutföras. Här är ett exempel web.config-fragment.

    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostname="[app hostname]" />
    </applicationInitialization>

<a name="Delete"></a>

## <a name="to-delete-a-deployment-slot"></a>Ta bort en distributionsplats
Öppna bladet för den distributionsplatsen i bladet för en distributionsplats, klicka på **översikt** (standardsidan) och klicka på **ta bort** i kommandofältet.  

![Ta bort en distributionsplats][DeleteStagingSiteButton]

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="azure-powershell-cmdlets-for-deployment-slots"></a>Azure PowerShell-cmdlets för distributionsplatser
Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera Azure via Windows PowerShell, bland annat stöd för att hantera distributionsplatser i Azure App Service.

* Information om att installera och konfigurera Azure PowerShell och på autentisera Azure PowerShell med Azure-prenumerationen finns [hur du installerar och konfigurerar Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-a-web-app"></a>Skapa en webbapp
```
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-a-deployment-slot"></a>Skapa en distributionsplats
```
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-a-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Påbörja en växling med preview (flera fasen swap) och tillämpa mål platskonfigurationen på källplatsen
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Avbryta en väntande växling (swap med granskning) och återställa platskonfigurationen för källa
```
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Växla distributionsplatser
```
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="delete-deployment-slot"></a>Ta bort distributionsplatsen
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="azure-command-line-interface-azure-cli-commands-for-deployment-slots"></a>Azure-kommandoradsgränssnittet (Azure CLI)-kommandon för distributionsplatser
Azure CLI tillhandahåller plattformsoberoende kommandon för att arbeta med Azure, inklusive stöd för att hantera App Service-distributionsplatser.

* Anvisningar för att installera och konfigurera Azure CLI, inklusive information om hur du ansluter Azure CLI på Azure-prenumerationen, finns [installera och konfigurera Azure CLI](../cli-install-nodejs.md).
* Om du vill visa en lista med kommandon som är tillgängliga för Azure App Service i Azure CLI, anropa `azure site -h`.

> [!NOTE] 
> För [Azure CLI 2.0](https://github.com/Azure/azure-cli) kommandon för distributionsplatser, se [az webapp distributionsplatsen](/cli/azure/webapp/deployment/slot).

- - -
### <a name="azure-site-list"></a>Azure platslista
Information om appar i den aktuella prenumerationen anropa **azure platslista**, som i följande exempel.

`azure site list webappslotstest`

- - -
### <a name="azure-site-create"></a>Skapa Azure-webbplats
Om du vill skapa en distributionsplats, anropa **azure plats skapa** och ange namnet på en befintlig app och namnet på fack för att skapa, som i följande exempel.

`azure site create webappslotstest --slot staging`

Om du vill aktivera källkontrollen för den nya platsen använder den **--git** enligt följande exempel.

`azure site create --git webappslotstest --slot staging`

- - -
### <a name="azure-site-swap"></a>Azure site växlingsutrymme
Använd för att göra uppdaterade distributionen fack produktionsprogrammet den **azure plats växlingen** kommando för att utföra en växlingsåtgärd, som i följande exempel. Produktionsprogrammet får inte någon stillestånd eller ska genomgå kallstart.

`azure site swap webappslotstest`

- - -
### <a name="azure-site-delete"></a>ta bort Azure-webbplats
Om du vill ta bort en distributionsplats som inte längre behövs, Använd den **ta bort azure site** kommandot, som i följande exempel.

`azure site delete webappslotstest --slot staging`

- - -
> [!NOTE]
> Se hur en webbapp fungerar i praktiken. [Prova App Service](https://azure.microsoft.com/try/app-service/) omedelbart och skapa en tillfällig startapp – inget kreditkort behövs, inga åtaganden.
> 
> 

## <a name="next-steps"></a>Nästa steg
[Azure App Service Web App – blockera Webbåtkomst till icke-produktion distributionsplatser](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)
[introduktion till App Service på Linux](../app-service/containers/app-service-linux-intro.md)
[kostnadsfri utvärderingsversion av Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/)

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

