---
title: Lokal konfiguration av säkerhets agent (C#)
description: Läs mer om Azure Security Center för IoT Security Service, den lokala konfigurations filen för säkerhets agenten för C#.
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
ms.openlocfilehash: fc869a8ab905275c8082c4fd375f8f6d6d48d97e
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85205466"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Förstå den lokala konfigurations filen (C#-agenten)

Azure Security Center för IoT Security Agent använder konfigurationer från en lokal konfigurations fil.

Säkerhets agenten läser konfigurations filen en gång när agenten startar. Konfigurationer som finns i den lokala konfigurations filen innehåller både konfiguration av autentisering och andra Agent-relaterade konfigurationer.

C#-säkerhets agenten använder flera konfigurationsfiler:

- **General.config** -Agent-relaterade konfigurationer.
- Konfiguration för **Authentication.config** -autentisering (inklusive autentiseringsinformation).
- **SecurityIotInterface.config** -IoT-relaterade konfigurationer.

Konfigurationsfilerna innehåller standard konfigurationen. Konfiguration av autentisering fylls under Agent installationen och ändringar i konfigurations filen görs när agenten startas om.

## <a name="configuration-file-location"></a>Plats för konfigurations fil

För Linux:

- Konfigurationsfiler för operativ system finns i `/var/ASCIoTAgent` .

För Windows:

- Konfigurationsfiler för operativ system finns i säkerhets agentens katalog.

### <a name="generalconfig-configurations"></a>General.config konfigurationer

| Konfigurations namn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| agentId | GUID | Unikt ID för agent |
| readRemoteConfigurationTimeout | TimeSpan | Tids period för att hämta fjärrkonfiguration från IoT Hub. Om agenten inte kan hämta konfigurationen inom den angivna tiden, kommer åtgärden att ta slut.|
| schedulerInterval | TimeSpan | Internt schema intervall. |
| producerInterval | TimeSpan | Arbets intervall för händelse producent. |
| consumerInterval | TimeSpan | Arbets intervall för händelse förbrukare. |
| highPriorityQueueSizePercentage | 0 < nummer < 1 | Den del av total cache dedikerat för meddelanden med hög prioritet. |
| logLevel | "Off", "oåterkallelig", "fel", "varning", "information", "Felsök"  | Logg meddelanden som är lika med eller större än den här allvarlighets graden loggas i fel söknings konsolen (syslog i Linux). |
| fileLogLevel |  "Off", "oåterkallelig", "fel", "varning", "information", "Felsök"| Logg meddelanden som är lika med eller större än den här allvarlighets graden är inloggade i filen (syslog i Linux). |
| diagnosticVerbosityLevel | "Ingen", "vissa", "alla", | Utförlig nivå för diagnostiska händelser. Inga-diagnostiska händelser skickas, vissa-diagnostiska händelser med hög prioritet skickas, alla loggar skickas också som diagnostiska händelser. |
| logFilePath | Sökväg till fil | Om fileLogLevel > av skrivs loggar till den här filen. |
| defaultEventPriority | "Hög", "låg", "av" | Standard händelse prioritet. |

### <a name="generalconfig-example"></a>General.config exempel

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Konfigurations namn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| moduleName | sträng | Namn på säkerhetsmodulens identitet. Namnet måste motsvara modulens identitets namn i enheten. |
| deviceId | sträng | ID för enheten (som registrerats i Azure IoT Hub). || schedulerInterval | TimeSpan-sträng | Internt schema intervall. |
| gatewayHostname | sträng | Värd namnet för Azure IoT Hub. <vanligt vis>. azure-devices.net |
| filePath | sträng-sökväg till fil | Sökväg till filen som innehåller hemligheten för autentisering.|
| typ | "SymmetricKey", "SelfSignedCertificate" | Användar hemligheten för autentisering. Välj *SymmetricKey* om användar hemligheten är en symmetrisk nyckel väljer du *självsignerat certifikat* om hemligheten är ett självsignerat certifikat. |
| identity | "DPS", "modul", "enhet" | Autentiseringsidentitet – DPS om autentisering görs via DPS, modul om autentisering görs med autentiseringsuppgifter för modulen eller om autentisering görs med autentiseringsuppgifter för enheten.
| certificateLocationKind |  "Lokalfil", "Store" | Lokalfil om certifikatet lagras i en fil, lagrar du om certifikatet finns i ett certifikat arkiv. |
| idScope | sträng | ID-omfång för DPS |
| registrationId | sträng  | Registrerings-ID för DPS-enhet. |
|

### <a name="authenticationconfig-example"></a>Authentication.config exempel

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Konfigurations namn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "MQTT" | IoT Hub Transport typ. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config exempel

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Nästa steg

- Läs [översikten över](overview.md) Azure Security Center för IoT-tjänsten
- Läs mer om Azure Security Center för IoT- [arkitektur](architecture.md)
- Aktivera Azure Security Center för IoT [-tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om tjänsten Azure Security Center for IoT
- Lär dig hur du kommer åt [rå säkerhets data](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå säkerhets [aviseringar](concept-security-alerts.md)
