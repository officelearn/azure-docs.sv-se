---
title: Skapa aviseringar med dynamiska tröskelvärden i Azure Monitor
description: Skapa aviseringar med maskininlärningsbaserade dynamiska tröskelvärden
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 1d21c7ed93ac2ce2ab61282707d57fbf43e0b71a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261082"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Måttaviseringar med dynamiska tröskelvärden i Azure Monitor

Måttavisering med identifiering av dynamiska tröskelvärden utnyttjar avancerad maskininlärning (ML) för att lära sig måttens historiska beteende, identifiera mönster och avvikelser som indikerar möjliga serviceproblem. Det ger stöd för både ett enkelt användargränssnitt och åtgärder i stor skala genom att tillåta användare att konfigurera varningsregler via Azure Resource Manager API, på ett helt automatiserat sätt.

När en varningsregel har skapats aktiveras det endast när det övervakade måttet inte fungerar som förväntat, baserat på dess anpassade tröskelvärden.

Vi skulle älska att höra din <azurealertsfeedback@microsoft.com>feedback, hålla det kommer på .

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Varför och när rekommenderas dynamisk villkorstyp?

1. **Skalbar varning** – Dynamiska tröskelvärdens varningsregler kan skapa skräddarsydda tröskelvärden för hundratals måttserier åt gången, men ändå ge samma enkelhet att definiera en varningsregel för ett enda mått. De ger dig färre aviseringar att skapa och hantera. Du kan använda antingen Azure-portalen eller Azure Resource Manager-API:et för att skapa dem. Den skalbara metoden är särskilt användbar när du hanterar måttdimensioner eller när du använder på flera resurser, till exempel på alla prenumerationsresurser.  [Läs mer om hur du konfigurerar måttaviseringar med dynamiska tröskelvärden med hjälp av mallar](alerts-metric-create-templates.md).

1. **Smart Metric Pattern Recognition** – Med hjälp av vår ML-teknik kan vi automatiskt upptäcka metriska mönster och anpassa oss till metriska förändringar över tid, vilket ofta kan inkludera säsongsvariationer (varje timme / dagligen/ veckovis). Att anpassa sig till måttens beteende över tid och aviseringar baserat på avvikelser från dess mönster lindrar bördan av att känna till "rätt" tröskelvärdet för varje mått. ML-algoritmen som används i dynamiska tröskelvärden är utformad för att förhindra bullriga (lågprecision) eller breda (låga återkallelse) tröskelvärden som inte har ett förväntat mönster.

1. **Intuitiv konfiguration** – Dynamiska tröskelvärden gör det möjligt att ställa in måttaviseringar med hjälp av begrepp på hög nivå, vilket minskar behovet av att ha omfattande domänkunskap om måttet.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Hur konfigurerar du aviseringsregler med dynamiska tröskelvärden?

