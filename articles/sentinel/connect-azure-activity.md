---
title: Ansluta Azure-aktivitetsdata till Sentinel-förhandsversion i Azure | Microsoft Docs
description: Lär dig mer om att ansluta Azure-aktivitetsdata till Sentinel-Azure.
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
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: d0cc13227bfe02594a57a7fb0ba8ee1cb3383d56
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785167"
---
# <a name="connect-data-from-azure-activity-log"></a>Anslut data från Azure-aktivitetsloggen

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Azure-aktivitetsloggen](../azure-monitor/platform/activity-logs-overview.md) i Azure Sentinel-med ett enda klick. Aktivitetsloggen är en prenumerationslogg som ger insikt i prenumerationsnivå händelser som inträffade i Azure. Detta omfattar en mängd data från Azure Resource Manager driftdata uppdateringar på Service Health-händelser. Med aktivitetsloggen kan du fastställa den ”vad, vem, och när” för någon skriva åtgärden (PUT, POST, DELETE) som vidtas på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. Aktivitetsloggen inkluderar inte läsåtgärder (GET) eller åtgärder för resurser som använder klassiskt / ”RDFE”-modellen. 


## <a name="prerequisites"></a>Nödvändiga komponenter

- Användare med global administratör eller administratörsbehörighet för säkerhet


## <a name="connect-to-azure-activity-log"></a>Ansluta till Azure-aktivitetsloggen

1. I Azure Sentinel väljer **datakopplingar** och klicka sedan på den **Azure-aktivitetsloggen** panelen.

2. Välj de prenumerationer som du vill spela in Azure Sentinel i loggfönstret aktivitet för Azure. 

3. Klicka på **Anslut**.

4. Om du vill använda relevanta schemat i Log Analytics för aktivitet för Azure-aviseringar, Sök efter **AzureActivity**.


 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet lärde du dig att ansluta Azure-aktivitetsloggen till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
