---
title: Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor
description: Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 367a7020e56db7105da7624c7911ae5b59f1365a
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92558965"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Hantera aviseringar från System Center Operations Manager, Zabbix och Nagios i Azure Monitor

Nu kan du Visa aviseringarna från nagios, zabbix och System Center Operations Manager i [Azure Monitor](./alerts-overview.md). Dessa aviseringar kommer från integreringar med nagios/zabbix-servrar eller System Center Operations Manager till Log Analytics. 

## <a name="prerequisites"></a>Förutsättningar
Alla poster i Log Analytics-lagringsplatsen med en typ av avisering importeras till Azure Monitor, så du måste utföra den konfiguration som krävs för att samla in dessa poster.
1. För **nagios** -och **zabbix** -aviseringar [konfigurerar du servrarna](../learn/quick-collect-linux-computer.md) för att [skicka aviseringar](./data-sources-custom-logs.md?toc=/azure/azure-monitor/toc.json) till Log Analytics.
1. För **System Center Operations Manager** aviseringar [ansluter du Operations Manager hanterings gruppen till din Log Analytics-arbetsyta](./om-agents.md). Därefter distribuerar du [aviseringshantering](./alert-management-solution.md) -lösningen från Marketplace för Azure-lösningar. När du är färdig importeras alla aviseringar som skapats i System Center Operations Manager till Log Analytics.

## <a name="view-your-alert-instances"></a>Visa dina aviserings instanser
När du har konfigurerat importen till Log Analytics kan du börja Visa aviserings instanser från dessa övervaknings tjänster i [Azure Monitor](./alerts-overview.md). När de finns i Azure Monitor kan du [Hantera dina aviserings instanser](./alerts-managing-alert-instances.md?toc=%252fazure%252fazure-monitor%252ftoc.json), [Hantera smarta grupper som skapats i dessa aviseringar](./alerts-managing-smart-groups.md?toc=%252fazure%252fazure-monitor%252ftoc.json) och [ändra status för dina aviseringar och smarta grupper](./alerts-managing-alert-states.md?toc=%252fazure%252fazure-monitor%252ftoc.json).

> [!NOTE]
>  1. Med den här lösningen kan du bara Visa System Center Operations Manager/zabbix/nagios-utlösta varnings instanser i Azure Monitor. Aviserings regel konfigurationen kan bara visas/redige ras i respektive övervaknings verktyg. 
>  1. Alla utlösta varnings instanser blir tillgängliga både i Azure Monitor och Azure Log Analytics. För närvarande finns det inget sätt att välja mellan de två eller inmatade enbart vissa aviseringar.
>  1. Alla aviseringar från System Center Operations Manager, zabbix och nagios har signal typen "okänd" eftersom den underliggande typen av telemetri inte är tillgänglig.
>  1. Nagios-aviseringar är inte tillstånds känsliga – till exempel går det inte att [övervaka villkoret](./alerts-overview.md) för en avisering från "utlöst" till "löst". I stället visas både "utlöst" och "löst" som separata varnings instanser.