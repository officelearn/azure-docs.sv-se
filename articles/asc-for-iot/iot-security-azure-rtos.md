---
title: Säkerhets modul för Azure återställnings tider-översikt
description: Lär dig mer om säkerhetsmodulen för Azure återställnings tider support och implementering som en del av tjänsten Azure Security Center for IoT.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514483"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>Översikt: säkerhetsmodul för Azure-återställnings tider (för hands version)

Azure Security Center för IoT återställnings tider Security-modulen innehåller en omfattande säkerhetslösning för Azure återställnings tider-enheter. Azure återställnings tider levereras nu med Azure IoT-säkerhetsmodulen inbyggd och ger täckning för vanliga hot och potentiella skadliga aktiviteter i real tids operativ system enheter. 

![Azure Security Center för IoT Azure-återställnings tider](./media/architecture/azure-rtos-security-monitoring.png)


Säkerhetsmodulen för Azure återställnings tider erbjuder följande funktioner: 
- Identifiering av skadlig nätverks aktivitet
- Anpassad avisering baserad, enhets beteende bas linje
- Förbättra enhetens säkerhets hygien

### <a name="detection-of-malicious-network-activities"></a>Identifiering av skadliga nätverks aktiviteter

Inkommande och utgående nätverks aktivitet för varje enhet övervakas (protokoll som stöds: TCP, UDP, ICMP på IPv4 och IPv6). Azure Security Center for IoT kontrollerar var och en av dessa nätverks aktiviteter mot Microsoft Threat Intelligence-flödet. Flödet uppdateras i real tid med miljon tals unika hot indikatorer som samlas in över hela världen. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Enhets beteende bas linje baserat på anpassade aviseringar

Bas linje tillåter klustring av enheter till säkerhets grupper och definierar förväntat beteende för varje grupp. Eftersom IoT-enheter vanligt vis är utformade för att fungera i väldefinierade och begränsade scenarier, är det enkelt att skapa en bas linje som definierar förväntat beteende med en uppsättning parametrar. Eventuella avvikelser från bas linjen utlöser en avisering. 

### <a name="improve-your-device-security-hygiene"></a>Förbättra din enhets säkerhets hygien

Genom att använda den rekommenderade infrastruktur Azure Security Center för IoT får du kunskap och insikter om problem i miljön som påverkar och skadar enhetens position. Svag IoT Device Security-position kan ge potentiella attacker att lyckas om det inte har ändrats, eftersom säkerhet alltid mäts av den svagaste länken i en organisation. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Kom igång med att skydda Azure återställnings tider-enheter

Säkerhetsmodulen för Azure återställnings tider tillhandahålls som kostnads fri nedladdning för dina enheter. Azure Security Center för IoT Cloud service är tillgänglig med en 30-dagars utvärderings version per Azure-prenumeration. Kom igång genom att ladda ned [säkerhetsmodulen för Azure återställnings tider](https://github.com/azure-rtos/iot-security-module-preview) . 


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om säkerhetsmodulen för Azure återställnings tider-tjänsten. Mer information om säkerhetsmodulen och hur du kommer igång finns i följande artiklar:

- [Koncept i Azure återställnings tider IoT Security Module](concept-rtos-security-module.md)
- [Snabb start: Azure återställnings tider IoT-säkerhetsmodul](quickstart-azure-rtos-security-module.md)


