---
title: Skapa, visa och hantera aviseringar med hjälp av Azure-Monitor
description: Använd den nya miljön enhetlig Azure aviseringar skapa, visa och hantera mått och logga in Varningsregler från en plats.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/05/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 51912bab0a038e99ecf77b8012c4087b029d4508
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264348"
---
# <a name="create-view-and-manage-alerts-using-azure-monitor"></a>Skapa, visa och hantera aviseringar via Azure-Monitor  

## <a name="overview"></a>Översikt
Den här artikeln visar hur du konfigurerar aviseringar via gränssnittet nya aviseringar i Azure-portalen. Definitionen av en aviseringsregel är i tre delar:
- Mål: Specifika Azure-resurs, som ska övervakas
- Villkor: Specifika villkor eller logik som när visas signal ska utlösa åtgärd
- Åtgärd: Specifika anrop skickas till en mottagare av ett meddelande - e-post, SMS, webhook osv.

Azure-aviseringar innehåller också en samlad bild Varningsregler och möjligheten att hantera dem med en enda plats; inklusive visa alla olösta aviseringar. Mer information om funktioner från [Azure varningar – översikt över](monitoring-overview-unified-alerts.md).

Aviseringen använder termen **loggen aviseringar** att beskriva aviseringar där signalen är en anpassad fråga baserat på [logganalys](../log-analytics/log-analytics-tutorial-viewdata.md) eller [Programinsikter](../application-insights/app-insights-analytics.md). Den [nya mått avisering funktionen](monitoring-near-real-time-metric-alerts.md) ger möjlighet att aviseringen på [flerdimensionellt mått](monitoring-metric-charts.md) för specifika Azure-resurser. Aviseringar för denna resurs kan använda ytterligare filter för att skapa dimensioner **Multi-Dimensional mått aviseringar**.


> [!NOTE]
> När aviseringar som Azure erbjuder en ny och förbättrad upplevelse för att skapa aviseringar i Azure. Den befintliga [aviseringar (klassisk)](monitoring-overview-alerts.md) upplevelse förblir användbart.
>

Detaljerad nästa finns stegvisa instruktioner för att använda Azure-aviseringar.

