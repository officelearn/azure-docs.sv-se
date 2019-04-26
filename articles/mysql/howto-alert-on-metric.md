---
title: Konfigurera aviseringar för mått för Azure Database för MySQL i Azure portal
description: Den här artikeln beskrivs hur du konfigurerar och åtkomst måttaviseringar för Azure Database för MySQL från Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: mysql
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: 387877efea4df30685fcf443a4d114c4feb66401
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525660"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Använda Azure portal för att konfigurera aviseringar i mått för Azure Database för MySQL 

Den här artikeln visar hur du ställer in Azure Database för MySQL-aviseringar med Azure portal. Du kan få en avisering baserat på övervakning av mått för dina Azure-tjänster.

Aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Avisering utlösarna båda när villkoret uppfylls och sedan efteråt när villkoret som inte längre är uppfyllt. 

Du kan konfigurera en avisering om att göra följande när den utlöses:
* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webbhook

Du kan konfigurera och få information om Varningsregler med:
* [Azure Portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure CLI](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST-API](https://docs.microsoft.com/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel på ett mått från Azure portal
1. I den [Azure-portalen](https://portal.azure.com/), väljer Azure Database for MySQL-server som du vill övervaka.

2. Under den **övervakning** avsnittet på sidopanelen, Välj **aviseringar** enligt:

   ![Välj Aviseringsregler](./media/howto-alert-on-metric/2-alert-rules.png)

3. Välj **Lägg till metrisk varning** (+ ikonen).

4. Den **skapa regeln** öppnas som på bilden nedan. Fyll i informationen som krävs:

   ![Lägg till metrisk varning formulär](./media/howto-alert-on-metric/4-add-rule-form.png)

5. I den **villkor** väljer **Lägg till villkor**.

6. Välj ett mått från listan över signaler bli aviserad om. I det här exemplet väljer du ”Storage procent”.
   
   ![Välj mått](./media/howto-alert-on-metric/6-configure-signal-logic.png)

7. Konfigurera alert logic, inklusive den **villkor** (ex.) ”Större än”), **tröskelvärdet** (ex.) 85 procent), **tidsmängd**, **Period** tid måttregel måste vara uppfyllda innan aviseringen utlösare (t.ex. ”Under de senaste 30 minuterna”), och **frekvens**.
   
   Välj **klar** när du är klar.

   ![Välj mått](./media/howto-alert-on-metric/7-set-threshold-time.png)

8. I den **åtgärdsgrupper** väljer **Skapa ny** att skapa en ny grupp om du vill ta emot meddelanden på aviseringen.

9. Fyll i formuläret ”Lägg till åtgärdsgrupp” med ett namn, kort namn, prenumeration och resursgrupp.

10. Konfigurera en **e-post/SMS/Push/röst** åtgärdstyp.
    
    Välj ”e-post Azure Resource Manager roll” välja Prenumerationsägare, deltagare och läsare för att ta emot meddelanden.
   
    Du kan också ange en giltig URI i den **Webhook** fältet om du vill att den anropas när aviseringen utlöses.

    Välj **OK** när du är klar.

    ![Åtgärdsgrupp](./media/howto-alert-on-metric/10-action-group-type.png)

11. Ange varningsregelns namn, beskrivning och allvarlighetsgrad.

    ![Åtgärdsgrupp](./media/howto-alert-on-metric/11-name-description-severity.png) 

12. Välj **skapa varningsregel** att skapa aviseringen.

    Inom några minuter, aviseringen är aktiv och utlöser som det beskrivits.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du markera den och göra följande:

* Visa ett diagram över tröskelvärde för mått och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varningsregeln.
* **Inaktivera** eller **aktivera** aviseringen om du vill att tillfälligt stoppa eller återuppta ta emot meddelanden.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [konfigurerar webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Hämta en [översikt över mått samling](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarar.
