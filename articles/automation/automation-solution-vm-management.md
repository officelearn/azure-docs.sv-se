---
title: "Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning | Microsoft Docs"
description: "VM-hanteringslösningar startar och stoppar Azure-resurshanteraren för virtuella datorer i ett schema och övervakar proaktivt från Log Analytics."
services: automation
documentationCenter: 
authors: georgewallace
manager: carmonm
editor: 
ms.assetid: 06c27f72-ac4c-4923-90a6-21f46db21883
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: magoedte
ms.openlocfilehash: e6f1189b9729c57718a5cd6d6f6a583b94f6f142
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning i Azure Automation

Starta/stoppa virtuella datorer kontorstid lösning startar och stoppar Azure virtuella datorer på användardefinierade scheman, ger insikter via logganalys och skickar valfri e-postmeddelanden med hjälp av [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Den stöder både Azure Resource Manager och klassiska virtuella datorer i de flesta scenarier. 

Den här lösningen ger en decentraliserad automation-funktion för kunder som vill minska kostnaderna utnyttja serverlösa, låg kostnad resurser. Funktionerna omfattar:

* Schema för virtuella datorer för att starta/stoppa
* Schemalägga virtuella datorer för att starta/stoppa i stigande ordning med hjälp av Azure-taggar (stöds inte för klassiska virtuella datorer)
* Automatisk stoppa virtuella datorer baserat på låg CPU-användning

## <a name="prerequisites"></a>Krav

- Runbooks använder ett [Azure Kör som-konto](automation-offering-get-started.md#authentication-methods).  Kör som-kontot är den lämpligaste autentiseringsmetoden eftersom den använder certifikatautentisering istället för ett lösenord som kan upphöra att gälla eller ändras ofta.  

- Den här lösningen kan endast hantera virtuella datorer som finns i samma prenumeration som där Automation-kontot finns.  

- Den här lösningen bara distribueras till följande Azure regioner - Australien sydost, Kanada Central, centrala Indien, östra USA, östra, Sydostasien, Storbritannien, Syd och västra Europa.  
    
    > [!NOTE]
    > Runbooks hantera VM-schemat kan rikta virtuella datorer i en region.  

- Du måste välja Välj för att skicka e-postmeddelanden när runbooks för start och stopp VM, medan onboarding från Azure Marketplace **Ja** att distribuera SendGrid. 

    > [!IMPORTANT]
    > SendGrid är en tjänst från tredje part, kontakta för support med SendGrid [SendGrid](https://sendgrid.com/contact/).  
    >
   
    Med SendGrid är följande:

    * Högst ett SendGrid konto per användare per prenumeration
    * Högst två SendGrid konton per prenumeration

Om du har distribuerat en tidigare version av den här lösningen, måste du först ta bort det från ditt konto innan du distribuerar den här versionen.  

## <a name="solution-components"></a>Lösningskomponenter

Den här lösningen innehåller förkonfigurerade runbooks, scheman och integrering med logganalys som gör att du kan anpassa Start och stopp av dina virtuella datorer till suite företaget måste. 

### <a name="runbooks"></a>Runbooks

I följande tabell listas de runbooks som distribueras till ditt Automation-konto.  Det rekommenderas inte att göra ändringar i runbook-koden, men i stället skriva egna runbook för nya funktioner.

> [!NOTE]
> Inte direkt att köra en runbook med namnet ”underordnade” läggas till i slutet av namnet.
>

Alla överordnade runbooks innehåller den *WhatIf* parametern, som under inställd på **SANT**, stöder med uppgift om exakt hur runbook ska vidta när kör utan den *WhatIf* parametern och validerar rätt VMs riktas.  Runbooks utför endast de angivna åtgärderna när den *WhatIf* parametern anges till **FALSKT**. 

|**Runbook** | **Parametrar** | **Beskrivning**|
| --- | --- | ---| 
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropas från den överordnade runbooken. Skapar aviseringar på basis av per resurs för AutoStop scenario.| 
|AutoStop_CreateAlert_Parent | WhatIf: True eller False <br> VMList | Skapar eller uppdaterar Azure Varningsregler på virtuella datorer i målgrupperna för prenumerationen eller resursen. <br> VMList: Kommaavgränsad lista över virtuella datorer.  Till exempel *vm1 vm2, vm3*| 
|AutoStop_Disable | ingen | Inaktivera AutoStop aviseringar och standardschemat.| 
|AutoStop_StopVM_Child | WebHookData | Anropas från överordnad runbook bara. Varningsregler anropa runbook så att den inte arbetet med att stoppa den virtuella datorn.|  
|Bootstrap_Main | ingen | Använda en gång för att ställa in bootstrap konfigurationer, till exempel webhookURI som normalt inte kan nås från Azure Resource Manager. Denna runbook tas bort automatiskt om distributionen är korrekt.|  
|ScheduledStartStop_Child | VMName <br> Åtgärd: Stoppa och starta <br> resourceGroupName | Anropas från överordnad runbook bara. Stöder faktiska körningen av för schemalagda stoppa ska startas eller stoppas.|  
|ScheduledStartStop_Parent | Åtgärd: Stoppa och starta <br> WhatIf: True eller False | Detta börjar gälla på alla virtuella datorer i prenumerationen såvida du inte redigerar den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** som begränsar den ska köras endast på dessa Target-resursgrupper. Du kan också utesluta specifika virtuella datorer genom att uppdatera den **External_ExcludeVMNames** variabeln. WhatIf fungerar på samma sätt som andra runbooks.|  
|SequencedStartStop_Parent | Åtgärd: Stoppa och starta <br> WhatIf: True eller False | Skapa en tagg som kallas **SequenceStart** och en annan tagg kallas **SequenceStop** på varje virtuell dator som du vill starta aktivitetssekvensen\\Stoppa aktiviteten för. Värdet för taggen ska vara ett positivt heltal (1,2,3) som motsvarar den ordning som du vill starta\\stoppa i stigande ordning. WhatIf fungerar på samma sätt som andra runbooks. <br> **Obs: Virtuella datorer måste vara inom resursgrupper definierade External_Start_ResourceGroupNames External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation-variabler och ha lämpliga taggar för åtgärder ska börja gälla.**|

### <a name="variables"></a>Variabler

I följande tabell visar de variabler som skapats i ditt Automation-konto.  Vi rekommenderar att du bara ändra variabler med prefixet **externa**, ändra variabler med prefixet **internt** kan orsaka oönskade effekter.  

|**Variabeln** | **Beskrivning**|
---------|------------|
|External_AutoStop_Condition | Det här är den villkorsstyrd operatör som behövs för att konfigurera villkoret innan en avisering. Giltiga värden är **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, **LessThanOrEqual**.|  
|External_AutoStop_Description | Varning om du vill stoppa den virtuella datorn om processor % överstiger tröskelvärdet.|  
|External_AutoStop_MetricName | Namnet på måttet prestanda Azure varningsregeln är konfigureras för.| 
|External_AutoStop_Threshold | Tröskelvärdet för Azure varningsregeln har angett i variabeln *External_AutoStop_MetricName*. Procentandel värdena kan variera mellan 1 och 100.|  
|External_AutoStop_TimeAggregationOperator | Tid aggregering operatorn som tillämpas på den valda fönsterstorleken för att utvärdera villkoret. Giltiga värden är **genomsnittlig**, **minsta**, **maximala**, **totala**, **senaste**.|  
|External_AutoStop_TimeWindow | Fönstrets storlek som Azure analyserar valda måtten för att utlösa en avisering. Den här parametern accepterar indata i timespan format. Möjliga värden är från 5 minuter till sex timmar.|  
|External_EmailFromAddress | Anger avsändaren av e-postmeddelandet.|  
|External_EmailSubject | Anger texten i ämnesraden för e-postmeddelandet.|  
|External_EmailToAddress | Anger mottagaren av e-postmeddelandet. Avgränsa namnen med kommatecken.|  
|External_ExcludeVMNames | Ange Virtuella datornamnen som ska undantas, Avgränsa namnen med kommatecken utan blanksteg.|  
|External_IsSendEmail | Anger alternativ för att skicka e-postmeddelande när åtgärden har slutförts.  Ange **Ja** eller **nr** inte skicka e-postmeddelandet.  Det här alternativet ska vara **nr** om du inte har skapat SendGrid under den inledande distributionen.|  
|External_Start_ResourceGroupNames | Anger en eller flera resursgrupper, avgränsa värden med kommatecken, avsedda för Start-åtgärder.|  
|External_Stop_ResourceGroupNames | Anger en eller flera resursgrupper avgränsa värden med kommatecken, stoppa åtgärder som mål.|  
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|  
|Internal_AutoSnooze_WebhookUri | Anger Webhook URI kallas för AutoStop scenariot.|  
|Internal_AzureSubscriptionId | Anger Azure prenumerations-Id.|  
|Internal_ResourceGroupName | Anger Azure Automation-konto resursgruppens namn.|  
|Internal_SendGridAccountName | Anger namnet på SendGrid.|  
|Internal_SendGridPassword | Anger lösenordet för SendGrid.|  

<br>

I alla scenarier är den **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, och **External_ExcludeVMNames** variabler krävs för att rikta virtuella datorer med undantag för att tillhandahålla ett kommatecken avgränsade lista över virtuella datorer för den **AutoStop_CreateAlert_Parent** runbook. Det vill säga måste din virtuella dator finnas i mål resursgrupper för Starta/stoppa åtgärder ska ske. Logiken fungerar lite som Azure princip i att du kan rikta på gruppen prenumerationen eller resursen och har åtgärder ärvs av nya virtuella datorer. Den här metoden slipper du upprätthålla en separat schema för varje virtuell dator och hantera Starta/Stoppa i skala.

### <a name="schedules"></a>Scheman

I följande tabell visas varje standardscheman som skapats i ditt Automation-konto.  Du kan ändra dem eller skapa egna anpassade scheman.  Som standard var och en av dessa scheman inaktiverade utom för **Scheduled_StartVM** och **schemalagda StopVM**.

Det rekommenderas inte att aktivera alla scheman som kan det leda till ett överlapp som till vilka schema utför en åtgärd.  I stället är det bäst att avgöra vilka optimeringar som du vill utföra och ändra därefter.  Visa exempelscenarier i översiktsavsnittet ytterligare förklaring.   

|**ScheduleName** | **Frekvens** | **Beskrivning**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Körs var åttonde timme | Kör runbook AutoStop_CreateAlert_Parent var åttonde timme, vilket i sin tur stoppar Virtuella datorer baserat värdena i ”External_Start_ResourceGroupNames”, ”External_Stop_ResourceGroupNames” och ”External_ExcludeVMNames” i Azure Automation-variabler.  Alternativt kan du ange en kommaavgränsad lista över virtuella datorer med hjälp av parametern ”VMList”.|  
|Scheduled_StopVM | Användardefinierade, varje dag | Kör Scheduled_Parent runbook med en parameter av ”Avbryt” varje dag vid den angivna tiden.  Stoppas automatiskt alla Virtuella datorer som uppfyller de regler som definierats via tillgången variabler.  Rekommenderar att du aktiverar systerföretaget schemalägga schemalagda StartVM.|  
|Scheduled_StartVM | Användardefinierade, varje dag | Kör Scheduled_Parent runbook med en parameter av *starta* varje dag vid den angivna tiden.  Startar automatiskt alla Virtuella datorer som uppfyller de definierade regler och en del av lämplig variabler.  Det rekommenderas att du aktiverar systerföretaget schema **schemalagda StopVM**.|
|Sekvenserade StopVM | 1:00:00 (UTC) varje fredag | Kör Sequenced_Parent runbook med en parameter av *stoppa* varje fredag vid en viss tidpunkt.  Kommer sekventiellt (stigande) Stoppa alla Virtuella datorer med en tagg av **SequenceStop** definierade och en del av lämplig variabler.  Referera till Runbooks avsnitt för mer information om värden och variabler för tillgången.  Vi rekommenderar du aktiverar systerföretaget schema **Sequenced StartVM**.|
|Sekvenserade StartVM | 1:00 PM (UTC) varje måndag | Kör Sequenced_Parent runbook med en parameter av *starta* varje måndag vid den angivna tidpunkten.  Kommer sekventiellt (fallande) starta alla Virtuella datorer med en tagg av **SequenceStart** definierade och en del av lämplig variabler.  Referera till Runbooks avsnitt för mer information om värden och variabler för tillgången.  Det rekommenderas att du aktiverar systerföretaget schema **Sequenced StopVM**.|

<br>

## <a name="configuration"></a>Konfiguration

Utför följande steg för att lägga till lösningen Starta/Stoppa virtuella datorer vid låg belastning på nätverket [förhandsgranskning] i Automation-konto och konfigurera variabler för att anpassa lösningen.

1. Klicka på **Nytt** i Azure Portal.<br> ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)<br>  
2. I rutan Marketplace skriver **starta VM**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Starta/Stoppa VMs kontorstid [förhandsgranskning]** i sökresultatet.  
3. I den **Starta/Stoppa VMs kontorstid [förhandsgranskning]** för den valda lösningen Granska sammanfattningen och klickar sedan på **skapa**.  
4. Den **Lägg till lösning** visas där du uppmanas att konfigurera lösningen innan du kan importera till Automation-prenumeration.<br><br> ![Bladet VM-hantering, lägga till lösning](media/automation-solution-vm-management/azure-portal-add-solution-01.png)<br><br>
5.  På den **Lägg till lösning** bladet väljer **arbetsytan** och här du väljer en OMS-arbetsyta som är kopplad till samma Azure-prenumerationen som Automation-konto eller skapa en ny arbetsyta.  Om du inte har en arbetsyta kan du välja **Skapa ny arbetsyta** och på den **OMS-arbetsytan** rutan Utför följande: 
   - Ange ett namn för den nya **OMS-arbetsytan**.
   - Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   - Du kan skapa en ny **resursgrupp** eller välja en befintlig resursgrupp.  
   - Välj en **Plats**.  De enda platser som angetts för markeringen är för närvarande **Australien sydost**, **Kanada Central**, **centrala Indien**, **östra USA**, **Östra**, **Sydostasien**, **Storbritannien, Syd**, och **Västeuropa**.
   - Välj en **Prisnivå**.  Lösningen erbjuds i två nivåer: kostnadsfri eller betald med OMS.  Den kostnadsfria nivån har en gräns för mängden information som samlas in varje dag, kvarhållningsperioden och körtid för runbook-jobb.  Betalningsnivån har ingen daglig gräns för insamlad data.  

        > [!NOTE]
        > När nivån Per GB (fristående) betald visas som ett alternativ kan är den inte tillämplig.  Om du väljer den och fortsätter att försöka skapa den här lösningen i din prenumeration kommer detta att misslyckas.  Detta åtgärdas när den här lösningen släpps officiellt.<br>Om du använder den här lösningen, kommer den endast använda automation-jobbminuter och logga inmatning.  Lösningen lägger inte till ytterligare OMS-noder i miljön.  

6. När du har angett informationen som krävs på bladet **OMS arbetsyta**, klickar du på **Skapa**.  När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn.  Du kommer tillbaka till bladet **Lägg till lösning**.  
7. På bladet **Lägga till lösning** väljer du **Automation-konto**.  Om du skapar en ny OMS arbetsyta, kommer du dessutom att behöva skapa tt ny Automation-konto som ska associeras med den nya OMS-arbetsytan, inklusivedin Azure-prenumeration, resursgrupp och region.  Du kan välja **Skapa ett Automation-konto** och ange följande på bladet **Lägga till Automation-konto**: 
  - I fältet **namn** anger du namnet på Automation-kontot.

    Alla andra alternativ fylls i automatiskt baserat på den valda OMS-arbetsytan. Dessa alternativ kan inte ändras.  Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen.  När du klickar på **OK** verifieras konfigurationsalternativen och Automation-kontot skapas.  Du kan spåra förloppet under **Meddelanden** på menyn. 

    Annars kan du välja ett befintligt Automation kör som-konto.  Observera att det konto som du väljer får inte redan vara kopplat till en annan OMS-arbetsyta. Om detta är fallet kommer ett meddelande att visas i bladet som informerar dig om detta.  Om det redan är länkat måste du välja ett annat Automation kör som-konto eller skapa ett nytt.<br><br> ![Automation-konto som redan är länkat till OMS-arbetsyta](media/automation-solution-vm-management/vm-management-solution-add-solution-blade-autoacct-warning.png)<br>

8. Välj slutligen **Konfiguration** på bladet **Lägga till lösning**. Bladet **Parametrar** visas.<br><br> ![Parametrarna-fönstret för lösning](media/automation-solution-vm-management/azure-portal-add-solution-02.png)<br><br>  På bladet **Parametrar** uppmanas du att:  
   - Ange **målresursnamn**, vilket är ett resursgruppsnamn som innehåller virtuella datorer som hanteras av den här lösningen.  Du kan ange flera namn och skilja dem åt med kommatecken (värden inte är skiftlägeskänsliga).  Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen.
   - Specity den **VM undanta lista (sträng)**, som är namnet på på eller flera virtuella datorer från målresursgruppen.  Du kan ange flera namn och skilja dem åt med kommatecken (värden inte är skiftlägeskänsliga).  Användning av jokertecken stöds.
   - Välj ett **Schema** som är ett återkommande datum och tid för att starta och stoppa virtuella datorer i målresursgrupperna.  Schemat har konfigurerats som UTC-tidszonen och välja en annan region finns inte som standard.  Om du vill konfigurera schemat för en viss tidszon när du har konfigurerat lösningen, se [Ändra schema för start och stopp](#modifying-the-startup-and-shutdown-schedule) nedan.
   - Ta emot **e-postmeddelanden** SendGrid, acceptera standardvärdet för **Ja** och ange en giltig e-postadress.  Om du väljer **nr** och du senare som ska ta emot e-postaviseringar måste du distribuera lösningen igen från Azure marketplace.  

10. När du har slutfört konfigurationen av de ursprungliga inställningarna som krävs för lösningen väljer du **Skapa**.  Alla inställningar kommer att valideras och sedan kommer lösningen att distribueras i din prenumeration.  Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **Meddelanden** på menyn. 

## <a name="collection-frequency"></a>Insamlingsfrekvens

Automation-loggen och jobbet dataströmmen jobbdata inhämtas till logganalys databasen var femte minut.  

## <a name="using-the-solution"></a>Använda lösningen

När du lägger till den VM hanteringslösningen i logganalys-arbetsytan från Azure-portalen på **StartStopVM visa** panel har lagts till på instrumentpanelen.  Den här panelen visar antalet och grafisk representation av runbooks jobb för den lösning som har startat och har slutförts.<br><br> ![Panelen VM-hantering, StartStopVM-vy](media/automation-solution-vm-management/vm-management-solution-startstopvm-view-tile.png)  

I ditt Automation-konto kan du komma åt och hantera lösningen genom att välja den **arbetsytan** alternativet och välj på sidan logganalys **lösningar** i den vänstra rutan.  På den **lösningar** väljer lösningen **Start-Stop-VM [Workspace]** från listan.<br><br> ![Lösningar listan i logganalys](media/automation-solution-vm-management/azure-portal-select-solution-01.png)  

Välja lösningen som ska visas i **Start-Stop-VM [Workspace]** lösning bladet där du kan granska viktig information som den **StartStopVM** panelen, som i logganalys-arbetsytan som Visar antalet och grafisk representation av runbook-jobb för lösningen som har startat och har slutförts.<br><br> ![Automation uppdateringshantering lösningssida](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)  

Du kan också utföra ytterligare analys av jobbposter genom att klicka på panelen ringen härifrån och från lösningen instrumentpanelen visas jobbhistorik fördefinierade loggen sökfrågor och växla till avancerade för Log Analytics-portalen för att söka baserat på dina sökningar.  

Alla överordnade runbooks innehåller den *WhatIf* parametern, som under inställd på **SANT**, stöder med uppgift om exakt hur runbook ska vidta när kör utan den *WhatIf* parametern och validerar rätt VMs riktas.  Runbooks utför endast de angivna åtgärderna när den *WhatIf* parametern anges till **FALSKT**.  


### <a name="scenario-1-daily-stopstart-vms-across-a-subscription-or-target-resource-groups"></a>Scenario 1: Dagligen Stoppa/starta virtuella datorer över en prenumeration eller mål-resursgrupper 

Det här är standardkonfigurationen när du distribuerar lösningen.  Du kan till exempel konfigurera den för att stoppa alla virtuella datorer över en prenumeration i kväll när du lämnar arbete och starta dem på morgonen när du är på kontoret. När du konfigurerar scheman ** schemalagda StartVM ”och **schemalagda StopVM** under distributionen kan de starta och stoppa riktade virtuella datorer.
>[!NOTE]
>Tidszonen är din aktuella tidszon när du anger parametern schema time; men den lagras i UTC-format i Azure Automation.  Du behöver inte göra några tidszonskonverteringen som hanteras under distributionen.

Du kan styra vilka virtuella datorer som ingår i omfattningen genom att konfigurera två variabler - **External_Start_ResourceGroupNames**, ** External_Stop_ResourceGroupNames, och **External_ExcludeVMNames**.  

>[!NOTE]
> Värdet för variabeln **ResourceGroup målnamn**”lagras som värde för båda **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler. Du kan ändra var och en av dessa variabler för olika resursgrupper för ytterligare granularitet.  Starta åtgärden användas **External_Start_ResourceGroupNames** och stoppa åtgärden användas **External_Stop_ResourceGroupNames** i stället. Nya virtuella datorer läggs automatiskt till starta och stoppa scheman.

Starta manuellt om du vill testa och validera konfigurationen av **ScheduledStartStop_Parent** runbook och ange den *åtgärd* parameter till **starta** eller **stoppa**  och *WhatIf* parameter till **SANT**.<br><br> ![Konfigurera parametrar för runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> På så sätt kan du förhandsgranska den åtgärd som skulle utföras och göra nödvändiga ändringar innan du implementerar mot produktion virtuella datorer.  När du är nöjd kan du manuellt köra runbook med parametern inställd på **FALSKT** eller låt Automation schemat **schema-StartVM** och **schema-StopVM**kör automatiskt följande föreskrivna schemat.

### <a name="scenario-2-sequence-the-stopstart-vms-across-a-subscription-using-tags"></a>Scenario 2: Aktivitetssekvensen den stoppa/starten virtuella datorer över en prenumeration med hjälp av taggar
I en miljö som innehåller två eller flera komponenter på flera virtuella datorer stöder en distribuerad arbetsbelastning är det viktigt att stödja de komponenter som är igång/Stoppad i ordning.  Du åstadkommer detta genom att utföra följande steg.

1. Lägga till en **SequenceStart** och **SequenceStop** tagg med ett positivt heltalsvärde till virtuella datorer i din prenumeration som är riktade i **External_Start_ResourceGroupNames**och  **External_Stop*ResourceGroupNames** variabler.  Start och stopp åtgärder utförs i stigande ordning.  Information om hur du tagga en virtuell dator finns [tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en Linux-dator i Azure](../virtual-machines/linux/tag.md)
2. Ändra scheman **Sequenced StartVM** och **Sequenced StopVM** datum och tid för att möta dina krav och aktiverar schemat.  

Starta manuellt om du vill testa och validera konfigurationen av **SequencedStartStop_Parent** runbook och ange den *åtgärd* parameter till **starta** eller **stoppa**  och *WhatIf* parameter till **SANT**.<br><br> ![Konfigurera parametrar för runbook](media/automation-solution-vm-management/solution-startrunbook-parameters-01.png)<br> På så sätt kan du förhandsgranska den åtgärd som skulle utföras och göra nödvändiga ändringar innan du implementerar mot produktion virtuella datorer.  När du är nöjd kan du manuellt köra runbook med parametern inställd på **FALSKT** eller låt Automation schemat **Sequenced StartVM** och **Sequenced StopVM**kör automatiskt följande föreskrivna schemat.  

### <a name="scenario-3-auto-stopstart-vms-across-a-subscription-based-on-cpu-utilization"></a>Scenario 3: Automatisk Stoppa/starta virtuella datorer över en prenumeration baserat på CPU-användning
Den här lösningen kan hjälpa genom att utvärdera Azure virtuella datorer som inte används under arbetsbelastningen perioder som efter timmar och automatiskt stänga ner dem om processorbelastningen är mindre än x % för hantering av kostnaden för virtuella datorer som körs i din prenumeration.  

Som standard lösningen är förkonfigurerad att utvärdera procent CPU-mått och om genomsnittlig användning 5% eller mindre.  Detta styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Du kan bara aktivera inriktning åtgärd mot en prenumeration och resursgrupp eller specifik lista med virtuella datorer, men inte båda.  

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Rikta stop-åtgärd mot en prenumeration och resurs-grupp

1. Konfigurera den **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange mål virtuella datorer.  
2. Aktivera och uppdatera den **Schedule_AutoStop_CreateAlert_Parent** schema.
3. Kör den **AutoStop_CreateAlert_Parent** runbook med den *åtgärd* parametern **starta** och *WhatIf* parametern värdet  **SANT** förhandsgranska ändringarna.

#### <a name="target-stop-action-by-vm-list"></a>Stoppåtgärder för mål av VM-lista

1. Kör den **AutoStop_CreateAlert_Parent** runbook med den *åtgärd* parametern **starta**, lägga till en kommaavgränsad lista över virtuella datorer i den *VMList* parameter, och *WhatIf* parametern **SANT** förhandsgranska ändringarna.  
2. Konfigurera den **External_ExcludeVMNames** parameter med kommatecken avgränsade lista över virtuella datorer (VM1 VM2, VM3).
3. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** varabies.  I det här scenariot behöver du skapa egna Automation-schema. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).

Nu när du har ett schema för stoppa virtuella datorer baserat på CPU-användning måste du aktivera en av de nedan scheman för att starta dem.

* Mål starta prenumeration och resursgrupp.  Hittar du i [Scenario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) för att testa och aktivera **schemalagda StartVM** schema.
* Mål starta prenumeration, resursgrupp och tagg.  Hittar du i [Scenario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) för att testa och aktivera ”Sequenced-StartVM” schemalägga.


### <a name="configuring-e-mail-notifications"></a>Konfigurera e-postaviseringar

Om du vill konfigurera e-postaviseringar när lösningen har distribuerats, kan du ändra följande tre variabler:

* External_EmailFromAddress - Ange avsändarens e-postadress
* External_EmailToAddress - kommatecken avgränsade lista över e-postadresser (user@hotmail.com, user@outlook.com) för att ta emot e-postmeddelanden
* External_IsSendEmail - om inställd på **Ja**, kommer du få e-post och inaktivera e-postaviseringar, anger du värdet till **nr**.   


### <a name="modifying-the-startup-and-shutdown-schedules"></a>Ändra scheman för start och stopp

Hantera Start och stopp scheman i den här lösningen följer samma steg som beskrivs i [schemaläggning av en runbook i Azure Automation](automation-schedules.md).     

## <a name="log-analytics-records"></a>Log Analytics-poster

Automation skapar två typer av poster i OMS-databasen.

### <a name="job-logs"></a>Jobbloggar

Egenskap | Beskrivning|
----------|----------|
Anropare |  Den som initierade åtgärden.  Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
Kategori | Klassificering av typ av data.  För Automation är värdet JobLogs.|
CorrelationId | GUID som är korrelations-Id för runbook-jobbet.|
JobId | GUID som är Id för runbook-jobbet.|
operationName | Anger åtgärdstypen i Azure.  För Automation är värdet Job.|
resourceId | Anger resurstypen i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera.  För Automation är värdet Azure Automation.|
ResourceType | Anger resurstypen i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
resultType | Status för runbookjobbet.  Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
resultDescription | Beskriver jobbstatusen för runbook.  Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
RunbookName | Anger namnet på runbooken.|
SourceSystem | Anger källsystemet för data som skickats.  För Automation är värdet: OpsManager|
StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
SubscriptionId | Anger prenumerations-ID för jobbet.
Tid | Datum och tid då runbook-jobbet körs.|


### <a name="job-streams"></a>Arbetsflöden

Egenskap | Beskrivning|
----------|----------|
Anropare |  Den som initierade åtgärden.  Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
Kategori | Klassificering av typ av data.  För Automation är värdet JobStreams.|
JobId | GUID som är Id för runbook-jobbet.|
operationName | Anger åtgärdstypen i Azure.  För Automation är värdet Job.|
ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
resourceId | Anger resurs-Id i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera.  För Automation är värdet Azure Automation.|
ResourceType | Anger resurstypen i Azure.  För Automation är värdet Automation-kontot som är kopplat till runbook.|
resultType | Resultatet av runbook-jobbet då händelsen skapades.  Möjliga värden:<br>- Ärendeposter|
resultDescription | Innehåller utdataströmmen från runbook.|
RunbookName | Anger namnet på runbooken.|
SourceSystem | Anger källsystemet för data som skickats.  För Automation är värdet OpsManager|
StreamType | Typ av jobbström. Möjliga värden:<br>- Status<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
Tid | Datum och tid då runbook-jobbet körs.|

När du utför alla loggsökningar som returnerar poster för **JobLogs** eller **JobStreams**, kan du välja vyn **JobLogs** eller **JobStreams** som visar en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen. 

Fråga | Beskrivning|
----------|----------|
Hitta jobb för runbook ScheduledStartStop_Parent har slutförts | söka efter kategori == ”JobLogs” &#124; där (RunbookName_s == ”ScheduledStartStop_Parent”) &#124; där (ResultType == ”slutfört”) &#124; Sammanfatta AggregatedValue = count() av ResultType, bin (TimeGenerated, 1h) &#124; Sortera efter TimeGenerated desc|
Hitta jobb för runbook SequencedStartStop_Parent har slutförts | söka efter kategori == ”JobLogs” &#124; där (RunbookName_s == ”SequencedStartStop_Parent”) &#124; där (ResultType == ”slutfört”) &#124; Sammanfatta AggregatedValue = count() av ResultType, bin (TimeGenerated, 1h) &#124; Sortera efter TimeGenerated desc

## <a name="removing-the-solution"></a>Tar bort lösningen

Om du inte längre behöver använda lösningen några ytterligare kan du ta bort den från Automation-kontot.  Ta bort lösningen tar endast bort runbooks, tas inte bort scheman eller variabler som skapades när lösningen har lagts till.  Resurserna som du måste ta bort manuellt om du inte använder dem med andra runbooks.  

Utför följande steg för att ta bort lösningen:

1.  Välj ditt Automation-konto **arbetsytan** i den vänstra rutan.  
2.  På den **lösningar** väljer lösningen **Start-Stop-VM [Workspace]**.  På den **VMManagementSolution [Workspace]** sida från menyn Klicka **ta bort**.<br><br> ![Ta bort VM Mgmt lösning](media/automation-solution-vm-management/vm-management-solution-delete.png)
3.  I den **ta bort lösningen** och bekräfta att du vill ta bort lösningen.
4.  När informationen har verifierats och lösningen tas bort, du kan följa förloppet under **meddelanden** på menyn.  Du kommer tillbaka till den **lösningar** sidan när processen för att ta bort lösningen har startat.  

Automation-konto och logganalys-arbetsytan tas inte bort som en del av den här processen.  Om du inte vill behålla logganalys-arbetsytan måste du manuellt ta bort den.  Detta kan åstadkommas också från Azure-portalen.   Startsidan i Azure portal, Välj **logganalys** och klicka sedan på den **logganalys** bladet välj arbetsytan och klicka på **ta bort** på menyn på den arbetsytan inställningar-bladet.  
      
## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du konstruerar olika sökfrågor och granskar jobbloggarna i Automation med Log Analytics i [Logga sökningar i Log Analytics](../log-analytics/log-analytics-log-searches.md)
- Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra runbook-jobb](automation-runbook-execution.md)
- Läs mer om logganalys och samling datakällor i [insamling av Azure storage-data i logganalys-översikt](../log-analytics/log-analytics-azure-storage.md)






   

