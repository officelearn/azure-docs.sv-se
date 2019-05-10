---
title: Åtgärdsregler för Azure Monitor-aviseringar
description: För att förstå vad Åtgärdsregler är och hur du konfigurerar och hanterar dem.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: d27adadc9720dd2ad6a0dd133524bfaf32e63045
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65227994"
---
# <a name="action-rules-preview"></a>Åtgärdsregler (förhandsversion)

Den här artikeln beskriver vad Åtgärdsregler är och hur du konfigurerar och hanterar dem.

## <a name="what-are-action-rules"></a>Vad är Åtgärdsregler?

Åtgärdsregler kan du definiera åtgärder (eller Undertryckning av åtgärder) i alla Resource Manager-omfattningen (prenumeration, resursgrupp eller resurs). De har en mängd olika filter som gör det möjligt att begränsa till viss del av de aviseringsinstanser som du vill arbeta med. 

Med Åtgärdsregler kan du:

* Förhindra åtgärder och meddelanden om du har planerat underhållsfönster eller för helgen/helgdagar, i stället för att inaktivera var och en varningsregel för individuellt.
* Definiera åtgärder och meddelanden i hög skala: I stället att definiera en åtgärdsgrupp separat för varje regel för varning, kan du nu definiera en åtgärdsgrupp att utlösa för aviseringar som genereras på ett scope. Jag kan till exempel välja att ha åtgärdsutlösare grupp 'ContosoActionGroup' för varje avisering som genererats i min prenumeration.

## <a name="configuring-an-action-rule"></a>Konfigurera en åtgärdsregel för

Du kan komma åt funktionen genom att välja **hanterar åtgärder** från aviseringarna landningssida i Azure Monitor. Välj sedan **Åtgärdsregler (förhandsversion)**. Du kan komma åt dem genom att välja **Åtgärdsregler (förhandsversion)** från instrumentpanelen för landningssidan för aviseringar.

![Åtgärdsregler från Azure Monitor-landningssida](media/alerts-action-rules/action-rules-landing-page.png)

Välj **+ ny åtgärdsregel**. 

![Lägg till ny åtgärdsregel](media/alerts-action-rules/action-rules-new-rule.png)

Alternativt kan du också välja att skapa en åtgärdsregel för när du konfigurerar en aviseringsregel.

![Lägg till ny åtgärdsregel](media/alerts-action-rules/action-rules-alert-rule.png)

Du bör nu se Öppna åtgärd regeln skapa flödet. Konfigurera följande element: 

