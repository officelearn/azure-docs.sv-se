---
title: Ansluta Microsoft Defender ATP-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Microsoft Defender Advanced Threat Protection-data till Azure Sentinel.
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588220"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Ansluta varningar från Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> Inmatning av Microsoft Defender Advanced Threat Protection loggar är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Du kan strömma aviseringar från [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) till Azure Sentinel med ett enda klick. Med den här anslutningen kan du strömma aviseringarna från Microsoft Defender Advanced Threat Protection till Azure Sentinel. 

## <a name="prerequisites"></a>Krav

- Giltig licens för Microsoft Defender Advanced Threat Protection som är aktiverad enligt beskrivningen i [Validera licensieringsetablering och fullständig uppsättning för Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Du måste vara administratör eller säkerhetsadministratör på Azure Sentinel-klienten.


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>Ansluta till Microsoft Defender avancerat hotskydd

Om Microsoft Defender Advanced Threat Protection distribueras och intag av dina data kan aviseringarna enkelt strömmas till Azure Sentinel.


1. Välj **Datakopplingar**i Azure Sentinel, klicka på panelen **Microsoft Defender Advanced Threat Protection** och välj Öppna **kopplingssida**.
1. Klicka på **Anslut**. 
1. Om du vill använda det relevanta schemat i Log Analytics för Defender ATP-aviseringar söker du efter **SecurityAlert** och **providernamnet** är **MDATP**.




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender ATP till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
