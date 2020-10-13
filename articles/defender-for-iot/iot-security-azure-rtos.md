---
title: Säkerhets modul för Azure återställnings tider-översikt
description: Lär dig mer om säkerhetsmodulen för Azure återställnings tider support och implementering som en del av tjänsten Defender for IoT
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 22bd12bbdcccef2fd0e9010f926cd18e95d42967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761863"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Översikt: Defender för IoT Security Module för Azure återställnings tider (för hands version)

Återställnings tider för Defender for IoT för innehåller en omfattande säkerhetslösning för Azure återställnings tider-enheter. Azure återställnings tider levereras nu med Azure IoT-säkerhetsmodulen inbyggd och ger täckning för vanliga hot och potentiella skadliga aktiviteter i real tids operativ system enheter. 

![Defender för IoT Azure-återställnings tider](./media/architecture/azure-rtos-security-monitoring.png)


Säkerhetsmodulen för Azure återställnings tider erbjuder följande funktioner: 
- Identifiering av skadlig nätverks aktivitet
- Anpassad avisering baserad, enhets beteende bas linje
- Förbättra enhetens säkerhets hygien

## <a name="detection-of-malicious-network-activities"></a>Identifiering av skadliga nätverks aktiviteter

Inkommande och utgående nätverks aktivitet för varje enhet övervakas (protokoll som stöds: TCP, UDP, ICMP på IPv4 och IPv6). Defender för IoT kontrollerar var och en av dessa nätverks aktiviteter mot Microsoft Threat Intelligence-flödet. Flödet uppdateras i real tid med miljon tals unika hot indikatorer som samlas in över hela världen. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Enhets beteende bas linje baserat på anpassade aviseringar

Bas linje tillåter klustring av enheter till säkerhets grupper och definierar förväntat beteende för varje grupp. Eftersom IoT-enheter vanligt vis är utformade för att fungera i väldefinierade och begränsade scenarier, är det enkelt att skapa en bas linje som definierar förväntat beteende med en uppsättning parametrar. Eventuella avvikelser från bas linjen utlöser en avisering. 

## <a name="improve-your-device-security-hygiene"></a>Förbättra din enhets säkerhets hygien

Genom att använda Rekommenderad infrastruktur Defender för IoT får du kunskap och information om problem i miljön som påverkar och skadar position på dina enheter. Svag IoT Device Security-position kan ge potentiella attacker att lyckas om det inte har ändrats, eftersom säkerhet alltid mäts av den svagaste länken i en organisation. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Kom igång med att skydda Azure återställnings tider-enheter

Säkerhetsmodulen för Azure återställnings tider tillhandahålls som kostnads fri nedladdning för dina enheter. Tjänsten Defender för IoT Cloud är tillgänglig med en 30-dagars utvärderings version per Azure-prenumeration. Kom igång genom att ladda ned [säkerhetsmodulen för Azure återställnings tider](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) . 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om säkerhetsmodulen för Azure återställnings tider-tjänsten. Mer information om säkerhetsmodulen och hur du kommer igång finns i följande artiklar:

- [Koncept i Azure återställnings tider IoT Security Module](concept-rtos-security-module.md)
- [Snabb start: Azure återställnings tider IoT-säkerhetsmodul](quickstart-azure-rtos-security-module.md)
