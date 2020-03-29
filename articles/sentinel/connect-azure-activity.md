---
title: Ansluta Azure-aktivitetsdata till Azure Sentinel | Microsoft-dokument
description: Lär dig hur du ansluter Azure Activity-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124992"
---
# <a name="connect-data-from-azure-activity-log"></a>Ansluta data från Azure Activity-logg

Du kan strömma loggar från [Azure Activity-loggen](../azure-monitor/platform/platform-logs-overview.md) till Azure Sentinel med ett enda klick. Aktivitetsloggen är en prenumerationslogg som registrerar och visar händelser på prenumerationsnivå i Azure, från Azure Resource Manager-driftdata till uppdateringar av Service Health-händelser. Med aktivitetsloggen kan du bestämma "vad, vem och när" för en skrivåtgärd (PUT, POST, DELETE) som utförs på resurserna i prenumerationen. Du kan också lära dig status för åtgärden och andra relevanta egenskaper. Aktivitetsloggen innehåller inte läsåtgärder (GET) eller åtgärder för resurser som använder modellen Klassisk/"RDFE". 

## <a name="prerequisites"></a>Krav

- Användaren måste ha deltagarbehörighet till log analytics-arbetsytan.
- Användaren måste ha läsbehörighet för alla prenumerationer vars loggar du vill strömma till Azure Sentinel.

## <a name="set-up-the-azure-activity-connector"></a>Konfigurera Azure Activity-anslutningsappen

1. Välj Datakopplingar på **navigeringsmenyn**i Azure Sentinel . Klicka på **Azure Activity**i listan över kopplingar och sedan på knappen **Öppna kopplingssidan** längst ned till höger.

2. Klicka på länken Konfigurera **Azure-aktivitetsloggar >under** fliken **Instruktioner.**

3. I **loggfönstret För Azure-aktivitet** väljer du de prenumerationer vars loggar du vill strömma till Azure Sentinel. 

4. Klicka på **Anslut**i prenumerationsfönstret som öppnas till höger .

5. Om du vill använda det relevanta schemat i `AzureActivity` Logganalys för Azure-aktivitetsaviseringar skriver du i frågefönstret.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du ansluter Azure Activity-loggen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel med hjälp av [inbyggda](tutorial-detect-threats-built-in.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.
