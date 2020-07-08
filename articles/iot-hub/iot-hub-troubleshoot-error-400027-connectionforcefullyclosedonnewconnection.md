---
title: Fel sökning av Azure IoT Hub-fel 400027 ConnectionForcefullyClosedOnNewConnection
description: Förstå hur du åtgärdar fel 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960534"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

I den här artikeln beskrivs orsaker och lösningar för **400027 ConnectionForcefullyClosedOnNewConnection** -fel.

## <a name="symptoms"></a>Symtom

Din enhets-till-moln-dubbla åtgärd (till exempel Read eller patch rapporterade egenskaper) eller direkt metod anrop Miss lyckas med felkoden **400027**.

## <a name="cause"></a>Orsak

En annan klient skapade en ny anslutning till IoT Hub med samma autentiseringsuppgifter, så IoT Hub stängde den tidigare anslutningen. IoT Hub tillåter inte att fler än en klient ansluter med samma uppsättning autentiseringsuppgifter.

## <a name="solution"></a>Lösning

Se till att varje klient ansluter till IoT Hub med sin egen identitet.