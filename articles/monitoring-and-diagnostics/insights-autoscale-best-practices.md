---
title: "Metodtips för Autoskala | Microsoft Docs"
description: "Autoskala mönster i Azure för Web Apps, skaluppsättningar för den virtuella datorn och molntjänster"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 9fa2b94b-dfa5-4106-96ff-74fd1fba4657
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: ancav
ms.openlocfilehash: df5059b5509ca4989369cf3bcba8cb89f1c25db4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-autoscale"></a>Bästa metoder för autoskalning
Den här artikeln lär du metodtips Autoskala i Azure. Azure övervakaren Autoskala gäller enbart för [Skalningsuppsättningar i virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [molntjänster](https://azure.microsoft.com/services/cloud-services/), och [App Service - Webbappar](https://azure.microsoft.com/services/app-service/web/). Andra Azure-tjänster använda olika metoder för skalning.

## <a name="autoscale-concepts"></a>Autoskala begrepp
* En resurs kan bara ha *en* autoskalningsinställning
* En autoskalningsinställning kan ha en eller flera profiler och varje profil kan ha en eller flera Autoskala regler.
* En autoskalningsinställning skalas instanser vågrätt, vilket är *ut* genom att öka instanserna och *i* genom att minska antalet instanser.
  En autoskalningsinställning har en högsta och lägsta standardvärdet instanser.
* Ett jobb Autoskala alltid associerade mått till skala genom, kontrollerar om den har passerat den konfigurerade tröskeln för skalbar eller skala i. Du kan visa en lista av mätvärden som autoskalning kan skala genom på [Azure-Monitor autoskalning vanliga mått](insights-autoscale-common-metrics.md).
* Alla tröskelvärden beräknas på en instansnivå. Till exempel ”skala ut med 1 instans när Genomsnittlig CPU > 80% när instansantalet är 2”, innebär att skalbar när Genomsnittlig CPU i alla instanser som är större än 80%.
* Du får alltid meddelanden via e-post. Mer specifikt får ägare, deltagare och läsare av målresursen e-post. Du får alltid en *recovery* e-post när Autoskala återställer från ett fel och startar fungerar normalt.
* Du kan anmäla sig för att ta emot en lyckad skala åtgärd avisering via e-post och webhooks.

## <a name="autoscale-best-practices"></a>Autoskala bästa praxis
Använd följande metodtips när du använder Autoskala.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Se till att de högsta och lägsta värdena är olika och ha en tillräcklig marginal mellan dem.
Om du har en inställning som har minst = 2, maximalt = 2 och aktuellt instanser är 2, ingen skalningsåtgärd kan uppstå. Behåll en tillräcklig marginal mellan antalet högsta och lägsta instanser, vilket är inklusiva. Autoskala skalas alltid mellan dessa gränser.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Manuell skalning återställs av Autoskala min och max
Om du manuellt uppdatera instansantalet till ett värde över eller under maximalt, skalas Autoskala motorn automatiskt tillbaka till lägsta (om nedan) eller högsta (om ovan). Exempelvis kan du ange intervallet mellan 3 och 6. Om du har en instans som körs kan skalas Autoskala motorn till 3 instanser på dess nästa körning. På samma sätt är det skulle skala in 8 instanser tillbaka till 6 på dess nästa körning.  Manuell skalning är mycket tillfällig om du återställer närmare Autoskala.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Använd alltid en skalbar och skala i regel kombination som utför en ökning och minskning
Om du använder endast en del av en kombination av har Autoskala skala in att enkel, eller i en, tills de högsta eller lägsta, nåtts.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Växla inte mellan Azure-portalen och den klassiska Azure-portalen när du hanterar Autoskala
Använd Azure-portalen (portal.azure.com) att skapa och hantera automatiska inställningar för molntjänster och Apptjänster (webbprogram). Använd PowerShell, CLI eller REST API för Skalningsuppsättningar i virtuella datorer att skapa och hantera autoskalningsinställning. Växla inte mellan den klassiska Azure-portalen (manage.windowsazure.com) och Azure portal (portal.azure.com) när du hanterar Autoskala konfigurationer. Den klassiska Azure-portalen och dess underliggande backend har begränsningar. Flytta till Azure-portalen för att hantera Autoskala med ett grafiskt användargränssnitt. Alternativen är att använda Autoskala PowerShell, CLI eller REST API (via resursutforskaren i Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Välj lämplig statistiken för diagnostik-mått
För diagnostik statistik, kan du välja mellan *genomsnittlig*, *minsta*, *maximala* och *totala* som ett mått för att skala med. De vanligaste statistiken är *genomsnittlig*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Välj tröskelvärdena noggrant för alla typer av mått
Vi rekommenderar att du noggrant välja olika tröskelvärden för skalbar och skala in baserat på praktiska situationer.

Vi *rekommenderar inte* Autoskala inställningar som i exemplen nedan med samma eller mycket lik tröskelvärdena för ut och villkor:

* Öka instanser av 1 räkna när antal trådar < = 600
* Minska instanser med 1 räkna när antal trådar > = 600

Nu ska vi titta på ett exempel på vad kan leda till ett problem som kan verka förvirrande. Överväg följande sekvens.

1. Anta att det finns 2 instanser börja med och sedan det genomsnittliga antalet trådar per instans växer till 625.
2. Autoskala skalas ut lägger till en 3-instans.
3. Därefter förutsätter att medelvärde för antalet trådar i instansen hamnar 575.
4. Innan du skala blir Autoskala försöker beräkna vilka sluttillstånd om den anpassas i. Till exempel 575 x 3 (aktuella standardinstansantalet) = 1,725 / 2 (sista antal instanser när skala) = 862.5 trådar. Det innebär att Autoskala måste omedelbart skalbar igen trots att det skalas, om medelvärde för antalet trådar är densamma eller även hamnar bara en liten del. Men om det skalas upp igen och hela processen skulle upprepa, vilket leder till en oändlig loop.
5. Om du vill undvika detta (som kallas ”flapping”), skalas Autoskala inte alls. I stället hoppar över och reevaluates villkoret nästa gång tjänstens jobbet körs. Detta kan förvirrande många eftersom Autoskala inte visas då fungerar när medelvärde för antalet trådar 575.

Beräkning av under en skala i är avsedd för att undvika ”växlar” situationer där skala i och skalbar åtgärder kontinuerligt gå fram och tillbaka. Behåll detta i åtanke när du väljer samma tröskelvärden för skalbar och.

Vi rekommenderar att du väljer en tillräcklig marginal mellan skalbara och tröskelvärden. Överväg följande bättre regeln kombination som ett exempel.

* Öka instanser av 1 räkna när CPU i % > = 80
* Minska instanser med 1 räkna när CPU i % < = 60

I det här fallet  

1. Anta att det finns 2 instanser ska börja med.
2. Om den genomsnittliga CPU i % över instanser går till mellan 80 kan skalas Autoskala lägga till en tredje instans.
3. Anta nu att CPU-% ligger över tid och 60.
4. Autoskalas skala i regeln beräknar sluttillstånd om det vore skala in. Till exempel 60 x 3 (aktuella standardinstansantalet) = 180 / 2 (sista antal instanser när skala) = 90. Så Autoskala inte skala in eftersom det skulle behöva skalbar igen omedelbart. I stället hoppas över och skala.
5. Nästa gång Autoskala kontrollerar Processorn fortsätter att växla till 50. Beräknar igen - 50 x 3, instans = 150 / 2 instanser = 75, vilket är under tröskelvärdet skalbar på 80, så att det skalas in korrekt till 2 instanser.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Överväganden för skalning tröskelvärden för särskilda mått
 För särskilda mätvärden, till exempel lagrings- och Service Bus-kö längd mått är tröskelvärdet det genomsnittliga antalet meddelanden som är tillgängliga per aktuellt antal instanser. Välj noga tröskelvärdet för det här måttet.

Vi illustrerar den med ett exempel för att se till att du förstår hur bättre.

* Öka instanser av 1 antal när lagring kön meddelandet antal > = 50
* Minska instanser av 1 antal när lagring kön meddelandet antal < = 10

Överväg följande ordning:

1. Det finns 2 lagring kön instanser.
2. Meddelanden fortfarande och när du har läst lagring kön, det totala antalet läser 50. Du kan anta att Autoskala ska starta en skalbar-åtgärd. Observera dock att det fortfarande är 50/2 = 25 meddelanden per instans. Därför uppstår inte skalbar. För det första skalbart ska hända, ska det totala antalet meddelanden i kön lagring 100.
3. Därefter förutsätter att det totala antalet meddelanden når 100.
4. En 3 lagring kön instans läggs till på grund av en skalbar-åtgärd.  Nästa åtgärd skalbar sker inte förrän det totala antalet meddelanden i kön når 150 eftersom 150/3 = 50.
5. Antal meddelanden i kön hämtar nu mindre. Den första skala-åtgärden med 3 instanser händer när Totalt antal meddelanden i alla köer lägga till upp till 30 eftersom 30/3 = 10 meddelanden per instans som är tröskelvärdet skala i.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Överväganden för skalning när flera profiler har konfigurerats i en autoskalningsinställning
Du kan välja en standardprofil som används alltid utan några beroende av schemat eller tid, i en autoskalningsinställning eller kan du välja en återkommande profil eller för en viss period med datum och tid.

När Autoskala tjänsten bearbetar dem, kontrollerar den alltid i följande ordning:

1. Fast datum-profil
2. Återkommande profil
3. Standardprofil (”Always”)

Om en profil är uppfyllt, kontrollerar inte Autoskala villkoret nästa profil under den. Autoskala bearbetar endast en profil i taget. Det innebär att om du vill också inkludera ett bearbetningsvillkor från en lägre nivå profil måste du inkludera regler samt i den aktuella profilen.

Nu ska vi se detta med hjälp av ett exempel:

På bilden nedan visas ett autoskalningsinställning med en standardprofil minsta instanser = 2 och högsta förekomster = 10. I det här exemplet regler är konfigurerade för att skala ut när antalet meddelanden i kön är större än 10 och skala in när antalet meddelanden i kön är mindre än 3. Resursen kan nu skala mellan 2 och 10 instanser.

Dessutom är en återkommande profil som angetts för måndag. Den värdet för minsta instanser = 2 och högsta förekomster = 12. Detta innebär måndag, första gången Autoskala söker efter det här villkoret om instansantalet är 2, den växer till den nya minst 3. Så länge Autoskala fortsätter att söka efter den här profilen villkor matchas (måndag), bara bearbetas processorbaserad scale-out/i reglerna som konfigurerats för den här profilen. Just nu är kontrollerar den inte om kölängden. Men om du även vill kön längd villkoret som ska kontrolleras bör du ta regler från standardprofilen samt i profilen för måndag.

På liknande sätt när Autoskala växlar tillbaka till standardprofil, kontrollerar först den om de lägsta och högsta villkor är uppfyllda. Om antalet instanser då 12 skalningen i till 10, det maximalt tillåtna för standardprofilen.

![Autoskala inställningar](./media/insights-autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Överväganden för skalning när flera regler är konfigurerade i en profil
Finns det fall där du kan behöva ange flera regler i en profil. Följande uppsättning Autoskala regler som används av tjänster använder när flera regler har angetts.

På *skala ut*, Autoskala körs om någon regel uppfylls.
På *skala i*, Autoskala kräver alla regler ska uppfyllas.

Anta att du har följande 4 automatiska regler för att illustrera:

* Om CPU < 30% skala i med 1
* Om minnet < 50% skala i med 1
* Om CPU > 75%, skala ut med 1
* Om minnet > 75%, skala ut med 1

Följande inträffar:

* Om Processorn är 76% och minne är 50%, vi skala ut.
* Om Processorn är 50% minne är 76% vi skala ut.

Å andra sidan, om är 25 procent för processor och minne är % av 51 Autoskala har **inte** skala i. För att skala i, CPU måste vara 29% och minne 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Välj alltid en säker standardinstansantalet
Standard-instanser är viktigt Autoskala skalas din tjänst till att antalet när mått inte är tillgängliga. Därför ska du välja en standard-instanser som är acceptabel för dina arbetsbelastningar.

### <a name="configure-autoscale-notifications"></a>Konfigurera meddelanden om autoskalning
Autoskala meddelar administratörer och deltagare i resursen via e-post om något av följande villkor inträffar:

* Autoskala tjänsten kan inte vidta en åtgärd.
* Mått är inte tillgängligt för Autoskala tjänst kan besluta skala.
* Mått är tillgängliga (återställning) igen för att fatta ett beslut skala.
  Du kan konfigurera e-post eller webhook-meddelanden till Håll dig informerad för lyckade skalningsåtgärder förutom villkoren ovan.
  
Du kan också använda en avisering i aktivitetsloggen för att övervaka hälsotillståndet hos Autoskala-motorn. Här följer exempel på [skapa en aktivitet loggen aviseringen om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) eller [skapa en aktivitet loggen aviseringen om du vill övervaka alla misslyckade Autoskala skala / skala ut åtgärder på din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

## <a name="next-steps"></a>Nästa steg
- [Skapa en aktivitet loggen aviseringen om du vill övervaka alla Autoskala motorn åtgärder för din prenumeration.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Skapa en aktivitet loggen aviseringen om du vill övervaka alla misslyckade Autoskala skala / skala ut åtgärder för din prenumeration](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
