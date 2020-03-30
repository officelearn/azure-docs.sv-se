---
title: Metodtips för automatisk skalning
description: Mönster för automatisk skalning i Azure för webbappar, skalningsuppsättningar för virtuella datorer och molntjänster
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: a05cf87e660cc6c388ea2055bb174c47b99da4a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248923"
---
# <a name="best-practices-for-autoscale"></a>Bästa metoder för autoskalning
Automatisk skalning av Azure Monitor gäller endast för [skalningsuppsättningar för virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/)datorer, [Molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Webbappar](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Begrepp för automatisk skalning

* En resurs kan bara ha *en* inställning för automatisk skalning
* En automatisk skalningsinställning kan ha en eller flera profiler och varje profil kan ha en eller flera regler för automatisk skalning.
* En automatisk skalningsinställning skalar instanser vågrätt, vilket är *resultatet* genom att öka instanserna och *in* genom att minska antalet instanser.
  En automatisk skalningsinställning har ett maximalt, lägsta och standardvärde för instanser.
* Ett jobb för automatisk skalning läser alltid det associerade måttet för att skala efter, och kontrollerar om det har passerat det konfigurerade tröskelvärdet för utskalning eller skalning. Du kan visa en lista över mått som automatisk skalning kan skalas av på [Azure Monitor för automatisk skalning](autoscale-common-metrics.md)av vanliga mått .
* Alla tröskelvärden beräknas på instansnivå. Till exempel "skala ut med en instans när genomsnittlig CPU > 80% när instansantalet är 2", betyder utskalning när den genomsnittliga PROCESSORN för alla instanser är större än 80%.
* Alla fel i automatisk skalning loggas till aktivitetsloggen. Du kan sedan konfigurera en [aktivitetsloggvarning](./../../azure-monitor/platform/activity-log-alerts.md) så att du kan meddelas via e-post, SMS eller webhooks när det finns ett automatiskt skalningsfel.
* På samma sätt bokförs alla lyckade skalningsåtgärder i aktivitetsloggen. Du kan sedan konfigurera en aktivitetsloggvarning så att du kan meddelas via e-post, SMS eller webhooks när det finns en lyckad automatisk skalningsåtgärd. Du kan också konfigurera e-post- eller webhook-meddelanden för att få meddelanden om lyckade skalningsåtgärder via fliken Meddelanden på inställningen för automatisk skalning.

## <a name="autoscale-best-practices"></a>Metodtips för automatisk skalning

Använd följande metodtips när du använder automatisk skalning.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Se till att de högsta och lägsta värdena är olika och har lämplig marginal mellan sig

Om du har en inställning som har minimum=2, maximum=2 och det aktuella antalet instanser är 2, kan ingen skalningsåtgärd utföras. Håll en tillräcklig marginal mellan det maximala och lägsta antalet instanser, som är inkluderande. Automatisk skalning skalas alltid mellan dessa gränser.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuell skalning återställs vid autoskalning med min och max

Om du uppdaterar instansantalet manuellt till ett värde över eller under det högsta antalet, skalas motorn för automatisk skalning automatiskt tillbaka till det minsta (om nedan) eller det högsta (om ovan). Du kan till exempel ange intervallet mellan 3 och 6. Om du har en instans som körs skalas motorn för automatisk skalning till tre instanser vid nästa körning. På samma sätt, om du manuellt ställer in skalan till åtta instanser, kommer den att skalas tillbaka till sex instanser vid nästa körning vid nästa körning.  Manuell skalning är tillfälligt om du inte återställer reglerna för automatisk skalning också.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Använd alltid en skalnings- och skalningsregelkombination som ökar och minskar
Om du bara använder en del av kombinationen utförs endast automatisk skalning i en enda riktning (skala ut eller in) tills den når det maximala eller minsta antalet instanser som definierats i profilen. Detta är inte optimalt, helst vill du att din resurs ska skalas upp i tider av hög användning för att säkerställa tillgänglighet. På samma sätt, i tider av låg användning du vill att din resurs ska skala ner, så att du kan realisera kostnadsbesparingar.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Välj lämplig statistik för ditt diagnostikmått
För diagnostikmått kan du välja mellan *Medel,* *Minimum*, *Maximum* och *Total* som mått att skala efter. Den vanligaste statistiken är *Medelvärde*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Välj tröskelvärden noga för alla måttyper
Vi rekommenderar att du noggrant väljer olika tröskelvärden för utskalning och skalning baserat på praktiska situationer.

Vi *rekommenderar inte* inställningar för automatisk skalning som exemplen nedan med samma eller liknande tröskelvärden för ut och under förhållanden:

* Öka instanserna med 1 antal när trådantal >= 600
* Minska instanser med 1 antal när trådantal <= 600

Låt oss titta på ett exempel på vad som kan leda till ett beteende som kan verka förvirrande. Tänk på följande sekvens.

1. Anta att det finns två instanser till att börja med och sedan det genomsnittliga antalet trådar per instans växer till 625.
2. Automatisk skalning skalas ut lägga till en tredje instans.
3. Anta sedan att det genomsnittliga antalet trådar över instansen sjunker till 575.
4. Innan du skalar ned försöker automatisk skalning uppskatta vad det slutliga tillståndet kommer att vara om det skalas in. Till exempel 575 x 3 (antal aktuella instanser) = 1 725 / 2 (slutligt antal instanser när de skalas ned) = 862,5 trådar. Detta innebär automatisk skalning skulle behöva omedelbart skala ut igen även efter det skalas i, om den genomsnittliga tråden räkna förblir densamma eller till och med faller bara en liten mängd. Men om det skalas upp igen, skulle hela processen upprepas, vilket leder till en oändlig loop.
5. För att undvika den här situationen (kallas "flaxande" skalar inte automatisk skalning alls. I stället hoppar den över och omvärderar villkoret igen nästa gång tjänstens jobb körs. Flaxningtillståndet kan förvirra många eftersom automatisk skalning inte skulle visas när det genomsnittliga antalet trådar var 575.

Uppskattning under en nedskalning är avsedd att undvika "flaxande" situationer, där skalning och utskalningsåtgärder kontinuerligt går fram och tillbaka. Tänk på det här när du väljer samma tröskelvärden för utskalning och in.

Vi rekommenderar att du väljer en tillräcklig marginal mellan utskalning och i tröskelvärden. Som ett exempel bör du tänka på följande bättre regelkombination.

* Öka instanserna med 1 antal när CPU% >= 80
* Minska instanser med 1 antal när CPU% <= 60

I detta fall  

1. Anta att det finns 2 instanser att börja med.
2. Om den genomsnittliga processorn % för instanser går till 80 skalas automatisk skalning ut lägga till en tredje instans.
3. Anta nu att med tiden CPU% sjunker till 60.
4. Automatisk skalningsregeln beräknar det slutliga tillståndet om det skulle skalas in. Till exempel 60 x 3 (antal aktuella instanser) = 180 / 2 (slutligt antal instanser när de skalas ned) = 90. Så automatisk skalning inte skala in eftersom det skulle behöva skala ut igen omedelbart. I stället hoppar den över skalning ner.
5. Nästa gång automatisk skalning kontroller, fortsätter processorn att falla till 50. Den uppskattar igen - 50 x 3 instans = 150 / 2 instanser = 75, vilket är under skalningströskeln på 80, så det skalas i framgångsrikt till 2 instanser.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Att tänka på vid skalning av tröskelvärden för särskilda mått
 För särskilda mått som lagring eller längd på servicebusskö är tröskelvärdet det genomsnittliga antalet tillgängliga meddelanden per aktuellt antal instanser. Välj försiktigt tröskelvärdet för det här måttet.

Låt oss illustrera det med ett exempel för att se till att du förstår beteendet bättre.

* Öka instanser med 1 antal när antalet meddelanden om lagringskö >= 50
* Minska instanser med 1 antal när antalet meddelanden om lagringskö <= 10

Tänk på följande sekvens:

1. Det finns två lagringsköinstanser.
2. Meddelanden fortsätter att komma och när du granskar lagringskön, den totala antalet läser 50. Du kan anta att automatisk skalning bör starta en utskalningsåtgärd. Observera dock att det fortfarande är 50/2 = 25 meddelanden per instans. Så, skala ut sker inte. För att den första utskalningen ska ske ska det totala antalet meddelanden i lagringskön vara 100.
3. Anta sedan att det totala antalet meddelanden når 100.
4. En tredje lagringsköinstans läggs till på grund av en utskalningsåtgärd.  Nästa utskalningsåtgärd inträffar inte förrän det totala antalet meddelanden i kön når 150 eftersom 150/3 = 50.
5. Nu blir antalet meddelanden i kön mindre. Med tre instanser inträffar den första skalningsåtgärden när den totala antalet meddelanden i alla köer lägger till upp till 30 eftersom 30/3 = 10 meddelanden per instans, vilket är tröskelvärdet för inskalning.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Att tänka på vid skalning när flera profiler har konfigurerats för autoskalning
I en automatisk skalningsinställning kan du välja en standardprofil, som alltid tillämpas utan något beroende av schema eller tid, eller så kan du välja en återkommande profil eller en profil för en fast period med ett datum och tidsintervall.

När tjänsten för automatisk skalning bearbetar dem kontrolleras den alltid i följande ordning:

1. Profil för fast datum
2. Återkommande profil
3. Standardprofil ("Alltid")

Om ett profilvillkor är uppfyllt kontrollerar automatisk skalning inte nästa profilvillkor under det. Automatisk skalning bearbetar bara en profil i taget. Det innebär att om du även vill inkludera ett bearbetningsvillkor från en profil på lägre nivå måste du även inkludera dessa regler i den aktuella profilen.

Låt oss granska med hjälp av ett exempel:

Bilden nedan visar en automatisk skalningsinställning med en standardprofil med minsta instans = 2 och högsta instans = 10. I det här exemplet konfigureras regler för att skala ut när antalet meddelanden i kön är större än 10 och skala in när antalet meddelanden i kön är mindre än tre. Så nu kan resursen skala mellan två och tio instanser.

Dessutom finns det en återkommande profil inställd på måndag. Den ställs in för minsta instans = 3 och maximala instanser = 10. Det innebär att den första gången automatiskt skalning på måndag söker efter det här villkoret, om instansantalet är två skalas det till det nya minst tre. Så länge automatisk skalning fortsätter att hitta det här profilvillkoret som matchas (måndag) bearbetar den bara de CPU-baserade utskalnings-/i-regler som konfigurerats för den här profilen. För närvarande kontrollerar den inte om kölängden. Men om du också vill att kölängdsvillkoret ska kontrolleras bör du även inkludera dessa regler från standardprofilen i din måndagsprofil.

På samma sätt, när automatisk skalning växlar tillbaka till standardprofilen, kontrollerar den först om de lägsta och högsta villkoren är uppfyllda. Om antalet instanser vid den tidpunkten är 12 skalas det till 10, det högsta tillåtna värdet för standardprofilen.

![inställningar för automatisk skalning](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Att tänka på vid skalning när flera regler har konfigurerats i en profil

Det finns fall där du kan behöva ange flera regler i en profil. Följande regler för automatisk skalning används av motorn för automatisk skalning när flera regler har angetts.

Vid *utskalning*körs automatisk skalning om någon regel uppfylls.
Vid *inskalning*kräver automatisk skalning att alla regler uppfylls.

För att illustrera, anta att du har följande fyra regler för automatisk skalning:

* Om CPU < 30 % kan du skala in med 1
* Om minne < 50 % skalas du in med 1
* Om CPU > 75 % skalas ut med 1
* Om minne > 75 % skalas ut med 1

Sedan inträffar följande:

* Om CPU är 76% och minne är 50%, skalar vi ut.
* Om CPU är 50% och minne är 76%, skalar vi ut.

Å andra sidan, om CPU är 25% och minnet är 51% automatisk skalning **inte** skala in. För att kunna skalas in måste processorn vara 29 % och minne 49 %.

### <a name="always-select-a-safe-default-instance-count"></a>Välj alltid ett säkert standardantal instanser
Standardinstansantalet är viktigt eftersom automatisk skalning skalar din tjänst till det antalet när mått inte är tillgängliga. Välj därför ett standardinstansantal som är säkert för dina arbetsbelastningar.

### <a name="configure-autoscale-notifications"></a>Konfigurera aviseringar för autoskalning
Automatisk skalning bokförs i aktivitetsloggen om något av följande villkor inträffar:

* Automatisk skalning utfärdar en skalningsåtgärd.
* Tjänsten För automatisk skalning har slutförts en skalningsåtgärd.
* Tjänsten Automatisk skalning kan inte vidta en skalningsåtgärd.
* Mått är inte tillgängliga för tjänsten för automatisk skalning för att fatta ett skalningsbeslut.
* Mått är tillgängliga (återställning) igen för att fatta ett skalningsbeslut.

Du kan också använda en aktivitetsloggavisering för att övervaka hälsotillståndet för motorn för automatisk skalning. Här är några exempel på hur [du skapar en aktivitetsloggavisering för att övervaka alla motoråtgärder för automatisk skalning på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) eller för att skapa en [aktivitetsloggavisering för att övervaka alla misslyckade åtgärder för automatisk skalning av in/utskalning av din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Förutom att använda aviseringar för aktivitetsloggar kan du också konfigurera e-post- eller webhook-meddelanden för att få meddelanden om lyckade skalningsåtgärder via meddelandefliken på inställningen för automatisk skalning.

## <a name="next-steps"></a>Efterföljande moment
- [Skapa en aktivitetsloggavisering för att övervaka alla motoråtgärder för automatisk skalning i din prenumeration.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitetsloggavisering för att övervaka alla misslyckade åtgärder för automatisk skalning i/skala ut på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

