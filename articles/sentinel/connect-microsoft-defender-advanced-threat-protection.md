---
title: Anslut Microsoft Defender ATP-data till Azure Sentinel | Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756349"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>Ansluta aviseringar från Microsoft Defender Avancerat skydd 


> [!IMPORTANT]
> Inmatningen av aviseringar från Microsoft Defender Avancerat skydd finns för närvarande i offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Med [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) -anslutningen kan du strömma aviseringar från Microsoft Defender Avancerat skydd till Azure Sentinel. På så sätt kan du mer utförligt analysera säkerhets händelser i din organisation och skapa spel böcker för effektiv och omedelbar respons.

## <a name="prerequisites"></a>Krav

- Du måste ha en giltig licens för Microsoft Defender Avancerat skydd, enligt beskrivningen i [Konfigurera Microsoft Defender ATP-distribution](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
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
