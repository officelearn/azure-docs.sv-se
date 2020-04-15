---
title: Lokal konfiguration av säkerhetsagenten (C#)
description: Läs mer om Azure Security Center for IoT security service, säkerhetsagent lokal konfigurationsfil för C#.
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
ms.openlocfilehash: adf0d72763e0cb1892d64c68a6dce05abbf6f582
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311667"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Förstå den lokala konfigurationsfilen (C#-agenten)

Azure Security Center för IoT-säkerhetsagent använder konfigurationer från en lokal konfigurationsfil.

Säkerhetsagenten läser konfigurationsfilen en gång när agenten startar. Konfigurationer som finns i den lokala konfigurationsfilen innehåller både autentiseringskonfiguration och andra agentrelaterade konfigurationer.

C#-säkerhetsagenten använder flera konfigurationsfiler:

- **General.config** - Agentrelaterade konfigurationer.
- **Authentication.config** - Autentiseringsrelaterad konfiguration (inklusive autentiseringsinformation).
- **SecurityIotInterface.config** - IoT-relaterade konfigurationer.

Konfigurationsfilerna innehåller standardkonfigurationen. Autentiseringskonfigurationen fylls i under agentinstallationen och ändringar i konfigurationsfilen görs när agenten startas om.

## <a name="configuration-file-location"></a>Plats för konfigurationsfil

För Linux:

- Konfigurationsfiler för operativsystemet finns i `/var/ASCIoTAgent`.

För Windows:

- Konfigurationsfiler för operativsystemet finns i säkerhetsagentens katalog.

### <a name="generalconfig-configurations"></a>General.config-konfigurationer

| Konfigurationsnamn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| agentId (agentId) | GUID | Agent unik identifierare |
| readRemoteConfigurationTimeout | TimeSpan | Tidsperiod för hämtning av fjärrkonfiguration från IoT Hub. Om agenten inte kan hämta konfigurationen inom den angivna tiden kommer åtgärden att time out.|
| schedulerInterval | TimeSpan | Internt schemaintervall. |
| producentInterval | TimeSpan | Händelseproducentarintervall. |
| konsumentenInterval | TimeSpan | Intervall för händelsekonsumentarbetare. |
| highPriorityQueueSizePercentage | 0 < nummer < 1 | Den del av den totala cachen som är avsedd för meddelanden med hög prioritet. |
| logLevel | "Av", "Fatal", "Error", "Warning", "Information", "Debug"  | Loggmeddelanden som är lika och över den här allvarlighetsgraden loggas för att felsöka konsolen (Syslog i Linux). |
| filLoggLevel |  "Av", "Fatal", "Error", "Warning", "Information", "Debug"| Loggmeddelanden som är lika och över den här allvarlighetsgraden loggas till filen (Syslog i Linux). |
| diagnostikVerbosityNivå | "Ingen", "Vissa", "Alla", | Verbositet nivå av diagnostiska händelser. Inga - diagnostiska händelser skickas inte, Vissa - Endast diagnostiska händelser med stor betydelse skickas, Alla - alla loggar skickas också som diagnostiska händelser. |
| logFilePath | Sökväg till fil | Om fileLogLevel > Off skrivs loggar till den här filen. |
| defaultEventPriority | "Hög", "Låg", "Av" | Standardhändelseprioritet. |

### <a name="generalconfig-example"></a>Allmänt.config exempel

```XML
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

| Konfigurationsnamn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| moduleName (modulNamn) | sträng | Namn på säkerhetsmodulens identitet. Det här namnet måste motsvara modulens identitetsnamn i enheten. |
| deviceId | sträng | ID för enheten (som registrerats i Azure IoT Hub). || schedulerInterval | TimeSpan sträng | Internt schemaintervall. |
| gatewayHostname | sträng | Värdnamn för Azure Iot Hub. Vanligtvis <min-hubb>.azure-devices.net |
| Filepath | sträng - sökväg till fil | Sökväg till filen som innehåller autentiseringshemligheten.|
| typ | "SymmetricKey", "SjälvsigneradCertifiera" | Användarhemligheten för autentisering. Välj *SymmetricKey* om användarhemligheten är en symmetrisk nyckel väljer du *självsignerat certifikat* om hemligheten är ett självsignerat certifikat. |
| identity | "DPS", "Modul", "Enhet" | Autentiseringsidentitet - DPS om autentisering görs via DPS, Modul om autentisering görs med modulautentisering eller enhet om autentisering görs med hjälp av enhetsautentiseringsuppgifter.
| certifikatLokaliseringKintt |  "LocalFile", "Store" | LocalFile om certifikatet lagras i en fil, lagra om certifikatet finns i ett certifikatarkiv. |
| idScope | sträng | DPS-ID-scope |
| registrationId (registreringId) | sträng  | REGISTRERINGS-ID för DPS-enheter. |
|

### <a name="authenticationconfig-example"></a>Exempel på authentication.config

```XML
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

| Konfigurationsnamn | Möjliga värden | Information |
|:-----------|:---------------|:--------|
| transportTyp | "Ampq" "Mqtt" | Transporttyp för IoT Hub. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config exempel

```XML
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Nästa steg

- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera Azure Security Center för [IoT-tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md) om Azure Security Center för IoT-tjänsten
- Lär dig hur du kommer åt [rådata om säkerhet](how-to-security-data-access.md)
- Förstå [rekommendationer](concept-recommendations.md)
- Förstå [säkerhetsaviseringar](concept-security-alerts.md)
