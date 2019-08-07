---
title: Aktivera fusion i för hands versionen av Azure Sentinel | Microsoft Docs
description: Lär dig hur du aktiverar fusion i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780445"
---
# <a name="enable-fusion"></a>Aktivera fusion

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Machine Learning i Azure Sentinel är inbyggt direkt från början. Vi har utformat systemet med ML-innovationer som syftar till att göra säkerhetsanalytiker, säkerhets data forskare och ingenjörer produktiva. En sådan innovation är en inbyggd Azure Sentinel-fusion särskilt för att minska aviserings utmattningen.

Fusion använder sig av diagram drivna Machine Learning-algoritmer för att korrelera mellan miljon tals mindre åter givning av avvikande aktiviteter från olika produkter, till exempel Azure AD Identity Protection och Microsoft Cloud App Security, för att kombinera dem till ett hanterbart antal intressanta säkerhets incidenter.

## <a name="enable-fusion"></a>Aktivera fusion

1. I Azure Portal väljer du ikonen för att öppna Cloud Shell.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  I **Välkommen till Cloud Shell** fönster som öppnas nedan väljer du PowerShell.

3.  Välj den prenumeration där du distribuerade Azure Sentinel och **skapa lagring**.

4. När du har autentiserats och din Azure-enhet har skapats kör du följande kommandon i kommando tolken:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Inaktivera fusion

Följ samma procedur som ovan och kör följande kommando:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Visa status för fusion

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du aktiverar fusion i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

