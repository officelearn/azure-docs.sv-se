---
title: Felsökning av Azure IoT Hub-fel 400027 ConnectionForcefullyClosedOnNewConnection
description: Förstå hur du åtgärdar fel 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960534"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

I den här artikeln beskrivs orsakerna och lösningarna för **400027 ConnectionForcefullyClosedOnNewConnection-fel.**

## <a name="symptoms"></a>Symtom

Din dubbelåtgärd från enhet till moln (t.ex. läs- eller korrigeringsrapporterade egenskaper) eller direkt metodanrop misslyckas med felkoden **400027**.

## <a name="cause"></a>Orsak

En annan klient skapade en ny anslutning till IoT Hub med samma autentiseringsuppgifter, så IoT Hub stängde den tidigare anslutningen. IoT Hub tillåter inte att fler än en klient ansluter med samma uppsättning autentiseringsuppgifter.

## <a name="solution"></a>Lösning

Se till att varje klient ansluter till IoT Hub med sin egen identitet.