![Ny åtgärd regeln Skapa flöde](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope

Välj först omfattning, det vill säga målresursen, resursgrupp eller prenumeration. Du har också möjlighet att välja flera en kombination av några av ovanstående (inom en enskild prenumeration). 

![Åtgärden regelomfång](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Filtervillkor

Dessutom kan du definiera filter för att ytterligare begränsa till en specifik delmängd av aviseringar definierade omfattning. 

De tillgängliga filtren är: 

* **Allvarlighetsgrad**: Välj en eller flera aviseringar allvarlighetsgrader. Allvarlighetsgrad = allvarlighetsgrad 1 innebär att åtgärdsregeln gäller för alla aviseringar med allvarlighetsgrad som allvarlighetsgrad 1.
* **Övervaka tjänst**: Filtrera utifrån ursprungliga övervakningstjänsten. Detta är också välja flera. Till exempel övervaka Service = ”Application Insights” innebär att åtgärdsregeln gäller för alla ”Application Insights” baserade aviseringar.
* **Resurstypen**: Filtrera baserat på en viss resurstyp. Detta är också välja flera. Till exempel typ = ”virtuella datorer” innebär att åtgärdsregeln gäller för alla virtuella datorer.
* **Avisera regel-ID**: Kan du filtrera fram specifika Varningsregler med hjälp av Resource Manager-ID för den.
* **Övervaka tillståndet**: Filter för aviseringsinstanser med ”Fired” eller ”löst” som övervakarens villkor.
* **Beskrivning**: Regex matchar inom den beskrivning som definierats som en del av regeln.
* **Aviseringskontext (nyttolast)**: RegEx matchar inom den [aviseringskontext](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) fält i en aviseringsinstansen.

Dessa filter används tillsammans med varandra. Till exempel om jag ange 'Resurstypen' = 'Virtuella datorer ”och” allvarlighetsgrad ”= Sev0, så jag har filtrerat för alla 'Sev0' aviseringar på endast Mina virtuella datorer. 

![Åtgärden regeln filter](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Konfiguration för Undertryckning eller åtgärd

Därefter konfigurerar du åtgärdsregeln för Undertryckning av aviseringar eller stöd för grupper i åtgärden. Du kan inte välja båda. Konfigurationen fungerar på alla aviseringsinstanser som matchar den tidigare definierade omfång och filter.

#### <a name="suppression"></a>Undertryckning

Om du väljer **Undertryckning**, konfigurera varaktighet för Undertryckning av åtgärder och meddelanden. Välj något av följande:
* **Från och med nu (alltid)**: Ignorerar alla meddelanden på obestämd tid.
* **Vid ett schemalagt klockslag**: Utelämna meddelanden inom en begränsad tid.
* **Med en upprepning**: Utelämna på ett återkommande schema, vilket kan vara daglig, veckovis eller månadsvis.

![Åtgärden regeln Undertryckning](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Åtgärdsgrupp

Om du väljer **åtgärdsgrupp** i växlingsknappen, Lägg till en befintlig åtgärdsgrupp eller skapa en ny. 

> [!NOTE]
> Du kan associera endast en åtgärdsgrupp med en åtgärdsregel för.

![Åtgärden regeln åtgärdsgrupp](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Information om åtgärd-regel

Slutligen kan konfigurera följande information för åtgärdsregeln
* Namn
* Resursgruppen som den ska sparas
* Beskrivning 

## <a name="example-scenarios"></a>Exempelscenarier

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Scenario 1: Undertryckning av aviseringar baserat på allvarlighetsgrad

Contoso vill hindra meddelanden för alla Sev4 aviseringar på alla virtuella datorer i prenumerationen 'ContosoSub' varje lördag.

**Lösning:** Skapa en åtgärdsregel för
* Scope = 'ContosoSub'
* Filter
    * Allvarlighetsgrad = 'Sev4'
    * Resurstyp = ”virtuella datorer.
* Undertryckning med återkommande inställd på veckovis och ”lördag” och ”söndag” kontrolleras

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Scenario 2: Undertryckning av aviseringar baserat på aviseringskontext (nyttolast)

Contoso vill hindra meddelanden för alla logga aviseringar som genereras för dator-01 i 'ContosoSub' på obestämd tid som genomgår underhåll.

**Lösning:** Skapa en åtgärdsregel för
* Scope = 'ContosoSub'
* Filter
    * Övervaka tjänst = ”Log Analytics”
    * Aviseringskontext (nyttolast) innehåller dator-01
* Undertryckning inställd på ' från och med nu (alltid) ”

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Scenario 3: Åtgärdsgrupp som definierats på en resursgrupp

Contoso har definierat [en metrisk varning på prenumerationsnivå](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor), utan vill definierar de åtgärder som utlöser aviseringar separat för sina resursgruppen ”ContosoRG”.

**Lösning:** Skapa en åtgärdsregel för
* Scope = 'ContosoRG'
* Inga filter
* Åtgärdsgrupp inställd på 'ContosoActionGroup'

## <a name="managing-your-action-rules"></a>Hantera din Åtgärdsregler

Du kan visa och hantera dina Åtgärdsregler från listvyn enligt nedan.

![Åtgärdsregler listvy](media/alerts-action-rules/action-rules-list-view.png)

Härifrån kan kan du aktivera/inaktivera/ta bort Åtgärdsregler i stor skala genom att markera kryssrutan bredvid dem. När du klickar på någon åtgärdsregel öppnas sidan dess konfiguration så att du kan uppdatera dess definition och aktivera/inaktivera den.

## <a name="best-practices"></a>Regelverk

Loggaviseringar som skapats med den ['antal resultat ”](alerts-unified-log.md) alternativ generera **en enda avisering instans** med hela sökresultatet (som kan vara på flera datorer till exempel). I det här scenariot, om en åtgärdsregel för använder 'Varningskontexten (nyttolast) ”-filter ska det riktas in på aviseringsinstansen så länge det finns en matchning. I scenario 2 som beskrivs ovan, om sökresultatet för log-avisering som genererats innehåller både dator-01 och ”dator-02” hela meddelandet undertrycks (dvs, det finns inget meddelande genereras för dator-02 alls).

![Åtgärdsregler och aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Du bäst utnyttjar loggvarningar med Åtgärdsregler, rekommenderar vi att du att skapa aviseringar med de ['metriska måttenheter'](alerts-unified-log.md) alternativet. Med det här alternativet genereras separat aviseringsinstanser baserat på gruppfältet som definierats. I scenario 2, sedan genereras separat aviseringsinstanser för dator-01 och dator-02. Med åtgärdsregeln som beskrivs i scenariot, skulle endast meddelande för dator-01 förhindras medan aviseringen för dator-02 kan fortsätta att utlöses som vanligt.

![Åtgärdsregler och aviseringar (antal resultat)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Vanliga frågor

* F. Jag skulle vilja se alla möjliga överlappande åtgärd regler så att jag undvika dubbla meddelanden när du konfigurerar en åtgärdsregel för. Är det möjligt att göra detta?

    A. Du kan se en lista över Åtgärdsregler som överlappar på samma definitionsområde (i förekommande fall) när du har definierat ett scope när du konfigurerar en åtgärdsregel för. Den här överlappar varandra kan vara något av följande alternativ:
    * En exakt matchning: Till exempel finns åtgärdsregeln som du definierar och överlappande åtgärdsregeln på samma prenumeration.
    * En del: Till exempel åtgärdsregeln som du definierar är på en prenumeration och överlappande åtgärdsregeln är på en resursgrupp i prenumerationen.
    * Inbegriper: Till exempel åtgärdsregeln som du definierar är på en resursgrupp och överlappande åtgärdsregeln finns på den prenumeration som innehåller resursgruppen.
    * En skärningspunkt: Till exempel åtgärdsregeln som du definierar finns på ”VM1” och ”VM2” och överlappande åtgärdsregeln finns på ”VM2” och ”VM3”.

    ![Överlappande Åtgärdsregler](media/alerts-action-rules/action-rules-overlapping.png)

* F. Även konfigurera en aviseringsregel är det möjligt att veta om det finns redan Åtgärdsregler definierade som kan agera utifrån varningsregeln jag definiera?

    A. Du kan se listan över Åtgärdsregler som fungerar på samma definitionsområde (i förekommande fall) genom att klicka på ”Visa konfigurerade åtgärder' under avsnittet” åtgärder ”när du har definierat målresursen för din varningsregeln. Den här listan har fyllts i baserat på följande scenarier för detta scope:
    * En exakt matchning: Till exempel finns varningsregeln som du definierar och åtgärdsregeln på samma prenumeration.
    * En del: Till exempel varningsregeln som du definierar finns på en prenumeration och åtgärdsregeln är på en resursgrupp i prenumerationen.
    * Inbegriper: Till exempel varningsregeln som du definierar finns på en resursgrupp och åtgärdsregeln finns på den prenumeration som innehåller resursgruppen.
    * En skärningspunkt: Till exempel varningsregeln som du definierar finns på ”VM1” och ”VM2” och åtgärdsregeln finns på ”VM2” och ”VM3”.
    
    ![Överlappande Åtgärdsregler](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* F. Kan jag se aviseringar som har inaktiverats av en åtgärdsregel?

    A. I den [aviseringar listsidan](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), det finns ytterligare en kolumn som kan väljas kallas 'Undertryckning Status'. Om meddelandet för en aviseringsinstansen hindrades visas statusen i listan.

    ![Undertryckt avisering instanser](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* F. Vad händer om det finns en åtgärdsregel med en åtgärdsgrupp och en annan med Undertryckning active i samma definitionsområde?

    A. **Undertryckning alltid företräde på samma definitionsområde**.

* F. Vad händer om jag har en resurs som övervakas i två separata Åtgärdsregler? Får jag ett eller två meddelanden? Till exempel ”VM2” i det här scenariot:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    A. Åtgärdsgrupp 'AG1' skulle aktiveras en gång för varje avisering på ”VM1” och ”VM3”. För varje avisering på ”VM2” åtgärdsgrupp 'AG1' skulle aktiveras två gånger (**Åtgärdsregler inte ta bort duplicerad åtgärder**). 

* F. Vad händer om jag har en resurs som övervakas i två separata Åtgärdsregler och en kräver åtgärd medan en annan för Undertryckning? Till exempel ”VM2” i det här scenariot:

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    A. Åtgärdsgrupp 'AG1' skulle aktiveras en gång för varje avisering på ”VM1”. Åtgärder och meddelanden för varje avisering för ”VM2” och ”VM3” ignoreras. 

* F. Vad händer om jag har en aviseringsregel och en åtgärdsregel för som definierats för samma resurs anropa olika åtgärdsgrupper? Till exempel ”VM1” i det här scenariot:

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    A. Åtgärdsgrupp 'AG1' skulle aktiveras en gång för varje avisering på ”VM1”. När aviseringsregeln '1' utlöses utlöser det även 'AG2' dessutom. **Åtgärdsgrupper som definierats i Åtgärdsregler och Varningsregler är oberoende av varandra, med inga avduplicering**. 

## <a name="next-steps"></a>Nästa steg

- [Läs mer om aviseringar i Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
