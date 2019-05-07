---
title: Aktivera fusion i förhandsversionen av Azure Sentinel | Microsoft Docs
description: Lär dig hur du aktiverar fusion i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: bf9a78006d13614a73a3fccfc57f28ce850456d9
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204477"
---
# <a name="enable-fusion"></a>Aktivera fusion

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning i Azure Sentinel är inbyggda direkt från början. Vi har Smart utformad systemet med ML-innovationer som syftar till att göra säkerhetsanalytiker, security dataforskare och ingenjörer produktiv. En sådan innovation är Azure Sentinel Fusion särskilt för att minska avisering utmattning.

Fusion använder graph drivs machine learning-algoritmer för att korrelera mellan flera miljoner lägre exakthet avvikande aktiviteter från olika produkter, till exempel Azure AD Identity Protection och Microsoft Cloud App Security, att kombinera dem till ett hanterbara antal intressant security fall.

## <a name="enable-fusion"></a>Aktivera fusion

1. Välj ikonen för att öppna Cloud Shell i Azure-portalen.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  I den **Välkommen till Cloud Shell** fönster som öppnas nedanför, Välj PowerShell.

3.  Välj den prenumeration som du har distribuerat Azure Sentinel och **skapa lagring**.

4. När du är autentiserad och Azure enheten har skapats, Kommandotolken, kör du följande kommandon:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Inaktivera fusion

Följ samma steg som ovan och kör följande kommando:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Visa status för fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du aktiverar Fusion i Azure-Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

