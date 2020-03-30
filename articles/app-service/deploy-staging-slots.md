---
title: Konfigurera mellanlagringsmiljöer
description: Lär dig hur du distribuerar appar till en icke-produktionsplats och automatisktwap i produktion. Öka tillförlitligheten och eliminera avbrott i appen från distributioner.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300879"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Konfigurera mellanlagringsmiljöer i Azure App Service
<a name="Overview"></a>

När du distribuerar din webbapp, webbapp på Linux, den mobila serverdelen eller API-appen till [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)kan du använda en separat distributionsplats i stället för standardproduktionsplatsen när du kör i nivån **Standard,** **Premium**eller **Enolated** App Service-plan. Distributionsplatser är liveappar med egna värdnamn. Appinnehåll och konfigurationselement kan växlas mellan två distributionsplatser, inklusive produktionsplatsen. 

Att distribuera ditt program till en plats som inte är produktion har följande fördelar:

* Du kan validera appändringar i en distributionsplats för mellanlagring innan du byter ut den mot produktionsplatsen.
* Distribuera en app till en plats först och byta den till produktion ser till att alla instanser av facket värms upp innan de byts ut till produktion. Detta eliminerar driftstopp när du distribuerar din app. Trafikomdirigeringen är sömlös och inga begäranden tas bort på grund av växlingsåtgärder. Du kan automatisera hela arbetsflödet genom att konfigurera [automatisk växling](#Auto-Swap) när validering före byte inte behövs.
* Efter en växling har den plats med tidigare iscensatt app nu den tidigare produktionsappen. Om ändringarna som byts ut till produktionsplatsen inte är som du förväntar dig kan du utföra samma byte omedelbart för att få tillbaka din "senast fungerande plats".

Varje App Service-plannivå stöder olika antal distributionsplatser. Det finns ingen extra kostnad för att använda distributionsplatser. Information om hur många platser appens nivå stöder finns i Begränsningar för [App-tjänsten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Om du vill skala appen till en annan nivå kontrollerar du att målnivån stöder antalet platser som appen redan använder. Om din app till exempel har fler än fem platser kan du inte skala ned den till **standardnivån,** eftersom **standardnivån** endast stöder fem distributionsplatser. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Lägga till ett fack
Appen måste köras på nivån **Standard,** **Premium**eller **Isolerad** för att du ska kunna aktivera flera distributionsplatser.


1. i [Azure-portalen](https://portal.azure.com/)söker du efter och väljer **App Services** och väljer din app. 
   
    ![Sök efter App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. Välj Lägg**till** **plats i** > den vänstra rutan .
   
    ![Lägg till en ny distributionsplats](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Om appen inte redan finns på nivån **Standard,** **Premium**eller **Isolerad** visas ett meddelande som anger nivåerna som stöds för att aktivera mellanlagrade publiceringar. Nu kan du välja **Uppgradera** och gå till fliken **Skala** i appen innan du fortsätter.
   > 

3. I dialogrutan **Lägg till en plats** ger du platsen ett namn och väljer om du vill klona en appkonfiguration från en annan distributionsplats. Välj **Lägg till** för att fortsätta.
   
    ![Konfigurationskälla](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    Du kan klona en konfiguration från valfri befintlig plats. Inställningar som kan klonas inkluderar appinställningar, anslutningssträngar, språkramsversioner, webbuttag, HTTP-version och plattformsbitness.

4. När kortplatsen har lagts till väljer du **Stäng** för att stänga dialogrutan. Den nya platsen visas nu på sidan **Distributionsplatser.** Som standard är **Traffic %** inställd på 0 för den nya platsen, med all kundtrafik dirigerad till produktionsplatsen.

5. Välj den nya distributionsplatsen för att öppna platsens resurssida.
   
    ![Rubrik för distributionsplats](./media/web-sites-staged-publishing/StagingTitle.png)

    Mellanlagringsplatsen har en hanteringssida precis som alla andra App Service-appar. Du kan ändra kortplatsens konfiguration. Namnet på platsen visas högst upp på sidan för att påminna dig om att du visar distributionsplatsen.

6. Välj appens URL på platsens resurssida. Distributionsplatsen har ett eget värdnamn och är också en live-app. Information om hur du begränsar allmänhetens åtkomst till distributionsplatsen finns i [IP-begränsningar för Azure App Service](app-service-ip-restrictions.md).

Den nya distributionsplatsen har inget innehåll, även om du klonar inställningarna från en annan plats. Du kan till exempel [publicera till den här platsen med Git](app-service-deploy-local-git.md). Du kan distribuera till platsen från en annan databasgren eller en annan databas. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Vad händer under en swap

### <a name="swap-operation-steps"></a>Steg för växlingsåtgärd

När du byter två platser (vanligtvis från en mellanlagringsplats till produktionsplatsen) gör App Service följande för att säkerställa att målplatsen inte upplever driftstopp:

1. Använd följande inställningar från målplatsen (till exempel produktionsplatsen) på alla instanser av källplatsen: 
    - [Kortplatsspecifika](#which-settings-are-swapped) appinställningar och anslutningssträngar, om tillämpligt.
    - [Kontinuerliga distributionsinställningar,](deploy-continuous-deployment.md) om det är aktiverat.
    - [Autentiseringsinställningar](overview-authentication-authorization.md) för App Service, om det är aktiverat.
    
    Något av dessa fall utlöser alla instanser i källplatsen för omstart. Under [växling med förhandsgranskning](#Multi-Phase)markerar detta slutet på den första fasen. Växlingsåtgärden pausas och du kan verifiera att källplatsen fungerar korrekt med målplatsens inställningar.

1. Vänta tills varje instans i källfacket för att slutföra omstarten. Om någon instans inte startar om återställs alla ändringar i källplatsen och åtgärden stoppas.

1. Om [lokal cache](overview-local-cache.md) är aktiverad utlöser du lokal cacheinitualisering genom att göra en HTTP-begäran till programroten ("/") på varje instans av källplatsen. Vänta tills varje instans returnerar http-svar. Lokal cacheinitisering orsakar en ny omstart på varje instans.

1. Om [automatisk växling](#Auto-Swap) är aktiverat med [anpassad uppvärmning](#Warm-up)utlöses [programinitiering](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) genom att göra en HTTP-begäran till programroten ("/") på varje instans av källplatsen.

    Om `applicationInitialization` inget anges utlöser du en HTTP-begäran till programroten för källplatsen för varje instans. 
    
    Om en instans returnerar ett HTTP-svar anses den vara uppvärmd.

1. Om alla instanser på källplatsen har värmts upp framgångsrikt, byt de två kortplatserna genom att växla routningsreglerna för de två kortplatserna. Efter det här steget har målplatsen (till exempel produktionsplatsen) appen som tidigare har värmts upp i källfacket.

1. Nu när källplatsen har app för bytesappen tidigare i målplatsen utför du samma åtgärd genom att använda alla inställningar och starta om instanserna.

Vid varje punkt i växlingsåtgärden sker allt arbete med att initiera de utbytta apparna på källfacket. Målplatsen förblir online medan källplatsen förbereds och värms upp, oavsett var bytet lyckas eller misslyckas. Om du vill byta en mellanlagringsplats med produktionsplatsen, se till att produktionsplatsen alltid är målplatsen. På så sätt påverkar växlingsåtgärden inte din produktionsapp.

### <a name="which-settings-are-swapped"></a>Vilka inställningar byts ut?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Om du vill konfigurera en appinställning eller anslutningssträng så att den håller sig till en viss plats (inte byts) går du till **sidan Konfiguration** för den platsen. Lägg till eller redigera en inställning och välj sedan **inställningen för distributionsplats**. Om du markerar den här kryssrutan visas App Service om att inställningen inte kan bytas ut. 

![Inställning av kortplats](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Byt två platser 
Du kan byta distributionsplatser på appens sida **för distributionsplatser** och **översiktssidan.** För teknisk information om slot swap, se [Vad händer under byte .](#AboutConfiguration)

> [!IMPORTANT]
> Innan du byter en app från en distributionsplats till produktion kontrollerar du att produktionen är din målplats och att alla inställningar i källfacket är konfigurerade precis som du vill ha dem i produktion.
> 
> 

Så här byter du distributionsplatser:

1. Gå till appens sida **för distributionsplatser** och välj **Byt**.
   
    ![Knappen Byt](./media/web-sites-staged-publishing/SwapButtonBar.png)

    I dialogrutan **Byt** visas inställningar i de valda käll- och målplatserna som ska ändras.

2. Välj önskade **käll-** och **målplatser.** Vanligtvis är målet produktionsplatsen. Välj också flikarna **Källändringar** och **Måländringar** och kontrollera att konfigurationsändringarna förväntas. När du är klar kan du byta kortplatserna direkt genom att välja **Swap**.

    ![Slutföra växlingen](./media/web-sites-staged-publishing/SwapImmediately.png)

    Om du vill se hur målplatsen skulle köras med de nya inställningarna innan bytet faktiskt sker väljer du inte **Byt**, men följ instruktionerna i [Byt med förhandsgranskning](#Multi-Phase).

3. När du är klar stänger du dialogrutan genom att välja **Stäng**.

Om du har några problem läser [du Felsöka swappar](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Byt med förhandsgranskning (utbyte i flera faser)

Innan du byter till produktion som målplats, verifiera att appen körs med de utbytta inställningarna. Källplatsen värms också upp innan bytet är klart, vilket är önskvärt för verksamhetskritiska applikationer.

När du utför en växling med förhandsgranskning utför App Service samma [växlingsåtgärd](#AboutConfiguration) men pausar efter det första steget. Du kan sedan verifiera resultatet på mellanlagringsplatsen innan du slutför bytet. 

Om du avbryter växlingen återanvänder App Service konfigurationselement på källplatsen.

Så här byter du med förhandsgranskning:

1. Följ stegen i [Byt distributionsplatser](#Swap) men välj **Utför växling med förhandsgranskning**.

    ![Byt med förhandsgranskning](./media/web-sites-staged-publishing/SwapWithPreview.png)

    Dialogrutan visar hur konfigurationen i källfacket ändras i fas 1 och hur käll- och målfacket ändras i fas 2.

2. När du är redo att starta bytet väljer du **Starta växling**.

    När fas 1 är klar får du ett meddelande i dialogrutan. Förhandsgranska bytet i källfacket `https://<app_name>-<source-slot-name>.azurewebsites.net`genom att gå till . 

3. När du är redo att slutföra den väntande växlingen väljer du **Slutför växling** i **växling** och väljer **Slutför växling**.

    Om du vill avbryta en väntande swap väljer du **Avbryt växling** i stället.

4. När du är klar stänger du dialogrutan genom att välja **Stäng**.

Om du har några problem läser [du Felsöka swappar](#troubleshoot-swaps).

Mer om du vill automatisera en flerfasswap finns i [Automatisera med PowerShell](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Rulla tillbaka en swap
Om några fel uppstår i målplatsen (till exempel produktionsplatsen) efter en kortplatsswapp, återställ platserna till deras pre-swap-lägen genom att byta samma två platser omedelbart.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Konfigurera automatisk växling

> [!NOTE]
> Automatisk växling stöds inte i webbappar på Linux.

Automatisk växling effektiviserar Azure DevOps-scenarier där du vill distribuera din app kontinuerligt med noll kalla starter och noll driftstopp för kunder i appen. När automatisk växling är aktiverat från en plats till produktion byter App Service automatiskt [ut appen i produktion](#swap-operation-steps) efter att den har värmts upp i källfacket varje gång du skickar kodändringarna till den platsen.

   > [!NOTE]
   > Innan du konfigurerar automatisk växling för produktionsplatsen bör du överväga att testa automatisk växling på en målplats som inte är produktion.
   > 

Så här konfigurerar du automatisk växling:

1. Gå till appens resurssida. Välj Önskade*\<källplats för * **distributionsplatser** > > >  **konfigurationsinställningar** > **.**
   
2. För **Automatisk växling aktiverad**väljer du **På**. Välj sedan önskad målplats för **distributionsplats för automatisk växling**och välj **Spara** i kommandofältet. 
   
    ![Val för att konfigurera automatisk växling](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Kör en kod push till källplatsen. Automatisk växling sker efter en kort tid och uppdateringen återspeglas på målplatsens webbadress.

Om du har några problem läser [du Felsöka swappar](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Ange anpassad uppvärmning

Vissa appar kan kräva anpassade uppvärmningsåtgärder före bytet. Med `applicationInitialization` konfigurationselementet i web.config kan du ange anpassningsåtgärder. [Växlingsåtgärden](#AboutConfiguration) väntar på att den här anpassade uppvärmningen ska slutföras innan du byter med målplatsen. Här är ett exempel web.config fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Mer information om hur `applicationInitialization` du anpassar elementet finns i [Vanligaste distributionsfacksväxlingsfel och hur du åtgärdar dem](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

Du kan också anpassa uppvärmningsbeteendet med en eller båda av följande [appinställningar:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Vägen till ping för att värma upp din webbplats. Lägg till den här appinställningen genom att ange en anpassad sökväg som börjar med ett snedstreck som värde. Ett exempel är `/statuscheck`. Standardvärdet är `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Giltiga HTTP-svarskoder för uppvärmningsåtgärden. Lägg till den här appinställningen med en kommaavgränsad lista med HTTP-koder. Ett exempel `200,202` är . Om den returnerade statuskoden inte finns i listan stoppas uppvärmnings- och växlingsåtgärderna. Som standard är alla svarskoder giltiga.

> [!NOTE]
> Konfigurationselementet `<applicationInitialization>` är en del av varje appstart, medan de två appinställningarna för uppvärmningsbeteende endast gäller för kortplatsbyten.

Om du har några problem läser [du Felsöka swappar](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Övervaka en swap

Om [växlingsåtgärden](#AboutConfiguration) tar lång tid att slutföra kan du få information om växlingsåtgärden i [aktivitetsloggen](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

Välj **Aktivitetslogg**i den vänstra rutan på appens resurssida i portalen .

En växlingsåtgärd visas `Swap Web App Slots`i loggfrågan som . Du kan expandera den och välja en av underoperationerna eller felen för att se informationen.

## <a name="route-traffic"></a>Rutttrafik

Som standard dirigeras alla klientbegäranden till`http://<app_name>.azurewebsites.net`appens produktions-URL ( ) till produktionsplatsen. Du kan dirigera en del av trafiken till en annan plats. Den här funktionen är användbar om du behöver feedback från användare för en ny uppdatering, men du inte är redo att släppa den till produktion.

### <a name="route-production-traffic-automatically"></a>Dirigera produktionstrafiken automatiskt

Så här dirigerar du produktionstrafiken automatiskt:

1. Gå till appens resurssida och välj **Distributionsplatser**.

2. I kolumnen **Trafik %** för den plats som du vill dirigera till anger du en procentsats (mellan 0 och 100) som ska representera mängden total trafik som du vill dirigera. Välj **Spara**.

    ![Ange en trafikprocent](./media/web-sites-staged-publishing/RouteTraffic.png)

När inställningen har sparats dirigeras den angivna procentandelen klienter slumpmässigt till den icke-produktionsplats. 

När en klient automatiskt dirigeras till en viss plats är den "fäst" på den platsen under klientsessionens livstid. I klientwebbläsaren kan du se vilken plats din `x-ms-routing-name` session är fäst vid genom att titta på cookien i dina HTTP-huvuden. En begäran som dirigeras till "mellanlagringsplatsen" `x-ms-routing-name=staging`har cookien . En begäran som dirigeras till produktionsplatsen `x-ms-routing-name=self`har cookien .

   > [!NOTE]
   > Bredvid Azure-portalen kan du [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) också använda kommandot i Azure CLI för att ange routningsprocenterna från CI/CD-verktyg som DevOps-pipelines eller andra automatiseringssystem.
   > 

### <a name="route-production-traffic-manually"></a>Ruttproduktionstrafik manuellt

Förutom automatisk trafikroutning kan App Service dirigera begäranden till en viss plats. Detta är användbart när du vill att användarna ska kunna välja att eller välja bort din betaapp. Om du vill dirigera produktionstrafik `x-ms-routing-name` manuellt använder du frågeparametern.

Om du till exempel vill att användarna ska välja bort din betaapp kan du lägga den här länken på din webbsida:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

Strängen `x-ms-routing-name=self` anger produktionsplatsen. När klientwebbläsaren har åtkomst till länken omdirigeras den till produktionsplatsen. Varje efterföljande `x-ms-routing-name=self` begäran har cookien som fäster sessionen till produktionsplatsen.

Om du vill att användarna ska kunna välja din betaapp anger du samma frågeparameter till namnet på den icke-produktionsplats som inte är produktionsplats. Här är ett exempel:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Som standard får nya platser en `0%`routningsregel för , som visas i grått. När du uttryckligen anger `0%` det här värdet till (visas i svart text) `x-ms-routing-name` kan användarna komma åt mellanlagringsplatsen manuellt med hjälp av frågeparametern. Men de dirigeras inte automatiskt till platsen eftersom routningsprocenten är satt till 0. Detta är ett avancerat scenario där du kan "dölja" din mellanlagringsplats från allmänheten samtidigt som interna team kan testa ändringar på platsplatsen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Ta bort en plats

Sök efter och välj din app. Välj **platsplats för distributionsplatser** > *\<om du vill ta bort>*  >  **översikt**. Välj **Ta bort** i kommandofältet.  

![Ta bort en distributionsplats](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatisera med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell är en modul som tillhandahåller cmdlets för att hantera Azure via Windows PowerShell, inklusive stöd för att hantera distributionsplatser i Azure App Service.

Information om hur du installerar och konfigurerar Azure PowerShell och om hur du autentiserar Azure PowerShell med din Azure-prenumeration finns i [Installera och konfigurera Microsoft Azure PowerShell](/powershell/azure/overview).  

---
### <a name="create-a-web-app"></a>Skapa en webbapp
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Skapa en plats
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Initiera en växling med en förhandsgranskning (flerfasswap) och tillämpa målplatskonfigurationen på källplatsen
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Avbryta en väntande swap (byt med granskning) och återställa källfackskonfigurationen
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Växla distributionsfack
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Övervaka växlingshändelser i aktivitetsloggen
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Ta bort en plats
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatisera med Resource Manager-mallar

[Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) är deklarativa JSON-filer som används för att automatisera distributionen och konfigurationen av Azure-resurser. Om du vill byta fack med Hjälp av Resource Manager-mallar anger du två egenskaper på *Microsoft.Web/sites/slots* och *Microsoft.Web/sites-resurser:*

- `buildVersion`: Det här är en strängegenskap som representerar den aktuella versionen av appen som distribueras i platsen. Till exempel: "v1", "1.0.0.1" eller "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: Det här är en `buildVersion` strängegenskap som anger vad facket ska ha. Om targetBuildVersion inte är `buildVersion`lika med den aktuella , kommer detta att `buildVersion`utlösa växlingsåtgärden genom att hitta den plats som har den angivna .

### <a name="example-resource-manager-template"></a>Exempel på resurshanterarens mall

Följande Resource Manager-mall `buildVersion` uppdaterar mellanlagringsplatsen och `targetBuildVersion` ställer in på produktionsplatsen. Detta kommer att byta de två platserna. Mallen förutsätter att du redan har en webbapp skapad med en plats med namnet "mellanlagring".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Den här Resource Manager-mallen är idempotent, vilket innebär att den kan köras upprepade gånger och skapa samma tillstånd för kortplatserna. Efter den första `targetBuildVersion` körningen `buildVersion`matchar den aktuella , så en växling kommer inte att utlösas.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatisera med CLI

Information om [Azure CLI-kommandon](https://github.com/Azure/azure-cli) för distributionsplatser finns i [az webapp-distributionsplats](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Felsöka swappar

Om något fel uppstår under en [kortplatssbyte](#AboutConfiguration)loggas den in *D:\home\LogFiles\eventlog.xml*. Den är också inloggad i den programspecifika felloggen.

Här är några vanliga växlingsfel:

- En HTTP-begäran till programroten är tidsstämd. Växlingsåtgärden väntar i 90 sekunder för varje HTTP-begäran och försöker igen upp till 5 gånger. Om alla försök har timeats ut stoppas växlingsåtgärden.

- Initiering av lokal cache kan misslyckas när appinnehållet överskrider den lokala diskkvot som angetts för den lokala cachen. Mer information finns i [Översikt över lokal cache](overview-local-cache.md).

- Under anpassad uppvärmning görs [HTTP-begäranden](#Warm-up)internt (utan att gå igenom den externa webbadressen). De kan misslyckas med vissa URL skriva regler i *Web.config*. Regler för omdirigering av domännamn eller genomgående HTTPS kan till exempel förhindra att uppvärmningsbegäranden når appkoden. Du kan lösa problemet genom att ändra reglerna genom att lägga till följande två villkor:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Utan en anpassad uppvärmning kan url-omskrivningsreglerna fortfarande blockera HTTP-begäranden. Du kan lösa problemet genom att ändra reglerna genom att lägga till följande villkor:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Vissa [IP-begränsningsregler](app-service-ip-restrictions.md) kan förhindra att växlingsåtgärden skickar HTTP-begäranden till din app. IPv4-adressintervall som `10.` börjar `100.` med och är interna för distributionen. Du bör tillåta dem att ansluta till din app.

- När du har byt kortplats kan appen få oväntade omstarter. Detta beror på att efter en växling, värdnamn bindande konfiguration går ur synk, vilket i sig inte orsakar omstarter. Vissa underliggande lagringshändelser (t.ex. redundansväxlingar för lagringsvolym) kan dock identifiera dessa avvikelser och tvinga alla arbetsprocesser att starta om. Om du vill minimera dessa typer av omstarter ställer du in [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` appinställningen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) på *alla platser*. Den här appinställningen fungerar dock *inte* med WCF-appar (Windows Communication Foundation).

## <a name="next-steps"></a>Nästa steg
[Blockera åtkomst till ankomst- och avgångstider som inte är produktion](app-service-ip-restrictions.md)
