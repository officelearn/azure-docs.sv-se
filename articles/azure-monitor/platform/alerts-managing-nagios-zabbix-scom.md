---
title: Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor
description: Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667456"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor

Nu kan du visa aviseringar från Nagios, Zabbix och System Center Operations Manager i [Azure Monitor](https://aka.ms/azure-alerts-overview). Dessa aviseringar kommer från integrationer med Nagios/Zabbix-servrar eller System Center Operations Manager i Log Analytics. 

## <a name="prerequisites"></a>Krav
Alla poster i Log Analytics-databasen med en typ av avisering importeras till Azure Monitor, så du måste utföra den konfiguration som krävs för att samla in dessa poster.
1. För **Nagios-** och **Zabbix-aviseringar** [konfigurerar du dessa servrar](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) så att aviseringar [skickas](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) till Log Analytics.
1. För **System Center Operations Manager-aviseringar** ansluter du [hanteringsgruppen för Operations Manager till logganalysarbetsytan](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Distribuera därför [alerthanteringslösningen](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) från Marketplace för Azure-lösningar. När det är gjort importeras alla aviseringar som skapas i System Center Operations Manager till Log Analytics.

## <a name="view-your-alert-instances"></a>Visa dina varningsinstanser
När du har konfigurerat importen till Log Analytics kan du börja visa varningsinstanser från dessa övervakningstjänster i [Azure Monitor](https://aka.ms/azure-alerts-overview). När de finns i Azure Monitor kan du [hantera dina varningsinstanser,](https://aka.ms/managing-alert-instances) [hantera smarta grupper som skapats på dessa aviseringar](https://aka.ms/managing-smart-groups) och ändra tillståndet för dina aviseringar och smarta [grupper](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Med den här lösningen kan du bara visa systemcenteroperationshanteraren/Zabbix/Nagios sparken-varningsinstanser i Azure Monitor. Konfiguration av varningsregel kan bara visas/redigeras i respektive övervakningsverktyg. 
>  1. Alla sparkenade varningsinstanser är tillgängliga både i Azure Monitor och Azure Log Analytics. För närvarande finns det inget sätt att välja mellan de två eller inta endast specifika avfyrade varningar.
>  1. Alla aviseringar från System Center Operations Manager, Zabbix och Nagios har signaltypen "Okänd" eftersom den underliggande telemetritypen inte är tillgänglig.
>  1. Nagios varningar är inte tillståndskänsliga - till exempel kommer [övervakaren villkoret](https://aka.ms/azure-alerts-overview) för en varning inte gå från "Sparken" till "Löst". I stället visas både "Sparken" och "Löst" som separata varningsinstanser. 

