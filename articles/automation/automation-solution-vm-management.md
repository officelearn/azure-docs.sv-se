---
title: Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning (förhandsgranskning)
description: Denna lösning för hantering av virtuell dator startar och stoppar Azure Resource Manager virtuella datorer på ett schema och övervakar proaktivt från logganalys.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 2838d8fd53d4e2e564bb7784cb5489e9a167d5bb
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="startstop-vms-during-off-hours-solution-preview-in-azure-automation"></a>Starta/stoppa virtuella datorer vid låg belastning på nätverket lösning (förhandsgranskning) i Azure Automation

Starta/stoppa virtuella datorer kontorstid lösning startar och stoppar Azure virtuella datorer på användardefinierade scheman, ger insikter via Azure logganalys och skickar valfri e-postmeddelanden med hjälp av [SendGrid](https://azuremarketplace.microsoft.com/marketplace/apps/SendGrid.SendGrid?tab=Overview). Den stöder både Azure Resource Manager och klassiska virtuella datorer i de flesta scenarier.

Den här lösningen innehåller en decentraliserad automation-alternativ för användare som vill minska kostnaderna genom att använda serverlösa och billig resurser. Med den här lösningen kan du:

* Schemalägga virtuella datorer för att starta och stoppa.
* Schemalägga virtuella datorer för att starta och stoppa i stigande ordning med hjälp av Azure-taggar (stöds inte för klassiska virtuella datorer).
* Automatisk stoppa virtuella datorer baserat på låg CPU-belastning.

## <a name="prerequisites"></a>Förutsättningar

* Runbooks använder ett [Azure Kör som-konto](automation-offering-get-started.md#authentication-methods). Kör som-kontot är den föredragna autentiseringsmetoden eftersom den använder certifikatautentisering i stället för ett lösenord som kan gälla eller som ändras ofta.
* Denna lösning hanterar endast virtuella datorer som finns i samma prenumeration som Azure Automation-konto.
* Den här lösningen är bara distribueras till följande Azure-regioner: Australien sydost, Kanada Central, centrala Indien, östra USA, östra, Sydostasien, Storbritannien, Syd och västra Europa.

  > [!NOTE]
  > Runbooks hantera VM-schemat kan rikta virtuella datorer i en region.

* Om du vill skicka e-postmeddelanden när start och stopp VM runbooks är klar när onboarding från Azure Marketplace, Välj **Ja** att distribuera SendGrid.

  > [!IMPORTANT]
  > SendGrid är en tjänst från tredje part. Kontakta för support, [SendGrid](https://sendgrid.com/contact/).

  Med SendGrid är:

  * Högst ett SendGrid konto per användare per prenumeration.
  * Högst två SendGrid konton per prenumeration.

## <a name="deploy-the-solution"></a>Distribuera lösningen

Utför följande steg för att lägga till de virtuella datorerna Starta/Stoppa vid låg belastning på nätverket lösningen till ditt Automation-konto och sedan konfigurera variabler för att anpassa lösningen.

1. Klicka på **Skapa en resurs** i Azure Portal.
1. Marketplace-sidan skriver du ett nyckelord som **starta** eller **Starta/Stoppa**. När du börjar skriva filtreras listan baserat på det du skriver. Alternativt kan du skriver i en eller flera nyckelord från det fullständiga namnet på lösningen och tryck sedan på RETUR. Välj **Starta/Stoppa VMs kontorstid [förhandsgranskning]** i sökresultatet.
1. I den **Starta/Stoppa VMs kontorstid [förhandsgranskning]** för den valda lösningen granskar du sammanfattningen och klicka sedan på **skapa**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

1. Den **Lägg till lösning** visas. Du uppmanas att konfigurera lösningen innan du kan importera till Automation-prenumeration.
   ![Lösning för VM lägger du till sidan](media/automation-solution-vm-management/azure-portal-add-solution-01.png)
1. På den **Lägg till lösning** väljer **arbetsytan**. Välj en logganalys-arbetsyta som är kopplad till samma Azure-prenumerationen som Automation-kontot finns i. Om du inte har en arbetsyta, Välj **Skapa ny arbetsyta**. På den **OMS-arbetsytan** utför följande:
   * Ange ett namn för den nya **OMS-arbetsytan**.
   * Välj en **prenumeration** att länka till genom att välja den nedrullningsbara listan om standard valt inte är lämplig.
   * För **resursgruppen**, du kan skapa en ny resursgrupp eller välj en befintlig.
   * Välj en **Plats**. De enda platserna som är tillgängliga för närvarande **Australien sydost**, **Kanada Central**, **centrala Indien**, **östra USA**, **Östra**, **Sydostasien**, **Storbritannien, Syd**, och **Västeuropa**.
   * Välj en **Prisnivå**. Lösningen erbjuder två nivåer: **lediga** och **Per nod (OMS)**. Den kostnadsfria nivån har en gräns på mängden data som samlas in varje dag, kvarhållningsperioden och runbook-jobbet runtime minuter. Per nod nivån har inte en gräns på mängden data som samlas in varje dag.

        > [!NOTE]
        > Även om nivån Per GB (fristående) betald visas som ett alternativ kan är den inte tillämplig. Om du väljer det och fortsätta med att skapa den här lösningen i din prenumeration, misslyckas. Detta åtgärdas när den här lösningen släpps officiellt. Den här lösningen endast använder automation jobbet minuter och logga införandet. Lägger inte till fler noder i din miljö.

1. När du har angett informationen som krävs på den **OMS-arbetsytan** klickar du på **skapa**. Du kan följa förloppet under **meddelanden** på menyn, som returnerar du det **Lägg till lösning** sidan när du är klar.
1. På den **Lägg till lösning** väljer **Automation-konto**. Om du skapar en ny logganalys-arbetsyta, måste du också skapa ett nytt Automation-konto som ska associeras med den. Välj **skapa ett Automation-konto**, och på den **lägga till Automation-konto** anger du följande:
   * I fältet **namn** anger du namnet på Automation-kontot.

    Alla andra alternativ fylls i automatiskt baserat på logganalys-arbetsytan som valts. Dessa alternativ kan inte ändras. Ett Azure kör som-konto är standardmetoden för autentisering för runbooks som ingår i den här lösningen. När du klickar på **OK**konfigurationsalternativen verifieras och Automation-kontot har skapats. Du kan spåra förloppet under **Meddelanden** på menyn.

1. Slutligen på den **Lägg till lösning** väljer **Configuration**. Den **parametrar** visas.

   ![Sidan parametrar för lösning](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

   Här, uppmanas du att:
   * Ange den **mål ResourceGroup namn**. Dessa är resursgruppnamn som innehåller virtuella datorer som ska hanteras av den här lösningen. Du kan ange flera namn och skilja dem åt med kommatecken (värden inte är skiftlägeskänsliga). Användning av jokertecken stöds om du vill inkludera virtuella datorer i alla resursgrupper i prenumerationen. Det här värdet lagras i den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** variabler.
   * Ange den **VM undanta lista (sträng)**. Detta är namnet på en eller flera virtuella datorer från målresursgruppen. Du kan ange flera namn och skilja dem åt med kommatecken (värden inte är skiftlägeskänsliga). Användning av jokertecken stöds. Det här värdet lagras i den **External_ExcludeVMNames** variabeln.
   * Välj en **schema**. Detta är ett återkommande datum och tid för att starta och stoppa virtuella datorer i target-resursgrupper. Som standard konfigureras schemat till UTC-tidszonen. Det går inte att välja en annan region. Om du vill konfigurera schemat för en viss tidszon när du har konfigurerat lösningen, se [Ändra schema för start och stopp](#modify-the-startup-and-shutdown-schedule).
   * Ta emot **e-postmeddelanden** SendGrid, acceptera standardvärdet för **Ja** och ange en giltig e-postadress. Om du väljer **nr** men besluta vid ett senare tillfälle att du vill ta emot e-postmeddelanden, kan du uppdatera den **External_EmailToAddress** variabel med giltiga e-postadresser avgränsade med semikolon, och sedan ändra variabeln **External_IsSendEmail** med värdet **Ja**.

1. När du har konfigurerat de ursprungliga inställningarna som krävs för lösningen, klickar du på **OK** att stänga den **parametrar** och välja **skapa**. När alla inställningar validerats, distribuera lösningen till din prenumeration. Den här processen kan ta flera sekunder att slutföra och du kan följa förloppet under **meddelanden** på menyn.

## <a name="scenarios"></a>Scenarier

Lösningen innehåller tre olika scenarier. Dessa scenarier är:

### <a name="scenario-1-startstop-vms-on-a-schedule"></a>Scenario 1: Starta/stoppa virtuella datorer enligt ett schema

Det här är standardkonfigurationen när du distribuerar lösningen. Du kan till exempel konfigurera den för att stoppa alla virtuella datorer över en prenumeration när du lämnar arbetet i kvällen och starta dem på morgonen när du är på kontoret. När du konfigurerar scheman **schemalagda StartVM** och **schemalagda StopVM** under distributionen kan de starta och stoppa riktade virtuella datorer. Konfigurera den här lösningen för att bara stoppa virtuella datorer stöds, se [ändra scheman för start och stopp](#modify-the-startup-and-shutdown-schedules) att lära dig hur du konfigurerar ett anpassat schema.

>[!NOTE]
>Tidszonen är din aktuella tidszon när du anger parametern time schema. Men lagras den i UTC-format i Azure Automation. Du behöver inte göra några tidszonskonverteringen som hanteras under distributionen.

Du kan styra vilka virtuella datorer som ingår i omfattningen genom att konfigurera följande variabler: **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, och **External_ ExcludeVMNames**.

Du kan aktivera inriktning åtgärd mot en prenumeration och resursgrupp eller inriktning på en specifik lista med virtuella datorer, men inte båda.

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start och stopp åtgärder mot en prenumeration och resurs-grupp

1. Konfigurera den **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange mål virtuella datorer.
2. Aktivera och uppdatera den **schemalagda StartVM** och **schemalagda StopVM** scheman.
3. Kör den **ScheduledStartStop_Parent** runbook med parametern ACTION har angetts till **starta** och parametern WHATIF inställd på **SANT** förhandsgranska ändringarna.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta åtgärden start och stopp av VM-lista

1. Kör den **ScheduledStartStop_Parent** runbook med parametern ACTION har angetts till **starta**, lägga till en kommaavgränsad lista över virtuella datorer i den *VMList* parametern och ange sedan den WHATIF parametern **SANT**. Se ändringarna.
2. Konfigurera den **External_ExcludeVMNames** parameter med en kommaavgränsad lista över virtuella datorer (VM1 VM2, VM3).
3. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. I det här scenariot måste du skapa Automation schemat. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).

>[!NOTE]
> Värdet för **ResourceGroup målnamn** lagras som värde för båda **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames**. Du kan ändra var och en av dessa variabler för olika resursgrupper för ytterligare granularitet. Startåtgärden använder **External_Start_ResourceGroupNames**, och stoppåtgärden använder **External_Stop_ResourceGroupNames**. Virtuella datorer läggs automatiskt till starta och stoppa scheman.

### <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a>Scenario 2: Starta/stoppa virtuella datorer i en sekvens med hjälp av taggar

I en miljö som innehåller två eller flera komponenter på flera virtuella datorer stöder en distribuerad arbetsbelastning är det viktigt att stödja sekvensen där komponenter startas och stoppas i ordning. Du åstadkommer detta genom att utföra följande steg:

#### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Rikta start och stopp åtgärder mot en prenumeration och resurs-grupp

1. Lägg till en **SequenceStart** och en **SequenceStop** tagg med ett positivt heltalsvärde till virtuella datorer som är riktade i **External_Start_ResourceGroupNames** och  **External_Stop_ResourceGroupNames** variabler. Start och stopp åtgärder utförs i stigande ordning. Information om hur du tagga en virtuell dator finns [tagga en virtuell Windows-dator i Azure](../virtual-machines/windows/tag.md) och [tagga en Linux-dator i Azure](../virtual-machines/linux/tag.md).
2. Ändra scheman **Sequenced StartVM** och **Sequenced StopVM** för datum och tid som uppfyller dina krav och aktivera schemat.
3. Kör den **SequencedStartStop_Parent** runbook med parametern ACTION har angetts till **starta** och parametern WHATIF inställd på **SANT** förhandsgranska ändringarna.
4. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot produktion virtuella datorer. När redo, manuellt köra runbook med parametern inställd på **FALSKT**, eller låt Automation schemat **Sequenced StartVM** och **Sequenced StopVM** kör automatiskt efter det angivna schemat.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta åtgärden start och stopp av VM-lista

1. Lägg till en **SequenceStart** och en **SequenceStop** tagg med ett positivt heltalsvärde till virtuella datorer som du planerar att lägga till den **VMList** variabeln. 
2. Kör den **SequencedStartStop_Parent** runbook med parametern ACTION har angetts till **starta**, lägga till en kommaavgränsad lista över virtuella datorer i den *VMList* parametern och ange sedan den WHATIF parametern **SANT**. Se ändringarna.
3. Konfigurera den **External_ExcludeVMNames** parameter med en kommaavgränsad lista över virtuella datorer (VM1 VM2, VM3).
4. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. I det här scenariot måste du skapa Automation schemat. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).
5. Förhandsgranska åtgärden och gör nödvändiga ändringar innan du implementerar mot produktion virtuella datorer. När redo, manuellt köra runbook med parametern inställd på **FALSKT**, eller låt Automation schemat **Sequenced StartVM** och **Sequenced StopVM** kör automatiskt efter det angivna schemat.

### <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a>Scenario 3: Starta/Stoppa automatiskt baserat på CPU-användning

Den här lösningen hjälper dig att hantera kostnaden för virtuella datorer som körs i din prenumeration genom att utvärdera Azure virtuella datorer som inte används under arbetsbelastningen perioder som efter timmar och automatiskt stänga dem om processorbelastningen är mindre än x %.

Som standard är lösningen förkonfigurerade att utvärdera procent CPU mått om genomsnittlig användning är 5 procent eller mindre. Detta styrs av följande variabler och kan ändras om standardvärdena inte uppfyller dina krav:

* External_AutoStop_MetricName
* External_AutoStop_Threshold
* External_AutoStop_TimeAggregationOperator
* External_AutoStop_TimeWindow

Du kan aktivera inriktning åtgärd mot en prenumeration och resursgrupp eller inriktning på en specifik lista med virtuella datorer, men inte båda.

#### <a name="target-the-stop-action-against-a-subscription-and-resource-group"></a>Rikta stop-åtgärd mot en prenumeration och resurs-grupp

1. Konfigurera den **External_Stop_ResourceGroupNames** och **External_ExcludeVMNames** variabler för att ange mål virtuella datorer.
2. Aktivera och uppdatera den **Schedule_AutoStop_CreateAlert_Parent** schema.
3. Kör den **AutoStop_CreateAlert_Parent** runbook med parametern ACTION har angetts till **starta** och parametern WHATIF inställd på **SANT** förhandsgranska ändringarna.

#### <a name="target-the-start-and-stop-action-by-vm-list"></a>Rikta åtgärden start och stopp av VM-lista

1. Kör den **AutoStop_CreateAlert_Parent** runbook med parametern ACTION har angetts till **starta**, lägga till en kommaavgränsad lista över virtuella datorer i den *VMList* parametern och ange sedan den WHATIF parametern **SANT**. Se ändringarna.
2. Konfigurera den **External_ExcludeVMNames** parameter med en kommaavgränsad lista över virtuella datorer (VM1 VM2, VM3).
3. Det här scenariot inte att behandla den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupnames** variabler. I det här scenariot måste du skapa Automation schemat. Mer information finns i [schemaläggning av en runbook i Azure Automation](../automation/automation-schedules.md).

Du måste aktivera något av följande scheman för att starta dem nu när du har ett schema för att stoppa virtuella datorer baserat på CPU-användning.

* Mål starta prenumeration och resursgrupp. Hittar du i [Scenario 1](#scenario-1:-daily-stop/start-vms-across-a-subscription-or-target-resource-groups) för att testa och aktivera **schemalagda StartVM** scheman.
* Mål starta åtgärd av prenumerationen, resursgruppen och tagg. Hittar du i [Scenario 2](#scenario-2:-sequence-the-stop/start-vms-across-a-subscription-using-tags) för att testa och aktivera **Sequenced StartVM** scheman.

## <a name="solution-components"></a>Lösningskomponenter

Den här lösningen innehåller förkonfigurerade runbooks, scheman och integrering med logganalys så kan du anpassa Start och stopp av virtuella datorer så att den passar dina affärsbehov.

### <a name="runbooks"></a>Runbooks

I följande tabell visas de runbooks som distribueras till ditt Automation-konto med den här lösningen. Du bör inte göra ändringar i runbook-koden. I stället skriva egna runbook för nya funktioner.

> [!IMPORTANT]
> Inte direkt köra en runbook med ”underordnade” i slutet av namnet.

Alla överordnade runbooks innehåller den *WhatIf* parameter. Om värdet är **SANT**, *WhatIf* stöder med uppgift om exakt hur runbook ska vidta när kör utan den *WhatIf* parameter och validerar rätt virtuella datorer som ska mål. En runbook bara utför åtgärderna definierade när den *WhatIf* parametern anges till **FALSKT**.

|**Runbook** | **Parametrar** | **Beskrivning**|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Anropas från den överordnade runbooken. Denna runbook skapar aviseringar på grundval av per resurs för AutoStop scenariot.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True eller False  | Skapar eller uppdaterar Azure Varningsregler på virtuella datorer i målgrupperna för prenumerationen eller resursen. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel *vm1 vm2, vm3*.<br> *WhatIf* validerar runbook-logik utan att köra.|
|AutoStop_Disable | ingen | Inaktiverar AutoStop aviseringar och standardschemat.|
|AutoStop_StopVM_Child | WebHookData | Anropas från den överordnade runbooken. Varningsregler anropa denna runbook för att stoppa den virtuella datorn.|
|Bootstrap_Main | ingen | Används en gång för att ställa in bootstrap konfigurationer, till exempel webhookURI, som normalt inte nås från Azure Resource Manager. Denna runbook tas bort automatiskt vid distributionen.|
|ScheduledStartStop_Child | VMName <br> Åtgärd: Starta eller stoppa <br> ResourceGroupName | Anropas från den överordnade runbooken. Utför en åtgärd för att stoppa schemalagda att starta eller stoppa.|
|ScheduledStartStop_Parent | Åtgärd: Starta eller stoppa <br>VMList <br> WhatIf: True eller False | Detta påverkar alla virtuella datorer i prenumerationen. Redigera den **External_Start_ResourceGroupNames** och **External_Stop_ResourceGroupNames** ska köras endast på dessa mål resursgrupper. Du kan också utesluta specifika virtuella datorer genom att uppdatera den **External_ExcludeVMNames** variabeln.<br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel *vm1 vm2, vm3*.<br> *WhatIf* validerar runbook-logik utan att köra.|
|SequencedStartStop_Parent | Åtgärd: Starta eller stoppa <br> WhatIf: True eller False<br>VMList| Skapa taggar med namnet **SequenceStart** och **SequenceStop** på varje virtuell dator som du vill starta/stoppa sekvensaktivitet. Värdet för taggen ska vara ett positivt heltal (1, 2, 3) som motsvarar den ordning som du vill starta eller stoppa. <br> VMList: Kommaavgränsad lista över virtuella datorer. Till exempel *vm1 vm2, vm3*. <br> *WhatIf* validerar runbook-logik utan att köra. <br> **Obs**: virtuella datorer måste vara inom resursgrupper som har definierats som External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation-variabler. De måste ha lämpliga taggar för åtgärder ska börja gälla.|

### <a name="variables"></a>Variabler

I följande tabell visas de variabler som skapats i ditt Automation-konto. Du bör bara ändra variabler med prefixet **externa**. Ändra variabler med prefixet **internt** orsakar oönskade effekter.

|**variabeln** | **Beskrivning**|
---------|------------|
|External_AutoStop_Condition | Villkorsstyrd operatör som behövs för att konfigurera villkoret innan en avisering. Giltiga värden är **GreaterThan**, **GreaterThanOrEqual**, **LessThan**, och **LessThanOrEqual**.|
|External_AutoStop_Description | Aviseringen för att stoppa den virtuella datorn om CPU-procent överstiger tröskelvärdet.|
|External_AutoStop_MetricName | Namnet på måttet prestanda för vilka Azure varningsregeln är konfigureras.|
|External_AutoStop_Threshold | Tröskelvärdet för Azure varningsregeln har angett i variabeln *External_AutoStop_MetricName*. Procentandel värdena kan variera mellan 1 och 100.|
|External_AutoStop_TimeAggregationOperator | Tid aggregering operatorn som tillämpas på den valda fönsterstorleken för att utvärdera villkoret. Giltiga värden är **genomsnittlig**, **minsta**, **maximala**, **totala**, och **senaste**.|
|External_AutoStop_TimeWindow | Fönstrets storlek under vilka Azure analyserar valda mått för att utlösa en avisering. Den här parametern accepterar indata i timespan format. Möjliga värden är från 5 minuter och 6 timmar.|
|External_EmailFromAddress | Anger avsändaren av e-postmeddelandet.|
|External_EmailSubject | Anger texten i ämnesraden för e-postmeddelandet.|
|External_EmailToAddress | Anger mottagare av e-postmeddelandet. Avgränsa namnen med kommatecken.|
|External_ExcludeVMNames | Ange Virtuella datornamnen som ska undantas, Avgränsa namnen med kommatecken utan blanksteg.|
|External_IsSendEmail | Anger om du vill skicka e-postmeddelande när åtgärden har slutförts. Ange **Ja** eller **nr** inte skicka e-postmeddelandet. Det här alternativet ska vara **nr** om du inte har aktiverat e-postaviseringar under den inledande distributionen.|
|External_Start_ResourceGroupNames | Anger en eller flera resursgrupper, avgränsa värden med kommatecken, avsedda för start-åtgärder.|
|External_Stop_ResourceGroupNames | Anger en eller flera resursgrupper, avgränsa värden med kommatecken, stoppa åtgärder som mål.|
|Internal_AutomationAccountName | Anger namnet på Automation-kontot.|
|Internal_AutoSnooze_WebhookUri | Anger Webhook URI kallas för AutoStop scenariot.|
|Internal_AzureSubscriptionId | Anger Azure prenumerations-ID.|
|Internal_ResourceGroupName | Anger Automation-konto resursgruppens namn.|
|Internal_SendGridAccountName | Anger namnet på SendGrid.|
|Internal_SendGridPassword | Anger lösenordet för SendGrid.|

I alla scenarier är den **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames**, och **External_ExcludeVMNames** variabler krävs för att rikta virtuella datorer, med undantag för att tillhandahålla en kommaavgränsad lista över virtuella datorer för den **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**, och  **ScheduledStartStop_Parent** runbooks. Som är måste dina virtuella datorer finnas på mål resursgrupper för start och stoppa åtgärder ska inträffa. Logiken fungerar liknande Azure principen och att du kan använda prenumerationen eller resursen grupp som mål och åtgärder som ärvs av nya virtuella datorer. Den här metoden slipper du upprätthålla en separat schema för varje virtuell dator och hantera startar och stoppar i skalan.

### <a name="schedules"></a>Scheman

I följande tabell visas varje standardscheman som skapats i ditt Automation-konto.  Du kan ändra dem eller skapa egna anpassade scheman. Var och en av dessa är inaktiverat som standard utom för **Scheduled_StartVM** och **Scheduled_StopVM**.

Du bör inte aktivera alla scheman eftersom kan det skapa överlappande Schemaläggningsåtgärder. Det är bäst att avgöra vilka optimeringar som du vill utföra och ändra därefter. Visa exempelscenarier i översiktsavsnittet ytterligare förklaring.

|**namn på schema** | **Frekvens** | **Beskrivning**|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | Var åttonde timme | Kör AutoStop_CreateAlert_Parent runbook var åttonde timme, vilket i sin tur avbryter VM-baserad värdena i External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames och External_ExcludeVMNames i Azure Automation-variabler. Alternativt kan du ange en kommaavgränsad lista över virtuella datorer med hjälp av parametern VMList.|
|Scheduled_StopVM | Användardefinierade, varje dag | Kör Scheduled_Parent runbook med en parameter av *stoppa* varje dag vid den angivna tiden. Stoppar automatiskt alla virtuella datorer som uppfyller de regler som definierats av tillgångsinformation variabler. Du bör aktivera relaterade schemat **schemalagda StartVM**.|
|Scheduled_StartVM | Användardefinierade, varje dag | Kör Scheduled_Parent runbook med en parameter av *starta* varje dag vid den angivna tiden.  Startar automatiskt alla virtuella datorer som uppfyller de regler som definierats av lämplig variabler. Du bör aktivera relaterade schemat **schemalagda StopVM**.|
|Sequenced-StopVM | 1:00:00 (UTC) varje fredag | Kör Sequenced_Parent runbook med en parameter av *stoppa* varje fredag vid den angivna tidpunkten. Sekventiellt (stigande) stoppas alla virtuella datorer med en tagg av **SequenceStop** definieras av lämplig variabler. Se avsnittet Runbooks för mer information om värden och variabler för tillgången. Du bör aktivera relaterade schemat **Sequenced StartVM**.|
|Sequenced-StartVM | 1:00 PM (UTC) varje måndag | Kör Sequenced_Parent runbook med en parameter av *starta* varje måndag vid den angivna tidpunkten. Sekventiellt (fallande) börjar alla virtuella datorer med en tagg av **SequenceStart** definieras av lämplig variabler. Se avsnittet Runbooks för mer information om värden och variabler för tillgången. Du bör aktivera relaterade schemat **Sequenced StopVM**.|

## <a name="log-analytics-records"></a>Log Analytics-poster

Automation skapar två typer av poster i logganalys-arbetsytan: jobbet loggar och jobbet dataströmmar.

### <a name="job-logs"></a>Jobbloggar

Egenskap | Beskrivning|
----------|----------|
Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
Kategori | Klassificering av typ av data. För Automation är värdet JobLogs.|
CorrelationId | GUID som är Korrelations-ID för runbook-jobbet.|
JobId | GUID som är ID för runbook-jobbet.|
operationName | Anger åtgärdstypen i Azure. Värdet är jobb för automatisering.|
resourceId | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
resultType | Status för runbookjobbet. Möjliga värden:<br>- Startad<br>- Stoppad<br>-Pausad<br>- Misslyckades<br>- Slutförd|
resultDescription | Beskriver jobbstatusen för runbook. Möjliga värden:<br>-Jobbet har startats<br>-Jobbet misslyckades<br>-Jobbet slutfördes|
RunbookName | Anger namnet på runbooken.|
SourceSystem | Anger källsystemet för data som skickats. Värdet är OpsManager för automatisering|
StreamType | Anger händelsetypen. Möjliga värden:<br>- Verbose<br>- Utdata<br>- Fel<br>- Varning|
SubscriptionId | Anger prenumerations-ID för jobbet.
Tid | Datum och tid då runbook-jobbet körs.|

### <a name="job-streams"></a>Arbetsflöden

Egenskap | Beskrivning|
----------|----------|
Anropare |  Den som initierade åtgärden. Möjliga värden är antingen en e-postadress eller ett system för schemalagda jobb.|
Kategori | Klassificering av typ av data. För Automation är värdet JobStreams.|
JobId | GUID som är ID för runbook-jobbet.|
operationName | Anger åtgärdstypen i Azure. Värdet är jobb för automatisering.|
ResourceGroup | Anger resursgruppens namn på runbook-jobbet.|
resourceId | Anger resurs-ID i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
ResourceProvider | Anger den Azure-tjänst som tillhandahåller de resurser som du kan distribuera och hantera. För Automation är värdet Azure Automation.|
ResourceType | Anger resurstypen i Azure. För Automation är värdet Automation-kontot som är kopplat till runbook.|
resultType | Resultatet av runbook-jobbet då händelsen skapades. Ett möjligt värde är:<br>- Ärendeposter|
resultDescription | Innehåller utdataströmmen från runbook.|
RunbookName | Anger namnet på runbooken.|
SourceSystem | Anger källsystemet för data som skickats. Värdet är OpsManager för automatisering.|
StreamType | Typ av jobbström. Möjliga värden:<br>-Pågår<br>- Utdata<br>- Varning<br>- Fel<br>- Felsökning<br>- Verbose|
Tid | Datum och tid då runbook-jobbet körs.|

När du utför alla loggen sökningar som returnerar kategori poster för **JobLogs** eller **JobStreams**, kan du välja den **JobLogs** eller **JobStreams**visas en uppsättning paneler som sammanfattar de uppdateringar som returneras av sökningen.

## <a name="sample-log-searches"></a>Exempel på loggsökningar

Följande tabell innehåller exempel på sökningar i loggen för jobbposter som har samlats in av den här lösningen.

Fråga | Beskrivning|
----------|----------|
Hitta jobb för runbook ScheduledStartStop_Parent har slutförts | search Category == "JobLogs" &#124; where ( RunbookName_s == "ScheduledStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc|
Hitta jobb för runbook SequencedStartStop_Parent har slutförts | search Category == "JobLogs" &#124; where ( RunbookName_s == "SequencedStartStop_Parent" ) &#124; where ( ResultType == "Completed" )  &#124; summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) &#124; sort by TimeGenerated desc

## <a name="viewing-the-solution"></a>Visa lösningen

För att komma åt lösningen, navigera till ditt Automation-konto, Välj **arbetsytan** under **relaterade resurser**. Välj på sidan logganalys **lösningar** under **allmänna**. På den **lösningar** väljer lösningen **Start-Stop-VM [workspace]** från listan.

Om du väljer lösningen visas den **Start-Stop-VM [workspace]** lösning sidan. Här kan du granska viktig information som den **StartStopVM** panelen. Som i logganalys-arbetsytan visar den här panelen ett antal och en grafisk representation av runbook-jobb för lösningen som har startat och har slutförts.

![Automation uppdateringshantering lösningssida](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Härifrån kan kan du utföra ytterligare analys av jobbposter genom att klicka på panelen ringen. Lösning instrumentpanelen visar jobbhistoriken och fördefinierade loggen sökfrågor. Växla till avancerade för Log Analytics-portalen för att söka baserat på dina sökningar.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Konfigurera e-postaviseringar när lösningen har distribuerats genom att ändra följande tre variabler:

* External_EmailFromAddress: Ange avsändarens e-postadress.
* External_EmailToAddress: Ange en kommaavgränsad lista över e-postadresser (user@hotmail.com, user@outlook.com) för att ta emot e-postmeddelanden.
* External_IsSendEmail: Värdet **Ja** att ta emot e-postmeddelanden. Ange värdet till om du vill inaktivera e-postaviseringar **nr**.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Ändra scheman för start och stopp

Hantera Start och stopp scheman i den här lösningen följer samma steg som beskrivs i [schemaläggning av en runbook i Azure Automation](automation-schedules.md).

Konfigurera lösningen så att bara stoppa virtuella datorer på en viss tid stöds. Om du vill göra det måste du:

1. Se till att du har lagt till resursgrupper för de virtuella datorerna att stänga av den **External_Start_ResourceGroupNames** variabeln.
2. Skapa schemat för den tid som du vill stänga av de virtuella datorerna.
3. Navigera till den **ScheduledStartStop_Parent** runbook och klicka på **schema**. På så sätt kan du välja det schema som du skapade i föregående steg.
4. Välj **parametrar och körinställningar** och ange parametern ACTION till ”Stopp”.
5. Spara ändringarna genom att klicka på **OK**.

## <a name="update-the-solution"></a>Uppdatera lösningen

Om du har distribuerat en tidigare version av den här lösningen, du måste först ta bort det från ditt konto innan du distribuerar en uppdaterad version. Följ stegen för att [ta bort lösningen](#remove-the-solution) och följ stegen ovan för att [distribuera lösningen](#deploy-the-solution).

## <a name="remove-the-solution"></a>Ta bort lösningen

Om du inte längre behöver använda lösningen kan du ta bort den från Automation-kontot. Ta bort lösningen tas endast runbooks. Det tar inte bort scheman eller variabler som skapades när lösningen har lagts till. Resurserna som du måste ta bort manuellt om du inte använder dem med andra runbooks.

Utför följande steg för att ta bort lösningen:

1. Välj ditt Automation-konto **arbetsytan** från den vänstra sidan.
1. På den **lösningar** väljer lösningen **Start-Stop-VM [Workspace]**. På den **VMManagementSolution [Workspace]** sida på menyn, Välj **ta bort**.<br><br> ![Ta bort VM Mgmt lösning](media/automation-solution-vm-management/vm-management-solution-delete.png)
1. I den **ta bort lösningen** och bekräfta att du vill ta bort lösningen.
1. När informationen har verifierats och lösningen tas bort, du kan följa förloppet under **meddelanden** på menyn. Du kommer tillbaka till den **lösningar** sidan när processen för att ta bort lösningen har startat.

Automation-konto och logganalys-arbetsytan tas inte bort som en del av den här processen. Om du inte vill behålla logganalys-arbetsytan måste du ta bort den manuellt. Detta kan åstadkommas från Azure portal:

1. Azure portal startsidan, Välj **logganalys**.
1. På den **logganalys** väljer arbetsytan.
1. Välj **ta bort** på menyn på inställningssidan för arbetsytan.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar olika sökfrågor och granska loggarna för Automation-jobb med Log Analytics finns [logga sökningar i logganalys](../log-analytics/log-analytics-log-searches.md).
* Läs mer om att köra runbook, hur du övervakar runbook-jobb och andra tekniska detaljer i [Spåra ett runbook-jobb](automation-runbook-execution.md).
* Läs mer om logganalys och samling datakällor i [insamling av Azure storage-data i logganalys översikt](../log-analytics/log-analytics-azure-storage.md).
