---
title: Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor
description: Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77667456"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor

Nu kan du Visa aviseringarna från nagios, zabbix och System Center Operations Manager i [Azure Monitor](https://aka.ms/azure-alerts-overview). Dessa aviseringar kommer från integreringar med nagios/zabbix-servrar eller System Center Operations Manager till Log Analytics. 

## <a name="prerequisites"></a>Krav
Alla poster i Log Analytics-lagringsplatsen med en typ av avisering importeras till Azure Monitor, så du måste utföra den konfiguration som krävs för att samla in dessa poster.
1. För **nagios** -och **zabbix** -aviseringar [konfigurerar du servrarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) för att [skicka aviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) till Log Analytics.
1. För **System Center Operations Manager** aviseringar [ansluter du Operations Manager hanterings gruppen till din Log Analytics-arbetsyta](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Därefter distribuerar du [aviseringshantering](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) -lösningen från Marketplace för Azure-lösningar. När du är färdig importeras alla aviseringar som skapats i System Center Operations Manager till Log Analytics.

## <a name="view-your-alert-instances"></a>Visa dina aviserings instanser
När du har konfigurerat importen till Log Analytics kan du börja Visa aviserings instanser från dessa övervaknings tjänster i [Azure Monitor](https://aka.ms/azure-alerts-overview). När de finns i Azure Monitor kan du [Hantera dina aviserings instanser](https://aka.ms/managing-alert-instances), [Hantera smarta grupper som skapats i dessa aviseringar](https://aka.ms/managing-smart-groups) och [ändra status för dina aviseringar och smarta grupper](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Med den här lösningen kan du bara Visa System Center Operations Manager/zabbix/nagios-utlösta varnings instanser i Azure Monitor. Aviserings regel konfigurationen kan bara visas/redige ras i respektive övervaknings verktyg. 
>  1. Alla utlösta varnings instanser blir tillgängliga både i Azure Monitor och Azure Log Analytics. För närvarande finns det inget sätt att välja mellan de två eller inmatade enbart vissa aviseringar.
>  1. Alla aviseringar från System Center Operations Manager, zabbix och nagios har signal typen "okänd" eftersom den underliggande typen av telemetri inte är tillgänglig.
>  1. Nagios-aviseringar är inte tillstånds känsliga – till exempel går det inte att [övervaka villkoret](https://aka.ms/azure-alerts-overview) för en avisering från "utlöst" till "löst". I stället visas både "utlöst" och "löst" som separata varnings instanser. 

