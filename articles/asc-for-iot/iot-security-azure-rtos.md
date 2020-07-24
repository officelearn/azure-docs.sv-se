---
title: Stöd för Azure återställnings tider
description: Läs om support för Azure återställnings tider i Azure Security Center for IoT-tjänsten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099751"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Azure Security Center for IoT Security-lösningen för Azure återställnings tider 

Azure Security Center för IoT-säkerhetsmodulen innehåller en omfattande säkerhets lösning för Azure återställnings tider-enheter. Azure återställnings tider levereras med en inbyggd säkerhetsmodul som täcker vanliga hot om operativ system enheter i real tid. 

![Azure Security Center för IoT Azure-återställnings tider](./media/architecture/azure-rtos-security-monitoring.png)


Azure Security Center för IoT-säkerhetsmodulen med stöd för Azure återställnings tider erbjuder följande funktioner: 
- Identifiering av skadlig nätverks aktivitet
- Anpassad avisering baserad, enhets beteende bas linje
- Förbättra enhetens säkerhets hygien

### <a name="detection-of-malicious-network-activities"></a>Identifiering av skadliga nätverks aktiviteter

Inkommande och utgående nätverks aktivitet för varje enhet övervakas (protokoll som stöds: TCP, UDP, ICMP på IPv4 och IPv6). Azure Security Center for IoT kontrollerar var och en av dessa nätverks aktiviteter mot Microsoft Threat Intelligence-flödet. Flödet uppdateras i real tid med miljon tals unika hot indikatorer som samlas in över hela världen. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Enhets beteende bas linje baserat på anpassade aviseringar

Bas linje tillåter klustring av enheter till säkerhets grupper och definierar förväntat beteende för varje grupp. Eftersom IoT-enheter vanligt vis är utformade för att fungera i väldefinierade och begränsade scenarier, är det enkelt att skapa en bas linje som definierar förväntat beteende med en uppsättning parametrar. Eventuella avvikelser från bas linjen utlöser en avisering. 

### <a name="improve-your-device-security-hygiene"></a>Förbättra din enhets säkerhets hygien

Genom att använda den rekommenderade infrastruktur Azure Security Center för IoT får du kunskap och insikter om problem i miljön som påverkar och skadar enhetens position. Låg IoT Device Security-position kan ge potentiella attacker att lyckas om den blir oförändrad, eftersom säkerhet alltid mäts av den svagaste länken i en organisation. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Kom igång med att skydda Azure återställnings tider-enheter

- Azure Security Center för IoT-säkerhetsmodulen för Azure återställnings tider tillhandahålls som kostnads fri nedladdning för dina enheter. Azure Security Center för IoT Cloud service är tillgänglig med en 30-dagars utvärderings version per Azure-prenumeration. Hämta [Azure Security Center för IoT-säkerhetsmodulen för Azure återställnings tider](https://github.com/azure-rtos/iot-security-module-preview) för att komma igång. 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Azure Security Center för IoT Azure återställnings tider-support. Information om hur du kommer igång och aktiverar din säkerhets lösning i IoT Hub finns i följande artiklar:

- [Förutsättningar för tjänsten](service-prerequisites.md)
- [Komma igång](getting-started.md)
- [Konfigurera lösningen](quickstart-configure-your-solution.md)
- [Aktivera säkerhet i IoT Hub](quickstart-onboard-iot-hub.md)
- [Vanliga frågor och svar om Azure Security Center för IoT](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT-säkerhetsaviseringar](concept-security-alerts.md)
