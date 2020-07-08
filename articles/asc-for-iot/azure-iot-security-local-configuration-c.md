---
title: Lokal konfiguration av säkerhets agent (C)
description: Läs mer om att Azure Security Center för lokala Gent konfigurationer för C.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 842a69c27ceb0d56df5a7b49eb9922b88d8d4b32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85206945"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Förstå filen LocalConfiguration.json – C-agent

Azure Security Center för IoT Security Agent använder konfigurationer från en lokal konfigurations fil.
Säkerhets agenten läser konfigurationen en gång, vid agent start.
Konfigurationen som påträffades i den lokala konfigurations filen innehåller konfiguration av autentisering och andra Agent-relaterade konfigurationer.
Filen innehåller konfigurationer i nyckel värdes par i JSON-format och konfigurationerna fylls i när agenten installeras.

Filen finns som standard på:/var/ASCIoTAgent/LocalConfiguration.jspå

Ändringar i konfigurations filen sker när agenten startas om.

## <a name="security-agent-configurations-for-c"></a>Konfigurationer för säkerhets agent för C

| Konfigurations namn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| AgentId | GUID | Agentens unika identifierare |
| TriggerdEventsInterval | ISO8601 sträng | Scheduler-intervall för insamling av utlöst händelser |
| ConnectionTimeout | ISO8601 sträng | Tids gränsen för tids gränsen uppnåddes för anslutningen till IoThub |
| Autentisering | JsonObject | Konfiguration av autentisering. Det här objektet innehåller all information som behövs för autentisering mot IoTHub |
| Identitet | "DPS", "SecurityModule", "Device" | Autentiseringsidentitet – DPS om autentisering görs via DPS, SecurityModule om autentisering görs via autentiseringsuppgifter för säkerhetsmodulen eller enheten om autentisering görs med autentiseringsuppgifter för enhet |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | användar hemlighet för autentisering – Välj SasToken om användnings hemligheten är en symmetrisk nyckel väljer du självsignerat certifikat om hemligheten är ett självsignerat certifikat  |
| FilePath | Sökväg till fil (sträng) | Sökväg till filen som innehåller hemligheten för autentisering |
| Värdnamn | sträng | Värd namnet för Azure IoT Hub. <vanligt vis>. azure-devices.net |
| DeviceId | sträng | ID för enheten (som registrerats i Azure IoT Hub) |
| – | JsonObject | DPS-relaterade konfigurationer |
| IDScope | sträng | ID-omfång för DPS |
| RegistrationId | sträng  | Registrerings-ID för DPS-enhet |
| Loggning | JsonObject | Konfigurationer för agent loggning |
| SystemLoggerMinimumSeverity | 0 <= antal <= 4 | logg meddelanden som är lika med eller större än den här allvarlighets graden loggas till/var/log/syslog (0 är den lägsta allvarlighets graden) |
| DiagnosticEventMinimumSeverity | 0 <= antal <= 4 | logg meddelanden som är lika med eller större än den här allvarlighets graden skickas som diagnostiska händelser (0 är den lägsta allvarlighets graden) |

## <a name="security-agent-configurations-code-example"></a>Kod exempel för konfiguration av säkerhets agent

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure Security Center för IoT-tjänsten
- Läs mer om Azure Security Center för IoT- [arkitektur](architecture.md)
- Aktivera Azure Security Center för IoT [-tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om tjänsten Azure Security Center for IoT
- Lär dig hur du kommer åt [rå säkerhets data](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå säkerhets [aviseringar](concept-security-alerts.md)
