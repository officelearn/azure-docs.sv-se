---
title: Svara på händelser med Azure Log Analytics-aviseringar | Microsoft Docs
description: Den här självstudien hjälper dig att förstå aviseringar i Log Analytics som visar viktig information på din arbetsyta. Du kan få tidiga meddelanden om problem eller anropa åtgärder om du vill försöka åtgärda dem.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2018
ms.author: magoedte
ms.custom: mvc
ms.component: na
ms.openlocfilehash: c6c7b3f897e38fbd67098c9f881380bc073f13da
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432658"
---
# <a name="respond-to-events-with-azure-monitor-alerts"></a>Svara på händelser med Azure Monitor-aviseringar
Loggsökningsregler skapas av Azure-aviseringar för att automatiskt köra angivna loggfrågor med jämna mellanrum.  Om resultatet av loggfrågan matchar särskilda villkor skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [Åtgärdsgrupper](../monitoring-and-diagnostics/monitoring-action-groups.md).  Den här självstudien är en fortsättning på självstudien [Skapa och dela instrumentpaneler med Log Analytics-data](log-analytics-tutorial-dashboards.md).   

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en varningsregel
> * Konfigurera en åtgärdsgrupp som skickar ett e-postmeddelande

Du måste ha en befintlig virtuell dator [som är ansluten till Log Analytics-arbetsytan](log-analytics-quick-collect-azurevm.md) för att kunna utföra exemplet i självstudien.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-alerts"></a>Skapa aviseringar
Aviseringar skapas av aviseringsregler i Azure Monitor och kan automatiskt köra sparade frågor eller anpassade loggsökningar med jämna mellanrum.  Du kan skapa aviseringar baserat på specifika prestandamått, när vissa händelser skapas, om en händelse saknas, eller om flera händelser skapas inom ett visst tidsintervall.  Aviseringar kan exempelvis användas för att meddela dig när den genomsnittliga CPU-användningen överskrider ett visst tröskelvärde, när en saknad uppdatering eller när en händelse genereras när det upptäckts att en specifik Windows-tjänst eller Linux-daemon inte körs.  Om resultatet av loggsökningen matchar särskilda villkor, skapas en avisering. Regeln kan sedan automatiskt köra en eller flera åtgärder, som att t.ex. meddela dig om aviseringen eller anropa en annan process.

I följande exempel skapar du ett måttaviseringsregel som baseras på frågan *Virtuella datorer i Azure – processoranvändning* som sparats i kursen [Visualisera data](log-analytics-tutorial-dashboards.md). En avisering skapas för varje virtuell dator som överskrider ett tröskelvärde på 90 %.

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Monitor**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Monitor**.
1. Skapa en ny avisering genom att välja **Aviseringar** det vänstra fönstret och sedan klicka på **Ny aviseringsregel** högst upp på sidan.

    ![Skapa en ny aviseringsregel](./media/log-analytics-tutorial-response/alert-rule-02.png)

1. I det första steget ska du välja din Log Analytics-arbetsyta som resurs i avsnittet **Skapa avisering** eftersom detta är en loggbaserad aviseringssignal.  Filtrera resultaten genom att välja en specifik **prenumeration** från den nedrullningsbara listan, om du har mer än en prenumeration, som innehåller den virtuella datorn och Log Analytics-arbetsytan som du skapade tidigare.  Filtrera **resurstypen** genom att välja **Log Analytics** i den nedrullningsbara listan.  Välj slutligen **resursen** **DefaultLAWorkspace** och klicka spå **Klar**.

    ![Skapa en aviseringssteg 1-uppgift](./media/log-analytics-tutorial-response/alert-rule-03.png)

1. I avsnittet **Aviseringsvillkor** klickar du på **Lägg till villkor** för att definiera frågan och sedan ange logik som aviseringsregeln följer. I fönsterrutan **Konfigurera signallogiken** väljer du **Anpassad loggsökning** som signalnamn och anger din fråga i **Sökfråga**.

    Exempel:
    ```
    Perf
    | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total"
    | summarize AggregatedValue=avg(CounterValue) by bin(TimeGenerated, 1m)
    ```

    Fönstret uppdateras och visar aviseringens konfigurationsinställningar.  Högst upp visas resultaten för den valda signalens senaste 30 minuter.

