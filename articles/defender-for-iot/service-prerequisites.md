---
title: Komponenter & krav
description: Information om allt som behövs för att komma igång med krav för Azure Defender för IoT-tjänster.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089187"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Krav för Azure Defender för IoT

Den här artikeln innehåller en förklaring av de olika komponenterna i Defender for IoT-tjänsten, vad du behöver för att börja och beskriver de grundläggande begreppen för att förstå tjänsten.

## <a name="minimum-requirements"></a>Minimikrav

- Övervakning utan agent för IoT och enheter (baserat på CyberX-teknik)
    - Nätverks växlar som stöder trafik övervakning via SPAN-port
    - Maskin varu apparater för NTA-sensor, mer information finns i [certifierad maskin vara](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Rollen Azure-prenumerations **deltagare** (krävs endast under onboarding för att definiera allokerade enheter)
    - IoT Hub (kostnads fri eller standard nivå) **deltagar** roll (för moln ansluten hantering)
- Säkerhet för hanterade IoT-enheter som hanteras via Azure IoT Hub
    - IoT Hub (standard-nivå) **deltagar** rollen
    - IoT Hub: växlings funktionen **för Azure Defender för IoT** måste vara aktive rad
    - För stöd för säkerhetsmodulen på enhets nivå  
        - Defender för IoT-agenter har stöd för en växande lista över enheter och plattformar, se [listan över plattformar som stöds](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>Service regioner som stöds

Mer information finns i [IoT Hub regioner som stöds](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) . 

Defender för IoT dirigerar all trafik från alla europeiska regioner till regionala Data Center i västra Europa och alla återstående regioner till det centrala amerikanska regionala data centret.

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure IoT-säkerhet
- Lär dig hur du [aktiverar tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar om Defender för IoT](resources-frequently-asked-questions.md)
- Utforska hur du [förstår Defender för IoT-aviseringar](concept-security-alerts.md)
