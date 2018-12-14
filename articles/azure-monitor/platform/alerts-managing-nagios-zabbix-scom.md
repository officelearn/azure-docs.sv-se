---
title: Hantera aviseringar från andra övervakningstjänster i Azure Monitor
description: Hantera Nagios och Zabbix SCOM-aviseringar i Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: bc597d42fe89c0e03c4af1db3a935031b9043a98
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346543"
---
# <a name="manage-alerts-from-other-monitoring-services"></a>Hantera aviseringar från andra övervakningstjänster

Nu kan du visa din varningar från Nagios och Zabbix System Center Operations Manager i den [unified aviseringsgränssnittet](https://aka.ms/azure-alerts-overview). Dessa aviseringar kommer från integreringar med Nagios/Zabbix-servrar eller System Center Operations Manager till Log Analytics. 

## <a name="prerequisites"></a>Förutsättningar
Alla poster i Log Analytics-databasen med en typ av avisering kommer få importeras till den enhetliga aviseringsgränssnittet så måste du utföra de konfigurationssteg som krävs för att samla in dessa poster.
1. För **Nagios** och **Zabbix** aviseringar, [Konfigurera servrarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) att skicka aviseringar till Log Analytics.
1. För **System Center Operations Manager** aviseringar, [ansluta din Operations Manager-hanteringsgrupp till Log Analytics-arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Alla varningar som skapats i System Center Operations Manager importeras till Log Analytics.

## <a name="view-your-alert-instances"></a>Visa avisering instanser
När du har konfigurerat import till Log Analytics kan du börja visa aviseringsinstanser från dessa övervakningstjänster i den [unified aviseringsgränssnittet](https://aka.ms/azure-alerts-overview). När de befinner sig i enhetlig aviseringarna, kan du [hantera din aviseringsinstanser](https://aka.ms/managing-alert-instances), [hantera smarta grupper som skapats på dessa aviseringar](https://aka.ms/managing-smart-groups) och [ändrar status för dina aviseringar och smart grupper](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Nagios-aviseringar i enhetlig aviseringarna är inte tillståndskänsliga – till exempel den [övervaka villkor](https://aka.ms/azure-alerts-overview) varning kommer inte att gå från ”Fired” till ”löst”. I stället visas både ”Fired” och ”löst” som separata aviseringsinstanser. 
