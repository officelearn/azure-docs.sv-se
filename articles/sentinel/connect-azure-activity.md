---
title: Samla in Azure-aktivitetsdata i förhandsversionen av Azure Sentinel | Microsoft Docs
description: Lär dig hur du samlar in Azure-aktivitetsdata i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 42ce8d06489cfacf7ba9bb3de1425224b5df95e7
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240583"
---
# <a name="collect-data-from-azure-activity-log"></a>Samla in data från Azure-aktivitetsloggen

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md) i Azure Sentinel-med ett enda klick. Aktivitetsloggen är en prenumerationslogg som ger insikt i prenumerationsnivå händelser som inträffade i Azure. Detta omfattar en mängd data från Azure Resource Manager driftdata uppdateringar på Service Health-händelser. Med aktivitetsloggen kan du fastställa den ”vad, vem, och när” för någon skriva åtgärden (PUT, POST, DELETE) som vidtas på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. Aktivitetsloggen inkluderar inte läsåtgärder (GET) eller åtgärder för resurser som använder klassiskt / ”RDFE”-modellen. 


## <a name="prerequisites"></a>Förutsättningar

- Användare med global administratör eller administratörsbehörighet för säkerhet


## <a name="connect-to-azure-activity-log"></a>Ansluta till Azure-aktivitetsloggen

1. I Azure Sentinel väljer **datainsamling** och klicka sedan på den **Azure-aktivitetsloggen** panelen.

2. Välj de prenumerationer som du vill spela in Azure Sentinel i loggfönstret aktivitet för Azure. 

3. Klicka på **Anslut**.

4. Om du vill använda relevanta schemat i Log Analytics för aktivitet för Azure-aviseringar, Sök efter **AzureActivity**.


 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig att ansluta Azure-aktivitetsloggen till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
