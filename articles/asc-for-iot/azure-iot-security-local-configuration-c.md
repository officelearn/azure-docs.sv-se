---
title: Förstå Azure Security Center för agentlokala konfigurationer för C | Microsoft-dokument
description: Lär dig mer om Azure Security Center för agentlokala konfigurationer för C.
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
ms.openlocfilehash: 2725a824da26dafcbc215e4c302ec38ad4b5a699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600545"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Förstå filen LocalConfiguration.json – C-agent

Azure Security Center för IoT-säkerhetsagent använder konfigurationer från en lokal konfigurationsfil.
Säkerhetsagenten läser konfigurationen en gång, vid agentstart.
Konfigurationen som finns i den lokala konfigurationsfilen innehåller autentiseringskonfiguration och andra agentrelaterade konfigurationer.
Filen innehåller konfigurationer i "Key-Value" par i JSON-notation och konfigurationerna fylls i när agenten installeras. 

Som standard finns filen på: /var/ASCIoTAgent/LocalConfiguration.json

Ändringar i konfigurationsfilen sker när agenten startas om. 

## <a name="security-agent-configurations-for-c"></a>Konfigurationer av säkerhetsagenter för C
| Konfigurationsnamn | Möjliga värden | Information | 
|:-----------|:---------------|:--------|
| AgentId (på andra) | GUID | Agenten Unik identifierare |
| TriggerdEventsInterval | ISO8601 sträng | Schemaschemaintervall för utlöst händelser insamling |
| AnslutningTimeout | ISO8601 sträng | Tidsperiod innan anslutningen till IoThub tar time out |
| Autentisering | JsonObject (olika) | Autentiseringskonfiguration. Det här objektet innehåller all information som behövs för autentisering mot IoTHub |
| Identitet | "DPS", "SecurityModule", "Enhet" | Autentiseringsidentitet - DPS om autentisering görs via DPS, SecurityModule om autentisering görs via autentiseringsmodulen eller enhet om autentisering görs med enhetsautentiseringsuppgifter |
| AuthenticationMethod | "SasToken", "Självsigneradcertifiera" | användarhemligheten för autentisering - Välj SasToken om användningshemligheten är en symmetrisk nyckel, välj självsignerat certifikat om hemligheten är ett självsignerat certifikat  |
| Filepath | Sökväg till fil (sträng) | Sökväg till filen som innehåller autentiseringshemligheten |
| Värdnamn | sträng | Värdnamnet för azure iot-hubben. vanligtvis <min-hubb>.azure-devices.net |
| DeviceId | sträng | Enhetens ID (som registrerats i Azure IoT Hub) |
| Dps | JsonObject (olika) | DPS-relaterade konfigurationer |
| IDScope | sträng | DPS-ID-scope |
| RegistrationId (RegistreringId) | sträng  | Registrerings-ID för DPS-enheter |
| Loggning | JsonObject (olika) | Agent logger relaterade konfigurationer |
| SystemLoggerMinimumSeverity | 0 <= antal <= 4 | loggmeddelanden som är lika och ovanför den här allvarlighetsgraden loggas till /var/log/syslog (0 är den lägsta allvarlighetsgraden) |
| DiagnosticEventMinimumSeverity | 0 <= antal <= 4 | loggmeddelanden som är lika och ovanför den här allvarlighetsgraden kommer att skickas som diagnostiska händelser (0 är den lägsta allvarlighetsgraden) |

## <a name="security-agent-configurations-code-example"></a>Exempel på kodex för säkerhetsagentkonfigurationer
```JSON
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
- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera Azure Security Center för [IoT-tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om Azure Security Center för IoT-tjänsten
- Lär dig hur du kommer åt [rådata om säkerhet](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [säkerhetsaviseringar](concept-security-alerts.md)