---
title: Åtkomst till data med hjälp av Azure Security Center för förhandsversionen av IoT | Microsoft Docs
description: Läs mer om hur du kommer åt dina säkerhetsdata för avisering och rekommendationer när du använder Azure Security Center för IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 0788a62de701cb85a65c7053006e28cf4fffe5cb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789082"
---
# <a name="access-your-security-data"></a>Få åtkomst till dina säkerhetsdata 

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) för IoT lagrar säkerhetsaviseringar, rekommendationer och raw säkerhetsdata (om du väljer att spara den) i Log Analytics-arbetsytan.

## <a name="log-analytics"></a>Log Analytics

Konfigurera vilka Log Analytics-arbetsyta används:

1. Öppna din IoT-hubb.
1. Klicka på **säkerhet**
2. Klicka på **inställningar**, och ändra konfigurationen för logganalys-arbetsytan.

Öppna Log Analytics-arbetsytan efter konfigurationen:

1. Välj en avisering eller en rekommendation i ASC för IoT. 
2. Klicka på **ytterligare undersökningar**, klicka sedan på **att se vilka enheter som har den här aviseringen Klicka här och visa kolumnen DeviceId**.

Mer information om hämtning av data från Log Analytics finns [Kom igång med frågor i Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Säkerhetsaviseringar

Säkerhetsaviseringar lagras i _AzureSecurityOfThings.SecurityAlert_ tabellen i Log Analytics-arbetsytan som konfigurerats för ASC för IoT-lösning.

Vi har angett ett antal användbara frågor som hjälper dig att komma igång med att utforska säkerhetsaviseringar.

### <a name="sample-records"></a>Exempelposter

Välj ett fåtal poster i slumpmässig

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
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Råstyrkeattack lyckades           | En Brute-force attack på enheten har slutförts        |    {”Fullständig källadress” ”: [\"10.165.12.18:\"]”, ”användarnamn” ”: [\"\"]”, ”DeviceId”: "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Genomförd lokal inloggning på enhet      | En lyckad lokal inloggning till enheten har identifierats     | {”Fjärradress” ”:”?, ”Remote Port” ”:”, ”lokal Port” ”:”, ”inloggningsgränssnitt”: ”/ bin/su”, ”inloggnings Process-Id”: "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Misslyckat inloggningsförsök lokalt på enheten  | Ett misslyckat lokala inloggningsförsök till enheten har identifierats |  {”Fjärradress” ”:”?, ”Remote Port” ”:”, ”lokal Port” ”:”, ”inloggningsgränssnitt”: ”/ bin/su”, ”inloggnings Process-Id”: "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Sammanfattning av enhet

Välj antal distinkta säkerhetsaviseringar som identifierats föregående vecka av IoT Hub, enhet, allvarlighetsgrad, typ av avisering.

```
// Select number of distinct security alerts detected last week by 
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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Råstyrkeattack lyckades           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | Misslyckat inloggningsförsök lokalt på enheten  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Genomförd lokal inloggning på enhet      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | Krypto mynt Miner                     | 4   |

### <a name="iot-hub-summary"></a>Översikt över IoT-hubb

Välj ett antal olika enheter som hade aviseringar under den senaste veckan av IoT Hub, allvarlighetsgrad, typ av avisering

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Råstyrkeattack lyckades           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medel        | Misslyckat inloggningsförsök lokalt på enheten  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Genomförd lokal inloggning på enhet      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medel        | Krypto mynt Miner                     | 1          |

## <a name="security-recommendations"></a>Säkerhetsrekommendationer

Säkerhetsrekommendationer lagras i _AzureSecurityOfThings.SecurityRecommendation_ tabellen i Log Analytics-arbetsytan som konfigurerats för ASC för IoT-lösning.

Vi tillhandahåller ett antal användbara frågor kan du börja utforska säkerhetsrekommendationer.

### <a name="sample-records"></a>Exempelposter

Välj ett fåtal poster i slumpmässig

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
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel | Active | Tillåtande brandväggsregel i indata kedjan hittades | En regel i brandväggen har hittats som innehåller ett tillåtande mönster för ett brett intervall av IP-adresser eller portar | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel | Active | Tillåtande brandväggsregel i indata kedjan hittades | En regel i brandväggen har hittats som innehåller ett tillåtande mönster för ett brett intervall av IP-adresser eller portar | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Sammanfattning av enhet

Välj antalet distinkta active säkerhetsrekommendationer genom IoT Hub, enhet, rekommendation allvarlighetsgrad och typen.

```
// Select number of distinct active security recommendations by 
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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | 4   |


## <a name="next-steps"></a>Nästa steg

- Läs ASC för IoT [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Förstå och utforska [ASC för IoT-aviseringar](concept-security-alerts.md)
- Förstå och utforska [ASC för IoT-rekommendation](concept-recommendations.md)