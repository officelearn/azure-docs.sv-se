---
title: Åtgärdsregler för Azure Monitor-aviseringar
description: Förstå vilka åtgärdsregler i Azure Monitor är och hur du konfigurerar och hanterar dem.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 0498325984641641d6dfc9ee6b89f66800b5c7d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546487"
---
# <a name="action-rules-preview"></a>Åtgärdsregler (förhandsgranskning)

Åtgärdsregler hjälper dig att definiera eller undertrycka åtgärder i alla Azure Resource Manager-scope (Azure-prenumeration, resursgrupp eller målresurs). De har olika filter som hjälper dig att begränsa den specifika delmängden av varningsinstanser som du vill agera på.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]

## <a name="why-and-when-should-you-use-action-rules"></a>Varför och när ska du använda åtgärdsregler?

### <a name="suppression-of-alerts"></a>Undertryckande av varningar

Det finns många scenarier där det är användbart att undertrycka de meddelanden som aviseringar genererar. Dessa scenarier sträcker sig från undertryckande under ett planerat underhållsfönster till undertryckande under icke-kontorstid. Till exempel vill det team som ansvarar för **ContosoVM** att undertrycka varningsmeddelanden för den kommande helgen, eftersom **ContosoVM** genomgår planerat underhåll. 

Även om teamet kan inaktivera varje varningsregel som konfigureras på **ContosoVM** manuellt (och aktivera den igen efter underhåll), är det inte en enkel process. Åtgärdsregler hjälper dig att definiera varningsdämpning i stor skala med möjligheten att flexibelt konfigurera underdämningsperioden. I föregående exempel kan teamet definiera en åtgärdsregel för **ContosoVM** som undertrycker alla varningsmeddelanden för helgen.


### <a name="actions-at-scale"></a>Åtgärder i stor skala

Även om varningsregler hjälper dig att definiera den åtgärdsgrupp som utlöser när aviseringen genereras, har kunderna ofta en gemensam åtgärdsgrupp i hela deras verksamhetsområde. Ett team som ansvarar för resursgruppen **ContosoRG** definierar förmodligen samma åtgärdsgrupp för alla aviseringsregler som **definierats inom ContosoRG**. 

Åtgärdsregler hjälper dig att förenkla den här processen. Genom att definiera åtgärder i stor skala kan en åtgärdsgrupp utlösas för alla aviseringar som genereras på det konfigurerade scopet. I föregående exempel kan teamet definiera en åtgärdsregel för **ContosoRG** som utlöser samma åtgärdsgrupp för alla aviseringar som genereras i den.

> [!NOTE]
> Åtgärdsregler gäller för närvarande inte för Azure Service Health-aviseringar.

## <a name="configuring-an-action-rule"></a>Konfigurera en åtgärdsregel

Du kan komma åt funktionen genom att välja **Hantera åtgärder** från målsidan **för aviseringar** i Azure Monitor. Välj sedan **Åtgärdsregler (förhandsgranskning)**. Du kan komma åt reglerna genom att välja **Åtgärdsregler (förhandsgranskning)** på instrumentpanelen på målsidan för aviseringar.

