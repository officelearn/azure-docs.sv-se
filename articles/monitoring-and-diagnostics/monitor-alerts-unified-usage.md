---
title: Skapa, visa och hantera aviseringar med Azure Monitor
description: Använd den nya upplevelsen för enhetlig Azure-aviseringar för att skapa, visa och hantera mått och logga Varningsregler från ett och samma ställe.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a913075c051c6b784495917b7edbd7340254a212
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952638"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Skapa, visa och hantera aviseringar med Azure Monitor  

## <a name="overview"></a>Översikt
Den här artikeln visar hur du ställer in aviseringar med hjälp av det nya gränssnittet för aviseringar i Azure-portalen. Definitionen för en varningsregel är i tre delar:
- Mål: Specifika Azure-resursen, vilket är som ska övervakas
- Villkor: Specifika villkor eller logik som när synts signalen ska utlösa åtgärd
- Åtgärd: Specifika anrop som skickats till en mottagare av ett meddelande - e-post, SMS, webhook osv.

Azure-aviseringar innehåller också en enhetlig vy för alla aviseringsregler och möjlighet att hantera dem en och samma plats; inklusive alla olösta aviseringar. Läs mer om funktioner från [Azure aviseringar – översikt över](monitoring-overview-unified-alerts.md).

Aviseringen använder termen **Loggaviseringar** att beskriva aviseringar där signalen är anpassad fråga baserat på [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Application Insights](../application-insights/app-insights-analytics.md). Den [nya metrisk varning funktionen](monitoring-near-real-time-metric-alerts.md) ger möjlighet att aviseringen på [flerdimensionella mått](monitoring-metric-charts.md) för specifika Azure-resurser. Aviseringar för sådana resurs kan använda ytterligare filter på dimensioner som skapar **Multi-Dimensional mått aviseringar**.


> [!NOTE]
> Erbjuder en ny och förbättrad upplevelse för att skapa aviseringar i Azure samtidigt som Azure-aviseringar. Den befintliga [aviseringar (klassisk)](monitoring-overview-alerts.md) upplevelse förblir användbart.
>

