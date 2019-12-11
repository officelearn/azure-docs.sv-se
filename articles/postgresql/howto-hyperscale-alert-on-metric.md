---
title: Konfigurera aviseringar – storskalig (citus) – Azure Database for PostgreSQL
description: Den här artikeln beskriver hur du konfigurerar och får åtkomst till mått aviseringar för Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a830f344023967f07ab809d67833f99e4e2958
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977615"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Använd Azure Portal för att ställa in aviseringar för mått för Azure Database for PostgreSQL-storskalig skalning (citus)

Den här artikeln visar hur du konfigurerar Azure Database for PostgreSQL aviseringar med hjälp av Azure Portal. Du kan få en avisering utifrån övervaknings måtten för dina Azure-tjänster.

Vi ställer in en avisering som ska utlösas när värdet för ett angivet mått överskrider ett tröskelvärde. Aviseringen utlöses när villkoret först uppfylls och fortsätter att utlösa efteråt.

Du kan konfigurera en avisering för att utföra följande åtgärder när den utlöser:
* Skicka e-postaviseringar till tjänst administratör och-administratörer.
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webhook.

Du kan konfigurera och hämta information om aviserings regler med hjälp av:
* [Azure-portalen](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [REST-API:et för Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en varnings regel för ett mått från Azure Portal
1. I [Azure Portal](https://portal.azure.com/)väljer du den Azure Database for PostgreSQLs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **aviseringar** som visas:

   ![Välj aviserings regler](./media/howto-hyperscale-alert-on-metric/2-alert-rules.png)

3. Välj **ny varnings regel** (+ ikon).

4. Sidan **Skapa regel** öppnas som visas nedan. Fyll i den information som krävs:

   ![Lägg till mått aviserings formulär](./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png)

5. I avsnittet **villkor** väljer du **Lägg till**.

6. Välj ett mått i listan över signaler att bli aviserad om. I det här exemplet väljer du "lagrings procent".
   
   ![Välj mått](./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurera aviserings logiken:

    * **Operator** (t. ex. "Större än")
    * **Tröskel värde** (t. ex. 85 procent)
    * Sammanställnings **kornig het** som mått regeln måste uppfyllas innan aviserings utlösare (t. ex. "Under de senaste 30 minuterna")
    * **utvärderings frekvens** (t. ex. "1 minut")
   
   Välj **klar** när du är klar.

   ![Välj mått](./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png)

8. I avsnittet **Åtgärds grupper** väljer du **Skapa nytt** för att skapa en ny grupp för att ta emot meddelanden på aviseringen.

9. Fyll i formuläret "Lägg till åtgärds grupp" med ett namn, ett kort namn, en prenumeration och en resurs grupp.

    ![Åtgärds grupp](./media/howto-hyperscale-alert-on-metric/9-add-action-group.png)

10. Konfigurera en **e-post/SMS/push/röst** -åtgärds typ.
    
    Välj "e-Azure Resource Manager roll" om du vill skicka meddelanden till prenumerations ägare, deltagare och läsare.
   
    Välj **OK** när du är klar.

    ![Åtgärds grupp](./media/howto-hyperscale-alert-on-metric/10-action-group-type.png)

11. Ange ett namn, en beskrivning och en allvarlighets grad för varnings regeln.

    ![Åtgärds grupp](./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png) 

12. Välj **skapa aviserings regel** för att skapa aviseringen.

    Inom några minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

## <a name="manage-your-alerts"></a>Hantera aviseringar

När du har skapat en avisering kan du välja den och utföra följande åtgärder:

* Visa ett diagram som visar mått tröskelvärdet och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varnings regeln.
* **Inaktivera** eller **Aktivera** aviseringen om du tillfälligt vill stoppa eller återuppta mottagning av meddelanden.

## <a name="next-steps"></a>Nästa steg
* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Få en [Översikt över mått samlingen](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) för att se till att tjänsten är tillgänglig och svarar.
