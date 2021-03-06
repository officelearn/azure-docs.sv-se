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
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506336"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

I den här artikeln beskrivs orsaker och lösningar för **400027 ConnectionForcefullyClosedOnNewConnection** -fel.

## <a name="symptoms"></a>Symtom

Enheten är frånkopplad med **Communication_Error** som **ConnectionStatusChangeReason** med hjälp av transport typen .NET SDK och MQTT.

Din enhets-till-moln-dubbla åtgärd (till exempel Read eller patch rapporterade egenskaper) eller direkt metod anrop Miss lyckas med felkoden **400027**.

## <a name="cause"></a>Orsak

En annan klient skapade en ny anslutning till IoT Hub med samma autentiseringsuppgifter, så IoT Hub stängde den tidigare anslutningen. IoT Hub tillåter inte att fler än en klient ansluter med samma uppsättning autentiseringsuppgifter.

## <a name="solution"></a>Lösning

Se till att varje klient ansluter till IoT Hub med sin egen identitet.