---
title: Anslut Microsoft Defender ATP-data till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Microsoft Defender Advanced Threat Protection-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256752"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Ansluta aviseringar från Microsoft Defender Avancerat skydd 


> [!IMPORTANT]
> Det finns för närvarande en offentlig för hands version av loggar för att mata in Microsoft Defender Advanced Threat Protection.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Du kan strömma aviseringar från [Microsoft Defender Avancerat skydd](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) till Azure Sentinel med ett enda klick. Med den här anslutningen kan du strömma aviseringar från Microsoft Defender Avancerat skydd till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Giltig licens för Microsoft Defender Avancerat skydd som är aktiverat enligt beskrivningen i [validera licensierings etablering och fullständig konfiguration för Microsoft Defender Avancerat skydd](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Du måste vara administratör eller säkerhets administratör på Azure Sentinel-klienten.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Anslut till Microsoft Defender Avancerat skydd

Om Microsoft Defender Avancerat skydd distribueras och data matas in kan aviseringarna enkelt strömmas till Azure Sentinel.


1. I Azure Sentinel väljer du **data kopplingar**, klickar på panelen **Microsoft Defender Avancerat skydd** och väljer **Öppna kopplings sida**.
1. Klicka på **Anslut**. 
1. Om du vill använda det relevanta schemat i Log Analytics för Defender ATP-aviseringar kan du söka efter **SecurityAlert** och **Providerns namn** är **MDATP**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender ATP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
