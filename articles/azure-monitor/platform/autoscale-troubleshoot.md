---
title: Felsöka automatisk azure-skalning
description: Spåra problem med Azure-automatisk skalning som används i Service Fabric, Virtuella datorer, Webbappar och molntjänster.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751329"
---
# <a name="troubleshooting-azure-autoscale"></a>Felsöka automatisk azure-skalning
 
Azure Monitor automatisk skalning hjälper dig att ha rätt mängd resurser som körs för att hantera belastningen på ditt program. Det gör att du kan lägga till resurser för att hantera ökningar i belastning och även spara pengar genom att ta bort resurser som sitter inaktiva. Du kan skala baserat på ett schema, fast datumtid eller resursmått som du väljer. Mer information finns i [Översikt över automatisk skalning](autoscale-overview.md).

Tjänsten automatisk skalning ger dig mått och loggar för att förstå vilka skalningsåtgärder som har inträffat och utvärderingen av de villkor som ledde till dessa åtgärder. Du kan hitta svar på frågor som:

- Varför har min service skalas ut eller in?
- Varför har inte min tjänst skalats?
- Varför misslyckades en åtgärd för automatisk skalning?
- Varför tar en åtgärd för automatisk skalning tid att skala?
  
## <a name="autoscale-metrics"></a>Mätvärden för automatisk skalning

