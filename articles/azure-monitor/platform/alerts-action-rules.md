---
title: Åtgärdsregler för Azure Monitor-aviseringar
description: För att förstå vad Åtgärdsregler i Azure Monitor är och hur du konfigurerar och hanterar dem.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656727"
---
# <a name="action-rules-preview"></a>Åtgärdsregler (förhandsversion)

Åtgärdsregler hjälper dig att definiera eller utelämna åtgärder i alla Azure Resource Manager-omfattningen (Azure-prenumeration, resursgrupp eller resurs). De har olika filter som hjälp du begränsa delmängd av aviseringsinstanser som du vill arbeta med.

## <a name="why-and-when-should-you-use-action-rules"></a>När och varför ska du använda Åtgärdsregler?

### <a name="suppression-of-alerts"></a>Undertryckning av aviseringar

Det finns många scenarier där det är användbart för att undertrycka aviseringar som genererar aviseringar. De här scenarierna sträcker sig från Undertryckning ett planerat underhållsfönster att Undertryckning utanför arbetstid. Till exempel teamet som ansvarar för **ContosoVM** vill utelämna aviseringar för kommande helgen eftersom **ContosoVM** planerat underhåll pågår. 

Även om teamet kan inaktivera varje varningsregel som är konfigurerad på **ContosoVM** manuellt och aktivera det igen när underhåll, det är inte en enkel process. Åtgärdsregler hjälper dig att definiera Undertryckning av aviseringar i stor skala med möjlighet att konfigurera ett flexibelt Undertryckning. I exemplet ovan teamet kan definiera en åtgärdsregel på **ContosoVM** som ignorerar alla aviseringar för det är helgen.


### <a name="actions-at-scale"></a>Åtgärder i stor skala

Även om Varningsregler hjälper dig att definiera åtgärdsgruppen som utlöser när aviseringen genereras, har en gemensam åtgärdsgrupp kunder ofta i deras omfång av åtgärder. Till exempel en grupp som ansvarar för resursgruppen **ContosoRG** förmodligen definierar samma åtgärdsgruppen för alla Varningsregler som definierats i **ContosoRG**. 

Åtgärdsregler hjälpa dig att förenkla den här processen. Genom att definiera åtgärder i stor skala, kan en åtgärdsgrupp aktiveras för en avisering som genereras på det konfigurerade omfånget. I exemplet ovan teamet kan definiera en åtgärdsregel på **ContosoRG** som ska utlösa samma åtgärdsgruppen för alla varningar som genererats i den.

> [!NOTE]
> Åtgärdsregler gäller för närvarande inte för Azure Service Health-aviseringar.

## <a name="configuring-an-action-rule"></a>Konfigurera en åtgärdsregel för

Du kan komma åt funktionen genom att välja **hanterar åtgärder** från den **aviseringar** landningssida i Azure Monitor. Välj **Åtgärdsregler (förhandsversion)** . Du kan komma åt regler genom att välja **Åtgärdsregler (förhandsversion)** från instrumentpanelen för landningssidan för aviseringar.

![Åtgärdsregler från Azure Monitor-landningssida](media/alerts-action-rules/action-rules-landing-page.png)

Välj **+ ny åtgärdsregel**. 

![Lägg till ny åtgärdsregel](media/alerts-action-rules/action-rules-new-rule.png)

Du kan också skapa en åtgärdsregel för när du konfigurerar en aviseringsregel.

![Lägg till ny åtgärdsregel](media/alerts-action-rules/action-rules-alert-rule.png)

Du bör nu se sidan flöde för att skapa Åtgärdsregler. Konfigurera följande element: 

