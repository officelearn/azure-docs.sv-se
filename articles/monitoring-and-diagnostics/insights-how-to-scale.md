---
title: Skala instansantalet manuellt eller med Autoskala med Azure Portal | Microsoft Docs
description: "Lär dig mer om att skala dina Azure-tjänster."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2397596a-071f-4d49-8893-bec5f735bd7b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: ancav
ms.openlocfilehash: d171538ea57839eccddcc74ca099a39aee34ea10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="scale-instance-count-manually-or-automatically"></a>Skala instansantalet manuellt eller automatiskt
I den [Azure Portal](https://portal.azure.com/), du kan ange instansantal av tjänsten manuellt eller, du kan ange parametrar för att det automatiskt skala baserat på begäran. Detta är vanligtvis kallas *skala ut* eller *skala i*.

Innan skalning baserat på instansantal, bör du tänka skalning påverkas av **prisnivå** förutom instansantal. Olika prisnivåer kan ha olika antal kärnor och minne, och så de får bättre prestanda för samma antal instanser (vilket är *skala upp* eller *skala*). Den här artikeln innehåller *skala i* och *ut*.

Du kan skala i portalen och du kan också använda den [REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx) eller [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor) att justera skalan manuellt eller automatiskt.

> [!NOTE]
> Den här artikeln beskriver hur du skapar en autoskalningsinställning i portalen på [http://portal.azure.com](http://portal.azure.com). Autoskala inställningar skapas i den här portalen måste redigeras den klassiska portalen ([http://manage.windowsazure.com](http://manage.windowsazure.com)).
> 
> 

## <a name="scaling-manually"></a>Skalning manuellt
1. I den [Azure Portal](https://portal.azure.com/), klickar du på **Bläddra**, gå sedan till den resurs som du vill skala, till exempel en **programtjänstplanen**.
2. Klicka på **Inställningar > skala ut (apptjänstplan).**
3. Längst upp i den **skala** bladet som du kan se en historik över Autoskala åtgärder i tjänsten.
   
    ![Skala bladet](./media/insights-how-to-scale/Insights_ScaleBladeDayZero.png)
   
   > [!NOTE]
   > Åtgärder som utförs av Autoskala kommer att visas i det här diagrammet. Om du justerar instansantalet manuellt, visas inte ändringen i det här diagrammet.
   > 
   > 
4. Du kan justera antalet **instanser** med skjutreglaget.
5. Klicka på den **spara** kommandot och du kommer skalas till det antalet instanser för nästan omedelbart.

## <a name="scaling-based-on-a-pre-set-metric"></a>Skalning utifrån en förinställd mått
Om du vill antalet instanser ska justeras automatiskt baserat på ett mått, väljer du det mått som du vill ha i den **skala genom** listrutan. Till exempel en **programtjänstplanen** du kan skala genom **CPU-procent**.

1. När du väljer ett mått får du ett skjutreglage och textrutor för att ange antalet instanser som du vill skala mellan:
   
    ![Skala bladet med CPU-procent](./media/insights-how-to-scale/Insights_ScaleBladeCPU.png)
   
    Autoskala tar aldrig din tjänst under eller över de gränser som du anger, oavsett dina belastningen.
2. Andra kan välja du målområdet för måttet. Om du har valt exempelvis **CPU-procent**, du kan ange ett mål för Genomsnittlig CPU för alla instanser i din tjänst. En skalbar sker när Genomsnittlig CPU överskrider den maximala som du definierar, på samma sätt kan en skala i sker när Genomsnittlig CPU sjunker under minst.
3. Klicka på den **spara** kommando. Autoskala kontrollerar varje några minuter för att se till att du är i instansen omfång och mål för dina mått. När tjänsten tar emot ytterligare trafik, får du fler instanser utan att göra något.

## <a name="scale-based-on-other-metrics"></a>Skala baserat på andra mått
Du kan skala baserat på mått än förinställningar som visas i den **skala genom** listrutan och kan även ha en komplex uppsättning skala ut och skala i regler.

### <a name="adding-or-changing-a-rule"></a>Lägga till eller ändra en regel
1. Välj den **schema och prestanda regler** i den **skala genom** listrutan: ![Prestandaregler](./media/insights-how-to-scale/Insights_PerformanceRules.png)
2. Om du tidigare hade Autoskala visas på en vy över de exakta reglerna som du hade.
3. Skala baserat på ett mått klick i **Lägg till regel** rad. Du kan också klicka på något av de befintliga rader att byta från det mått som du tidigare tvungen att mått som du vill skala genom.
   ![Lägg till regel](./media/insights-how-to-scale/Insights_AddRule.png)
4. Nu måste du välja vilka mått som du vill skala med. När du väljer ett mått som det finns några saker att tänka på:
   
   * Den *resurs* måttet kommer från. Vanligtvis det här är samma som den resurs du skalning. Om du vill skala av djupet för en kö för lagring men är den kö som du vill skala av resursen.
   * Den *måttnamnet* sig själv.
   * Den *tid aggregering* av måttet. Detta är hur data är kombinera över den *varaktighet*.
5. När du har valt dina mått du tröskelvärdet för måttet och operatorn. Anta exempelvis att du kunde **större än** **80%**.
6. Välj den åtgärd som du vill utföra. Det finns ett par olika typer av åtgärder:
   
   * Öka eller minska med – det här lägga till eller ta bort den **värdet** antal instanser som du definierar
   * Öka eller minska procent - instansantalet ändras med en procent. Du kan till exempel placera 25 i den **värdet** fält, och om du har för närvarande 8 instanser, 2 skulle läggas till.
   * Öka eller minska till – detta anger instansantalet till den **värdet** du definierar.
7. Slutligen kan du välja lågfrekvent ned - hur länge den här regeln ska vänta efter den föregående skalningsåtgärden skala igen.
8. När du har konfigurerat din regel nådde **OK**.
9. När du har konfigurerat alla regler som du vill, var noga med att träffa den **spara** kommando.

### <a name="scaling-with-multiple-steps"></a>Skalning med flera steg
Exemplen ovan är relativt enkla. Men om du vill ha mer aggressivt om att skala upp (eller ned), du kan även lägga till flera skala regler för samma mått. Du kan till exempel definiera två skalningsregler på CPU-procent:

1. Skala ut med 1 instans om CPU-procent är över 60%
2. Skala ut 3 instanser om CPU-procent är över 85%

![Flera skalningsregler](./media/insights-how-to-scale/Insights_MultipleScaleRules.png)

Med regeln ytterligare om din belastningen överskrider 85% innan en skalningsåtgärd visas två ytterligare instanser i stället för en.

## <a name="scale-based-on-a-schedule"></a>Skala enligt ett schema
Som standard när du skapar en regel för skala gäller alltid. Du kan se det när du klickar på rubriken profil:

![Profil](./media/insights-how-to-scale/Insights_Profile.png)

Du kanske vill ha mer aggressivt skalning under dagen och veckan än på det är helgen. Du kan även stänga av tjänsten helt av arbetstid.

1. Gör på den profil som du har, Välj **återkommande** i stället för **alltid,** och välj de tider som du vill tillämpa profilen.
2. Till exempel har en profil som gäller under vecka, i den **dagar** listrutan avmarkera **lördag** och **söndag**.
3. Om du vill att en profil som används under daytime, ange den **starttid** mot den tid på dagen som du vill börja med.
   
    ![Standard upprepning](./media/insights-how-to-scale/Insights_ProfileRecurrence.png)
4. Klicka på **OK**.
5. Därefter behöver du lägga till profilen som du vill använda andra tider. Klicka på den **lägga till profilen** rad.
    ![Inaktivera arbete](./media/insights-how-to-scale/Insights_ProfileOffWork.png)
6. Namnge din nya sekund, profil, du kan till exempel kalla den **av arbete**.
7. Välj sedan **återkommande** igen och välj instans antal sidintervallet under denna tid.
8. Som med standardprofilen, Välj den **dagar** du vill att profilen ska tillämpas på, och **starttid** under dagen.
   
   > [!NOTE]
   > Autoskala används av reglerna för sommartid för **tidszon** du väljer. Dock under sommartid UTC-förskjutningen visas den grundläggande Tidszonförskjutning, inte sommartid besparingar UTC-förskjutningen.
   > 
   > 
9. Klicka på **OK**.
10. Nu kan behöver du lägga till de regler som du vill använda under din andra profil. Klicka på **Lägg till regel**, och sedan kan du skapa samma regel som du har under standardprofilen.
    
    ![Lägg till regel för att inaktivera arbete](./media/insights-how-to-scale/Insights_RuleOffWork.png)
11. Se till att skapa både en regel för att skala ut och skala i eller under profilen instansantalet kommer bara öka (eller minska).
12. Klicka slutligen på **spara**.

## <a name="next-steps"></a>Nästa steg
* [Övervaka tjänsten](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.
* [Aktivera övervakning och diagnostik](insights-how-to-use-diagnostics.md) att samla in detaljerade hög frekvens mått på din tjänst.
* [Få aviseringar](insights-receive-alert-notifications.md) när drifthändelser inträffar eller när mätvärden överskrider ett tröskelvärde.
* [Övervaka programprestanda](../application-insights/app-insights-azure-web-apps.md) om du vill förstå exakt hur din kod genomför i molnet.
* [Visa händelser och aktivitetsloggen](insights-debugging-with-events.md) vill veta allt som har skett i din tjänst.
* [Övervaka tillgänglighet och svarstider på en webbsida](../application-insights/app-insights-monitor-web-app-availability.md) med Application Insights så att du kan läsa mer om sidan inte är tillgänglig.

