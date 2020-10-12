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
ms.openlocfilehash: a5382313c837482f116f498f3a05c36447062b0a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90942197"
---
# <a name="security-module"></a>Säkerhetsmodul

I den här artikeln förklaras hur Defender för IoT använder enhets dubbla och moduler.

## <a name="device-twins"></a>Enhets dubbla

För IoT-lösningar som är inbyggda i Azure spelar enheten upp en viktig roll i både enhets hantering och process automatisering.

Defender för IoT erbjuder fullständig integrering med din befintliga plattform för IoT-enhets hantering, så att du kan hantera din enhets säkerhets status och använda befintliga funktioner för enhets kontroll. Integreringen uppnås genom att använda den IoT Hub dubbla mekanismen.

Lär dig mer om begreppet [enhets enheter](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) i Azure IoT Hub.

## <a name="security-module-twins"></a>Säkerhetsmodul, dubbla

Defender för IoT upprätthåller en säkerhetsmodul för varje enhet i tjänsten.
Säkerhetsmodulen är dubbelt innehåller all information som är relevant för enhets säkerhet för varje enskild enhet i din lösning.
Egenskaperna för enhets säkerhet underhålls i en dedikerad säkerhetsmodul för säkrare kommunikation och för att aktivera uppdateringar och underhåll som kräver färre resurser.

Mer information om hur du skapar, anpassar och konfigurerar den dubbla finns i [skapa säkerhetsmodul](quickstart-create-security-twin.md) , grupp två och [Konfigurera säkerhets agenter](how-to-agent-configuration.md) . Mer information om hur modulen finns i IoT Hub finns i [förstå modulernas dubblare](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) .

## <a name="see-also"></a>Se även

- [Översikt över Defender för IoT](overview.md)
- [Distribuera säkerhetsagenter](how-to-deploy-agent.md)
- [Autentiseringsmetoder för säkerhets agent](concept-security-agent-authentication-methods.md)
