---
title: Installations aviseringar och meddelanden (Azure Portal)
description: Använd Azure Portal för att skapa SQL Database aviseringar som kan utlösa meddelanden eller automatisering när de villkor du anger är uppfyllda.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209492"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Skapa aviseringar för Azure SQL Database-och Azure Synapse Analytics-databaser med Azure Portal

## <a name="overview"></a>Översikt

Den här artikeln visar hur du ställer in aviseringar för enstaka, pooler och informations lager databaser i Azure SQL Database och Azure Synapse Analytics (tidigare Azure SQL Data Warehouse) med hjälp av Azure Portal. Aviseringar kan skicka ett e-postmeddelande till dig eller anropa en webbhook när något mått (till exempel databas storlek eller CPU-användning) når tröskelvärdet. Den här artikeln innehåller också metod tips för att ställa in aviserings perioder.

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig ännu i en hanterad instans. Alternativt kan du använda SQL-agenten för att skicka e-postaviseringar för vissa mått baserat på [vyer för dynamisk hantering](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

Du kan få en avisering baserat på övervaknings mått för, eller händelser på, dina Azure-tjänster.

* **Mått värden** – aviseringen utlöses när värdet för ett angivet mått korsar ett tröskelvärde som du tilldelar i båda riktningarna. Det innebär att den utlöses både när villkoret först uppfylls och sedan efteråt när det villkoret inte längre uppfylls.
* **Aktivitets logg händelser** – en avisering kan utlösas vid *varje* händelse, eller endast när ett visst antal händelser inträffar.

Du kan konfigurera en avisering för att göra följande när den utlöser:

* Skicka e-postaviseringar till tjänst administratören och medadministratörer
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webbhook

Du kan konfigurera och hämta information om aviserings regler med hjälp av

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [kommando rads gränssnitt (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en varnings regel för ett mått med Azure Portal

1. I [portalen](https://portal.azure.com/)letar du reda på den resurs som du är intresse rad av övervakning och väljer den.
2. Välj **aviseringar** i avsnittet övervakning. Texten och ikonen kan variera något för olika resurser.  

   ![Övervakning](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Klicka på knappen **ny varnings regel** för att öppna sidan **Skapa regel** .
  ![Skapa regel](media/sql-database-insights-alerts-portal/create-rule.png)

4. I avsnittet **villkor** klickar du på **Lägg till**.
  ![definiera villkor](media/sql-database-insights-alerts-portal/create-rule.png)
5. På sidan **Konfigurera signal logik** väljer du en signal.
  ![väljer du signal](media/sql-database-insights-alerts-portal/select-signal.png).
6. När du har valt en signal, till exempel **processor procent**, visas sidan **Konfigurera signal logik** .
  ![konfigurera signal logik](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. På den här sidan konfigurerar du den tröskel typ, operatör, sammansättnings typ, tröskel värde, sammansättnings precision och utvärderings frekvens. Klicka sedan på **Klar**.
8. I **Skapa regel**väljer du en befintlig **Åtgärds grupp** eller skapar en ny grupp. Med en åtgärds grupp kan du definiera vilken åtgärd som ska vidtas när ett varnings tillstånd inträffar.
  ![definiera åtgärds gruppen](media/sql-database-insights-alerts-portal/action-group.png)

9. Definiera ett namn för regeln, ange en valfri beskrivning, Välj en allvarlighets grad för regeln, Välj om du vill aktivera regeln när regeln skapas och klicka sedan på **Skapa regel avisering** för att skapa mått regel aviseringen.

Inom 10 minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
