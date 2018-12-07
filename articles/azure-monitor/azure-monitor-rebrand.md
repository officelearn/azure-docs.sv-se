---
title: Azure Monitor namnbytet | Microsoft Docs
description: Beskriver de senaste anpassning och namnändringar som nyligen gjorts i Azure-hanteringstjänster.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 68986b3d60566ee190a92c6ccf8cda767824350f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000139"
---
# <a name="azure-monitor-branding-changes"></a>Azure Monitor anpassningsändringar
Den här artikeln beskriver de senaste anpassning och namnändringar som nyligen gjorts i Azure-hanteringstjänster. 

## <a name="consolidation-of-monitoring-services-into-azure-monitor"></a>Konsolidering av övervakningstjänster i Azure Monitor
Log Analytics och Application Insights har integrerats i Azure Monitor för att tillhandahålla en enda integrerad upplevelse för att övervaka Azure-resurser och hybridmiljöer. Inga funktioner har tagits bort från de här tjänsterna och användarna kan utföra samma scenarier som de alltid har slutförts utan förlust eller en kompromettering av alla funktioner.

Dokumentation för var och en av dessa tjänster har integrerats i en enda uppsättning innehåll för Azure Monitor. Detta hjälper dig att läsaren hitta allt innehåll för ett visst scenario för övervakning på en enda plats istället för att referera till flera uppsättningar av innehåll du behöver. Eftersom tjänsten konsoliderade utvecklas, kommer innehållet blir mer integreras.

## <a name="log-analytics-redefinition"></a>Log Analytics omdefiniering
Log Analytics har spelat en central roll i Azure-hanteringen genom att samla in telemetri och övriga data från olika källor och att tillhandahålla en fråga frågespråk och en analytisk motor som ger dig insikter om hur dina program och resurser. Den kommer att fortsätta att fylla kritiska rollen som en funktion i Azure Monitor. Andra funktioner som har som anses del av Log Analytics som agenter och lösningar kommer ompositioneras som funktioner i Azure Monitor. Deras funktioner har inte förändrats än möjliga förbättringar av deras upplevelse i Azure-portalen.

## <a name="retirement-of-operations-management-suite-brand"></a>Tillbakadragning av Operations Management Suite varumärke
Operations Management Suite (OMS) har en sammanslagning av följande Azure management-tjänster för licensiering:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Ny prisnivå har introducerats för dessa tjänster](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), och OMS paketera är inte längre tillgänglig för nya kunder. Ingen av de tjänster som ingick i OMS har ändrats, förutom konsolideringen i Azure Monitor som beskrivs ovan. 



## <a name="next-steps"></a>Nästa steg

- Läs en [översikt över Azure Monitor](overview.md) som beskriver dess olika komponenter och funktioner.
- Lär dig mer om den [övergång av OMS-portalen](../azure-monitor/platform/oms-portal-transition.md).