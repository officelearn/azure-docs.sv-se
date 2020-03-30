---
title: Komma åt data med Azure Security Center för IoT| Microsoft-dokument
description: Läs mer om hur du kommer åt dina säkerhetsaviseringar och rekommendationsdata när du använder Azure Security Center för IoT.
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
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597179"
---
# <a name="access-your-security-data"></a>Få tillgång till dina säkerhetsdata 

Azure Security Center för IoT lagrar säkerhetsaviseringar, rekommendationer och rådata för säkerhet (om du väljer att spara dem) på arbetsytan Logganalys.

## <a name="log-analytics"></a>Log Analytics

Så här konfigurerar du vilken log analytics-arbetsyta som används:

1. Öppna din IoT-hubb.
1. Klicka på **bladet Översikt** under avsnittet **Säkerhet**
2. Klicka på **Inställningar**och ändra konfigurationen för Logganalysarbetsyta.

Så här kommer du åt dina aviseringar och rekommendationer på arbetsytan Log Analytics efter konfigurationen:

1. Välj en avisering eller rekommendation i Azure Security Center för IoT. 
2. Klicka **på ytterligare undersökning**och klicka sedan på Om du vill se vilka enheter som har den här **aviseringen klickar du här och visar kolumnen DeviceId**.

Mer information om hur du frågar data från Log Analytics finns [i Komma igång med frågor i Logganalys](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Säkerhetsaviseringar

Säkerhetsaviseringar lagras i tabellen _AzureSecurityOfThings.SecurityAlert_ i logganalysarbetsytan som konfigurerats för Azure Security Center for IoT-lösningen.

Vi har tillhandahållit ett antal användbara frågor som hjälper dig att komma igång med att utforska säkerhetsaviseringar.

### <a name="sample-records"></a>Exempel på poster

Markera några slumpmässiga poster

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Beskrivning                                             | Utökade egenskaper                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Brute force attack lyckades           | En Brute force attack på enheten lyckades        |    { "Fullständig källadress":\""[ 10.165.12.18:\"]", "Användarnamn": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Lyckad lokal inloggning på enheten      | En lyckad lokal inloggning till enheten upptäcktes     | { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Misslyckades med lokala inloggningsförsök på enheten  | Ett misslyckat lokalt inloggningsförsök till enheten upptäcktes |  { "Remote Address": "?", "Remote Port": "", "Local Port": "", "Login Shell": "/bin/su", "Login Process Id": "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Sammanfattning av enhet

Få antalet olika säkerhetsaviseringar som upptäckts under den senaste veckan, grupperade efter IoT Hub, enhet, varnings allvarlighetsgrad, varningstyp.

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Brute force attack lyckades           | 9   |   
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | Misslyckades med lokala inloggningsförsök på enheten  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Lyckad lokal inloggning på enheten      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | Crypto Coin Gruvarbetare                     | 4   |

### <a name="iot-hub-summary"></a>Sammanfattning av IoT-hubb

Välj ett antal olika enheter som hade aviseringar under den senaste veckan, av IoT Hub, varnings allvarlighetsgrad, varningstyp

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

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices (CntDevices) |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Brute force attack lyckades           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medel        | Misslyckades med lokala inloggningsförsök på enheten  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Lyckad lokal inloggning på enheten      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medel        | Crypto Coin Gruvarbetare                     | 1          |

## <a name="security-recommendations"></a>Säkerhetsrekommendationer

Säkerhetsrekommendationer lagras i tabellen _AzureSecurityOfThings.SecurityRecommendation_ i arbetsytan Log Analytics som konfigurerats för Azure Security Center for IoT-lösningen.

Vi har tillhandahållit ett antal användbara frågor som hjälper dig att börja utforska säkerhetsrekommendationer.

### <a name="sample-records"></a>Exempel på poster

Markera några slumpmässiga poster

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
    
| TimeGenerated | IoTHubId | DeviceId | rekommendationerslätenlighet | RekommendationStat | RekommendationDisplayName | Beskrivning | RekommendationLägga tilldata |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel | Active | Tillåtande brandväggsregel i indatakedjan hittades | En regel i brandväggen har hittats som innehåller ett tillåtande mönster för ett brett spektrum av IP-adresser eller portar | {"Regler":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\":\"\"\",\"DestinationAddress : , DestinationPort :\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel | Active | Tillåtande brandväggsregel i indatakedjan hittades | En regel i brandväggen har hittats som innehåller ett tillåtande mönster för ett brett spektrum av IP-adresser eller portar | {"Regler":"[{\"\"SourceAddress\"\"\":\",\"\"\"SourcePort\":\"\"\",\"DestinationAddress : , DestinationPort :\"1337\"}]"} |

### <a name="device-summary"></a>Sammanfattning av enhet

Få antalet olika aktiva säkerhetsrekommendationer, grupperade efter IoT Hub, enhet, rekommendations allvarlighetsgrad och typ.

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

| IoTHubId                                                                                                       | DeviceId      | rekommendationerslätenlighet | Antal |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | 2   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | 1 |  
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | 4   |


## <a name="next-steps"></a>Nästa steg

- Läs [översikten](overview.md) över Azure Security Center for IoT
- Lär dig mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Förstå och utforska [Azure Security Center för IoT-aviseringar](concept-security-alerts.md)
- Förstå och utforska [Azure Security Center for IoT-rekommendation](concept-recommendations.md)
