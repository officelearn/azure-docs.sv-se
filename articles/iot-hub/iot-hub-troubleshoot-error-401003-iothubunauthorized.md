---
title: Felsökning av Azure IoT Hub-fel 401003 IoTHubUnauthorized
description: Förstå hur du åtgärdar fel 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284413"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

I den här artikeln beskrivs orsakerna och lösningarna för **401003 IoTHubUnauthorized-fel.**

## <a name="symptoms"></a>Symtom

### <a name="symptom-1"></a>Symptom 1

I diagnostikloggar ser du ett mönster av enheter som kopplar från med **401003 IoTHubUnauthorized**, följt av **404104 DeviceConnectionClosedRemotely**och sedan framgångsrikt ansluta strax efter.

### <a name="symptom-2"></a>Symptom 2

Begäranden till IoT Hub misslyckas med något av följande felmeddelanden:

* Auktoriseringshuvud saknas
* IotHub\*' ' innehåller inte den\*angivna enheten ' ' '
* Auktoriseringsregel '\*'\*tillåter inte åtkomst för ' ' '
* Autentiseringen misslyckades för den här enheten, förnyar token eller certifikat och återansluter
* Tumavtryck matchar inte konfigurationen: Tumavtryck: SHA1Hash=\*, SHA2Hash=\*; Konfiguration: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1

För MQTT är vissa SDK:er beroende av IoT Hub för att utfärda frånkopplingen när SAS-token upphör att veta när den ska uppdateras. Så 

1. SAS-token upphör att gälla
1. IoT Hub märker utgångsdatumet och kopplar enheten från **401003 IoTHubUnauthorized**
1. Enheten slutför frånkopplingen med **404104 DeviceConnectionClosedRemotely**
1. IoT SDK genererar en ny SAS-token
1. Enheten återansluter med IoT Hub framgångsrikt

### <a name="cause-2"></a>Orsak 2

IoT Hub kunde inte autentisera auth-huvudet, regeln eller nyckeln.

## <a name="solution"></a>Lösning

### <a name="solution-1"></a>Lösning 1

Ingen åtgärd behövs om du använder IoT SDK för anslutning med hjälp av enhetens anslutningssträng. IoT SDK återskapar den nya token för att återansluta på SAS token förfallodatum. 

Om felvolymen är ett problem växlar du till C SDK, som förnyar SAS-token före förfallodatum. Dessutom kan SAS-token uppdateras för AMQP utan frånkoppling.

### <a name="solution-2"></a>Lösning 2

I allmänhet bör felmeddelandet som presenteras förklara hur du åtgärdar felet. Om du av någon anledning inte har tillgång till felmeddelandets information kontrollerar du att:

- SAS eller annan säkerhetstoken som du använder har inte upphört att gälla. 
- Behörighetsautentiseringsuppgifterna är väl utformad för det protokoll som du använder. Mer information finns i [IoT Hub-åtkomstkontroll](iot-hub-devguide-security.md).
- Den auktoriseringsregel som används har behörigheten för den begärda åtgärden.

## <a name="next-steps"></a>Nästa steg

För att göra det enklare att autentisera till IoT Hub rekommenderar vi att du använder [Azure IoT SDK:er](iot-hub-devguide-sdks.md).