---
title: Anslut Microsoft Defender för slut punkts data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Microsoft Defender för slut punkts data (tidigare Microsoft Defender ATP) till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 72b2ba0ea444fb14ef9fc1bc3ea6aea3654677df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655535"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Ansluta aviseringar från Microsoft Defender för slut punkt (tidigare Microsoft Defender ATP)

> [!IMPORTANT]
>
> - **Microsoft Defender för slut punkten** kallades tidigare **Microsoft Defender Advanced Threat Protection** eller **MDATP**.
>
>     Du kan se att det gamla namnet fortfarande används i produkten (inklusive dess data koppling i Azure Sentinel) under en viss tids period.
>
> - Inmatningen av Microsoft Defender för slut punkts varningar är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med [Microsoft Defender för slut punkts](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) koppling kan du strömma aviseringar från Microsoft Defender för slut punkt till Azure Sentinel. På så sätt kan du mer utförligt analysera säkerhets händelser i din organisation och skapa spel böcker för effektiv och omedelbar respons.

> [!NOTE]
>
> Om du vill mata in nya rå data loggar från Microsoft Defender för slut punktens [avancerade jakt](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)använder du den nya anslutningen för Microsoft 365 Defender (tidigare Microsoft Threat Protection, [Se dokumentationen](./connect-microsoft-365-defender.md)).

## <a name="prerequisites"></a>Förutsättningar

- Du måste ha en giltig licens för Microsoft Defender för slut punkt enligt beskrivningen i [Konfigurera Microsoft Defender för slut punkts distribution](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- Du måste vara global administratör eller en säkerhets administratör på Azure Sentinel-klienten.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Anslut till Microsoft Defender för slut punkt

Om Microsoft Defender för slut punkt distribueras och data matas in kan aviseringarna enkelt strömmas till Azure Sentinel.

1. I Azure Sentinel väljer du **data kopplingar**, väljer **Microsoft Defender för slut punkt** (kan fortfarande kallas *Microsoft Defender Avancerat skydd*) från galleriet och väljer **Öppna kopplings sida**.

1. Klicka på **Anslut**. 

1. Om du vill fråga Microsoft Defender efter slut punkts aviseringar i **loggar** anger du **SecurityAlert** i frågefönstret och lägger till ett filter där **Providerns namn** är **MDATP**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender för slut punkt till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).