Aviseringar med dynamiska tröskelvärden kan konfigureras via måttaviseringar i Azure Monitor. [Läs mer om hur du konfigurerar måttaviseringar](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hur beräknas tröskelvärdena?

Dynamiska tröskelvärden lär sig kontinuerligt data i måttserien och försöker modellera den med hjälp av en uppsättning algoritmer och metoder. Den upptäcker mönster i data som säsongsvariation (Varje timme / Dag / Vecka), och kan hantera bullriga mått (såsom maskin-CPU eller minne) samt mått med låg spridning (såsom tillgänglighet och felfrekvens).

Tröskelvärdena väljs på ett sådant sätt att en avvikelse från dessa tröskelvärden indikerar en avvikelse i det metriska beteendet.

> [!NOTE]
> Säsongsmönsteridentifiering är inställt på en timme, dag eller veckointervall. Detta innebär att andra mönster som varannan timme mönster eller semiweekly kanske inte upptäcks.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Vad betyder inställningen "Känslighet" i dynamiska tröskelvärden?

Känslighet för tröskelvärde för avisering är ett begrepp på hög nivå som styr mängden avvikelse från måttbeteende som krävs för att utlösa en avisering.
Det här alternativet kräver inte domänkunskap om måttet som statiskt tröskelvärde. De tillgängliga alternativen är:

- Hög – Trösklarna kommer att vara snäva och nära mönstret för metriska serier. En varningsregel utlöses på den minsta avvikelsen, vilket resulterar i fler aviseringar.
- Medium – Mindre snäva och mer balanserade tröskelvärden, färre aviseringar än med hög känslighet (standard).
- Låg – Tröskelvärdena kommer att vara lösa med mer avstånd från metriska serier mönster. En varningsregel utlöses endast vid stora avvikelser, vilket resulterar i färre aviseringar.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Vilka inställningsalternativ för operatör finns i dynamiska tröskelvärden?

Dynamic Thresholds alerts-regeln kan skapa skräddarsydda tröskelvärden baserat på måttbeteende för både övre och nedre gränser med samma varningsregel.
Du kan välja vilken avisering som ska utlösas på något av följande tre villkor:

- Större än det övre tröskelvärdet eller lägre än det nedre tröskelvärdet (standard)
- Större än det övre tröskelvärdet
- Lägre än den lägre tröskeln.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Vad betyder de avancerade inställningarna i dynamiska tröskelvärden?

**Misslyckade perioder** - Dynamiska tröskelvärden kan du också konfigurera "Antal överträdelser för att utlösa aviseringen", ett minsta antal avvikelser som krävs inom ett visst tidsfönster för att systemet ska kunna höja en avisering (standardtidsfönstret är fyra avvikelser på 20 minuter). Användaren kan konfigurera misslyckade perioder och välja vad som ska aviseras genom att ändra de misslyckade perioderna och tidsperioden. Den här möjligheten minskar varningsbrus som genereras av tillfälliga toppar. Ett exempel:

Om du vill utlösa en avisering när problemet är kontinuerligt i 20 minuter, 4 gånger i följd under en viss periodgrupp på 5 minuter, använder du följande inställningar:

![Inställningar för perioder som inte fungerar för kontinuerligt problem i 20 minuter, 4 gånger i följd under en viss periodgrupp på 5 minuter](media/alerts-dynamic-thresholds/0008.png)

Om du vill utlösa en avisering när det uppstod ett brott mot en dynamisk tröskelvärden på 20 minuter från de senaste 30 minuterna med en period på 5 minuter använder du följande inställningar:

![Inställningar för felaktiga perioder för problem i 20 minuter av de senaste 30 minuterna med periodgruppering på 5 minuter](media/alerts-dynamic-thresholds/0009.png)

**Ignorera data före** - Användare kan också eventuellt definiera ett startdatum från vilket systemet ska börja beräkna tröskelvärdena från. Ett typiskt användningsfall kan uppstå när en resurs kördes i ett testläge och nu befordras för att betjäna en produktionsarbetsbelastning, och därför bör beteendet för alla mått under testfasen ignoreras.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hur tar du reda på varför en dynamic thresholds-avisering utlöstes?

Du kan utforska utlösta varningsinstanser i varningsvyn antingen genom att klicka på länken i e-postmeddelandet eller sms:et eller webbläsaren för att se varningsvyn i Azure-portalen. [Läs mer om varningsvyn](alerts-overview.md#alerts-experience).

Varningsvyn visar:

- Alla måttdetaljer för tillfället som den dynamiska tröskelvärdena avfyrades.
- Ett diagram över den period då aviseringen utlöstes som innehåller de dynamiska tröskelvärden som användes vid den tidpunkten.
- Möjlighet att ge feedback på dynamic thresholds alert och aviseringar visa erfarenhet, vilket kan förbättra framtida identifieringar.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Utlöser långsamma beteendeändringar i måttet en avisering?

Förmodligen inte. Dynamiska trösklar är bra för att upptäcka betydande avvikelser snarare än att långsamt utvecklas.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Hur mycket data används för att förhandsgranska och sedan beräkna tröskelvärden?

När en varningsregel först skapas beräknas tröskelvärdena i diagrammet baserat på tillräckligt många historiska data för att beräkna tim- eller dagliga säsongsmönster (10 dagar). När en varningsregel har skapats använder dynamiska tröskelvärden alla nödvändiga historiska data som är tillgängliga och lär sig kontinuerligt och anpassar sig baserat på nya data för att göra tröskelvärdena mer exakta. Detta innebär att diagrammet efter den här beräkningen också visar veckomönster.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Hur mycket data behövs för att utlösa en avisering?

Om du har en ny resurs eller saknade måttdata utlöser dynamiska tröskelvärden inte aviseringar före tre dagar och minst 30 exempel på måttdata är tillgängliga för att säkerställa korrekta tröskelvärden.
För befintliga resurser med tillräckliga måttdata kan dynamiska tröskelvärden utlösa aviseringar omedelbart.

## <a name="dynamic-thresholds-best-practices"></a>Metodtips för dynamiska tröskelvärden

Dynamiska tröskelvärden kan tillämpas på alla plattformar eller anpassade mått i Azure Monitor och det har också justerats för de vanliga program- och infrastrukturmåtten.
Följande objekt är metodtips för hur du konfigurerar aviseringar för vissa av dessa mått med dynamiska tröskelvärden.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamiska tröskelvärden för processorprocentmått för virtuella datorer

1. I [Azure Portal](https://portal.azure.com)klickar du på **Övervaka**. Monitor-vyn konsoliderar alla dina övervakningsinställningar och data i en vy.

2. Klicka på Aviseringar och klicka sedan på **+ Ny varningsregel**. **Alerts**

    > [!TIP]
    > De flesta resursblad har också **aviseringar** i sin resursmeny under **Övervakning,** du kan skapa aviseringar därifrån också.

3. Klicka på **Välj mål**i kontextfönstret som läses in och välj en målresurs som du vill avisera på. Använd listrutan **Prenumeration** **och resurstyp för virtuella** datorer för att hitta den resurs som du vill övervaka. Du kan också använda sökfältet för att hitta din resurs.

4. När du har valt en målresurs klickar du på **Lägg till villkor**.

5. Välj **"CPU-procent"**.

6. Du kan också förfina måttet genom att justera **Period** och **Aggregering**. Det rekommenderas att använda aggregeringstypen "Maximum" för den här måtttypen eftersom den är mindre representativ för beteendet. För "Maximal" aggregering typ statiskt tröskel kanske mer lämpligt.

7. Du kommer att se ett diagram för måttet för de senaste 6 timmarna. Definiera aviseringsparametrarna:
    1. **Villkorstyp** - Välj alternativet Dynamisk.
    1. **Känslighet** - Välj Medel/Låg känslighet för att minska varningsbruset.
    1. **Operator** - Välj 'Större än' om inte beteendet representerar programanvändningen.
    1. **Frekvens** - Överväg att sänka baserat på verksamhetens påverkan av varningen.
    1. **Misslyckade perioder** (avancerat alternativ) - Fönstret titta tillbaka ska vara minst 15 minuter. Om perioden till exempel är inställd på fem minuter bör misslyckade perioder vara minst tre eller fler.

8. Måttdiagrammet visar de beräknade tröskelvärdena baserat på de senaste uppgifterna.

9. Klicka på **Klar**.

10. Fyll i **aviseringsinformation** som **varningsregelnamn,** **beskrivning**och **allvarlighetsgrad**.

11. Lägg till en åtgärdsgrupp i aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

12. Klicka på **Klar** om du vill spara måttaviseringsregeln.

> [!NOTE]
> Måttaviseringsregler som skapas via portalen skapas i samma resursgrupp som målresursen.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamiska tröskelvärden för programstatistik HTTP-körningstid för HTTP-begäran

1. I [Azure Portal](https://portal.azure.com)klickar du på **Övervaka**. Monitor-vyn konsoliderar alla dina övervakningsinställningar och data i en vy.

2. Klicka på Aviseringar och klicka sedan på **+ Ny varningsregel**. **Alerts**

    > [!TIP]
    > De flesta resursblad har också **aviseringar** i sin resursmeny under **Övervakning,** du kan skapa aviseringar därifrån också.

3. Klicka på **Välj mål**i kontextfönstret som läses in och välj en målresurs som du vill avisera på. Använd listrutan **Prenumeration** **och Programstatistik För** att hitta den resurs som du vill övervaka. Du kan också använda sökfältet för att hitta din resurs.

4. När du har valt en målresurs klickar du på **Lägg till villkor**.

5. Välj **"HTTP-körningstid för begäran".**

6. Du kan också förfina måttet genom att justera **Period** och **Aggregering**. Det rekommenderas att använda aggregeringstypen "Maximum" för den här måtttypen eftersom den är mindre representativ för beteendet. För "Maximal" aggregering typ statiskt tröskel kanske mer lämpligt.

7. Du kommer att se ett diagram för måttet för de senaste 6 timmarna. Definiera aviseringsparametrarna:
    1. **Villkorstyp** - Välj alternativet Dynamisk.
    1. **Operator** - Välj "Större än" för att minska varningar som utlöses på förbättring i varaktighet.
    1. **Frekvens** - Överväg att sänka baserat på verksamhetens påverkan av varningen.

8. Måttdiagrammet visar de beräknade tröskelvärdena baserat på de senaste uppgifterna.

9. Klicka på **Klar**.

10. Fyll i **aviseringsinformation** som **varningsregelnamn,** **beskrivning**och **allvarlighetsgrad**.

11. Lägg till en åtgärdsgrupp i aviseringen antingen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

12. Klicka på **Klar** om du vill spara måttaviseringsregeln.

> [!NOTE]
> Måttaviseringsregler som skapas via portalen skapas i samma resursgrupp som målresursen.

## <a name="interpreting-dynamic-threshold-charts"></a>Tolka dynamiska tröskelvärden

Följande är ett diagram som visar ett mått, dess dynamiska tröskelvärden gränser och vissa aviseringar som utlöses när värdet var utanför de tillåtna tröskelvärdena.

![Läs mer om hur du konfigurerar måttaviseringar](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Använd följande information för att tolka föregående diagram.

- **Blå linje** - Det faktiska uppmätta måttet över tid.
- **Blått skuggat område** - Visar det tillåtna intervallet för måttet. Så länge måttvärdena håller sig inom det här intervallet kommer ingen avisering att ske.
- **Blå punkter** - Om du lämnade klicka på en del av diagrammet och sedan hovrar över den blå linjen, ser du en blå punkt visas under markören som visar ett enskilt aggregerat måttvärde.
- **Pop-up med blå punkt** - Visar det uppmätta måttvärdet (den blå punkten) och de övre och nedre värdena för tillåtet intervall.  
- **Röd punkt med en svart cirkel** - Visar det första måttvärdet utanför det tillåtna intervallet. Det här är värdet som utlöser en måttavisering och placerar den i ett aktivt tillstånd.
- **Röda punkter**- Ange ytterligare uppmätta värden utanför det tillåtna intervallet. De kommer inte att avfyra ytterligare måttaviseringar, men aviseringen förblir aktiv.
- **Rött område** - Visar den tid då måttvärdet var utanför det tillåtna intervallet. Aviseringen förblir aktiv så länge efterföljande uppmätta värden ligger utanför det tillåtna intervallet, men inga nya aviseringar utlöses.
- **Slutet av det röda området** - När den blå linjen är tillbaka inuti de tillåtna värdena stannar det röda området och den uppmätta värdelinjen blir blå. Statusen för måttaviseringen som utlöses vid tidpunkten för den röda punkten med svart kontur är inställd på att matchas. 