## <a name="create-an-alert-rule-with-the-azure-portal"></a>Skapa en aviseringsregel med Azure Portal
1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  
    ![Övervakning](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

2. Välj den **ny Aviseringsregel** för att skapa en ny avisering i Azure.
    ![Lägg till avisering](./media/monitor-alerts-unified/AlertsPreviewOption.png)

3. Avsnittet Skapa avisering visas med som består av tre delar: *definiera aviseringstillståndet*, *definiera aviseringsinformation*, och *definiera grupp*.

    ![Skapa regel](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

4.  Definiera villkoret för aviseringen genom att använda den **Välj resurser** länk och ange mål, genom att välja en resurs. Filter genom att välja den * prenumeration, * resurstyp och slutligen välja krävs *resurs*.

    >[!NOTE]

    > Kontrollera signaler som är tillgängliga för den markerade resursen innan du fortsätter.

    ![Välj resurs](./media/monitor-alerts-unified/Alert-SelectResource.png)

 Användargränssnittet kan du skapa olika typer av aviseringar från en plats. Beroende på vilken typ av avisering som önskas väljer nästa steg som:

    - För **mått aviseringar**: Följ steg 5 till 7; gå direkt till steg 11
    - För **loggen aviseringar**, hoppa till steg 8.

    > [!NOTE]

    > Aktiviteten loggen aviseringar stöds också, men finns i förhandsgranskningen. [Läs mer](monitoring-activity-log-alerts-new-experience.md).

5. * Mått aviseringar: Kontrollera **resurstypen** är markerad med signaltyp som **mått**, en gång sedan lämplig **resurs** är valt klickar du på *klar*vill gå tillbaka till skapa aviseringen. Därefter använder den **lägga till villkor** för att välja specifika signalen från lista över signal alternativ, övervakningstjänsten och typen som anges - som är tillgängliga för den tidigare markerade resursen.

    ![Välj en resurs](./media/monitor-alerts-unified/AlertsPreviewResourceSelection.png)

    > [!NOTE]

    >  Alla [nära realtid aviseringar](monitoring-near-real-time-metric-alerts.md) resurser som kan visas med övervakningstjänsten **plattform** och skicka en signal typ som **mått**

6. *Mått aviseringar*: när signalen är markerad logik för aviseringar kan anges. För referens anger historiska data för signal visas med alternativet för att justera tid fönster med **visa tidigare**, olika senaste sex timmar till föregående vecka. Med visualiseringen på plats, **avisering logik** kan väljas från visas alternativ för villkor, sammanställning och slutligen tröskelvärdet. Som förhandsgranskning av logiken som visas villkoret i visualiseringen tillsammans med signal historik att indikera när aviseringen skulle ha utlösts. Slutligen anger för vilken varaktighet, aviseringen ska söka efter de angivna villkoren genom att välja den **Period** alternativet tillsammans med hur ofta aviseringen ska köras genom att välja **frekvens**.

    ![Konfigurera signal logik för måttet](./media/monitor-alerts-unified/AlertsPreviewCriteria.png)

7. *Mått aviseringar*: om signalen är ett mått och sedan aviseringen fönster kan filtreras med flera datapunkter eller dimensioner för det Azure-resursen. 

    a. Välj en varaktighet från den **visa tidigare** listrutan visualisera en annan tidsperiod. Du kan välja dimensioner för mätvärdena som stöds att filtrera på tidsserier; välja dimensioner är valfri och upp till fem dimensioner kan användas. 

    b. **Varna logik** kan väljas från visas alternativ för *villkoret*, * aggregering, och *tröskelvärdet*. Som förhandsgranskning av logik som visas villkoret i visualiseringen tillsammans med signal historik att indikera när aviseringen skulle ha utlösts tidigare. 

    c. Om du vill ange hur länge väljer **Period** tillsammans med hur ofta aviseringen ska köras genom att välja **frekvens**.

    ![Konfigurera signal logik för flerdimensionella mått](./media/monitor-alerts-unified/AlertsPreviewCriteriaMultiDim.png)

8. *Logga varningar*: Kontrollera **resurstypen** är en analytics-källa som *logganalys* eller *Programinsikter* och skicka en signal typ som **logg** , en gång sedan lämplig **resurs** är valt, klickar du på *klar*. Därefter använda den **lägga till villkor** knappen för att visa en lista över signal alternativen för resursen och från listan över signal **anpassad logg sökning** alternativ för vald logga monitor-tjänsten som *logg Analytics* eller *Programinsikter*.

   ![Välj en resurs - sökningen anpassad logg](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Aviseringar visar kan importera analytics-fråga som signaltyp - **logg (sparad fråga)** enligt ovan bild. Så att användare kan perfekt frågan i Analytics och spara dem för framtida användning i varningar – mer information om hur du använder sparar frågan finns på [med loggen Sök i logganalys](../log-analytics/log-analytics-log-searches.md) eller [delade frågan i application insights Analytics](../log-analytics/log-analytics-overview.md). 

9.  *Logga varningar*: när du valt frågan för aviseringar kan anges i **sökfråga** fältet; om frågesyntaxen är felaktigt fältet visar fel i rött. Om frågesyntaxen är korrekt - referens visas historiska data för den angivna frågan som ett diagram med alternativet för att justera tidsfönstret senaste sex timmar till föregående vecka.

 ![Konfigurera varningsregel](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Visualisering av historiska data kan bara visas om resultatet av frågan har tidsinformation. Om din fråga resulterar i sammanfattningsdata eller specifika kolumnvärdena - visas samma som en enda rityta.

    >  För måttet mätning typ av logg påminnelser med hjälp av Application insights, kan du ange vilken specifik variabel för att gruppera data med hjälp av den **sammanställd på** alternativ, enligt nedan:

    ![Aggregera på alternativ](./media/monitor-alerts-unified/aggregate-on.png)

10.  *Logga varningar*: med visualiseringen på plats, **avisering logik** kan väljas från visas alternativ för villkor, sammanställning och slutligen tröskelvärdet. Ange slutligen i logiken, tid att utvärdera de angivna villkoren med hjälp av **Period** alternativet. Tillsammans med hur ofta aviseringen ska köras genom att välja **frekvens**.
För **loggen aviseringar** aviseringar kan baseras på:
   - *Antalet poster*: en avisering skapas om antalet poster som returneras av frågan är större än eller mindre än angivet värde.
   - *Mått mätning*: en avisering skapas om varje *aggregera värdet* överskrider tröskelvärdet som angetts i resultaten och det är *grupperade efter* valt värde. Antalet överträdelser för en avisering är antalet gånger som tröskelvärdet har överskridits i den valda tidsperioden. Du kan ange totala överträdelser för valfri kombination av överträdelser över resultatmängd eller på varandra följande överträdelser att kräva att överträdelserna måste ske i beräkningar. Lär dig mer om [loggen aviseringar och deras typer](monitor-alerts-unified-log.md).

    > [!TIP]
    > För närvarande i aviseringar - loggen sökaviseringar kan ta anpassade *period* och *frekvens* värde i minuter. Värdena kan variera mellan 5 minuter till 1440 minuter (det vill säga) 24 timmar. Så om du vill att aviseringen period ska säga tre timmar, konvertera den till minuter – 180 minuter före användning

11. Som det andra steget definierar du ett namn för aviseringen i den **varningsregelns namn** fältet tillsammans med en **beskrivning** med information om programvarukrav för aviseringen och **allvarlighetsgrad** värdet från den alternativen. Dessa uppgifter återanvänds i alla aviseringar, meddelanden eller push som utförs av Azure-Monitor. Dessutom kan användaren välja att aktivera varningsregeln skapar omedelbart genom att klicka på lämpligt sätt **Aktivera regel när du skapar** alternativet.

    För **loggen aviseringar** endast viss ytterligare funktionalitet är tillgänglig i aviseringsinformation:

    - **Ignorera aviseringar**: när du aktiverar Undertryckning för varningsregeln åtgärder för regeln är inaktiverade för en angiven tidsperiod när du har skapat en ny avisering. Regeln är fortfarande körs och skapar avisering poster under förutsättning att följande villkor är uppfyllda. Vilket gör att du tid att rätta till problemet utan att köra dubbla åtgärder.

        ![Ignorera aviseringar för logg-aviseringar](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Ange en Ignorera avisering värde större än frekvensen för aviseringen för att se till att meddelanden har stoppats utan överlappande

12. Som det tredje och sista steget, ange eventuella **grupp** måste aktiveras för varningsregeln när aviseringen är uppfyllt. Du kan välja alla befintliga grupp med varning eller skapa en ny grupp. Enligt markerad grupp, när aviseringen är utlösaren Azure kommer: skicka email(s), skicka SMS(s), anropa Webhook(s), åtgärda problemet med hjälp av Azure-Runbooks, push ITSM verktyget, osv. Lär dig mer om [åtgärdsgrupper](monitoring-action-groups.md).

    För **loggen aviseringar** viss ytterligare funktionalitet är tillgänglig att åsidosätta standardåtgärderna som:

    - **E-postavisering**: åsidosätter *e-postämne* i e-post, skickas via grupp; om en eller flera e-post-åtgärder finns i gruppen för denna åtgärd. Du kan inte ändra innehållet i e-postmeddelandet och det här fältet är **inte** för e-postadress.
    - **Inkludera anpassad Json-nyttolast**: åsidosätter webhook JSON som används av åtgärdsgrupper; om en eller flera webhook-åtgärder finns i gruppen för denna åtgärd. Användaren kan ange formatet för JSON som ska användas för alla webhooks som konfigurerats i associerade grupp; Mer information om webhook-format finns [webhook åtgärd för Log aviseringar](monitor-alerts-unified-log-webhook.md). Testa Webhook-alternativet har angetts för att kontrollera formatet och bearbetning av mål använder JSON-exemplet och det här alternativet som etiketter avsedd endast för **testning** syften.

        ![Åtgärden åsidosättningar för logg-aviseringar](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)

        > [!NOTE]
        > För **Test Webhook** alternativet ska fungera, bör ha stöd för slutpunkten [Cross Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) och användarens kan använda CORS-proxy för att komma runt ”inget Access Control-Tillåt-ursprung huvud” problem

13. Om alla fält är giltiga och med grön skalstreck den **skapa varningsregeln** på knappen och en avisering har skapats i Azure-Monitor - aviseringar. Alla aviseringar kan visas från aviseringar instrumentpanelen.

    ![Skapa en regel](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Inom några minuter aviseringen är aktiv och utlöser som beskrivits tidigare.

## <a name="view-your-alerts-in-azure-portal"></a>Visa aviseringar i Azure Portal

1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  

2. Den **aviseringar instrumentpanelen** visas - där alla Azure-aviseringar finns unified och visas i ett enda kort ![avisering instrumentpanelen](./media/monitoring-alerts-unified-usage/alerts-preview-overview.png)
3. Från övre vänstra till höger visar på instrumentpanelen en överblick över den följande: Klicka på att se en detaljerad lista:
    - *Utlöses aviseringar*: antalet aviseringar för närvarande har uppfyllts logik och utlöses i tillstånd
    - *Totalt antal Varningsregler*: antal Varningsregler skapas och i underordnad text, hur många som för närvarande har aktiverats
4. En lista över alla Eldad aviseringar visas som användaren kan välja för att visa detaljer
5. Hjälp i söka efter specifika aviseringar; alternativen listrutan överst kan använda för att filtrera specifika *prenumeration, resursgrupp eller resurs*. Ytterligare för någon olöst Varna ett sätt att använda den *Filter avisering* alternativet för att hitta för angivna nyckelordet - specifika matchande aviseringar med *namn, aviseringen kriterier, resursgrupp, och målresurs*

## <a name="managing-your-alerts-in-azure-portal"></a>Hantera aviseringar i Azure Portal
1. I den [portal](https://portal.azure.com/)väljer **övervakaren** och välj under avsnittet MONITOR - **aviseringar**.  
2. Välj den **hantera regler** knappen i det översta fältet att navigera till regeln hantering - där alla Varningsregler skapade räknas; inklusive aviseringar som har inaktiverats.
3. För att hitta för specifika Varningsregler kan antingen använda listrutan filter överst, vilket ger möjlighet att shortlist Varningsregler för specifika *prenumeration, resursgrupper och/eller resursen*. Alternativt med hjälp av sökfunktionen rutan ovanför listan varningsregeln markerat *filtrera aviseringar*, en kan ge nyckelord som matchas mot *avisering namn, villkor och målresurs*; om du vill visa endast regler för matchning.
   ![Hantera regler för avisering](./media/monitoring-alerts-unified-usage/alerts-preview-rules.png)
4. Om du vill visa eller ändra specifika varningsregeln, klicka på dess namn som ska visas som en klickbar länk.
5. Avisering som definierats framgår - strukturen i tre steg: 1) avisering 2) avisering detaljer 3) åtgärden villkorsgruppen. **Rikta kriterier** Klicka på om du vill ändra avisering logiken eller ett nytt villkor kan läggas till efter med hjälp av ikonen bin att ta bort den tidigare logiken. På samma sätt i aviseringsinformation avsnittet - **beskrivning** och **allvarlighetsgrad** kan ändras. Åtgärden gruppen kan ändras och en ny kan vara utformad för att länka till en avisering med hjälp av den **ny grupp** knappen.

   ![Ändra varningsregel](./media/monitor-alerts-unified/AlertsPreviewEdit.png)

6. Med den övre panelen ändringar av aviseringen kan vara reflekterande inklusive: **spara** att genomföra ändringarna så att aviseringen **Ignorera** gå tillbaka utan att spara ändringar som görs till aviseringen **inaktivera**  att inaktivera avisering - efter vilket den inte längre körs eller utlöser något. Slutligen **ta bort** du ta bort hela varningsregeln från Azure.

## <a name="next-steps"></a>Nästa steg

- Mer information om den nya [nära realtid mått aviseringar](monitoring-near-real-time-metric-alerts.md)
- Hämta en [översikt över mått samling](insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.
- Lär dig mer om [Logga varningar i Azure-aviseringar](monitor-alerts-unified-log.md)
- [Lär dig mer om aktiviteten loggen aviseringar i aviseringar (förhandsgranskning)-upplevelse](monitoring-activity-log-alerts-new-experience.md)
