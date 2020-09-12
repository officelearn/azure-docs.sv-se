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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657548"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Ansluta aviseringar från Microsoft Defender för slut punkt (tidigare Microsoft Defender ATP) 


> [!IMPORTANT]
> Inmatningen av Microsoft Defender för slut punkts varningar är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 

Med [Microsoft Defender för slut punkts](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) koppling kan du strömma aviseringar från Microsoft Defender för slut punkt till Azure Sentinel. På så sätt kan du mer utförligt analysera säkerhets händelser i din organisation och skapa spel böcker för effektiv och omedelbar respons.

## <a name="prerequisites"></a>Krav

- Du måste ha en giltig licens för Microsoft Defender för slut punkt enligt beskrivningen i [Konfigurera Microsoft Defender för slut punkts distribution](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing). 
- Du måste vara administratör eller säkerhets administratör på Azure Sentinel-klienten.


## <a name="connect-to-microsoft-defender-for-endpoint"></a>Anslut till Microsoft Defender för slut punkt

Om Microsoft Defender för slut punkt distribueras och data matas in kan aviseringarna enkelt strömmas till Azure Sentinel.


1. I Azure Sentinel väljer du **data kopplingar**, väljer **Microsoft Defender för slut punkt** (kan fortfarande kallas Microsoft Defender Avancerat skydd) från galleriet och väljer **Öppna kopplings sida**.
1. Klicka på **Anslut**. 
1. Om du vill använda det relevanta schemat i Log Analytics för Defender ATP-aviseringar kan du söka efter **SecurityAlert** och **Providerns namn** är **MDATP**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Microsoft Defender för slut punkt till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
