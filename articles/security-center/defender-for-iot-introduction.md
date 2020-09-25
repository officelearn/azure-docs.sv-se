---
title: Azure Defender för IoT
description: Lär dig mer om Azure Defender för IoT
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 307916680d9a05a5083aea057b2ef4b855a4ff57
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301813"
---
# <a name="introduction-to-azure-defender-for-iot"></a>Introduktion till Azure Defender för IoT

Förena säkerhets hanteringen och aktivera hot identifiering och analys från slut punkt till slut punkt och analys över hybrid moln arbets belastningar och din Azure IoT-lösning.

Azure Defender för IoT ger:

- **Till gångs identifiering och nätverks mappning**, inklusive information som enhets tillverkare, enhets typ och hur enheter kommunicerar i nätverket
- **Sårbarhets hantering**, inklusive information om CVEs, öppna portar och obehöriga Internet anslutningar
- **Kontinuerlig hot övervakning**, med real tids aviseringar som visar eventuella avvikande eller otillåten aktivitet, till exempel riktade attacker eller skadlig kod

Fullständig information finns i [den dedikerade dokumentationen](https://docs.microsoft.com/azure/asc-for-iot/overview).

## <a name="availability"></a>Tillgänglighet
|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|Kräver [Azure Defender](security-center-pricing.md)|
|Nödvändiga roller och behörigheter:|Skriv behörighet för datorns NSG: er|
|Moln|![Yes](./media/icons/yes-icon.png) Kommersiella moln<br>![Yes](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-devices-can-azure-defender-for-iot-secure"></a>Vilka enheter kan Azure Defender för IoT säkra?
Genom att kombinera funktionerna i Security Center-, Azure Defender-och CyberX-teknik utan agent kan du skydda:

- **Bygg-enheter** som är anslutna via IoT Hub. Detta gör det möjligt för organisationer att påskynda sina IoT-initiativ genom att säkra både moderna, Internet-inhemska enheter och traditionella ICS/SCADA-enheter med en enda enhetlig lösning.
    - Publicera nya enheter och Använd säkerhets principer för dina arbets belastningar (löv enheter, Microsoft Edge-enheter IoT Hub) för att säkerställa efterlevnaden av säkerhets standarder och förbättrad säkerhets position.

- **Ohanterade brownfield enheter** som används i miljöer med drift teknik, till exempel tillverkning, byggnads hanterings system (BMS), bio vetenskap, energi-och vatten verktyg, olja & gas och logistik. 
    - För att skydda ohanterade enheter använder Azure Defender för IoT en lokal sensor för passiv, icke-invasiv NTA (Network Traffic Analysis) som har noll prestanda påverkan på miljöiska miljöer. 
    - Den införlivar också en djupgående förståelse av specialiserade IoT/miljöiska protokoll – kombinerat med patenterad, IoT/miljö medveten beteende analys och maskin inlärning – för att eliminera behovet av att konfigurera regler eller signaturer, vilket resulterar i vanliga distributioner på några minuter eller timmar i stället för dagar eller veckor. 


## <a name="azure-defender-for-iot-integration-with-azure-sentinel"></a>Azure Defender för IoT-integrering med Azure Sentinel
Om du vill aktivera enhetlig IT/skydds övervakning och styrning kan Azure Defender för IoT integreras internt med [Azure Sentinel](../sentinel/overview.md).

SecOps team kan:

- Upptäcka och reagera snabbt på IoT/datahoten via spel böcker-/regionsspecifika SOAR-som ingår i Sentinel
- Dra nytta av en ständigt uppdaterad IoT/lättbar Hot information som tillhandahålls av avsnitt 52, Microsofts interna IoT/InStore Threat Intelligence-team
- Integrera Azure Defender för IoT med befintliga SOC-arbetsflöden och säkerhets verktyg från tredje part, till exempel Splunk, IBM QRadar och ServiceNow


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Defender för IoT i Azure Security Center. Mer information finns i:

- [Vi presenterar Azure Security Center for IoT](../asc-for-iot/overview.md)
