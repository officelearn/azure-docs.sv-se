---
title: Konfigurera aviseringar och meddelanden för en hanterad instans (Azure Portal)
description: Använd Azure Portal för att skapa SQL-hanterade instans varningar, som kan utlösa meddelanden eller automatisering när de villkor du anger är uppfyllda.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 04/02/2020
ms.openlocfilehash: a332627d149a36ba5d5beb2626023e58a221f0d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80639203"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Skapa aviseringar för Azure SQL-hanterad instans med hjälp av Azure Portal

## <a name="overview"></a>Översikt

Den här artikeln visar hur du konfigurerar aviseringar för databaser i Azure SQL Managed instance-databasen med hjälp av Azure Portal. Aviseringar kan skicka ett e-postmeddelande till dig eller anropa en webbhook när ett mått (till exempel instans lagrings storlek eller CPU-användning) når tröskelvärdet. Den här artikeln innehåller också metod tips för att ställa in aviserings perioder.

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
* [REST-API:et för Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en varnings regel för ett mått med Azure Portal

1. I [portalen](https://portal.azure.com/)letar du reda på den resurs som du är intresse rad av övervakning och väljer den.
2. Välj **aviseringar** i avsnittet övervakning. Texten och ikonen kan variera något för olika resurser.  

   ![Övervakning](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Klicka på knappen **ny varnings regel** för att öppna sidan **Skapa regel** .
   ![Skapa regel](media/sql-database-insights-alerts-portal/create-rule.png)

4. I avsnittet **villkor** klickar du på **Lägg till**.
   ![Definiera villkor](media/sql-database-insights-alerts-portal/create-rule.png)
5. På sidan **Konfigurera signal logik** väljer du en signal.
   ![Välj signal](media/sql-database-insights-alerts-portal/select-signal.png)
6. När du har valt en signal, till exempel **processor procent**, visas sidan **Konfigurera signal logik** .
   ![Konfigurera signallogiken](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. På den här sidan konfigurerar du den tröskel typ, operatör, sammansättnings typ, tröskel värde, sammansättnings precision och utvärderings frekvens. Klicka sedan på **färdig**.
8. I **Skapa regel**väljer du en befintlig **Åtgärds grupp** eller skapar en ny grupp. Med en åtgärds grupp kan du definiera vilken åtgärd som ska vidtas när ett varnings tillstånd inträffar.
  ![Definiera åtgärds grupp](media/sql-database-insights-alerts-portal/action-group.png)

9. Definiera ett namn för regeln, ange en valfri beskrivning, Välj en allvarlighets grad för regeln, Välj om du vill aktivera regeln när regeln skapas och klicka sedan på **Skapa regel avisering** för att skapa mått regel aviseringen.

Inom 10 minuter är aviseringen aktiv och utlösare enligt beskrivningen ovan.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [konfigurerar Webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
