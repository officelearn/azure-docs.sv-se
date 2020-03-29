---
title: Konfigurera aviseringar - Azure-portal - Azure Database för PostgreSQL - Single Server
description: I den här artikeln beskrivs hur du konfigurerar och kommer åt måttaviseringar för Azure Database för PostgreSQL - Single Server från Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fe099dcb49d176d27466c08749a5873904d1ae2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74766845"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---single-server"></a>Använd Azure-portalen för att ställa in aviseringar om mått för Azure Database för PostgreSQL - Single Server

Den här artikeln visar hur du konfigurerar Azure Database för PostgreSQL-aviseringar med Azure-portalen. Du kan få en avisering baserat på övervakningsmått för dina Azure-tjänster.

Aviseringen utlöser när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar. Aviseringen utlöser både när villkoret först uppfylls och sedan efteråt när villkoret inte längre uppfylls. 

Du kan konfigurera en avisering så att du utför följande åtgärder när den utlöses:
* Skicka e-postmeddelanden till tjänstadministratören och medadministratörerna.
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Ring en webhook.

Du kan konfigurera och få information om varningsregler med hjälp av:
* [Azure-portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel för ett mått från Azure-portalen
1. I [Azure-portalen](https://portal.azure.com/)väljer du den Azure-databas för PostgreSQL-server som du vill övervaka.

2. Under **avsnittet Övervakning** i sidofältet väljer du **Aviseringar** som visas:

   ![Välj varningsregler](./media/howto-alert-on-metric/2-alert-rules.png)

3. Välj **Lägg till måttvarning** (+ ikon).

4. Sidan **Skapa regel** öppnas enligt nedan. Fyll i den information som krävs:

   ![Lägg till måttaviseringsformulär](./media/howto-alert-on-metric/4-add-rule-form.png)

5. I avsnittet **Villkor** väljer du **Lägg till villkor**.

6. Välj ett mått i listan med signaler som ska aviseras. I det här exemplet väljer du "Lagringsprocent".
   
   ![Välj mått](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurera varningslogiken inklusive **villkoret** (t.ex. "Större än"), **Tröskel** (t.ex. 85 procent), **Tidsaggregering**, **Tidsperiod** måste måttregeln uppfyllas innan aviseringen utlöses (t.ex. "Under de senaste 30 minuterna" och **Frekvens**.
   
   Välj **Klar** när du är klar.

   ![Välj mått](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. I avsnittet **Åtgärdsgrupper** väljer du **Skapa ny** om du vill skapa en ny grupp för att ta emot aviseringar i aviseringen.

9. Fyll i formuläret "Lägg till åtgärdsgrupp" med namn, kortnamn, prenumeration och resursgrupp.

10. Konfigurera en åtgärdstyp **för e-post/SMS/Push/Voice.**
    
    Välj "E-post azure Resource Manager-roll" för att välja prenumerationsägare, deltagare och läsare för att ta emot meddelanden.
   
    Du kan också ange en giltig URI i **webhook-fältet** om du vill att den ska anropas när aviseringen utlöses.

    Välj **OK** när du är klar.

    ![Åtgärdsgrupp](./media/howto-alert-on-metric/10-action-group-type.png)

11. Ange ett aviseringsregelnamn, beskrivning och allvarlighetsgrad.

    ![Åtgärdsgrupp](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Välj **Skapa aviseringsregel** om du vill skapa aviseringen.

    Inom några minuter är aviseringen aktiv och utlöser som tidigare beskrivits.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du markera den och göra följande:

* Visa ett diagram som visar tröskelvärdet för mått och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varningsregeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagningsmeddelanden.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du konfigurerar webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Få en [översikt över statistiksamlingen](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för att se till att din tjänst är tillgänglig och svarar.
