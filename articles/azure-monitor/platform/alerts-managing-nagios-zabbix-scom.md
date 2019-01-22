---
title: Hantera aviseringar från SCOM, Zabbix och Nagios i Azure Monitor
description: Hantera aviseringar från SCOM, Zabbix och Nagios i Azure Monitor
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 48fb9d8eaf2003834a420b48d649c830c608fd6e
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421223"
---
# <a name="manage-alerts-from-scom-zabbix-and-nagios-in-azure-monitor"></a>Hantera aviseringar från SCOM, Zabbix och Nagios i Azure Monitor

Nu kan du visa din varningar från Nagios och Zabbix System Center Operations Manager i [Azure Monitor](https://aka.ms/azure-alerts-overview). Dessa aviseringar kommer från integreringar med Nagios/Zabbix-servrar eller System Center Operations Manager till Log Analytics. 

## <a name="prerequisites"></a>Förutsättningar
Alla poster i Log Analytics-databasen med en typ av avisering kommer få importeras till Azure Monitor, så måste du utföra de konfigurationssteg som krävs för att samla in dessa poster.
1. För **Nagios** och **Zabbix** aviseringar, [Konfigurera servrarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) till [Skicka aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) till Log Analytics.
1. För **System Center Operations Manager** aviseringar, [ansluta din Operations Manager-hanteringsgrupp till Log Analytics-arbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Genom att följa dessa, distribuera den [aviseringshantering](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) lösning från marketplace för Azure-lösningar. När du har gjort, importeras alla varningar som skapats i System Center Operations Manager till Log Analytics.

## <a name="view-your-alert-instances"></a>Visa avisering instanser
När du har konfigurerat import till Log Analytics kan du börja visa aviseringsinstanser från dessa övervakningstjänster i [Azure Monitor](https://aka.ms/azure-alerts-overview). När de befinner sig i Azure Monitor kan du [hantera din aviseringsinstanser](https://aka.ms/managing-alert-instances), [hantera smarta grupper som skapats på dessa aviseringar](https://aka.ms/managing-smart-groups) och [ändrar status för dina aviseringar och smart grupper](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Den här lösningen kan du bara vill visa SCOM/Zabbix/Nagios utlösta aviseringsinstanser i Azure Monitor. Varningsregelskonfiguration kan endast vara visas/redigeras i respektive övervakningsverktyg. 
>  1. Alla aktiverade aviseringsinstanser blir tillgänglig både i Azure Monitor och Azure Log Analytics. Det finns för närvarande inget sätt att välja mellan de två mata in endast specifika utlöst aviseringar.
>  1. Alla aviseringar från SCOM, Zabbix och Nagios har signaltyp ”okänt” eftersom den underliggande telemetri-typen inte är tillgänglig.
>  1. Nagios-aviseringar är inte tillståndskänsliga – till exempel den [övervaka villkor](https://aka.ms/azure-alerts-overview) varning kommer inte att gå från ”Fired” till ”löst”. I stället visas både ”Fired” och ”löst” som separata aviseringsinstanser. 

