---
title: Installationsaviseringar och meddelanden för hanterad instans (Azure-portal)
description: Använd Azure-portalen för att skapa SQL-aviseringar för hanterade instanser, som kan utlösa meddelanden eller automatisering när de villkor du anger uppfylls.
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
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639203"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Skapa aviseringar för Azure SQL Managed Instance med Azure-portalen

## <a name="overview"></a>Översikt

Den här artikeln visar hur du konfigurerar aviseringar för databaser i Azure SQL Managed Instance Database med Azure-portalen. Aviseringar kan skicka ett e-postmeddelande eller anropa en webbkrok när vissa mått (till exempel lagringsstorlek eller CPU-användning) når tröskelvärdet. Den här artikeln innehåller också metodtips för att ange varningsperioder.

Du kan få en avisering baserat på övervakningsmått för eller händelser på dina Azure-tjänster.

* **Måttvärden** - Aviseringen utlöser när värdet för ett angivet mått överskrider ett tröskelvärde som du tilldelar i båda riktningarna. Det vill än, det utlöser både när villkoret först uppfylls och sedan efteråt när detta villkor inte längre uppfylls.
* **Aktivitetslogghändelser** - En avisering kan *utlösas* vid varje händelse, eller endast när ett visst antal händelser inträffar.

Du kan konfigurera en avisering så att den gör följande när den utlöses:

* Skicka e-postmeddelanden till tjänstadministratören och medadministratörerna
* Skicka e-post till ytterligare e-postmeddelanden som du anger.
* Anropa en webbhook

Du kan konfigurera och få information om varningsregler med hjälp av

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [kommandoradsgränssnitt (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [REST-API:et för Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Skapa en aviseringsregel för ett mått med Azure-portalen

1. Leta reda på den resurs du är intresserad av att övervaka i [portalen](https://portal.azure.com/)och välj den.
2. Välj **Aviseringar** i avsnittet Övervakning. Texten och ikonen kan variera något för olika resurser.  

   ![Övervakning](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Välj knappen **Ny varningsregel** för att öppna sidan **Skapa regel.**
   ![Skapa regel](media/sql-database-insights-alerts-portal/create-rule.png)

4. Klicka på Lägg **till**i avsnittet **Villkor.**
   ![Definiera villkor](media/sql-database-insights-alerts-portal/create-rule.png)
5. Välj en signal på sidan **Konfigurera signallogik.**
   ![Välj signal](media/sql-database-insights-alerts-portal/select-signal.png)
6. När du har valt en signal, till exempel **CPU-procent,** visas sidan **Konfigurera signallogik.**
   ![Konfigurera signallogiken](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. På den här sidan konfigurerar du tröskeltypen, operatorn, aggregeringstypen, tröskelvärdet, aggregeringsgranulariteten och utvärderingsfrekvensen. Klicka sedan på **Klar**.
8. Välj en befintlig **åtgärdsgrupp** eller skapa en ny grupp i **regeln Skapa.** Med en åtgärdsgrupp kan du definiera vilken åtgärd som ska vidtas när ett varningsvillkor inträffar.
  ![Definiera åtgärdsgrupp](media/sql-database-insights-alerts-portal/action-group.png)

9. Definiera ett namn för regeln, ange en valfri beskrivning, välj en allvarlighetsgrad för regeln, välj om regeln ska aktiveras när regeln skapas och klicka sedan på **Skapa regelavisering** för att skapa måttregelaviseringen.

Inom 10 minuter är aviseringen aktiv och utlöser som tidigare beskrivits.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du konfigurerar webhooks i aviseringar](../azure-monitor/platform/alerts-webhooks.md).
