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
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: cbf287aef2c1792033a198070da605014a7b6281
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272849"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurera mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

När du distribuerar din webbapp, web Apps i Linux-, mobil serverdel och API-app till [Apptjänst](https://go.microsoft.com/fwlink/?LinkId=529714), du kan distribuera till en separat distributionsplats i stället för standard-produktionsplatsen när de körs i den **Standard**, **Premium**, eller **isolerad** nivå för App Service-plan. Distributionsplatser är faktiskt liveappar med egna värddatornamn. App webbappsinnehåll och konfigurationselement kan bytas mellan två distributionsfack, inklusive produktionsplatsen. Distribuera programmet till en icke-produktionsplatsen har följande fördelar:

* Du kan validera appändringar i en mellanlagringsplats för distributionen innan du växlar med produktionsplatsen.
* Distribuera en app till en plats först och växlar den till produktionen ser till att alla instanser av facket värmas upp innan du håller på att växlas till produktion. Detta eliminerar avbrott när du distribuerar din app. Trafik-omdirigering sker sömlös och inga förfrågningar ignoreras på grund av swap-åtgärder. Den här hela arbetsflödet kan automatiseras genom att konfigurera [automatiskt växla](#Auto-Swap) när före swap-verifiering är inte behövs.
* Efter en växling har facket med tidigare mellanlagrad app nu appen tidigare produktion. Om ändringarna växlas över till produktionsplatsen inte som förväntat, du kan utföra samma växlingen direkt för att få igång ”senaste kända bra webbplatsen” tillbaka.

Varje nivå för App Service-plan har stöd för ett annat antal distributionsplatser och det finns inga ytterligare avgifter för användning av distributionsfack. Att ta reda på hur många platser stöder din app, se [App tjänstbegränsningar](https://docs.microsoft.com/azure/azure-subscription-service-limits#app-service-limits). Om du vill skala din app till en annan nivå måste på mål-nivån stödja antalet platser som din app redan använder. Exempel: om din app har fler än fem platser, kan du inte skala den ned till **Standard** tier, eftersom **Standard** nivån stöder endast fem distributionsplatser. 

<a name="Add"></a>

## <a name="add-slot"></a>Lägg till plats
Appen måste köras i den **Standard**, **Premium**, eller **isolerad** nivå för att du kan aktivera flera distributionsplatser.

1. I den [Azure-portalen](https://portal.azure.com/), öppna appens [resurssida](../azure-resource-manager/manage-resources-portal.md#manage-resources).

2. I det vänstra navigeringsfönstret väljer du den **distributionsfack** och sedan klicka på **Lägg till plats**.
   
    ![Lägg till en ny distributionsplats](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Om appen inte redan är i den **Standard**, **Premium**, eller **isolerad** nivå, du får ett meddelande om de stödda nivåerna för att aktivera mellanlagrad publicering. Nu har du möjlighet att välja **uppgradera** och navigera till den **skala** fliken i din app innan du fortsätter.
   > 

3. I den **Lägg till en plats** dialogrutan namnge facket och välja om du vill klona appkonfiguration från en annan befintlig distributionsplats. Klicka på **Lägg till** att fortsätta.
   
    ![Konfigurationskälla](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Du kan klona konfigurationen från alla befintliga platsen. Inställningar som kan klonas omfattar appinställningar, anslutningssträngar, språk framework-versioner, webbsockets, HTTP-version och plattformen bitarna.

4. När platsen har lagts till, klickar du på **Stäng** att stänga dialogrutan. Nu visas den nya platsen i den **distributionsfack** sidan. Som standard den **trafik %** anges till 0 för den nya platsen med alla kund trafik som dirigerats till produktionsplatsen.

5. Klicka på den nya distributionsplatsen för att öppna sida att platsen.
   
    ![Distribution fack rubrik](./media/web-sites-staged-publishing/StagingTitle.png)

    Mellanlagringsplatsen har en hanteringssidan precis som andra App Service-app. Du kan ändra platsens konfiguration. Namnet på platsen visas överst på sidan för att påminna dig om att du visar distributionsplatsen.

6. Klicka på app-URL i platsens resurssida. Distributionsplatsen har sin egen värdnamn och är också en live-app. För att begränsa offentlig åtkomst till distributionsplatsen, se [Azure App Service IP-restriktioner](app-service-ip-restrictions.md).

Den nya distributionsplatsen har inget innehåll, även om du klonar inställningarna från en annan plats. Du kan till exempel [publicera till den här platsen med git](app-service-deploy-local-git.md). Du kan distribuera till facket från en annan databas gren eller en annan databas. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-swap"></a>Vad som händer under bytet

[Växla åtgärdsstegen](#swap-operation-steps)
[växlas ut vilka inställningar?](#which-settings-are-swapped)

### <a name="swap-operation-steps"></a>Växla åtgärdsstegen

När du växlar mellan två platser (vanligtvis från en mellanlagringsplatsen till produktionsplatsen) gör följande för att säkerställa att målplatsen inte drabbas av App Service:

1. Använd följande inställningar från målplatsen (t.ex. produktionsplatsen) till alla instanser av källplatsen: 
    - [Plats-specifika](#which-settings-are-swapped) appinställningar och anslutningssträngar, om tillämpligt.
    - [Kontinuerlig distribution](deploy-continuous-deployment.md) inställningar, om aktiverad.
    - [App Service-autentisering](overview-authentication-authorization.md) inställningar, om aktiverad.
    Någon av ovanstående fall utlöser alla instanser i käll-facket för att starta om. Under [växling med förhandsgranskning](#Multi-Phase), detta markerar slutet av den första fasen där växlingen har pausats och du kan kontrollera att källplatsen fungerar korrekt med mål platsinställningar.

1. Vänta tills alla instanser i käll-facket för att slutföra sin omstart. Om det inte går att starta om en instans, återställs alla ändringar till källplatsen växlingen och avbryter åtgärden.

1. Om [lokalt cacheminne](overview-local-cache.md) är aktiverat kan utlösa initiering av lokal cache genom att göra HTTP begära till programrot-(”/”) i varje instans av källplatsen och vänta tills varje instans returnerar alla HTTP-svar. Initiering av lokal cache gör att en ny omstart på varje instans.

1. Om [Autoväxling](#Auto-Swap) har aktiverats med [anpassade värma upp](#custom-warm-up), utlösare [program Initiation](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) genom att göra en HTTP-begäran till programrot-(”/”) i varje instans av källan fack. Om en instans returnerar alla HTTP-svar, uppfattas det värmas upp.

    Om ingen `applicationInitialization` har angetts, utlösa en HTTP-begäran till programmet roten käll-fack på varje instans. Om en instans returnerar alla HTTP-svar, uppfattas det värmas upp.

1. Om alla instanser på källplatsen värmas upp har, kan du växla på två platser genom att växla mellan routningsregler för två platser. Efter det här steget har målplatsen (t.ex. produktionsplatsen) den app som tidigare värmas upp i källplatsen.

1. Nu när källplatsen har tidigare före swap-appen för målplatsen, kan du utföra samma åtgärd med hjälp av alla inställningar och startar om instanserna.

När som helst av växlingen görs alla verk som tillhör initierar bytts ut appar på källplatsen. På målplatsen förblir online när källplatsen förberedd och värmas upp, oavsett var växlingen lyckas eller misslyckas. Kontrollera att produktionsplatsen alltid är på målplatsen för att växla en mellanlagringsplats med produktionsplatsen. På så sätt kan påverkas din produktionsapp inte av växlingen.

### <a name="which-settings-are-swapped"></a>Vilka inställningar växlas ut?
När du klonar konfigurationen från en annan distributionsplats kan klonade konfigurationen redigeras. Dessutom följa vissa konfigurationselement innehållet i en växling (inte port specifika) medan andra konfigurationselement stannar på samma plats efter en växling (specifika-port). I listan nedan visas de inställningar som ändras när du växlar mellan platser.

**Inställningar som växlas ut**:

* Allmänna inställningar – till exempel framework-version, 32/64-bitars Web sockets
* App-inställningar (kan konfigureras för att fästs mot en plats)
* Anslutningssträngar (kan konfigureras för att fästs mot en plats)
* Hanterarmappningar
* Inställningar för övervakning och diagnostik
* Offentliga certifikat
* WebJobs-innehåll
* Hybridanslutningar *
* VNet-integrering *
* Tjänstslutpunkter *
* Azure CDN *

Funktioner som markerats med en * planeras att göras Fäst till facket. 

**Inställningar som inte växlas**:

* Publiceringsslutpunkterna
* Anpassade domännamn
* Privata certifikat och SSL-bindningar
* Skalinställningar
* WebJobs-schemaläggare
* IP-begränsningar
* Alltid på
* Protokoll-inställningar (HTTP**S**, TLS-version, klientcertifikat)
* Diagnostiklogginställningar
* CORS

<!-- VNET and hybrid connections not yet sticky to slot -->

Om du vill konfigurera en app-inställningen eller anslutningssträng till fästs mot en specifik plats (inte växlas), navigera till den **Configuration** för platsen, lägga till eller redigera en inställning och välj sedan den **distribution platsinställning**box. Att välja den här kryssrutan anger App Service inställningen inte är under drift. 

![Platsinställning](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Växla två platser 
Du kan växla distributionsfack i din app **distributionsfack** sidan och **översikt** sidan. Teknisk information på växlingen finns [vad som händer under bytet](#what-happens-during-swap)

> [!IMPORTANT]
> Kontrollera att din målplatsen är för produktion och att alla inställningar i källplatsen är konfigurerade exakt som du vill ha i produktionsmiljön innan du växlar mellan en app från ett distributionsfack till produktionen.
> 
> 

Följ dessa steg om du vill växla distributionsfack:

1. Gå till din app **distributionsfack** och klicka på **växla**.
   
    ![Växla, knapp](./media/web-sites-staged-publishing/SwapButtonBar.png)

    Den **växla** dialogrutan visar inställningarna i de valda käll- och platser som ska ändras.

2. Välj önskat **källa** och **Target** platser. Målet är vanligtvis produktionsplatsen. Dessutom klickar du på den **ändringar av datakällan** och **Target ändringar** flikarna och kontrollera att konfigurationsändringarna förväntas. När du är klar kan du byta platserna omedelbart genom att klicka på **växling**.

    ![Slutför växling](./media/web-sites-staged-publishing/SwapImmediately.png)

    Om du vill se hur din målplatsen skulle köras med de nya inställningarna innan växlingen faktiskt händer, inte på **växling**, men följer du anvisningarna i [växling med förhandsgranskning](#Multi-Phase).

3. När du är klar stänger du dialogrutan genom att klicka på **Stäng**.

Om du stöter på problem, se [felsöka växlingar](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Växla med förhandsgranskning (med swap)

> [!NOTE]
> Växling med förhandsgranskning stöds inte i web apps på Linux.

Verifiera körningarna med inställningarna som du har bytts ut innan du växlar till produktion som den målplats innan växlingen sker. Källplatsen är också värmas upp innan växling, vilket också är önskvärt för verksamhetskritiska program.

När du utför en växling med förhandsgranskning, App-tjänsten utför samma [växlingsåtgärden](#what-happens-during-swap) men pausar efter det första steget. Du kan kontrollera resultaten på mellanlagringsplatsen innan du slutför växlingen. 

Om du avbryter växlingen lägger App Service konfigurationselement för källplatsen till källplatsen.

Följ dessa steg för att växla med förhandsgranskning.

1. Följ stegen i [växla distributionsfack](#Swap) men välj **utför växling med förhandsgranskning**.

    ![Växla med förhandsgranskning](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogrutan visar hur konfigurationen i källplatsen ändras i fas 1 och hur facket käll- och ändras i fas 2.

2. När du är klar att starta växlingen klickar du på **starta växlingen**.

    När fas 1 har slutförts meddelas du i dialogrutan. Förhandsgranska växling i källplatsen genom att gå till `https://<app_name>-<source-slot-name>.azurewebsites.net`. 

3. När du är klar att slutföra väntande växlingen Välj **fullständig växling** i **växlingsåtgärd** och klicka på **fullständig växling**.

    Om du vill avbryta en väntande växling, Välj **Avbryt växling** i stället och klickar på **Avbryt växling**.

4. När du är klar stänger du dialogrutan genom att klicka på **Stäng**.

Om du stöter på problem, se [felsöka växlingar](#troubleshoot-swaps).

Se automatisera med PowerShell för att automatisera en med-växling.

<a name="Rollback"></a>

## <a name="roll-back-swap"></a>Återställa växling
Om något fel uppstår för målplatsen (till exempel produktionsplatsen) efter en växling återställa platserna till deras före växling tillstånd genom att byta samma två platserna omedelbart.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling

> [!NOTE]
> Autoväxling stöds inte i web apps på Linux.

Autoväxling effektiviserar DevOps-scenarier där du vill distribuera din app kontinuerligt med noll kallstart och utan stilleståndstid för slutkunder av appen. När Autoväxling har aktiverats från en plats i produktionen, varje gång du överföra dina ändringar i koden till platsen, App Service automatiskt [växlingar appen till produktionen](#swap-operation-steps) när den är värmas upp i källplatsen.

   > [!NOTE]
   > Du kan testa Autoväxling på en icke-produktion målplatsen först innan du konfigurerar Autoväxling för produktionsplatsen.
   > 

Följ dessa steg om du vill konfigurera Autoväxling:

1. Gå till appens sida. Välj **distributionsfack** >  *\<önskade källplatsen >*  > **Configuration**  >  **Allmänna inställningar**.
   
2. I **Autoväxling aktiverat**väljer **på**, välj sedan önskad målplatsen i **distribution autoväxlingsplats**, och klicka på **spara** i den kommandofältet. 
   
    ![](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Köra en kod push till källplatsen. Autoväxling händer om en stund och uppdateringen återspeglas på URL: en för din målplatsen.

Om du stöter på problem, se [felsöka växlingar](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="custom-warm-up"></a>Anpassade värma upp
När du använder [Auto-Swap](#Auto-Swap), vissa appar kan kräva anpassade värma upp åtgärder före växlingen. Den `applicationInitialization` konfigurationselementet i web.config kan du ange anpassade initieringen åtgärder som ska utföras. Den [växlingsåtgärden](#what-happens-during-swap) väntar på den här anpassade värma upp ska slutföras innan du växlar med på målplatsen. Här är ett exempel web.config-fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Mer information om hur du anpassar den `applicationInitialization` element, se [vanligaste slot swap distributionsfel och hur du åtgärdar dem](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Du kan också anpassa beteendet värma upp med en eller flera av följande [appinställningar](configure-common.md):

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Sökvägen till att värma upp pinga din webbplats. Lägg till den här appinställningen genom att ange en anpassad sökväg som börjar med ett snedstreck som värde. Till exempel `/statuscheck`. Standardvärdet är `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Ogiltig HTTP-svarskoder för värma upp igen. Lägg till den här appinställningen med en kommaavgränsad lista över HTTP-koder. Till exempel: `200,202` . Om den returnerade statuskoden inte är i listan, stoppas värma upp och växling åtgärderna. Som standard är alla svarskoder giltiga.

Om du stöter på problem, se [felsöka växlingar](#troubleshoot-swaps).

## <a name="monitor-swap"></a>Övervaka växling

Om den [växlingsåtgärden](#what-happens-during-swap) tar lång tid att slutföra, du kan få information om växlingen i den [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

I appens sida i portalen i det vänstra navigeringsfältet väljer **aktivitetsloggen**.

En växlingsåtgärd visas i loggfråga som `Swap Web App Slots`. Du kan expandera den och välj en av katalogtjänstens eller fel att se detaljerna.

## <a name="route-traffic"></a>Dirigera trafik

Som standard alla klientbegäranden till appens produktions-URL (`http://<app_name>.azurewebsites.net`) dirigeras till produktionsplatsen. Du kan vidarebefordra en del av trafiken till en annan plats. Den här funktionen är användbar om du behöver Användarfeedback för en ny uppdatering, men du inte är redo att lansera till produktion.

### <a name="route-production-traffic-automatically"></a>Dirigera produktionstrafik automatiskt

Följ dessa steg för att dirigera produktionstrafik automatiskt:

1. Gå till appens sida och välj **distributionsfack**.

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

Som standard nya platser ges en routningsregel för `0%`, visas i grått. Genom att explicit ange det här värdet till `0%` (visas i svart text), användarna kan nå mellanlagringsplatsen manuellt med hjälp av den `x-ms-routing-name` Frågeparametern, men de kommer inte att dirigeras till facket automatiskt eftersom routning procentandelen anges till 0. Det här är ett avancerat scenario där du kan ”dölja” din mellanlagringsplats för allmänheten samtidigt som interna team att testa ändringar på platsen.

<a name="Delete"></a>

## <a name="delete-slot"></a>Ta bort fack

Gå till appens sida. Välj **distributionsfack** >  *\<fack att ta bort >*  > **översikt**. Klicka på **ta bort** i kommandofältet.  

![Ta bort ett Distributionsfack](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell är en modul som tillhandahåller cmdletar för att hantera Azure via Windows PowerShell, inklusive stöd för att hantera distributionsplatser i Azure App Service.

Information om att installera och konfigurera Azure PowerShell och om autentisering av Azure PowerShell med Azure-prenumerationen finns i [hur du installerar och konfigurerar du Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-web-app"></a>Skapa webbapp
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-slot"></a>Skapa platsen
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-swap-with-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-source-slot"></a>Initiera växling med förhandsgranskning (med swap) och tillämpa platskonfigurationen för mål på källplatsen
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-pending-swap-swap-with-review-and-restore-source-slot-configuration"></a>Avbryta väntande växlingsutrymme (växling med granskning) och återställa platskonfigurationen för källa
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Växla distributionsfack
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. “production”]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Övervaka växling händelser i loggen-aktivitet
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-slot"></a>Ta bort fack
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

---
<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-cli"></a>Automatisera med CLI

För [Azure CLI](https://github.com/Azure/azure-cli) kommandon för distributionsplatser, se [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Felsöka växlingar

Om något fel uppstår vid en [växling](#what-happens-during-swap), den är inloggad *D:\home\LogFiles\eventlog.xml*, samt programspecifika felloggen.

Här följer några vanliga swap-fel:

- En HTTP-begäran till programmet roten tidsgränsen. Växlingen väntar på 90 sekunder för varje HTTP-begäran, och försöker upp till 5 gånger. Om alla nya försök är tidsgränsen avbryts växlingen.

- Lokal cache initieringen misslyckas när appinnehållet överskrider kvoten för lokala diskar som angetts för den lokala cachen. Mer information finns i [översikt över Lokal cachelagring](overview-local-cache.md).

- Under [anpassade värma upp](#custom-warm-up)HTTP-begäranden som görs internt (utan att gå via den externa URL: en) och kan misslyckas med vissa URL-omskrivningsregler regler i *Web.config*. Regler för omdirigering domännamn eller framtvinga HTTPS kan till exempel förhindra att värma upp begäranden från att nå kod alls. Undvik problemet genom att ändra din omskrivningsregler genom att lägga till följande villkor:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Utan anpassade värma upp, kan HTTP-förfrågningar fortfarande ske av URL-omskrivningsregler. Undvik problemet genom att ändra din omskrivningsregler genom att lägga till följande villkor:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Vissa [regler för IP-begränsning](app-service-ip-restrictions.md) kan hindra växlingen från att skicka HTTP-begäranden till din app. IPv4-adressintervall som börjar med `10.` och `100.` är interna för din distribution och ska tillåtas att ansluta till din app.

## <a name="next-steps"></a>Nästa steg
[Blockera åtkomst till icke-produktionsplatser](app-service-ip-restrictions.md)
