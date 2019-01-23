---
title: Bästa metoder för autoskalning
description: Mönster för automatisk skalning i Azure för Web Apps, Virtual Machine Scale sets och molntjänster
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 85a326c97ecf8476bdd802a718e082d0e5c7a89c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467372"
---
# <a name="best-practices-for-autoscale"></a>Bästa metoder för autoskalning
Automatisk skalning i Azure Monitor gäller endast [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/), och [API Management-tjänster](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="autoscale-concepts"></a>Koncept för automatisk skalning
* En resurs kan endast ha *en* autoskalningsinställning
* En autoskalningsinställning kan ha en eller flera profiler och varje profil kan ha en eller flera regler för automatisk skalning.
* En autoskalningsinställning skalas instanser vågrätt, vilket är *ut* genom att öka instanserna och *i* genom att minska antalet instanser.
  En autoskalningsinställning har en högsta, lägsta och standardvärdet för instanser.
* Ett jobb för automatisk skalning alltid associerade mått som ska skalas efter, kontrollerar om det har överskred det konfigurerade tröskelvärdet för skala ut eller skala in. Du kan visa en lista över mått som automatisk skalning kan skala genom att på [vanliga mått för automatisk skalning för Azure Monitor](autoscale-common-metrics.md).
* Alla tröskelvärden beräknas på instansnivå. Till exempel ”skala ut med en instans när Genomsnittlig CPU > 80% när instansantalet är 2”, innebär att skala ut när den genomsnittliga CPU i alla instanser som är större än 80%.
* Alla Autoskala fel loggas i aktivitetsloggen. Du kan sedan konfigurera en [aktivitetsloggavisering](./../../azure-monitor/platform/activity-log-alerts.md) så att du kan bli meddelad via e-post, SMS eller webhooks när det finns ett fel för automatisk skalning.
* På samma sätt kan skickas alla lyckade skalningsåtgärder till aktivitetsloggen. Du kan sedan konfigurera en aktivitetsloggavisering så att du kan bli meddelad via e-post, SMS eller webhooks när det finns en lyckad Autoskala-åtgärd. Du kan också konfigurera e-post eller webhook-meddelande för att få ett meddelande om lyckad skalningsåtgärder via fliken meddelanden på knappen.

## <a name="autoscale-best-practices"></a>Metodtips för autoskalning
Använd följande metodtips när du använder automatisk skalning.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Se till att de högsta och lägsta värdena är olika och ha en tillräcklig marginal mellan dem.
Om du har en inställning som har minst = 2, maximalt = 2 och aktuellt instansantalet är 2, ingen skalningsåtgärd som kan uppstå. Håll en tillräcklig marginal mellan de högsta och lägsta antalet instanser i, vilket är inkluderande. Automatisk skalning skalar alltid mellan dessa gränser.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuell skalning återställs av Autoskala min och max
Om du manuellt uppdatera instansantalet till ett värde över eller under maximalt, skalas motorn för automatisk skalning automatiskt tillbaka till lägsta (om nedan) eller högsta (om ovan). Exempelvis kan du ställa in intervall mellan 3 och 6. Om du har en instans som körs kan skalas motorn för automatisk skalning till tre instanser på dess nästa körning. På samma sätt, om du manuellt anger skalan till åtta instanser på nästa körning skalar autoskalningsfunktionen den tillbaka till sex instanser på dess nästa körning.  Manuell skalning är tillfällig, såvida inte du återställer samt reglerna för automatisk skalning.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Använd alltid en skalbar och skala in kombination som utför en ökning och minskning
Om du använder bara en del av en kombination, vidtar automatisk skalning endast åtgärder i en riktning (skala ut eller i) tills den når högsta eller lägsta Dölj avstånd räknar av anges i profilen för. Det är inte optimalt, helst du vill att din resurs att skala upp vid tidpunkter med hög användning för att säkerställa tillgänglighet. På samma sätt kan ibland av låg användning som du vill att din resurs att skala ned, så du kan spara kostnadsbesparingar.

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Välj lämplig statistik för diagnostik-mått
För diagnostik mått som du kan välja bland *genomsnittliga*, *minsta*, *maximala* och *totala* som ett mått för att skala genom. De vanligaste statistik är *genomsnittlig*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Välj tröskelvärdena noggrant för alla typer av mått
Vi rekommenderar att du noggrant välja olika tröskelvärden för skalning och skala in baserat på praktiska situationer.

Vi *rekommenderar inte* inställningarna för automatisk skalning som i exemplen nedan med samma eller mycket lika tröskelvärdena för ut och in villkor:

* Öka instanser med 1 räknas när antal trådar < = 600
* Minska instanser med 1 räknas när antal trådar > = 600

Nu ska vi titta på ett exempel på vad som kan leda till ett problem som kan verka förvirrande. Överväg följande sekvens.

1. Anta att det finns två instanser att och sedan det genomsnittliga antalet trådar per instans växer till 625.
2. Automatisk skalning skalar ut lägger du till en tredje instans.
3. Därefter förutsätter att den genomsnittliga trådantalet över instans ligger till 575.
4. Innan du skalar, kommer autoskalning försöker beräkna det slutgiltiga tillståndet att om den skalas i. Till exempel 575 x 3 (aktuella instansantalet) = 1,725 / 2 (sista antal instanser när det skalas) = 862.5 trådar. Det innebär att automatisk skalning måste omedelbart skalbar igen trots att det skalas in, om den genomsnittliga trådantalet densamma eller även hamnar bara lite. Men om den skalas upp igen och hela processen skulle upprepa, vilket leder till en oändlig loop.
5. Du kan undvika den här situationen (som kallas ”flapping”), skalar automatisk skalning inte alls. I stället hoppar över och reevaluates villkoret igen nästa gång tjänstens jobbet körs. Detta kan förvirra många eftersom automatisk skalning inte verkar fungera när den genomsnittliga trådantalet var 575.

Uppskattning under en skala in är avsedd att undvika ”växlar” situationer kan skala in och skala ut hela tiden var fram och tillbaka. Behåll detta i åtanke när du väljer samma tröskelvärdena för skalbarhet och.

Vi rekommenderar att du väljer en tillräcklig marginal mellan skalbara och tröskelvärden. Överväg följande bättre regeln kombination exempelvis.

* Öka instanser med 1 räknas när CPU % > = 80
* Minska instanser med 1 räknas när CPU % < = 60

I det här fallet  

1. Anta att det finns 2 instanser att börja med.
2. Om den genomsnittliga CPU-% över instanser som går till 80, automatisk skalning att skalas ut att lägga till en tredje instans.
3. Anta nu att CPU-% ligger över tid och 60.
4. Automatisk skalnings inskalningsregel beräknar sluttillstånd om det vore att skala in. Till exempel 60 x 3 (aktuella instansantalet) = 180 / 2 (sista antal instanser när det skalas) = 90. Så att automatisk skalning inte skala in eftersom den skulle ha att skala ut direkt. I stället hoppas över och Nedskalning.
5. Nästa gång automatisk skalning kontrollerar Processorn fortsätter att växla till 50. Beräknar igen – 50 x 3, instans = 150 / 2 instanser = 75, vilket är under tröskelvärdet på 80, skalbara så att den skalas in har 2 instanser.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Överväganden för att skala tröskelvärden för särskilda mått
 För särskilda mått, till exempel lagring eller Service Bus-kö längd mått är tröskelvärdet det genomsnittliga antalet meddelanden som är tillgängliga per aktuellt antal instanser. Välj noga tröskelvärdet för det här måttet.

Nu ska vi illustrera det med ett exempel så att du förstår beteendet bättre.

* Öka instanser efter 1 antal när Lagringskö meddelande antal > = 50
* Minska instanser efter 1 antal när Lagringskö meddelande antal < = 10

Överväg följande ordning:

1. Det finns två instanser av storage-kö.
2. Meddelanden hela tiden och när du granskar lagringskön kan det totala antalet läser 50. Du kan anta att automatisk skalning ska starta en utskalningsåtgärd. Observera dock att den fortfarande är 50/2 = 25 meddelanden per instans. Därför uppstår inte skala ut. De första skalbara ske, bör det totala meddelandeantalet i kö för lagring vara 100.
3. Därefter anta att det totala meddelandeantalet når 100.
4. En 3 storage queue instans har lagts till på grund av en utskalningsåtgärd.  Nästa skalbar åtgärd kan inte utföras förrän det totala meddelandeantalet i kön når 150 eftersom 150/3 = 50.
5. Nu antalet meddelanden i kön blir mindre. Med tre instanser första inskalningsåtgärd händer när Totalt antal meddelanden i alla köer lägga till upp till 30 eftersom 30/3 = 10 meddelanden per instans som är tröskelvärdet för skala in.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Överväganden för att skala när flera profiler har konfigurerats i en autoskalningsinställning
Du kan välja en standardprofil som används alltid utan eventuella beroenden av schema eller den tid, i en autoskalningsinställning eller du kan välja en återkommande profil eller för en fast period med datum- och tidsintervallet.

När autoskalning tjänsten bearbetar dem, kontrollerar den alltid i följande ordning:

1. Fast datum-profil
2. Återkommande profil
3. Standardprofil (”alltid”)

Om en profil villkor är uppfyllt, kontrollerar inte automatisk skalning nästa profil villkoret under den. Automatisk skalning bearbetar endast en profil i taget. Det innebär att om du vill också inkludera ett bearbetningsvillkor från en lägre nivå-profil måste du inkludera dessa regler samt i den aktuella profilen.

Vi ska gå igenom det här med ett exempel:

Bilden nedan visar en autoskalningsinställning med en standardprofil för minsta instanser = 2 och högsta instanser = 10. I det här exemplet regler har konfigurerats för att skala ut när meddelandeantalet i kön är större än 10 och skala in när meddelandeantalet i kön är mindre än tre. Resursen kan nu skala mellan två och tio instanser.

Det finns dessutom en återkommande profil som angetts för måndag. Den är inställd för minsta instanser = 3 och högsta instanser = 10. Det innebär på måndag, automatisk skalning för första gången-söker efter det här tillståndet om instansantalet är två, utförs skalningen till den nya minst tre. Så länge som automatisk skalning fortsätter att hitta den här profilen villkoret matchas (måndag) bearbetas endast processorbaserad skala ut/in reglerna som konfigurerats för den här profilen. För närvarande kontrollerar den inte om kölängden. Men om du vill också kö längd villkoret som ska kontrolleras kan ta du dessa regler från standardprofilen samt i din profil måndag.

På samma sätt när autoskalning växlar tillbaka till standardprofilen, kontrollerar det om de lägsta och högsta villkor är uppfyllda. Om antalet instanser vid tidpunkten är 12 kan skalas i till 10, det maximalt tillåtna för standardprofilen.

![Inställningarna för automatisk skalning](./media/autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Överväganden för att skala om flera regler har konfigurerats i en profil
Det finns fall där du kan behöva ange flera regler i en profil. Följande uppsättning regler för automatisk skalning som används av tjänster används när flera regler har angetts.

På *skala ut*, automatisk skalning körs om Standardregeln är uppfyllt.
På *skala in*, automatisk skalning kräver alla regler för att vara uppfyllda.

Anta att du har följande fyra regler för automatisk skalning för att visa kan:

* Om CPU < 30%, skala in med 1
* Om minne < 50%, skala in med 1
* Om CPU > 75%, skala ut med 1
* Om minne > 75%, skala ut med 1

Följ inträffar:

* Om Processorn är 76% och minne är 50%, vi skala ut.
* Om Processorn är 50% och minne är 76% vi skala ut.

Å andra sidan, om Processorn är 25% och minne är 51% automatisk skalning gör **inte** skala in. För att skala in, CPU måste vara 29% och minne 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Välj alltid en säker standardinstanser
Standardinstansantalet är viktigt automatisk skalning skalar din tjänst till att antalet när mått inte är tillgängliga. Välj därför ett antal standardinstanser som är acceptabel för dina arbetsbelastningar.

### <a name="configure-autoscale-notifications"></a>Konfigurera autoskalningsmeddelanden
Automatisk skalning ska publicera till aktivitetsloggen om något av följande villkor inträffar:

* Automatisk skalning utfärdar en skalningsåtgärd
* Tjänsten för automatisk skalning är klar en skalningsåtgärd
* Tjänsten för automatisk skalning inte kan ta en skalningsåtgärd.
* Mått är inte tillgängliga för tjänsten för automatisk skalning kan besluta skala.
* Mått är tillgängliga (återställning) igen för att fatta ett beslut om skalning.

Du kan också använda en aktivitetsloggavisering för att övervaka hälsotillståndet för motorn för automatisk skalning. Här följer exempel [skapa en aktivitet Log aviseringen om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) eller [skapa en aktivitet Log aviseringen om du vill övervaka alla misslyckade autoskalning i / skala ut åtgärder på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

Förutom att använda aktivitetsloggaviseringar kan konfigurera du också e-post eller webhook-meddelande för att få ett meddelande om lyckad skalningsåtgärder via fliken meddelanden på knappen.

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitet Log aviseringen om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitet Log aviseringen om du vill övervaka alla misslyckade autoskalning i / skala ut åtgärder på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

