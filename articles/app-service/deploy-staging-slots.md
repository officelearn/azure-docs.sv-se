---
title: Konfigurera mellanlagringsmiljöer för webbappar i Azure App Service | Microsoft Docs
description: Lär dig hur du använder mellanlagrad publicering för web apps i Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
writer: cephalin
manager: jpconnoc
editor: mollybos
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2019
ms.author: cephalin
ms.openlocfilehash: 1d0f89285095e7edd67883a2bad1411f6e8942d2
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107214"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurera mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

> [!NOTE]
> Den här guiden visar hur du hanterar platser med hjälp av en ny förhandsgranskningssidan för hantering. Kunder som används för att befintliga hanteringssidan kan fortsätta att använda befintliga fack hanteringssidan som innan. 
>

När du distribuerar din webbapp, web Apps i Linux-, mobil serverdel och API-app till [Apptjänst](https://go.microsoft.com/fwlink/?LinkId=529714), du kan distribuera till en separat distributionsplats i stället för standard-produktionsplatsen när de körs i den **Standard**, **Premium**, eller **isolerad** nivå för App Service-plan. Distributionsplatser är faktiskt liveappar med egna värddatornamn. App webbappsinnehåll och konfigurationselement kan bytas mellan två distributionsfack, inklusive produktionsplatsen. Distribuera programmet till en icke-produktionsplatsen har följande fördelar:

* Du kan validera appändringar i en mellanlagringsplats för distributionen innan du växlar med produktionsplatsen.
* Distribuera en app till en plats först och växlar den till produktionen ser till att alla instanser av facket värmas upp innan du håller på att växlas till produktion. Detta eliminerar avbrott när du distribuerar din app. Trafik-omdirigering sker sömlös och inga förfrågningar ignoreras på grund av swap-åtgärder. Den här hela arbetsflödet kan automatiseras genom att konfigurera [automatiskt växla](#Auto-Swap) när före swap-verifiering är inte behövs.
* Efter en växling har facket med tidigare mellanlagrad app nu appen tidigare produktion. Om ändringarna växlas över till produktionsplatsen inte som förväntat, du kan utföra samma växlingen direkt för att få igång ”senaste kända bra webbplatsen” tillbaka.

Varje nivå för App Service-plan har stöd för ett annat antal distributionsplatser. Att ta reda på hur många platser stöder din app, se [App tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Om du vill skala din app till en annan nivå måste på mål-nivån stödja antalet platser som din app redan använder. Exempel: om din app har fler än fem platser, kan du inte skala den ned till **Standard** tier, eftersom **Standard** nivån stöder endast fem distributionsplatser.

<a name="Add"></a>

## <a name="add-slot"></a>Lägg till plats
Appen måste köras i den **Standard**, **Premium**, eller **isolerad** nivå för att du kan aktivera flera distributionsplatser.

1. I den [Azure-portalen](https://portal.azure.com/), öppna appens [resurssida](../azure-resource-manager/resource-group-portal.md#manage-resources).

2. I det vänstra navigeringsfönstret väljer du den **distributionsplatser (förhandsgranskning)** och sedan klicka på **Lägg till plats**.
   
    ![Lägg till en ny distributionsplats](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Om appen inte redan är i den **Standard**, **Premium**, eller **isolerad** nivå, du får ett meddelande om de stödda nivåerna för att aktivera mellanlagrad publicering. Nu har du möjlighet att välja **uppgradera** och navigera till den **skala** fliken i din app innan du fortsätter.
   > 

3. I den **Lägg till en plats** dialogrutan namnge facket och välja om du vill klona appkonfiguration från en annan befintlig distributionsplats. Klicka på **Lägg till** att fortsätta.
   
    ![Konfigurationskälla](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Du kan klona konfigurationen från alla befintliga platsen. Inställningar som kan klonas omfattar appinställningar, anslutningssträngar, språk framework-versioner, webbsockets, HTTP-version och plattformen bitarna.

4. När platsen har lagts till, klickar du på **Stäng** att stänga dialogrutan. Nu visas den nya platsen i den **distributionsplatser (förhandsgranskning)** sidan. Som standard den **trafik %** anges till 0 för den nya platsen med alla kund trafik som dirigerats till produktionsplatsen.

5. Klicka på den nya distributionsplatsen för att öppna sida att platsen.
   
    ![Distribution fack rubrik](./media/web-sites-staged-publishing/StagingTitle.png)

    Mellanlagringsplatsen har en hanteringssidan precis som andra App Service-app. Du kan ändra platsens konfiguration. Namnet på platsen visas överst på sidan för att påminna dig om att du visar distributionsplatsen.

6. Klicka på app-URL i platsens resurssida. Distributionsplatsen har sin egen värdnamn och är också en live-app. För att begränsa offentlig åtkomst till distributionsplatsen, se [Azure App Service IP-restriktioner](app-service-ip-restrictions.md).

Den nya distributionsplatsen har inget innehåll, även om du klonar inställningarna från en annan plats. Du kan till exempel [publicera till den här platsen med git](app-service-deploy-local-git.md). Du kan distribuera till facket från en annan databas gren eller en annan databas. 

<a name="AboutConfiguration"></a>

## <a name="which-settings-are-swapped"></a>Vilka inställningar växlas ut?
När du klonar konfigurationen från en annan distributionsplats kan klonade konfigurationen redigeras. Dessutom följa vissa konfigurationselement innehållet i en växling (inte port specifika) medan andra konfigurationselement stannar på samma plats efter en växling (specifika-port). I listan nedan visas de inställningar som ändras när du växlar mellan platser.

**Inställningar som växlas ut**:

* Allmänna inställningar – till exempel framework-version, 32/64-bitars Web sockets
* App-inställningar (kan konfigureras för att fästs mot en plats)
* Anslutningssträngar (kan konfigureras för att fästs mot en plats)
* Hanterarmappningar
* Inställningar för övervakning och diagnostik
* Offentliga certifikat
* WebJobs-innehåll
* Hybridanslutningar

**Inställningar som inte växlas**:

* Publiceringsslutpunkterna
* Anpassade domännamn
* Privata certifikat och SSL-bindningar
* Skalningsinställningar
* WebJobs-schemaläggare

<!-- VNET, IP restrictions, CORS, hybrid connections? -->

Om du vill konfigurera en app-inställningen eller anslutningssträng till fästs mot en specifik plats (inte växlas), navigera till den **programinställningar** för att platsen och välj sedan den **platsinställning** för den konfigurationselement som bör fästs mot facket. Markera en konfigurationselement som fack specifika talar om för App Service att det inte är under drift.

![Platsinställning](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Växla två platser 
Du kan växla distributionsfack i din app **distributionsplatser (förhandsgranskning)** sidan. 

Du kan också växla platser från den **översikt** och **distributionsfack** sidor, men för närvarande är det ger dig den gamla upplevelsen. Den här guiden visar hur du använder det nya användargränssnittet i den **distributionsplatser (förhandsgranskning)** sidan.

> [!IMPORTANT]
> Innan du växla en app från ett distributionsfack till produktion, se till att alla inställningar konfigureras exakt som du vill ha i mål-växling.
> 
> 

Följ dessa steg om du vill växla distributionsfack:

1. Gå till din app **distributionsplatser (förhandsgranskning)** och klicka på **växla**.
   
    ![Växla, knapp](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Den **växla** dialogrutan visar inställningarna i de valda käll- och platser som ska ändras.

2. Välj önskat **källa** och **Target** platser. Målet är vanligtvis produktionsplatsen. Dessutom klickar du på den **ändringar av datakällan** och **Target ändringar** flikarna och kontrollera att konfigurationsändringarna förväntas. När du är klar kan du byta platserna omedelbart genom att klicka på **växling**.

    ![Slutför växling](./media/web-sites-staged-publishing/SwapImmediately.png)

    Om du vill se hur din målplatsen skulle köras med de nya inställningarna innan växlingen faktiskt händer, inte på **växling**, men följer du anvisningarna i [växling med förhandsgranskning](#Multi-Phase).

3. När du är klar stänger du dialogrutan genom att klicka på **Stäng**.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Växla med förhandsgranskning (med swap)

> [!NOTE]
> Växling med förhandsgranskning stöds inte i web apps på Linux.

Verifiera körningarna med inställningarna som du har bytts ut innan du växlar till produktion som den målplats innan växlingen sker. Källplatsen är också värmas upp innan växling, vilket också är önskvärt för verksamhetskritiska program.

När du utför en växling med förhandsgranskning, gör följande i App Service när du startar växlingen:

- Behåller målplatsen har inte ändrats så att befintliga arbetsbelastningen på platsen (t.ex produktion) inte påverkas.
- Gäller konfigurationselement för målplatsen till källplatsen, inklusive fack-specifika anslutningssträngar och appinställningar.
- Startar om arbetsprocesser på käll-platsen med hjälp av dessa konfigurationselement. Du kan bläddra källplatsen och se när appen körs med konfigurationsändringarna.

Om du slutför växlingen i ett separat steg flyttar App Service facket värmas upp källa till mål-plats och på målplatsen till källplatsen. Om du avbryter växlingen lägger App Service konfigurationselement för källplatsen till källplatsen.

Följ dessa steg för att växla med förhandsgranskning.

1. Följ stegen i [växla distributionsfack](#Swap) men välj **utför växling med förhandsgranskning**.

    ![Växla med förhandsgranskning](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogrutan visar hur konfigurationen i källplatsen ändras i fas 1 och hur facket käll- och ändras i fas 2.

2. När du är klar att starta växlingen klickar du på **starta växlingen**.

    När fas 1 har slutförts meddelas du i dialogrutan. Förhandsgranska växling i källplatsen genom att gå till `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. När du är klar att slutföra väntande växlingen Välj **fullständig växling** i **växlingsåtgärd** och klicka på **fullständig växling**.

    Om du vill avbryta en väntande växling, Välj **Avbryt växling** i stället och klickar på **Avbryt växling**.

4. När du är klar stänger du dialogrutan genom att klicka på **Stäng**.

Om du vill automatisera en med-växling, se [automatisera med PowerShell](#automate-with-azure-powershell).

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Återställa växling
Om något fel uppstår för målplatsen (till exempel produktionsplatsen) efter en växling återställa platserna till deras före växling tillstånd genom att byta samma två platserna omedelbart.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling

> [!NOTE]
> Autoväxling stöds inte i web apps på Linux.

Autoväxling effektiviserar DevOps-scenarier där du vill distribuera din app kontinuerligt med noll kallstart och utan stilleståndstid för slutkunder av appen. När en plats autoswaps till produktion, varje gång du koden ändras till att platsen, App Service automatiskt byter ut appen till produktion när den är värmas upp i källplatsen.

   > [!NOTE]
   > Innan du konfigurerar automatisk växling för produktionsplatsen bör du överväga att testa automatisk växling på en icke-produktion målplatsen först.
   > 

Följ dessa steg om du vill konfigurera automatisk växling:

1. Gå till appens sida. Välj **distributionsplatser (förhandsgranskning)** > *\<önskade källplatsen >* > **programinställningar**.
   
2. I **automatiskt växla**väljer **på**, välj sedan önskad målplatsen i **automatiskt växla fack**, och klicka på **spara** i kommandofältet. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Köra en kod push till källplatsen. Autoväxling händer om en stund och uppdateringen återspeglas på URL: en för din målplatsen.

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Anpassade värma upp
När du använder [Auto-Swap](#Auto-Swap), vissa appar kan kräva anpassade värma upp åtgärder före växlingen. Den `applicationInitialization` konfigurationselementet i web.config kan du ange anpassade initieringen åtgärder som ska utföras. Växlingen väntar för den här anpassade värma upp ska slutföras innan du växlar med på målplatsen. Här är ett exempel web.config-fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

## <a name="monitor-swap"></a>Övervaka växling

Om växlingen tar lång tid att slutföra, kan du få information om växlingen i den [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

I appens sida i portalen i det vänstra navigeringsfältet väljer **aktivitetsloggen**.

En växlingsåtgärd visas i loggfråga som `Swap Web App Slots`. Du kan expandera den och välj en av katalogtjänstens eller fel att se detaljerna.

## <a name="route-traffic"></a>Dirigera trafik

Som standard alla klientbegäranden till appens produktions-URL (`http://<app_name>.azurewebsites.net`) dirigeras till produktionsplatsen. Du kan vidarebefordra en del av trafiken till en annan plats. Den här funktionen är användbar om du behöver Användarfeedback för en ny uppdatering, men du inte är redo att lansera till produktion.

### <a name="route-production-traffic-automatically"></a>Dirigera produktionstrafik automatiskt

Följ dessa steg för att dirigera produktionstrafik automatiskt:

1. Gå till appens sida och välj **distributionsplatser (förhandsgranskning)**.

2. I den **trafik %** kolumnen av plats som du vill dirigera till, ange ett procentvärde (mellan 0 och 100) som representerar hur mycket av den totala trafiken som du vill dirigera. Klicka på **Spara**.

    ![](./media/web-sites-staged-publishing/RouteTraffic.png)

När inställningen sparas kan dirigeras den angivna procentandelen av klienter slumpmässigt till icke-produktionsplatsen. 

När en klient dirigeras automatiskt till en specifik plats dess ”fästa” till platsen för den session som klienten. På klientens webbläsare kan du se vilken plats som din session har fästs på genom att titta på den `x-ms-routing-name` cookie i HTTP-huvuden. En begäran som dirigeras till ”” mellanlagringsplatsen har cookien `x-ms-routing-name=staging`. En begäran som dirigeras till produktionsplatsen har cookien `x-ms-routing-name=self`.

### <a name="route-production-traffic-manually"></a>Dirigera produktionstrafik manuellt

Förutom automatisk trafikroutning kan App Service dirigera begäranden till en specifik plats. Detta är användbart när du vill att användarna ska kunna delta i eller avstår från beta-app. För att dirigera produktionstrafik manuellt, använder du den `x-ms-routing-name` frågeparameter.

Så att användarna kan välja bort beta-app, till exempel kan du placera den här länken på din webbsida:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Strängen `x-ms-routing-name=self` anger produktionsplatsen. När klientens webbläsare får åtkomst till länken, inte bara omdirigeras till produktionsplatsen, men varje efterföljande begäran har den `x-ms-routing-name=self` cookie som PIN-koder, sessionen till produktionsplatsen.

Så att användare kan välja att beta-app genom att ange samma Frågeparametern namnet på den icke-produktionsplatsen, till exempel:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

<a name="Delete"></a>

## <a name="delete-slot"></a>Ta bort fack

Gå till appens sida. Välj **distributionsplatser (förhandsgranskning)** > *\<fack att ta bort >* > **översikt**. Klicka på **ta bort** i kommandofältet.  

![Ta bort ett Distributionsfack](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisera med PowerShell

Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera Azure via Windows PowerShell, inklusive stöd för att hantera distributionsplatser i Azure App Service.

Information om att installera och konfigurera Azure PowerShell och om autentisering av Azure PowerShell med Azure-prenumerationen finns i [hur du installerar och konfigurerar du Microsoft Azure PowerShell](/powershell/azure/overview).  

- - -
### <a name="create-web-app"></a>Skapa webbapp
```PowerShell
New-AzureRmWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

- - -
### <a name="create-slot"></a>Skapa platsen
```PowerShell
New-AzureRmWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

- - -
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Initiera växling med förhandsgranskning (med swap) och tillämpa platskonfigurationen för mål på källplatsen
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

- - -
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Avbryta väntande växlingsutrymme (växling med granskning) och återställa platskonfigurationen för källa
```PowerShell
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

- - -
### <a name="swap-deployment-slots"></a>Växla distributionsfack
```PowerShell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzureRmResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Övervaka växling händelser i loggen-aktivitet
```PowerShell
Get-AzureRmLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

- - -
### <a name="delete-slot"></a>Ta bort fack
```
Remove-AzureRmResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

- - -
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatisera med CLI

För [Azure CLI](https://github.com/Azure/azure-cli) kommandon för distributionsplatser, se [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="next-steps"></a>Nästa steg
[Blockera åtkomst till icke-produktionsplatser](app-service-ip-restrictions.md)
