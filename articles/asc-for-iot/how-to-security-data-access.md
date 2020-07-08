---
title: Åtkomst till säkerhets & rekommendations data
description: Lär dig mer om hur du kommer åt dina säkerhets aviseringar och rekommendations data när du använder Azure Security Center för IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311007"
---
# <a name="access-your-security-data"></a>Komma åt dina säkerhets data

Azure Security Center for IoT lagrar säkerhets aviseringar, rekommendationer och rå data (om du väljer att spara den) i arbets ytan Log Analytics.

## <a name="log-analytics"></a>Log Analytics

Konfigurera vilken Log Analytics arbets yta som används:

1. Öppna din IoT Hub.
1. Klicka på bladet **Översikt** under avsnittet **säkerhet**
1. Klicka på **Inställningar**och ändra Log Analytics arbets ytans konfiguration.

För att få åtkomst till aviseringar och rekommendationer i Log Analytics-arbetsytan efter konfigurationen:

1. Välj en avisering eller rekommendation i Azure Security Center för IoT.
1. Klicka på **ytterligare undersökning**och klicka sedan **för att se vilka enheter som har den här aviseringen Klicka här och visa kolumnen DeviceID**.

Information om hur du frågar efter data från Log Analytics finns i [Kom igång med frågor i Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Säkerhetsaviseringar

Säkerhets aviseringar lagras i _AzureSecurityOfThings. SecurityAlert_ -tabellen i Log Analytics arbets ytan som kon figurer ats för Azure Security Center för IoT-lösningen.

Vi har tillhandahållit ett antal användbara frågor som hjälper dig att komma igång med att utforska säkerhets aviseringar.

### <a name="sample-records"></a>Exempel poster

Välj några slumpmässiga poster

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Beskrivning                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Brute force attack lyckades           | En brute force-attack på enheten lyckades        |    {"Fullständig käll adress": "[ \" 10.165.12.18: \" ]", "användar namn": "[ \" \" ]", "DeviceID": "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Den lokala inloggningen på enheten har slutförts      | En lyckad lokal inloggning på enheten upptäcktes     | {"Fjärradress": "?", "Fjärrport": "", "lokal port": "", "inloggnings gränssnitt": "/bin/su", "inloggnings process-ID": "28207", "användar namn": "angripare", "DeviceId": "IoT-Device-Linux"} |
| 2018-11-19T12:40:31.000 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Det gick inte att aktivera lokalt inloggnings försök på enheten  | Ett misslyckat lokalt inloggnings försök till enheten upptäcktes |    {"Fjärradress": "?", "Fjärrport": "", "lokal port": "", "inloggnings gränssnitt": "/bin/su", "inloggnings process-ID": "22644", "användar namn": "angripare", "DeviceId": "IoT-Device-Linux"} |

### <a name="device-summary"></a>Enhets Sammanfattning

Hämta antalet distinkta säkerhets aviseringar som identifierats under den senaste veckan, grupperade efter IoT Hub, enhet, allvarlighets grad för avisering, aviserings typ.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Antal |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Brute force attack lyckades           | 9   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | Det gick inte att aktivera lokalt inloggnings försök på enheten  | 242 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Den lokala inloggningen på enheten har slutförts      | 31  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | Miner för kryptografi                     | 4   |

### <a name="iot-hub-summary"></a>Sammanfattning av IoT Hub

Välj ett antal distinkta enheter som hade aviseringar under den senaste veckan, efter IoT Hub, allvarlighets grad för avisering, aviserings typ

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Brute force attack lyckades           | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | Det gick inte att aktivera lokalt inloggnings försök på enheten  | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Den lokala inloggningen på enheten har slutförts      | 1          |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medium        | Miner för kryptografi                     | 1          |

## <a name="security-recommendations"></a>Säkerhetsrekommendationer

Säkerhets rekommendationer lagras i _AzureSecurityOfThings. SecurityRecommendation_ -tabellen i arbets ytan Log Analytics som har kon figurer ATS för IoT-lösningens Azure Security Center.

Vi har fått ett antal användbara frågor som hjälper dig att komma igång med att utforska säkerhets rekommendationer.

### <a name="sample-records"></a>Exempel poster

Välj några slumpmässiga poster

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Beskrivning | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | Aktiv | En tillåtande brand Väggs regel i indatamängden hittades | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för ett brett utbud av IP-adresser eller portar | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" destination port \" : \" 1337 \" }]"} |
| 2019-03-22T10:50:27.237 | /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium | Aktiv | En tillåtande brand Väggs regel i indatamängden hittades | En regel i brand väggen har hittats som innehåller ett tillåtet mönster för ett brett utbud av IP-adresser eller portar | {"Rules": "[{ \" SourceAddress \" : \" \" , \" SourcePort \" : \" \" , \" DestinationAddress \" : \" \" , \" destination port \" : \" 1337 \" }]"} |

### <a name="device-summary"></a>Enhets Sammanfattning

Hämta antalet distinkta aktiva säkerhets rekommendationer, grupperade efter IoT Hub, enhet, rekommendations allvarlighets grad och typ.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Antal |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | 2   |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 1 |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | 1  |
| /Subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medium        | 4   |

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure Security Center för IoT
- Lär dig mer om Azure Security Center för IoT- [arkitektur](architecture.md)
- Förstå och utforska [Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
- Förstå och utforska [Azure Security Center för IoT-rekommendation](concept-recommendations.md)
