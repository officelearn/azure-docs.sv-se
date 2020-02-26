---
title: Anslut Azure Activity data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Azures aktivitets data till Azure Sentinel.
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
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 28d5acd80deef193c7d2fea46f682f26abd756aa
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588611"
---
# <a name="connect-data-from-azure-activity-log"></a>Anslut data från Azure aktivitets logg



Du kan strömma loggar från [Azure aktivitets logg](../azure-monitor/platform/platform-logs-overview.md) till Azure Sentinel med ett enda klick. Aktivitets loggen är en prenumerations logg som ger inblick i händelser på prenumerations nivå som inträffat i Azure. Detta omfattar en mängd data, från Azure Resource Manager användnings data till uppdateringar på Service Health händelser. Med hjälp av aktivitets loggen kan du bestämma vad, vem och när för alla Skriv åtgärder (skicka, skicka och ta bort) på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. Aktivitets loggen innehåller inte Läs-(GET)-åtgärder eller åtgärder för resurser som använder modellen klassisk/"RDFE". 


## <a name="prerequisites"></a>Förutsättningar

- Användare med deltagar behörighet till Log Analytics arbets yta 


## <a name="connect-to-azure-activity-log"></a>Anslut till Azure aktivitets logg

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Azure aktivitets logg** .

2. I fönstret Azure aktivitets logg väljer du de prenumerationer som du vill strömma till Azure Sentinel. 

3. Klicka på **Anslut**.

4. Om du vill använda det relevanta schemat i Log Analytics för aviseringar för Azure-aktivitet söker du efter **AzureActivity**.


 

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azures aktivitets logg till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
