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
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756349"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Ansluta varningar från Microsoft Defender Advanced Threat Protection 


> [!IMPORTANT]
> Inmatning av Microsoft Defender Advanced Threat Protection-aviseringar är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

[Med Microsoft Defender Advanced Threat Protection-kopplingen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) kan du strömma aviseringar från Microsoft Defender Advanced Threat Protection till Azure Sentinel. Detta gör att du kan mer omfattande analysera säkerhetshändelser i hela organisationen och skapa spelböcker för effektiv och omedelbar respons.

## <a name="prerequisites"></a>Krav

- Du måste ha en giltig licens för Microsoft Defender Advanced Threat Protection, enligt beskrivningen i [Konfigurera Microsoft Defender ATP-distribution](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
