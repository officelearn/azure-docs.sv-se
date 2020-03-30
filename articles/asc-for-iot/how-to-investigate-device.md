---
title: Undersökningsguide för Azure Security Center för IoT-enhet| Microsoft-dokument
description: Det här hur du guidar förklarar hur du använder Azure Security Center för IoT för att undersöka en misstänkt IoT-enhet med Log Analytics.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 8d2fe8d63c7ece6f3b3426d8fc5a3454a61826f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596252"
---
# <a name="investigate-a-suspicious-iot-device"></a>Undersöka en misstänkt IoT-enhet

Azure Security Center för IoT-tjänstaviseringar ger tydliga indikationer när IoT-enheter misstänks vara inblandade i misstänkta aktiviteter eller när det finns indikationer på att en enhet har komprometterats. 

I den här guiden använder du de undersökningsförslag som tillhandahålls för att fastställa de potentiella riskerna för din organisation, bestämmer hur du åtgärdar och upptäcker de bästa sätten att förhindra liknande attacker i framtiden.  

> [!div class="checklist"]
> * Hitta enhetsdata
> * Undersöka med kql-frågor


## <a name="how-can-i-access-my-data"></a>Hur kommer jag åt mina data?

Som standard lagrar Azure Security Center för IoT dina säkerhetsaviseringar och rekommendationer på din Log Analytics-arbetsyta. Du kan också välja att lagra rådata.

Så här hittar du arbetsytan Log Analytics för datalagring:

1. Öppna din IoT-hubb, 
1. Klicka på **Översikt**under **Säkerhet**och välj sedan **Inställningar**.
1. Ändra konfigurationsinformationen för Logg Analytics-arbetsytan. 
1. Klicka på **Spara**. 

Gör följande konfiguration för att komma åt data som lagras på logganalysarbetsytan:

1. Välj och klicka på ett Azure Security Center för IoT-avisering i din IoT Hub. 
1. Klicka på **Ytterligare undersökning**. 
1. Välj **Om du vill se vilka enheter som har den här aviseringen klickar du här och visar kolumnen DeviceId**.

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Undersökningssteg för misstänkta IoT-enheter

Om du vill visa insikter och rådata om dina IoT-enheter går du till arbetsytan Log Analytics [för att komma åt dina data](#how-can-i-access-my-data).

Se exempel på kql-frågor nedan för att komma igång med att undersöka aviseringar och aktiviteter på enheten.

### <a name="related-alerts"></a>Relaterade aviseringar

Om du vill ta reda på om andra aviseringar utlöstes ungefär samtidigt använder du följande kql-fråga:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Användare med åtkomst

Så här tar du reda på vilka användare som har åtkomst till den här enheten med följande kql-fråga: 

 ```
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
 ```
Använd dessa data för att upptäcka: 
- Vilka användare har åtkomst till enheten?
- Har användare med åtkomst de förväntade behörighetsnivåerna?

### <a name="open-ports"></a>Öppna portar

Om du vill ta reda på vilka portar i enheten som används eller har använts använder du följande kql-fråga: 

 ```
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
 ```

Använd dessa data för att upptäcka:
- Vilka lyssningsuttag är aktiva på enheten?
- Ska de lyssningsuttag som för närvarande är aktiva tillåtas?
- Finns det några misstänkta fjärradresser anslutna till enheten?

### <a name="user-logins"></a>Användarinloggningar

Så här hittar du användare som har loggat in på enheten med hjälp av följande kql-fråga: 
 
 ```
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
 ```

Använd frågeresultaten för att upptäcka:
- Vilka användare loggade in på enheten?
- Ska användarna logga in?
- Har de användare som loggat in ansluta från förväntade eller oväntade IP-adresser?
  
### <a name="process-list"></a>Processlista

Om du vill ta reda på om processlistan är som förväntat använder du följande kql-fråga: 

 ```
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
```

Använd frågeresultaten för att upptäcka:

- Fanns det några misstänkta processer som körs på enheten?
- Utfördes processer av lämpliga användare?
- Innehöll några kommandoradskörningar korrekta och förväntade argument?

## <a name="next-steps"></a>Nästa steg

När du har undersökt en enhet och fått en bättre förståelse för dina risker kan du överväga att [konfigurera anpassade aviseringar](quickstart-create-custom-alerts.md) för att förbättra säkerhetshållningen för IoT-lösningen. Om du inte redan har en enhetsagent bör du överväga [att distribuera en säkerhetsagent](how-to-deploy-agent.md) eller [ändra konfigurationen av en befintlig enhetsagent](how-to-agent-configuration.md) för att förbättra dina resultat. 