Automatisk skalning ger dig [fyra mått](metrics-supported.md#microsoftinsightsautoscalesettings) för att förstå dess funktion. 

- **Observerat måttvärde** - Värdet för det mått som du valde att vidta skalningsåtgärden på, som den kan ses eller beräknas av motorn för automatisk skalning. Eftersom en enda automatisk skalningsinställning kan ha flera regler och därmed flera måttkällor kan du filtrera med "måttkälla" som en dimension.
- **Tröskelvärde** för mått – Tröskelvärdet som du anger för att vidta skalningsåtgärden. Eftersom en enda automatisk skalningsinställning kan ha flera regler och därmed flera måttkällor kan du filtrera med hjälp av "måttregel" som en dimension.
- **Observerad kapacitet** - Det aktiva antalet instanser av målresursen som ses av motor för automatisk skalning.
- **Skalningsåtgärder har initierats** – Antalet åtgärder för att skala ut och skala in som har initierats av autoskalningsmotorn. Du kan filtrera efter utskalning jämfört med skala i åtgärder.

Du kan använda [Statistikutforskaren](metrics-getting-started.md) för att kartlägga ovanstående mått på ett och samma ställe. Diagrammet ska visa:

  - det faktiska måttet
  - måttet som sett/beräknats av motor för automatisk skalning
  - tröskelvärdet för en skalningsåtgärd
  - kapacitetsförändringen 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Exempel 1 - Analysera en enkel regel för automatisk skalning 

Vi har en enkel automatisk skalningsinställning för en skalningsuppsättning för virtuella datorer som:

- skalas ut när den genomsnittliga CPU-procentandelen för en uppsättning är större än 70 % i 10 minuter 
- när processorprocenten för uppsättningen är mindre än 5 % i mer än 10 minuter. 

Nu ska vi granska måtten från tjänsten för automatisk skalning.
 
![Exempel på cpu-processorexempel för virtuell datorskala](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Exempel på cpu-processorexempel för virtuell datorskala](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Bild 1a - Procent cpu-mått för skalningsuppsättning för virtuella datorer och måttet Observerat måttvärde för automatisk skalning***

![Metrisk tröskel och observerad kapacitet](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Figur 1b - Metrisk tröskel och observerad kapacitet***

I figur 1b är **tröskelvärdet för metrisktröskel** (ljusblå linje) för skalningsregeln 70.  Den **observerade kapaciteten** (mörkblå linje) visar antalet aktiva instanser, som för närvarande är 3. 

> [!NOTE]
> Du måste filtrera **tröskelvärdet för måtttröskel** efter måttutlösareregeldimensionen skala ut (öka) för att se utskalningströskeln och av skalan i regel (minskning). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Exempel 2 - Avancerad automatisk skalning för en skalningsuppsättning för virtuella datorer

Vi har en automatisk skalningsinställning som gör att en virtuell datorskala inställd resurs skalas ut baserat på dess egna mått **Utgående flöden**. Observera att alternativet **dividera mått efter instansantal** för måtttröskeln är markerat. 

Åtgärdsregeln för skala är: 

Om värdet **för utgående flöde per instans** är större än 10, bör tjänsten för automatisk skalning skalas ut med 1 instans. 

I det här fallet beräknas den automatiska skalningsmotorns observerade måttvärde som det faktiska måttvärdet dividerat med antalet instanser. Om det observerade måttvärdet är mindre än tröskelvärdet initieras ingen utskalningsåtgärd. 
 
![Exempel på diagram för skalning av automatisk datorskalning](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Exempel på diagram för skalning av automatisk datorskalning](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Bild 2 - Exempel på måttdiagram för automatisk skalning av virtuella datorer***

I figur 2 kan du se två måttdiagram. 

Diagrammet överst visar det faktiska värdet för måttet **Utgående flöden.** Det faktiska värdet är 6. 

Diagrammet längst ned visar några värden. 
 - Det **observerade måttvärdet** (ljusblått) är 3 eftersom det finns 2 aktiva instanser och 6 dividerat med 2 är 3. 
 - **Den observerade kapaciteten** (lila) visar antalet instanser som ses av motorn med automatisk skalning. 
 - **Tröskelvärdet för metrisktröskel** (ljusgrönt) är inställt på 10. 

Om det finns flera skalningsåtgärdsregler kan du använda delnings- eller **tilläggsfilteralternativet** i diagrammet för statistikutforskaren för att titta på mått efter en viss källa eller regel. Mer information om hur du delar upp ett måttdiagram finns i [Avancerade funktioner i måttdiagram - delning](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Exempel 3 - Förstå händelser för automatisk skalning

På inställningsskärmen för automatisk skalning går du till fliken **Körhistorik** för att se de senaste skalningsåtgärderna. Fliken visar också förändringen i **Observerad kapacitet** över tid. Om du vill veta mer om alla åtgärder för automatisk skalning, inklusive åtgärder som inställningar för uppdatering/borttagning av automatisk skalning, visar du aktivitetsloggen och filtrerar efter automatisk skalning.

![Kör historik för automatisk skalning](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Resursloggar för automatisk skalning

Precis som alla andra Azure-resurser tillhandahåller tjänsten för automatisk skalning [resursloggar](platform-logs-overview.md). Det finns två kategorier av loggar.

- **Utvärderingar för** automatisk skalning - Den automatiska skalningsmotorn registrerar loggposter för varje enskild villkorsutvärdering varje gång den gör en kontroll.  Posten innehåller information om de observerade värdena för måtten, de utvärderade reglerna och om utvärderingen resulterade i en skalningsåtgärd eller inte.

- **Åtgärder för skalskalning** för automatisk skalning – Motorn registrerar skalningsåtgärder som initierats av tjänsten för automatisk skalning och resultaten av dessa skalningsåtgärder (framgång, fel och hur mycket skalning som inträffade enligt tjänsten för automatisk skalning).

Precis som med alla Azure Monitor-tjänster som stöds kan du använda [diagnostikinställningar](diagnostic-settings.md) för att dirigera dessa loggar:

- till din Log Analytics-arbetsyta för detaljerad analys
- till Event Hubs och sedan till icke-Azure-verktyg
- till ditt Azure-lagringskonto för arkivering  

![Diagnostikinställningar för automatisk skalning](media/autoscale-troubleshoot/diagnostic-settings.png)

Den föregående bilden visar diagnostikinställningarna för automatisk skalning av Azure-portalen. Där kan du välja fliken Diagnostik-/resursloggar och aktivera logginsamling och routning. Du kan också utföra samma åtgärd med REST API-, CLI-, PowerShell-, Resource Manager-mallar för diagnostikinställningar genom att välja resurstypen som *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Felsöka med loggar för automatisk skalning 

För bästa felsökningsupplevelse rekommenderar vi att du dirigerar loggarna till Azure Monitor Logs (Log Analytics) via en arbetsyta när du skapar inställningen för automatisk skalning. Den här processen visas i bilden i föregående avsnitt. Du kan validera utvärderingarna och skala åtgärder bättre med hjälp av Log Analytics.

När du har konfigurerat loggarna för automatisk skalning så att de skickas till log analytics-arbetsytan kan du köra följande frågor för att kontrollera loggarna. 

Prova den här frågan om du vill komma igång och visa de senaste utvärderingsloggarna för automatisk skalning:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Eller prova följande fråga för att visa de senaste åtgärdsloggarna för skala:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Använd följande avsnitt för att de här frågorna ska användas. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>En skalningsåtgärd inträffade som jag inte förväntade mig

Kör först frågan för skalningsåtgärd för att hitta den skalningsåtgärd som du är intresserad av. Om det är den senaste skalningsåtgärden använder du följande fråga:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Välj fältet CorrelationId i skalningsåtgärdersloggen. Använd CorrelationId för att hitta rätt utvärderingslogg. Om du kör nedanstående fråga visas alla utvärderade regler och villkor som leder till den skalningsåtgärden.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Vilken profil orsakade en skalningsåtgärd?

En skalad åtgärd inträffade, men du har överlappande regler och profiler och måste spåra vilken åtgärd som orsakade åtgärden. 

Hitta korrelationPå grund av skalningsåtgärden (som förklaras i exempel 1) och kör sedan frågan i utvärderingsloggar för att lära dig mer om profilen.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Hela profilutvärderingen kan också förstås bättre med hjälp av följande fråga

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>En skalningsåtgärd har inte inträffat

Jag förväntade mig en skala åtgärd och det inträffade inte. Det kanske inte finns några åtgärdshändelser eller loggar för skalning.

Granska mätvärdena för automatisk skalning om du använder en måttbaserad skalningsregel. Det är möjligt att det **observerade måttvärdet** eller **observerad kapacitet** inte är vad du förväntade dig att de skulle vara och därför sköt inte skalningsregeln. Du skulle fortfarande se utvärderingar, men inte en utskalningsregel. Det är också möjligt att nedkylningstiden höll en skala åtgärd från att inträffa. 

 Granska utvärderingsloggarna för automatisk skalning under den tidsperiod som du förväntade dig att skalningsåtgärden skulle inträffa. Granska alla utvärderingar den gjorde och varför den beslutade att inte utlösa en skala åtgärd.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Åtgärden Skala misslyckades

Det kan finnas ett fall där tjänsten för automatisk skalning vidtog skalningsåtgärden, men systemet beslutade att inte skala eller misslyckades med att slutföra skalningsåtgärden. Använd den här frågan för att hitta de misslyckade skalningsåtgärderna.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Skapa varningsregler för att få meddelanden om åtgärder eller fel för automatisk skalning. Du kan också skapa varningsregler för att få meddelanden om händelser med automatisk skalning.

## <a name="schema-of-autoscale-resource-logs"></a>Schema för resursloggar för automatisk skalning

Mer information finns i [resursloggar för automatisk skalning](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Nästa steg
Läs information om [metodtips för automatisk skalning](autoscale-best-practices.md). 
