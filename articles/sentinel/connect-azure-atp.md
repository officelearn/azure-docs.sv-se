---
title: Anslut Microsoft Defender för identitets-data (tidigare Azure ATP) till Azure Sentinel | Microsoft Docs
description: Lär dig att strömma loggar från Microsoft Defender för identitet (tidigare Azure Advanced Threat Protection) (ATP) till Azure Sentinel med ett enda klick.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 319e0d29ef297f2a675128d498820a2d5b8ac42e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579529"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>Anslut data från Microsoft Defender för identitet (tidigare Azure Advanced Threat Protection)

> [!IMPORTANT]
> Microsoft Defender för identitets data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma loggar från [Microsoft Defender för identitet](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) i Azure Sentinel med ett enda klick.

## <a name="prerequisites"></a>Förutsättningar

- Användare med behörighet som global administratör eller säkerhets administratör
- Du måste vara en för hands version av Microsoft Defender för identitet och aktivera integrering mellan Microsoft Defender för identitets-och Microsoft Cloud App Security. Mer information finns i [Microsoft Defender för identitets integrering](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

## <a name="connect-to-microsoft-defender-for-identity"></a>Anslut till Microsoft Defender för identitet

Kontrol lera att Microsoft Defender för hands version av Identity är [aktiverat i nätverket](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Om Microsoft Defender för identitet distribueras och data matas in kan de misstänkta aviseringarna enkelt strömmas i Azure Sentinel. Det kan ta upp till 24 timmar innan aviseringarna börjar strömma till Azure Sentinel.


1. Om du vill ansluta Microsoft Defender för identitet till Azure Sentinel måste du först aktivera integrering mellan Microsoft Defender för identitets-och Microsoft Cloud App Security. Information om hur du gör detta finns i [Microsoft Defender för identitets integrering](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection).

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Microsoft Defender för identitet (för hands version)** .

1. Du kan välja om du vill att aviseringar från Microsoft Defender för identitet automatiskt ska generera incidenter i Azure Sentinel automatiskt. Under **skapa incidenter** väljer du **Aktivera** för att aktivera den standard analys regel som skapar incidenter automatiskt från aviseringar som genereras i den anslutna säkerhets tjänsten. Du kan sedan redigera den här regeln under **analys** och sedan **aktiva regler**.

1. Klicka på **Anslut**.

1. Om du vill använda det relevanta schemat i Log Analytics för Microsoft Defender för identitets varningar söker du efter **SecurityAlert**.

> [!NOTE]
> Om aviseringarna är större än 30 KB slutar Azure Sentinel att visa fältet entiteter i aviseringarna.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender för identitet till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

