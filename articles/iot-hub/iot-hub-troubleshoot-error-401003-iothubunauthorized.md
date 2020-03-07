---
title: Fel sökning av Azure IoT Hub-fel 401003 IoTHubUnauthorized
description: Förstå hur du åtgärdar fel 401003 IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396487"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

I den här artikeln beskrivs orsaker och lösningar för **401003 IoTHubUnauthorized** -fel.

## <a name="symptoms"></a>Symtom

### <a name="symptom-1"></a>Symptom 1

I diagnostikloggar ser du ett mönster för enheter som kopplar från med **401003 IoTHubUnauthorized**, följt av **404104 DeviceConnectionClosedRemotely**och ansluter sedan strax efter.

### <a name="symptom-2"></a>Symptom 2

Begär anden till IoT Hub fungerar inte med något av följande fel meddelanden:

* Authorization-huvudet saknas
* IotHub '\*' innehåller inte den angivna enheten '\*'
* Auktoriseringsregeln "\*" tillåter inte åtkomst för "\*"
* Autentiseringen misslyckades för den här enheten, förnya token eller certifikat och återanslut
* Tumavtrycket matchar inte konfigurationen: tumavtryck: SHA1Hash =\*, SHA2Hash =\*; Konfiguration: PrimaryThumbprint =\*, SecondaryThumbprint =\*

## <a name="cause"></a>Orsak

### <a name="cause-1"></a>Orsak 1

Vissa SDK: er förlitar sig på IoT Hub att utfärda från koppling när SAS-token upphör att veta när de ska uppdateras. Därför 

1. SAS-token upphör att gälla
1. IoT Hub meddelar förfallo datumet och kopplar från enheten med **401003 IoTHubUnauthorized**
1. Enheten Slutför från koppling med **404104 DeviceConnectionClosedRemotely**
1. IoT SDK genererar en ny SAS-token
1. Enheten återansluter med IoT Hub

### <a name="cause-2"></a>Orsak 2

IoT Hub kunde inte autentisera auth-huvudet, regeln eller nyckeln.

## <a name="solution"></a>Lösning

### <a name="solution-1"></a>Lösning 1

Ingen åtgärd krävs om du använder IoT SDK för anslutning med enhets anslutnings strängen. IoT SDK återskapar den nya token för att återansluta vid förfallo datum för SAS-token. 

Om det är ett problem med fel volymen växlar du till C SDK, som förnyar SAS-token innan det går ut. För AMQP kan SAS-token Dessutom uppdateras utan från koppling.

### <a name="solution-2"></a>Lösning 2

I allmänhet bör det fel meddelande visas som förklarar hur du åtgärdar felet. Om du av någon anledning inte har till gång till fel meddelande informationen, se till att:

- SAS eller annan säkerhetstoken som du använder har inte gått ut. 
- Autentiseringsuppgifterna för auktorisering är väl utformade för det protokoll som du använder. Läs mer i [IoT Hub åtkomst kontroll](iot-hub-devguide-security.md).
- Den auktoriseringsregel som används har behörighet för den begärda åtgärden.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du använder [Azure IoT SDK](iot-hub-devguide-sdks.md): er för att under lätta autentiseringen till IoT Hub enklare.