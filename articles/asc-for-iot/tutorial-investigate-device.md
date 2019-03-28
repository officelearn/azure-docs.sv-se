---
title: ASC för IoT-enhet undersökning självstudien förhandsversion | Microsoft Docs
description: Den här artikeln förklarar hur du använder ASC för IoT du undersöker en misstänkt IoT-enhet med Log Analytics.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 5459c5a18742b92b77866241212f21c11d7851c9
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541583"
---
# <a name="tutorial-investigate-a-suspicious-iot-device"></a>Självstudier: Undersöka en misstänkt IoT-enhet

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

ASC för IoT aviseringar och bevis ange tydligt när IoT-enheter som misstänks för medverkan i misstänkta aktiviteter eller när indikationer finns att en enhet har komprometterats. 

I den här självstudien använder du de undersökning förslag som givits för att fastställa de potentiella riskerna för din organisation, hur du åtgärdar och identifiera de bästa sätten att förhindra att liknande attacker i framtiden.  

> [!div class="checklist"]
> * Hitta din enhetsdata
> * Undersök med hjälp av kql frågor


## <a name="where-can-i-access-my-data"></a>Var hittar jag mina data

Som standard lagrar ASC för IoT din säkerhetsaviseringar och rekommendationer i Log Analytics-arbetsytan. Du kan också välja att lagra dina rådata säkerhetsdata.

Att hitta den Log Analytics-arbetsytan för lagring av data:

1. Öppna din IoT-hubb 
1. Klicka på **Security**och välj sedan **inställningar**.
1. Ändra din konfigurationsinformation för Log Analytics-arbetsytan. 
1. Klicka på **Spara**. 

Gör följande för att komma åt data som lagras i Log Analytics-arbetsytan följande konfiguration:

1. Välj och klicka på en ASC för IoT-avisering i IoT Hub. 
1. Klicka på **ytterligare undersökningar**. 
1. Välj **att se vilka enheter som har den här aviseringen Klicka här och visa kolumnen DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Undersökningssteg misstänkta IoT-enheter

För att få åtkomst till insikter och rådata om dina IoT-enheter måste du gå till Log Analytics-arbetsytan [var hittar jag mina data](#where-can-i-access-my-data).

Kontrollera och undersöka enhetsdata för följande information och aktiviteter med hjälp av följande kql frågor:

- Om du vill veta om andra aviseringar har utlösts runt samma tid användning kql följande fråga:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

- Om du vill veta vilka användare som har åtkomst till den här enheten använder du följande kql fråga: 

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
  ~~~
Använd informationen för att identifiera: 
  1. Vilka användare som har åtkomst till enheten?
  2. Har användare med åtkomst till de behörighetsnivåer som förväntat? 

- Använd följande kql fråga om du vill veta hur användarna ska kunna få åtkomst till enheten:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
  ~~~

    Använd informationen för att identifiera:
  1. Vilka lyssnande sockets för närvarande är aktiva på enheten?
  2. Bör den lyssnande sockets som för närvarande aktiveras?

- Om du vill ta reda på vem som har loggat in på enheten kan använda följande kql fråga: 
 
  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
  ~~~

    Använd resultatet av frågan för att identifiera:
  1. Vilka användare som har loggat in på enheten?
  2. Anslöt de användare som loggat in från förväntade eller oväntade IP-adresser?
  
- Om du vill veta om enheten fungerar som förväntat, använder du följande kql-fråga:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
  ~~~

    Använd resultatet av frågan för att identifiera:

  1. Har några misstänkta processer som körs på enheten?
  2. Utfördes processer behöriga användare?
  3. Innehöll alla kommandoraden körningar korrekt och förväntade argument?

## <a name="next-steps"></a>Nästa steg
När du undersöker en enhet och få en bättre förståelse för dina risker, kan du överväga att [konfigurera anpassade varningar](quickstart-create-custom-alerts.md) att förbättra din säkerhetsposition för IoT-lösning. Om du inte redan har en agent för enheten kan du överväga att [distribuerar en säkerhetsagenten](select-deploy-agent.md) eller [ändrar konfigurationen för en befintlig enhet agent](concept-agent-configuration.md) att förbättra dina resultat. 
 