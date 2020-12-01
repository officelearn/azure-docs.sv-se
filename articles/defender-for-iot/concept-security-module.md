---
title: Säkerhetsmodulen och enheten är dubbla
description: Lär dig mer om begreppet säkerhetsmoduls dubbla och hur de används i Defender för IoT.
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
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: b33c456d47426a3721e8582f24ffd603db0429c9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340041"
---
# <a name="security-module"></a>Säkerhetsmodul

I den här artikeln förklaras hur Defender för IoT använder enhets dubbla och moduler.

## <a name="device-twins"></a>Enhets dubbla

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering.

Defender för IoT erbjuder fullständig integrering med din befintliga plattform för IoT-enhets hantering, så att du kan hantera din enhets säkerhets status och använda befintliga funktioner för enhets kontroll. Integreringen uppnås genom att använda den IoT Hub dubbla mekanismen.

Lär dig mer om begreppet [enhets enheter](../iot-hub/iot-hub-devguide-device-twins.md) i Azure IoT Hub.

## <a name="security-module-twins"></a>Säkerhetsmodul, dubbla

Defender för IoT upprätthåller en säkerhetsmodul för varje enhet i tjänsten.
Säkerhetsmodulen är dubbelt innehåller all information som är relevant för enhets säkerhet för varje enskild enhet i din lösning.
Egenskaperna för enhets säkerhet underhålls i en dedikerad säkerhetsmodul för säkrare kommunikation och för att aktivera uppdateringar och underhåll som kräver färre resurser.

Mer information om hur du skapar, anpassar och konfigurerar den dubbla finns i [skapa säkerhetsmodul](quickstart-create-security-twin.md) , grupp två och [Konfigurera säkerhets agenter](how-to-agent-configuration.md) . Mer information om hur modulen finns i IoT Hub finns i [förstå modulernas dubblare](../iot-hub/iot-hub-devguide-module-twins.md) .

## <a name="see-also"></a>Se även

- [Översikt över Defender för IoT](overview.md)
- [Distribuera säkerhetsagenter](how-to-deploy-agent.md)
- [Autentiseringsmetoder för säkerhets agent](concept-security-agent-authentication-methods.md)