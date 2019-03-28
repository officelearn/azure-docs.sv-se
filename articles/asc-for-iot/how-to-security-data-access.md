---
title: Åtkomst till data med hjälp av ASC för förhandsversionen av IoT | Microsoft Docs
description: Läs mer om hur du kommer åt dina säkerhetsdata för avisering och rekommendationer när du använder ASC för IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: e394f6025f7898aad7dde7b1acefd9f95029a554
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541999"
---
# <a name="access-your-security-data"></a>Få åtkomst till dina säkerhetsdata 

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC för IoT lagrar säkerhetsaviseringar, rekommendationer och raw säkerhetsdata (om du väljer att spara den) i Log Analytics-arbetsytan.

## <a name="log-analytics"></a>Log Analytics

Konfigurera vilka Log Analytics-arbetsyta används:

1. Öppna din IoT-hubb.
1. Klicka på **säkerhet**
2. Klicka på **inställningar**, och ändra konfigurationen för logganalys-arbetsytan.

Öppna Log Analytics-arbetsytan efter konfigurationen:

1. Välj en avisering i ASC för IoT. 
2. Klicka på **ytterligare undersökningar**, klicka sedan på **att se vilka enheter som har den här aviseringen Klicka här och visa kolumnen DeviceId**.

Mer information om hämtning av data från Log Analytics finns [Kom igång med frågor i Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Säkerhetsaviseringar

Säkerhetsaviseringar lagras i den **ASCforIoT.SecurityAlert** tabell i konfigurerade Log Analytics-arbetsytan.

Använd följande grundläggande kql frågor för att börja utforska säkerhetsaviseringar.

### <a name="sample-records-query"></a>Exempelfråga poster

Så här väljer slumpmässigt några poster: 

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

#### <a name="sample-query-results"></a>Exemplet frågeresultat 

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Beskrivning                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Råstyrkeattack lyckades           | En Brute-force attack på enheten har slutförts        |    {”Fullständig källadress” ”: [\"10.165.12.18:\"]”, ”användarnamn” ”: [\"\"]”, ”DeviceId”: "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Genomförd lokal inloggning på enhet      | En lyckad lokal inloggning till enheten har identifierats     | {”Fjärradress” ”:”?, ”Remote Port” ”:”, ”lokal Port” ”:”, ”inloggningsgränssnitt”: ”/ bin/su”, ”inloggnings Process-Id”: "28207", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Misslyckat inloggningsförsök lokalt på enheten  | Ett misslyckat lokala inloggningsförsök till enheten har identifierats |  {”Fjärradress” ”:”?, ”Remote Port” ”:”, ”lokal Port” ”:”, ”inloggningsgränssnitt”: ”/ bin/su”, ”inloggnings Process-Id”: "22644", "User Name": "attacker", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary-query"></a>Sammanfattning av frågan för enhet

Använd den här kql-fråga för att välja ett antal olika säkerhetsaviseringar upptäckte förra veckan av IoT Hub, enhet, allvarlighetsgrad, typ av avisering.

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

#### <a name="device-summary-query-results"></a>Sammanfattning av frågeresultat för enhet

| IoTHubId | DeviceId| AlertSeverity| DisplayName | Antal |
|----------|---------|------------------|---------|---------|
|/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Råstyrkeattack lyckades           | 9   |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | Misslyckat inloggningsförsök lokalt på enheten  | 242 |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Hög          | Genomförd lokal inloggning på enhet      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | Medel        | Krypto mynt Miner                     | 4   |
|

### <a name="iot-hub-summary"></a>IoT Hub-sammanfattning

Använd den här kql frågan för att välja ett antal olika enheter som hade aviseringar under den senaste veckan av IoT hub, allvarlighetsgrad, typ av avisering:

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

#### <a name="iot-hub-summary-query-results"></a>Sammanfattning av IoT Hub-frågeresultat

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Råstyrkeattack lyckades           | 1          |    
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medel        | Misslyckat inloggningsförsök lokalt på enheten  | 1          | 
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Hög          | Genomförd lokal inloggning på enhet      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | Medel        | Krypto mynt Miner                     | 1          |



## <a name="next-steps"></a>Nästa steg

- Läs ASC för IoT [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Förstå och utforska [ASC för IoT-aviseringar](concept-security-alerts.md)
