---
title: Konfigurera aviseringar för mått för Azure Database för MySQL i Azure portal
description: Den här artikeln beskrivs hur du konfigurerar och åtkomst måttaviseringar för Azure Database för MySQL från Azure-portalen.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 6db451ab7de50039c38b59612dd2ab1c61c35e7b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271259"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql"></a>Använda Azure portal för att konfigurera aviseringar i mått för Azure Database för MySQL 

Den här artikeln visar hur du ställer in Azure Database för MySQL-aviseringar med Azure portal. Du kan få en avisering baserat på övervakning av mått för dina Azure-tjänster.

Aviseringen utlöses när värdet för ett visst mått överskrider ett tröskelvärde som du tilldelar. Avisering utlösarna båda när villkoret uppfylls och sedan efteråt när villkoret som inte längre är uppfyllt. 

Du kan konfigurera en avisering om att göra följande när den utlöses:
* Skicka e-postmeddelanden till tjänstadministratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webbhook

Du kan konfigurera och få information om Varningsregler med:
* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [Kommandoradsgränssnittet (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST-API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Skapa en aviseringsregel på ett mått från Azure portal
1. I den [Azure-portalen](https://portal.azure.com/), väljer Azure Database for MySQL-server som du vill övervaka.

2. Under den **övervakning** avsnittet på sidopanelen, Välj **Aviseringsregler** enligt:

   ![Välj Aviseringsregler](./media/howto-alert-on-metric/1-alert-rules.png)

3. Välj **Lägg till metrisk varning** (+ ikonen). 

4. Den **Lägg till regel** öppnas som på bilden nedan.  Fyll i informationen som krävs:

   ![Lägg till metrisk varning formulär](./media/howto-alert-on-metric/2-add-rule-form.png)

   | Inställning | Beskrivning  |
   |---------|---------|
   | Namn | Ange ett namn för regeln. Det här värdet skickas för avisering om e-postmeddelandet. |
   | Beskrivning | Ange en kort beskrivning av regeln. Det här värdet skickas för avisering om e-postmeddelandet. |
   | Varning vid | Välj **mått** för den här typen av avisering. |
   | Prenumeration | Det här fältet innehåller redan den prenumeration som är värd för din Azure Database för MySQL. |
   | Resursgrupp | Det här fältet innehåller redan resursgruppen för din Azure Database för MySQL. |
   | Resurs | Det här fältet är innehåller namnet på din Azure Database för MySQL. |
   | Mått | Välj det mått som du vill skicka en avisering för. Till exempel **lagringsprocent**. |
   | Tillstånd | Välj det villkor för mått som ska jämföras med. Till exempel **är större än**. |
   | Tröskelvärde | Tröskelvärde för mått, till exempel 85 (procent). |
   | Period | Tidsperioden som måttregel måste vara uppfyllda innan aviseringen utlösare. Till exempel **under de senaste 30 minuterna**. |

   Baseras på exemplet söker aviseringen efter lagringsprocent över 85 procent under en 30-minuters period. Den här aviseringen utlöses när den genomsnittliga procentandelen för lagring har varit över 85 procent under 30 minuter. När den första utlösaren inträffar, utlöser den igen när den genomsnittliga procentandelen för lagring är mindre än 85% över 30 minuter.

5. Välj den meddelandemetoden som du vill använda för regeln. 

   Kontrollera **e-postägare, deltagare och läsare** om du vill prenumerationsadministratörer och medadministratörer för att få e-postaviseringar när aviseringen utlöses.

   Om du vill att ytterligare e-postmeddelanden tar emot ett meddelande när aviseringen utlöses, lägga till dem i den **administratören email(s)** fält. Avgränsa flera e-postmeddelanden med semikolon -  *email@contoso.com;email2@contoso.com*

   Du kan också ange en giltig URI i den **Webhook** fältet om du vill att den anropas när aviseringen utlöses.

6. Skapa aviseringen genom att välja **OK**.

   Inom några minuter, aviseringen är aktiv och utlöser som det beskrivits.

## <a name="manage-your-alerts"></a>Hantera aviseringar
När du har skapat en avisering kan du markera den och göra följande:

* Visa ett diagram över tröskelvärde för mått och de faktiska värdena från föregående dag som är relevanta för den här aviseringen.
* **Redigera** eller **ta bort** varningsregeln.
* **Inaktivera** eller **aktivera** aviseringen om du vill att tillfälligt stoppa eller återuppta ta emot meddelanden.


## <a name="next-steps"></a>Nästa steg
* Läs mer om [konfigurerar webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
* Hämta en [översikt över mått samling](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) att kontrollera att tjänsten är tillgänglig och svarar.