Detaljerad nästa är stegvis guide till med hjälp av Azure-aviseringar.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Skapa en aviseringsregel med Azure Portal
1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  
    ![Övervakning](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Välj den **ny Aviseringsregel** för att skapa en ny avisering i Azure.
    ![Lägg till avisering](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. Skapa avisering avsnittet visas med tre delar som består av: *definiera aviseringsvillkoret*, *definiera Aviseringsinformationen*, och *definiera åtgärdsgruppen*.

    ![Skapa regel](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definiera aviseringsvillkoren genom att använda den **Välj Resource** länk och ange mål, genom att välja en resurs. Filtret genom att välja den * prenumeration, * resurstyp och slutligen välja krävs *Resource*.

    >[!NOTE]

    > Kontrollera signaler som är tillgängliga för den valda resursen innan du fortsätter.

    ![Välj resurs](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Användargränssnittet kan du skapa olika typer av aviseringar från ett och samma ställe. Beroende på vilken typ av avisering som önskas väljer nästa steg som:

    - För **mått aviseringar**: Följ steg 5 till 7; gå direkt till steg 11
    - För **Loggaviseringar**, går du vidare till steg 8.

    > [!NOTE]

    > Aktivitetsloggaviseringar stöds också, men är i förhandsversion. [Läs mer](monitoring-activity-log-alerts-new-experience.md).

5. *Måttaviseringar*: se till att **resurstyp** är markerad med signaltyp som **mått**, en gång sedan lämplig **resource** är valt klickar du på  *Klar* vill gå tillbaka till skapa aviseringar. Därefter använder den **lägga till villkor** för att välja specifika signalen listan över signalen alternativ, övervakningstjänsten och typ som visas – som är tillgängliga för resursen valt tidigare.

    ![Välj en resurs](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Alla [nära realtidsaviseringar](monitoring-near-real-time-metric-alerts.md) kan resurser visas med monitor-tjänsten **plattform** och skicka en signal typ som **mått**

6. *Måttaviseringar*: när signalen väljs, logik för avisering kan anges. Referens historiska data för signalen visas där du kan justera den tid med **visa tidigare**, varierande från senaste sex timmar att förra veckan. Med visualiseringen på plats, **Alert Logic** kan väljas från visas alternativen för villkor, sammanställning och slutligen tröskelvärdet. Som förhandsversion av den logik som tillhandahålls, visas villkoret i visualiseringen tillsammans med signalen historik att indikera när aviseringen skulle har utlösts. Slutligen ange för vilka varaktighet, avisering bör se ut för det angivna villkoret genom att välja den **Period** alternativet tillsammans med hur ofta avisering ska köras genom att välja **frekvens**.

    ![Konfigurera signallogiken för mått](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Måttaviseringar*: om signalen är ett mått och aviseringar fönster kan filtreras med flera datapunkter eller dimensioner för dessa Azure-resursen. 

    a. Välj en varaktighet från den **visa tidigare** listrutan för att visualisera en annan tidsperiod. Du kan välja dimensioner för måtten som stöds att filtrera på en tidsserie; välja dimensioner är valfri och upp till fem dimensioner kan användas. 

    b. **Avisera Logic** kan väljas från visas alternativen för *villkor*, * aggregering, och *tröskelvärdet*. Som förhandsversion av den logik som tillhandahålls, visas villkoret i visualiseringen tillsammans med signalen historik att informera om att aviseringen skulle ha aktiverats tidigare. 

    c. Om du vill ange hur länge, Välj **Period** tillsammans med hur ofta en avisering ska köras genom att välja **frekvens**.

    ![Konfigurera signallogiken för flerdimensionella mått](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Loggaviseringar*: se till att **resurstyp** är en analytics-källa som *Log Analytics* eller *Programinsikter* och skicka en signal typ som **Log** , en gång sedan lämplig **resource** är valt, klickar du på *klar*. Sedan använda den **lägga till villkor** knappen för att visa en lista över signalen alternativ som är tillgängliga för resursen och från listan över signal **anpassade loggsökning** alternativ för den valda logga övervakningstjänst som *Log Analytics* eller *Programinsikter*.

   ![Välj en resurs - anpassade loggsökning](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Aviseringar listor importera analytics-fråga som signaltyp - **Log (sparad fråga)** enligt ovan bild. Så att användarna kan förbättrar din fråga i Analytics och spara dem för framtida användning i aviseringar – mer information om hur du använder för att spara frågan som är tillgängliga på [med hjälp av loggsökning i log analytics](../log-analytics/log-analytics-log-searches.md) eller [delade fråga i application insights Analytics](../log-analytics/log-analytics-overview.md). 

9.  *Loggaviseringar*: när du valt, fråga aviseringar kan anges i **sökfråga** fältet; om frågesyntaxen är felaktigt fältet visar fel i rött. Om frågesyntaxen är korrekt - referens visas historiska data för den angivna frågan som ett diagram med alternativet för att justera tidsfönster från senaste sex timmar att förra veckan.

 ![Konfigurera varningsregeln](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Historiska datavisualisering kan endast visas om resultatet av frågan har information om tid. Om din fråga resulterar i sammanfattade data eller specifika kolumnvärdena - visas samma som ett enda diagram.

    >  För måttet måttenhet för loggvarningar med Application insights kan du ange vilken specifik variabel för att gruppera data med hjälp av den **sammanställda på** alternativet; enligt nedan:

    ![Aggregera på alternativ](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Loggaviseringar*: med visualisering på plats, **Alert Logic** kan väljas från visas alternativen för villkor, sammanställning och slutligen tröskelvärdet. Ange sedan i logik, tiden för att utvärdera för det angivna villkoret med **Period** alternativet. Tillsammans med hur ofta avisering ska köras genom att välja **frekvens**.
För **Loggaviseringar** aviseringar kan baseras på:
   - *Antalet poster*: en avisering skapas om antalet poster som returneras av frågan är större än eller mindre än det angivna värdet.
   - *Metrisk måttenhet*: en avisering skapas om varje *aggregera värdet* överskrider tröskelvärdet som angetts i resultatet och det är *grupperade efter* valt värde. Antalet överträdelser för en avisering är antalet gånger som tröskelvärdet överstigs under den valda tidsperioden. Du kan ange Totalt antal överträdelser för valfri kombination av intrång i resultatuppsättningen eller efterföljande överträdelser att kräva att överträdelserna måste ske i efterföljande prover. Läs mer om [Loggaviseringar och deras typer](monitor-alerts-unified-log.md).

    > [!TIP]
    > För närvarande i aviseringar – loggsökningsaviseringar ta anpassade *period* och *frekvens* värde i minuter. Värden kan skilja sig från 5 minuter till 1440 minuter (det vill säga) 24 timmar. Så om du vill att aviseringen perioden ska anta tre timmar, konvertera den till minuter – 180 minuter före användning

11. Som det andra steget definierar du ett namn för aviseringen i den **varningsregelns namn** fältet tillsammans med en **beskrivning** med informationen för för aviseringen och **allvarlighetsgrad** värdet från den alternativen. Dessa uppgifter återanvänds i alla aviseringsmeddelanden, meddelanden eller push som görs av Azure Monitor. Dessutom kan användaren välja att aktivera varningsregeln skapats omedelbart genom att klicka på rätt sätt **aktivera regeln vid skapande** alternativet.

    För **Loggaviseringar** endast vissa ytterligare funktioner finns i varningsinformationen:

    - **Visa inga aviseringar**: när du aktiverar Undertryckning för regeln, åtgärder för regeln är inaktiverade för en definierad tidsperiod när du har skapat en ny avisering. Regeln körs fortfarande och skapar aviseringsposter förutsatt att följande villkor är uppfyllda. Så att du har tid att åtgärda problemet utan att köra duplicerade åtgärder.

        ![Visa inga aviseringar för Loggaviseringar](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Ange en Ignorera varning värde större än frekvensen för aviseringen för att se till att meddelanden stoppas utan överlappningar

12. Som det tredje och sista steget, anger eventuella **åtgärdsgrupp** måste aktiveras för regeln när varningsvillkor är uppfyllt. Du kan välja en befintlig åtgärdsgrupp med avisering eller skapa en ny åtgärdsgrupp. Enligt valde åtgärdsgrupp när aviseringen är utlösaren Azure kommer: skicka email(s), skicka SMS(s), anropa webhooks, åtgärda problemet med hjälp av Azure-Runbooks, push till ditt ITSM-verktyg, osv. Läs mer om [åtgärdsgrupper](monitoring-action-groups.md).

    För **Loggaviseringar** vissa ytterligare funktioner är tillgänglig för att åsidosätta standardåtgärder:

    - **E-postavisering**: åsidosätter *e-postämne* i e-post, skickas via åtgärdsgrupp; om en eller flera e poståtgärder finns i dessa åtgärdsgruppen. Du kan inte ändra innehållet i e-postmeddelandet och det här fältet är **inte** för e-postadress.
    - **Inkludera anpassad Json-nyttolast**: åsidosätter webhooken JSON som används av åtgärdsgrupper; om en eller flera webhook-åtgärder finns i dessa åtgärdsgruppen. Användaren kan ange formatet för JSON som ska användas för alla webhookar som konfigurerats i åtgärdsgruppen associerade; Mer information om webhook-format finns i [webhook-åtgärd för Loggaviseringar](monitor-alerts-unified-log-webhook.md). Testa Webhook-alternativet har angetts för att kontrollera format och bearbetning av mål med hjälp av exempel-JSON och det här alternativet som är märkt avsedd endast för **testning** syften.

        ![Åtgärden åsidosättningar för Loggaviseringar](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > För **testa Webhook** ska fungera måste du ha stöd för slutpunkten [Cross Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) och användarens kan använda CORS-proxy för att undvika problem med ”ingen Access-Control-Allow-Origin-rubrik”

13. Om alla fält är giltiga och med grön skalstreck den **skapa aviseringsregel** Klicka på knappen och en avisering skapas i Azure Monitor - aviseringar. Alla aviseringar kan visas från aviseringarna instrumentpanelen.

    ![Skapa en regel](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Inom några minuter, aviseringen är aktiv och utlöser som det beskrivits.

## <a name="view-your-alerts-in-azure-portal"></a>Visa dina aviseringar i Azure Portal

1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  

2. Den **aviseringar instrumentpanelen** visas - där alla Azure-aviseringar unified och visas i en enda panel ![Aviseringsinstrumentpanel](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Från övre vänster till höger visar instrumentpanelen på ett ögonblick, följande -, kan du klicka på att se en detaljerad lista:
    - *Utlösta aviseringar*: antalet aviseringar som för närvarande som har uppfyllts logik och i utlösta tillstånd
    - *Totalt antal Aviseringsregler*: antal aviseringsregler skapade och i underordnad text, tal som är aktiverade 
    

        > [!NOTE]
        > Att säkerställa konsekvent instrumentpanel med information om alla aktiverade aviseringar, inklusive aviseringar för application insights och log analytics; [Utökad enhetlig avisering (förhandsgranskning)](monitoring-overview-unified-alerts.md#enhanced-unified-alerts-public-preview) ska användas
  
  
4. En lista över alla aktiverade aviseringar visas som användaren kan välja för att visa detaljer
5. Hjälp i att hitta specifika aviseringar; alternativ för listrutan överst kan använda för att filtrera specifika *prenumeration, resursgrupp och/eller*. Ytterligare för någon olöst Varna ett sätt att använda den *Filter avisering* alternativet för att hitta för angivna nyckelordet - specifika matchande aviseringar med *namn, avisering kriterier, resursgrupp, och målresurs*

## <a name="managing-your-alerts-in-azure-portal"></a>Hantera dina aviseringar i Azure Portal
1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  
2. Välj den **hantera regler** knappen i det översta fältet att navigera till regeln hantering – där alla aviseringsregler skapade räknas, inklusive aviseringar som har inaktiverats.
3. För att hitta för specifika Varningsregler, kan en antingen använda filtren listrutan överst, vilket ger möjlighet att shortlist Varningsregler för specifika *prenumeration, resursgrupp och/eller resurs*. Alternativt på med hjälp av sökfunktionen fönstret ovanför listan varningsregel markerats *filtrera aviseringar*, en kan ge ett nyckelord som matchas mot *avisering namn, villkor och målresursen*; så att endast regler för matchning.
   ![Hantera regler för avisering](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Om du vill visa eller ändra specifika varningsregel, klickar du på dess namn som skulle visas som en klickbar länk.
5. Avisering som har definierats visas - i tre steg struktur: 1) avisering villkor 2) avisering detalj 3) åtgärdsgruppen. **Rikta kriterier** kan du klicka på om du vill ändra aviseringslogiken eller en ny kriterier kan läggas till när du har använt bin-ikonen för att ta bort den tidigare logiken. På samma sätt i varningsinformationen avsnittet - **beskrivning** och **allvarlighetsgrad** kan ändras. Och åtgärdsgruppen kan ändras eller en ny kan vara utformad att länka till en avisering med hjälp av den **ny åtgärdsgrupp** knappen.

   ![Ändra varningsregel](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Med hjälp av den övre panelen, ändringar av aviseringen kan vara reflekterande inklusive: **spara** utför eventuella ändringar som görs till aviseringen **Ignorera** återgå utan att spara ändringar som görs till aviseringen **inaktivera**  att inaktivera avisering - efter vilket den inte längre körs eller utlöser en åtgärd. Och slutligen **ta bort** permanent ta bort hela varningsregeln från Azure.

## <a name="next-steps"></a>Nästa steg

- Mer information om den nya [nästan i realtid måttaviseringar](monitoring-near-real-time-metric-alerts.md)
- Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarar.
- Lär dig mer om [Loggaviseringar i Azure-aviseringar](monitor-alerts-unified-log.md)
- [Lär dig mer om aviseringar för aktivitetsloggar i aviseringar (förhandsversion)](monitoring-activity-log-alerts-new-experience.md)
