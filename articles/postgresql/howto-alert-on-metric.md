---
title: Konfigurera aviseringar för mått för Azure-databas för PostgreSQL i Azure-portalen
description: Den här artikeln beskriver hur du konfigurerar och åtkomst mått aviseringar för Azure-databas för PostgreSQL från Azure-portalen.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: b4b15998276dd6c32e9c15622aa0251c6c066085
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
ms.locfileid: "29690266"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql"></a>Använda Azure portal för att ställa in aviseringar på mått för Azure-databas för PostgreSQL 

Den här artikeln visar hur du ställer in Azure-databas för PostgreSQL aviseringar med Azure-portalen. Du kan ta emot en avisering baserat på övervakning mätvärden för Azure-tjänster.

Aviseringen utlösare när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Aviseringen utlösare båda när villkoret uppfylls och sedan efteråt när villkor som inte längre är uppfyllt. 

Du kan konfigurera en avisering om du vill göra följande åtgärder när den utlöser:
* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer.
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webhook.

Du kan konfigurera och få information om Varningsregler med:
* [Azure-portalen](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../monitoring-and-diagnostics/insights-alerts-powershell.md)
* [Kommandoradsgränssnittet (CLI)](../monitoring-and-diagnostics/insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel på ett mått från Azure-portalen
1. I den [Azure-portalen](https://portal.azure.com/), Välj den Azure-databas för PostgreSQL-server som du vill övervaka.

2. Under den **övervakning** avsnitt i sidopanelen, Välj **Varna regler** som visas:

   ![Välj Varningsregler](./media/howto-alert-on-metric/1-alert-rules.png)

3. Välj **Lägg till mått avisering** (+ ikon). 

4. Den **Lägg till regel** öppnas som på bilden nedan.  Fyll i informationen som krävs:

   ![Lägg till mått avisering formulär](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Inställning | Beskrivning  |
   |---------|---------|
   | Namn | Ange ett namn för regeln. Det här värdet skickas via e-post för avisering. |
   | Beskrivning | Ange en kort beskrivning av regeln. Det här värdet skickas via e-post för avisering. |
   | Varning vid | Välj **mått** för den här typen av avisering. |
   | Prenumeration | Det här fältet är förinställd med prenumerationen som är värd för din Azure-databas för PostgreSQL. |
   | Resursgrupp | Det här fältet är förinställd med resursgruppen för din Azure-databas för PostgreSQL. |
   | Resurs | Det här fältet är förinställd med namnet på din Azure-databas för PostgreSQL. |
   | Mått | Välj det mått som du vill skicka en avisering för. Till exempel **lagringsprocent**. |
   | Villkor | Välj det villkor för mått som ska jämföras med. Till exempel **större än**. |
   | Tröskelvärde | Tröskelvärde för måttet, till exempel 85 (procent). |
   | Period | Tidsperioden som mått regeln måste uppfyllas innan aviseringen utlösare. Till exempel **under de senaste 30 minuterna**. |

   Baseras på exemplet söker aviseringen efter lagringsprocent över 85 procent under en period på 30 minuter. Den här aviseringen utlöses när den genomsnittliga procentandelen lagring har över 85 procent under 30 minuter. När den första utlösaren infaller utlöser igen när den genomsnittliga procentandelen för lagring är mindre än 85% under 30 minuter.

5. Välj metod för meddelande som du vill använda för varningsregeln. 

   Kontrollera **e-ägare, deltagare och läsare** om du vill prenumerationsadministratörer och medadministratörer kan skickas när aviseringen utlöses.

   Om du vill att ytterligare e-postmeddelanden ett meddelande när aviseringen utlöses, lägga till dem i den **ytterligare administratören email(s)** fältet. Avgränsa flera e-postmeddelanden med semikolon -  *email@contoso.com;email2@contoso.com*

   Du kan också ange en giltig URI i den **Webhook** om du vill att den anropas när aviseringen utlöses.

6. Välj **OK** att skapa aviseringen.

   Inom några minuter aviseringen är aktiv och utlöser som beskrivits tidigare.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du markera den och göra följande:

* Visa ett diagram som visar mått tröskelvärdet och faktiska värden från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varningsregeln.
* **Inaktivera** eller **aktivera** aviseringen om du vill att tillfälligt stoppa eller återuppta ta emot meddelanden.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [hur du konfigurerar webhooks i aviseringar](../monitoring-and-diagnostics/insights-webhooks-alerts.md).
* Hämta en [översikt över mått samling](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarstid.