![Åtgärdsregler från målsidan för Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Välj **+ Ny åtgärdsregel**. 

![Lägga till ny åtgärdsregel](media/alerts-action-rules/action-rules-new-rule.png)

Du kan också skapa en åtgärdsregel medan du konfigurerar om en varningsregel.

![Lägga till ny åtgärdsregel](media/alerts-action-rules/action-rules-alert-rule.png)

Du bör nu se flödessidan för att skapa åtgärdsregler. Konfigurera följande element: 

![Nytt flöde för att skapa åtgärdsregel](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Omfång

Välj först scope (Azure-prenumeration, resursgrupp eller målresurs). Du kan också välja en kombination av scope i en enda prenumeration.

![Scope för åtgärdsregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filtervillkor

Du kan dessutom definiera filter för att begränsa dem till en viss delmängd av aviseringarna. 

De tillgängliga filtren är: 

* **Allvarlighetsgrad**: Alternativet att välja en eller flera allvarlighetsgrader för aviseringar. **Allvarlighetsgrad = Sev1** innebär att åtgärdsregeln är tillämplig för alla aviseringar som är inställda på Sev1.
* **Övervakartjänst:** Ett filter baserat på den ursprungliga övervakningstjänsten. Det här filtret är också flera väljer. **Övervakartjänst = "Application Insights"** innebär till exempel att åtgärdsregeln gäller för alla Application Insights-baserade aviseringar.
* **Resurstyp:** Ett filter baserat på en viss resurstyp. Det här filtret är också flera väljer. **Resurstyp = "Virtuella datorer"** innebär till exempel att åtgärdsregeln är tillämplig för alla virtuella datorer.
* **Aviseringsregel-ID:** Ett alternativ för att filtrera efter specifika varningsregler med hjälp av resurshanterarens ID för varningsregeln.
* **Övervaka tillstånd:** Ett filter för varningsinstanser med antingen **Sparken** eller **Löst** som övervakarvillkor.
* **Beskrivning**: En regex (reguljärt uttryck) matchning som definierar en sträng matchning mot beskrivningen, definieras som en del av aviseringsregeln. Beskrivning innehåller till exempel **prod** matchar alla aviseringar som innehåller strängen "prod" i sina beskrivningar.
* **Aviseringskontext (nyttolast)**: En regex-matchning som definierar en strängmatchning mot aviseringskontextfälten för en aviserings nyttolast. **Aviseringskontext (nyttolast) innehåller till exempel "Dator-01"** matchar alla aviseringar vars nyttolaster innehåller strängen "Dator-01".

Dessa filter används tillsammans med varandra. Om du till exempel anger **resurstyp = Virtuella datorer** och **allvarlighetsgrad = Sev0**har du filtrerat för alla **Sev0-aviseringar** på endast dina virtuella datorer. 

![Åtgärdsregelfilter](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Konfiguration av övertryckning eller åtgärdsgrupp

Konfigurera sedan åtgärdsregeln för antingen stöd för undertryckande av aviseringar eller åtgärdsgrupp. Du kan inte välja båda. Konfigurationen fungerar på alla varningsinstanser som matchar det tidigare definierade omfånget och filtren.

#### <a name="suppression"></a>Dämpning

Om du väljer **undertryck**konfigurerar du varaktigheten för undertryckande av åtgärder och meddelanden. Välj ett av följande alternativ:
* **Från och med nu (Alltid)**: Undertrycker alla meddelanden på obestämd tid.
* **Vid en schemalagd tid**: Undertrycker meddelanden inom en begränsad varaktighet.
* **Med en upprepning**: Undertrycker meddelanden på ett återkommande dagligt, veckovis eller månadsvis schema.

![Undertryckande av åtgärdsregel](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Åtgärdsgrupp

Om du väljer **Åtgärdsgrupp** i växlingsknappen lägger du antingen till en befintlig åtgärdsgrupp eller skapar en ny. 

> [!NOTE]
> Du kan bara associera en åtgärdsgrupp med en åtgärdsregel.

![Lägga till eller skapa en ny åtgärdsregel genom att välja åtgärdsgrupp](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Information om åtgärdsregel

Konfigurera senast följande information för åtgärdsregeln:
* Namn
* Resursgrupp där den sparas
* Beskrivning 

## <a name="example-scenarios"></a>Exempelscenarier

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Undertryckande av aviseringar baserat på allvarlighetsgrad

Contoso vill undertrycka meddelanden för alla Sev4-aviseringar på alla virtuella datorer i prenumerationen **ContosoSub** varje helg.

**Lösning:** Skapa en åtgärdsregel med:
* Omfattning = **ContosoSub**
* Filter
    * Svårighetsgrad = **Sev4**
    * Resurstyp = **Virtuella datorer**
* Undertryckande med återkommande inställd på varje vecka och **lördag** och **söndag** kontrolleras

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Undertryckande av aviseringar baserat på varningskontext (nyttolast)

Contoso vill undertrycka meddelanden för alla loggaviseringar som genereras för **Dator-01** i **ContosoSub** på obestämd tid när det går igenom underhåll.

**Lösning:** Skapa en åtgärdsregel med:
* Omfattning = **ContosoSub**
* Filter
    * Övervaka tjänst = **Logga Analytics**
    * Varningskontext (nyttolast) innehåller **Dator-01**
* Dämpning inställd på **Från nu (Alltid)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Åtgärdsgrupp definierad i en resursgrupp

Contoso har definierat [en måttavisering på prenumerationsnivå](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Men den vill definiera de åtgärder som utlöser specifikt för aviseringar som genereras från resursgruppen **ContosoRG**.

**Lösning:** Skapa en åtgärdsregel med:
* Omfattning = **ContosoRG**
* Inga filter
* Åtgärdsgrupp inställd på **ContosoActionGroup**

> [!NOTE]
> *Åtgärdsgrupper som definieras i åtgärdsregler och varningsregler fungerar oberoende av dem utan deduplicering.* I det scenario som beskrivits tidigare utlöses en åtgärdsgrupp för aviseringsregeln tillsammans med den åtgärdsgrupp som definieras i åtgärdsregeln. 

## <a name="managing-your-action-rules"></a>Hantera dina åtgärdsregler

Du kan visa och hantera dina åtgärdsregler från listvyn:

![Lista vy över åtgärdsregler](media/alerts-action-rules/action-rules-list-view.png)

Härifrån kan du aktivera, inaktivera eller ta bort åtgärdsregler i stor skala genom att markera kryssrutan bredvid dem. När du väljer en åtgärdsregel öppnas dess konfigurationssida. Sidan hjälper dig att uppdatera åtgärdsregelns definition och aktivera eller inaktivera den.

## <a name="best-practices"></a>Bästa praxis

Loggaviseringar som du skapar med alternativet [antal resultat](alerts-unified-log.md) genererar en enda aviseringsinstans med hjälp av hela sökresultatet (som kan sträcka sig över flera datorer). I det här fallet, om en åtgärdsregel använder **filtret Aviseringskontext (nyttolast),** fungerar den på aviseringsinstansen så länge det finns en matchning. I scenario 2, som beskrivits tidigare, om sökresultaten för den genererade loggvarningen innehåller både **Dator-01** och **Dator-02,** ignoreras hela meddelandet. Det finns ingen anmälan genereras för **Computer-02** alls.

![Åtgärdsregler och loggvarningar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Om du vill använda loggaviseringar med åtgärdsregler bäst skapar du loggaviseringar med alternativet [måttmätning.](alerts-unified-log.md) Separata varningsinstanser genereras av det här alternativet, baserat på det definierade gruppfältet. I scenario 2 genereras sedan separata varningsinstanser för **Dator-01** och **Dator-02**. På grund av åtgärdsregeln som beskrivs i scenariot ignoreras endast meddelandet för **Dator-01.** Anmälan till **Computer-02** fortsätter att avfyras som vanligt.

![Åtgärdsregler och loggvarningar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>När jag konfigurerar en åtgärdsregel vill jag se alla möjliga överlappande åtgärdsregler, så att jag undviker dubblettmeddelanden. Är det möjligt att göra det?

När du har definierat ett scope när du konfigurerar en åtgärdsregel kan du se en lista över åtgärdsregler som överlappar samma scope (om sådana finns). Denna överlappning kan vara ett av följande alternativ:

* En exakt matchning: Åtgärdsregeln som du definierar och regeln om överlappande åtgärder finns till exempel på samma prenumeration.
* En delmängd: Åtgärdsregeln som du definierar finns till exempel på en prenumeration och den överlappande åtgärdsregeln finns på en resursgrupp inom prenumerationen.
* En superuppsättning: Åtgärdsregeln som du definierar finns till exempel i en resursgrupp och den överlappande åtgärdsregeln finns i prenumerationen som innehåller resursgruppen.
* En skärningspunkt: Åtgärdsregeln som du definierar finns till exempel på **VM1** och **VM2**och regeln om överlappande åtgärder finns på **VM2** och **VM3**.

![Överlappande åtgärdsregler](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>När jag konfigurerar en varningsregel, är det möjligt att veta om det redan finns åtgärdsregler definierade som kan fungera på den varningsregel som jag definierar?

När du har definierat målresursen för varningsregeln kan du se listan över åtgärdsregler som fungerar på samma omfång (om sådana finns) genom att välja **Visa konfigurerade åtgärder** under avsnittet **Åtgärder.** Den här listan är ifylld baserat på följande scenarier för scopet:

* En exakt matchning: Till exempel den aviseringsregel som du definierar och åtgärdsregeln finns på samma prenumeration.
* En delmängd: Till exempel är varningsregeln som du definierar på en prenumeration och åtgärdsregeln finns på en resursgrupp inom prenumerationen.
* En superuppsättning: Till exempel är aviseringsregeln som du definierar på en resursgrupp och åtgärdsregeln finns i prenumerationen som innehåller resursgruppen.
* En skärningspunkt: Till exempel är aviseringsregeln som du definierar på **VM1** och **VM2**och åtgärdsregeln finns på **VM2** och **VM3**.
    
![Överlappande åtgärdsregler](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Kan jag se aviseringar som har undertryckts av en åtgärdsregel?

På [sidan för varningsliste kan](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)du välja ytterligare en kolumn med namnet **Dämpningsstatus**. Om meddelandet för en varningsinstans utelämnades visas den statusen i listan.

![Utelämnade varningsinstanser](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Vad händer om det finns en åtgärdsregel med en åtgärdsgrupp och en annan med undertryckande aktivt i samma omfattning?

Övertryck har alltid företräde på samma omfång.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs som övervakas i två separata åtgärdsregler? Får jag en eller två aviseringar? VM2 **VM2** i följande scenario:

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

För varje avisering på VM1 och VM3 utlöses åtgärdsgruppen AG1 en gång. För varje avisering på **VM2**utlöses åtgärdsgrupp AG1 två gånger, eftersom åtgärdsregler inte deduplicerar åtgärder. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>Vad händer om jag har en resurs övervakad i två separata åtgärdsregler och en kräver åtgärder medan en annan för undertryckande? VM2 **VM2** i följande scenario:

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

För varje avisering på VM1 utlöses åtgärdsgruppen AG1 en gång. Åtgärder och meddelanden för varje avisering på VM2 och VM3 kommer att undertryckas. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>Vad händer om jag har en varningsregel och en åtgärdsregel definierad för samma resurs som anropar olika åtgärdsgrupper? VM1 **VM1** i följande scenario:

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
För varje avisering på VM1 utlöses åtgärdsgruppen AG1 en gång. När varningsregel "regel1" utlöses utlöses utlöses ag2 dessutom. Åtgärdsgrupper som definieras i åtgärdsregler och varningsregler fungerar oberoende av dem utan deduplicering. 

## <a name="next-steps"></a>Nästa steg

- [Läs mer om aviseringar i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