![Ny åtgärd regeln Skapa flöde](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Omfång

Välj först omfattningen (Azure-prenumeration, resursgrupp eller resurs). Du kan också flera-Välj en kombination av scope i en enskild prenumeration.

![Åtgärden regelomfång](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filtervillkor

Du kan också definiera filter för att begränsa dem till en viss del av aviseringarna. 

De tillgängliga filtren är: 

* **Allvarlighetsgrad**: Möjlighet att välja en eller flera aviseringar allvarlighetsgrader. **Allvarlighetsgrad = allvarlighetsgrad 1** innebär att den som åtgärdsregeln gäller för alla aviseringar som är inställd på allvarlighetsgrad 1.
* **Övervaka tjänst**: Ett filter baserat på den ursprungliga övervakningstjänsten. Det här filtret är också flera val. Till exempel **Monitor Service = ”Application Insights”** innebär att åtgärdsregeln gäller för alla Application Insights-baserade aviseringar.
* **Resurstypen**:  Ett filter baserat på en viss resurstyp. Det här filtret är också flera val. Till exempel **resurstyp = ”virtuella datorer”** innebär att åtgärdsregeln gäller för alla virtuella datorer.
* **Avisera regel-ID**: Ett alternativ för att filtrera specifika Varningsregler med hjälp av Resource Manager-ID för regeln.
* **Övervaka tillståndet**:  Ett filter för aviseringsinstanser med antingen **Fired** eller **löst** som övervakarens villkor.
* **Beskrivning**: En matchning för regex (återkommande uttryck) som definierar en strängmatchning mot beskrivning, definierad som en del av regeln. Till exempel **beskrivningen innehåller 'prod'** matchar alla aviseringar som innehåller strängen ”prod” i deras beskrivningar.
* **Avisera kontext (nyttolast)** : En regex-matchning som definierar en strängmatchning mot aviseringskontext fält för en avisering nyttolast. Till exempel **Avisera kontext (nyttolast) innehåller dator-01** matchar alla aviseringar vars nyttolaster innehåller strängen ”dator-01”.

Dessa filter används tillsammans med varandra. Exempel: Om du ställer in **resurstypen ' = virtuella datorer** och **allvarlighetsgrad ”= Sev0**, och du har filtrerat för alla **Sev0** aviseringar på endast dina virtuella datorer. 

![Åtgärden regeln filter](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Konfiguration för Undertryckning eller åtgärd

Konfigurera åtgärdsregeln för Undertryckning av aviseringar eller stöd för grupper i åtgärden. Du kan inte välja båda. Konfigurationen fungerar på alla aviseringsinstanser som matchar den tidigare definierade omfång och filter.

#### <a name="suppression"></a>Undertryckning

Om du väljer **Undertryckning**, konfigurera varaktighet för Undertryckning av åtgärder och meddelanden. Välj ett av följande alternativ:
* **Från och med nu (alltid)** : Ignorerar alla meddelanden på obestämd tid.
* **Vid ett schemalagt klockslag**: Undertrycker meddelanden inom en begränsad tid.
* **Med en upprepning**: Undertrycker meddelanden enligt ett återkommande schema för daglig, veckovis eller månadsvis.

![Åtgärden regeln Undertryckning](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Åtgärdsgrupp

Om du väljer **åtgärdsgrupp** i växlingsknappen, Lägg till en befintlig åtgärdsgrupp eller skapa en ny. 

> [!NOTE]
> Du kan associera endast en åtgärdsgrupp med en åtgärdsregel för.

![Lägg till eller skapa ny åtgärdsregel för genom att välja åtgärdsgrupp](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Information om åtgärd-regel

Senast, konfigurera följande information för åtgärdsregeln:
* Namn
* Resursgruppen där det sparas
* Beskrivning 

## <a name="example-scenarios"></a>Exempelscenarier

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Undertryckning av aviseringar baserat på allvarlighetsgrad

Contoso vill hindra meddelanden för alla Sev4 aviseringar på alla virtuella datorer i prenumerationen **ContosoSub** varje lördag.

**Lösning:** Skapa en åtgärdsregel för:
* Scope = **ContosoSub**
* Filter
    * Allvarlighetsgrad = **Sev4**
    * Resurstyp = **virtuella datorer**
* Undertryckning med återkommande inställd på veckovis, och **lördag** och **söndag** markerat

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Undertryckning av aviseringar baserat på aviseringskontext (nyttolast)

Contoso vill hindra meddelanden för alla logga aviseringar om **dator-01** i **ContosoSub** på obestämd tid som det genomgår underhåll.

**Lösning:** Skapa en åtgärdsregel för:
* Scope = **ContosoSub**
* Filter
    * Övervaka tjänst = **Log Analytics**
    * Avisera kontext (nyttolast) innehåller **dator-01**
* Undertryckning inställd **från och med nu (alltid)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Åtgärdsgrupp som definierats på en resursgrupp

Contoso har definierat [en metrisk varning på prenumerationsnivå](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Men det vill definierar de åtgärder som utlöser specifikt för aviseringar som genereras från resursgruppen **ContosoRG**.

**Lösning:** Skapa en åtgärdsregel för:
* Scope = **ContosoRG**
* Inga filter
* Åtgärdsgrupp inställd **ContosoActionGroup**

> [!NOTE]
> *Åtgärdsgrupper som definierats i Åtgärdsregler och Varningsregler är oberoende av varandra, med inga deduplicering.* I det scenario som beskrivs tidigare, om en åtgärdsgrupp har definierats för regeln, utlöser tillsammans med åtgärdsgrupp som definierats i åtgärdsregeln. 

## <a name="managing-your-action-rules"></a>Hantera din Åtgärdsregler

Du kan visa och hantera dina Åtgärdsregler från listvyn:

![Åtgärdsregler listvy](media/alerts-action-rules/action-rules-list-view.png)

Härifrån kan du aktivera, inaktivera eller ta bort Åtgärdsregler i stor skala genom att markera kryssrutan bredvid dem. När du väljer en åtgärdsregel för, öppnas dess konfigurationssida. Sidan kan du uppdatera åtgärd Regeldefinitionen och aktivera eller inaktivera den.

## <a name="best-practices"></a>Bästa praxis

Loggaviseringar som du skapar med den [antal resultat](alerts-unified-log.md) alternativ Generera en avisering instans med hjälp av hela sökresultatet (som kan omfatta på flera datorer). I det här scenariot, om en åtgärdsregel för använder den **Varningskontexten (nyttolast)** filtret fungerar på aviseringsinstansen så länge det finns en matchning. I Scenario 2 beskrivs ovan, om sökresultatet för genererade log-avisering innehåller både **dator-01** och **dator-02**, hela meddelandet undertrycks. Det finns inget meddelande som genereras för **dator-02** alls.

![Åtgärdsregler och aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Om du vill använda bäst loggvarningar med Åtgärdsregler, skapa aviseringar med de [metriska måttenheter](alerts-unified-log.md) alternativet. Separat aviseringsinstanser genereras av det här alternativet baserat på dess definierade gruppfältet. I Scenario 2 genereras sedan separat aviseringsinstanser för **dator-01** och **dator-02**. På grund av åtgärdsregeln som beskrivs i scenariot, endast meddelande för **dator-01** undertrycks. Meddelande för **dator-02** fortsätter att utlöses som vanligt.

![Åtgärdsregler och aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>När jag konfigurerar en åtgärdsregel för, vill jag se alla möjliga överlappande Åtgärdsregler, så att jag undvika dubbla meddelanden. Är det möjligt att göra detta?

När du har definierat ett scope när du konfigurerar en åtgärdsregel för, visas en lista över Åtgärdsregler som överlappar på samma definitionsområde (om sådan finns). Den här överlappar varandra kan vara något av följande alternativ:

* En exakt matchning: Till exempel finns åtgärdsregeln som du definierar och överlappande åtgärdsregeln på samma prenumeration.
* En delmängd: Till exempel åtgärdsregeln som du definierar är på en prenumeration och överlappande åtgärdsregeln är på en resursgrupp i prenumerationen.
* En supermängd: Till exempel åtgärdsregeln som du definierar är på en resursgrupp och överlappande åtgärdsregeln finns på den prenumeration som innehåller resursgruppen.
* En skärningspunkt: Åtgärdsregeln som du definierar är till exempel på **VM1** och **VM2**, och det finns överlappande åtgärdsregeln på **VM2** och **VM3**.

![Överlappande Åtgärdsregler](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>När jag konfigurerar en aviseringsregel, är det möjligt att veta om det finns redan Åtgärdsregler definieras som kan agera utifrån varningsregeln jag definiera?

När du har definierat målresursen för din varningsregeln kan du se en lista över Åtgärdsregler som fungerar på samma definitionsområde (i förekommande fall) genom att välja **visa konfigurerade åtgärder** under den **åtgärder** avsnittet. Den här listan har fyllts i baserat på de följande scenarierna för detta scope:

* En exakt matchning: Till exempel finns varningsregeln som du definierar och åtgärdsregeln på samma prenumeration.
* En delmängd: Till exempel varningsregeln som du definierar finns på en prenumeration och åtgärdsregeln är på en resursgrupp i prenumerationen.
* En supermängd: Till exempel varningsregeln som du definierar finns på en resursgrupp och åtgärdsregeln finns på den prenumeration som innehåller resursgruppen.
* En skärningspunkt: Varningsregeln som du definierar är till exempel på **VM1** och **VM2**, och åtgärdsregeln finns på **VM2** och **VM3**.
    
![Överlappande Åtgärdsregler](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan jag se aviseringar som har inaktiverats av en åtgärdsregel?

I den [aviseringar listsidan](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), du kan välja ytterligare en kolumn kallas **Undertryckning Status**. Om meddelandet för en aviseringsinstansen hindrades visas statusen i listan.

![Undertryckt avisering instanser](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Vad händer om det finns en åtgärdsregel med en åtgärdsgrupp och en annan med Undertryckning active i samma definitionsområde?

Undertryckning alltid företräde på samma definitionsområde.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som övervakas i två separata Åtgärdsregler? Får jag ett eller två meddelanden? Till exempel **VM2** i följande scenario:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

För varje avisering på VM1 och VM3 skulle åtgärdsgrupp AG1 aktiveras en gång. För varje avisering på **VM2**, åtgärdsgrupp AG1 skulle aktiveras två gånger, eftersom Åtgärdsregler inte deduplicera åtgärder. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som övervakas i två separata Åtgärdsregler och en kräver åtgärd medan en annan för Undertryckning? Till exempel **VM2** i följande scenario:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Åtgärdsgrupp AG1 skulle aktiveras en gång för varje avisering på VM1. Åtgärder och meddelanden för varje avisering på VM2 och VM3 ska ignoreras. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Vad händer om jag har en aviseringsregel och en åtgärdsregel för som definierats för samma resurs anropa olika åtgärdsgrupper? Till exempel **VM1** i följande scenario:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Åtgärdsgrupp AG1 skulle aktiveras en gång för varje avisering på VM1. När aviseringsregeln ”1” utlöses utlöser det även AG2 dessutom. Åtgärdsgrupper som definierats i Åtgärdsregler och Varningsregler är oberoende av varandra, med inga deduplicering. 

## <a name="next-steps"></a>Nästa steg

- [Läs mer om aviseringar i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
