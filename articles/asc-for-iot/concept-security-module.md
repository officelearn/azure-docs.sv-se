---
title: Förstå Azure Security Center för IoT-säkerhetsmodultvillingar| Microsoft-dokument
description: Lär dig mer om konceptet med tvillingar i säkerhetsmodulen och hur de används i Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596492"
---
# <a name="security-module"></a>Säkerhetsmodul


I den här artikeln beskrivs hur Azure Security Center för IoT använder enhetstvillingar och -moduler. 

## <a name="device-twins"></a>Enhetstvillingar

För IoT-lösningar som är inbyggda i Azure spelar enhetstvillingar en nyckelroll i både enhetshantering och processautomatisering.  

Azure Security Center för IoT erbjuder fullständig integrering med din befintliga IoT-enhetshanteringsplattform, så att du kan hantera enhetens säkerhetsstatus samt använda befintliga enhetskontrollfunktioner. Integration uppnås genom att använda sig av IoT Hub twin mekanism.  

Läs mer om konceptet [med enhetstvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) i Azure IoT Hub. 

## <a name="security-module-twins"></a>Tvillingar för säkerhetsmodul

Azure Security Center för IoT underhåller en säkerhetsmodultvilling för varje enhet i tjänsten.
Säkerhetsmodultvillingen innehåller all information som är relevant för enhetssäkerheten för varje specifik enhet i din lösning.
Enhetssäkerhetsegenskaper finns i en dedikerad säkerhetsmodultvilling för säkrare kommunikation och för att möjliggöra uppdateringar och underhåll som kräver färre resurser.  

Se [Skapa tvilling-](quickstart-create-security-twin.md) och konfigurera [säkerhetsagenter](how-to-agent-configuration.md) för att lära dig hur du skapar, anpassar och konfigurerar tvillingen. Se [Förstå modultvillingar](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) om du vill veta mer om begreppet modultvillingar i IoT Hub. 
 

## <a name="see-also"></a>Se även
- [Översikt över Azure Security Center för IoT](overview.md)
- [Distribuera säkerhetsagenter](how-to-deploy-agent.md)
- [Autentiseringsmetoder för säkerhetsagenter](concept-security-agent-authentication-methods.md)