1. Konfigurera aviseringen med följande information:  
   a. Välj **Metrisk måttenhet** i den nedrullningsbara listan **Baserat på*.  Ett metriskt mått skapar en avisering för varje objekt i frågan med ett värde som överstiger det angivna tröskelvärdet.  
   b. Välj **Större än** som **Villkor** och ange **90** som **Tröskelvärde**.  
   c. Välj **Efterföljande överträdelser** i avsnittet Utlös avisering baserat på och välj **Större än** i listrutan och ange värdet 3.  
   d. Godkänn standardinställningarna i avsnittet Utvärdering baserad på. Regeln körs var femte minut och returnera poster som har skapats i det här intervallet under den aktuella tiden.  
1. Slutför aviseringsregeln genom att klicka på **Spara**.

    ![Konfigurera aviseringssignal](./media/log-analytics-tutorial-response/alert-signal-logic-02.png)

1. Nu när vi går vidare till nästa steg ska du ge aviseringen ett namn i fältet **Aviseringsregelns namn**, t.ex. **CPU % högre än 90**.  Ge en **beskrivning** med information om aviseringen och välj alternativet **Critical(Sev 0)** som **Allvarlighetsgrad**.

    ![Konfigurera aviseringsinformation](./media/log-analytics-tutorial-response/alert-signal-logic-04.png)

1. Du kan aktivera aviseringsregeln omedelbart genom att acceptera standardvärdet för **Aktivera regel när du skapar**.  
1. I det tredje och sista steget anger du en **åtgärdsgrupp**, vilket säkerställer att samma åtgärder vidtas varje gång en avisering utlöses och kan användas för varje regel som du definierar.  Konfigurera en ny åtgärdsgrupp med följande information:  
   a. Välj **Ny åtgärdsgrupp** varvid fönstret **Lägg till grupp** visas.  
   b. Ange ett namn, t.ex. **IT-avdelningen – meddela** , i **Namn på åtgärdsgrupp** och ange ett **kortnamn**, t.ex. **it-avd-m**.  
   c. Kontrollera att standardvärdena för **Prenumeration** och **Resursgrupp** är korrekta. Om så inte skulle vara fallet väljer du korrekta värden från den nedrullningsbara listan.  
   d. Ge åtgärden ett namn i avsnittet Åtgärder, t.ex. **Skicka e-post** och välj **e-post/SMS/push/röst** i listrutan under **Åtgärdstyp**. Egenskapsrutan **e-post/SMS/Push/röst** öppnas till höger och ger ytterligare information.  
   e. Aktivera **E-post** i rutan **e-post/SMS/Push/röst** och ge en giltig SMTP-e-postadress som meddelandet kan levereras till.  
   f. Spara ändringarna genom att klicka på **OK**.  
       ![Skapa ny åtgärdsgrupp](./media/log-analytics-tutorial-response/action-group-properties-01.png)

1. Färdigställ åtgärdsgruppen genom att klicka på **OK**.
1. Färdigställ aviseringsregeln genom att klicka på **Skapa aviseringsregel**. Den börjar köras omedelbart.

    ![Slutför skapandet av ny aviseringsregel](./media/log-analytics-tutorial-response/alert-rule-01.png)

## <a name="view-your-alerts-in-azure-portal"></a>Visa dina aviseringar i Azure Portal
Nu när du har skapat en avisering kan du visa Azure-aviseringar i ett enskilt fönster och hantera alla aviseringsregler för alla dina Azure-prenumerationer. Det listar alla aviseringsregler (aktiverade såväl som inaktiverade) och det kan sorteras efter målresurser, resursgrupper, regelnamn eller status. En sammanfattning av alla utlösta aviseringar och det totala antalet konfigurerade/aktiverade aviseringsregler ingår.

![Statussida för Azure-aviseringar](./media/log-analytics-tutorial-response/azure-alerts-02.png)

När aviseringen utlöses speglar tabellen villkoret och hur många gånger det har förekommit under det valda tidsintervallet (standardinställningen är de senaste sex timmarna).  Det ska finnas ett e-postmeddelande i din inkorg, liknande det i följande exempel, som visar den felaktiga virtuella datorn och de översta resultaten som matchar sökfrågan i det här fallet.

![Exempel på e-postmeddelande med åtgärdsavisering](./media/log-analytics-tutorial-response/azure-alert-email-notification-01.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig hur varningsregler proaktivt kan identifiera och åtgärda problem när de kör loggsökningar i schemalagda intervall och matchar ett särskilt villkor.

Följ den här länken om du vill se inbyggda skriptexempel för Log Analytics.

> [!div class="nextstepaction"]
> [Skriptexempel för Log Analytics](powershell-samples.md)
