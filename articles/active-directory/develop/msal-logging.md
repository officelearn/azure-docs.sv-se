---
title: Logga in MSAL-program | Microsoft Identity Platform
description: Lär dig mer om loggning i MSAL-program (Microsoft Authentication Library).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dad8a276cd40b1ff04bbced833b5d70cec4fc87
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268587"
---
# <a name="logging-in-msal-applications"></a>Logga in MSAL-program

MSAL-appar (Microsoft Authentication Library) genererar logg meddelanden som kan hjälpa dig att diagnostisera problem. En app kan konfigurera loggning med några rader kod och har anpassad kontroll över detalj nivån och huruvida personliga och organisatoriska data loggas. Vi rekommenderar att du skapar ett återanrop för MSAL-loggning och ger användarna möjlighet att skicka loggar när de har problem med autentiseringen.

## <a name="logging-levels"></a>Loggnings nivåer

MSAL tillhandahåller flera nivåer av loggnings information:

- Fel: Indikerar att något har gått fel och ett fel har genererats. Använd för fel sökning och identifiering av problem.
- Varning: Det har inte nödvändigt vis uppstått något fel eller fel, men är avsedda för diagnostik och att hitta problem.
- Statusinformation MSAL kommer att logga händelser som är avsedda för informations ändamål som inte nödvändigt vis är avsedda för fel sökning.
- utförlig Standard. MSAL loggar fullständig information om biblioteks beteendet.

## <a name="personal-and-organizational-data"></a>Personliga och organisatoriska data

Som standard fångar MSAL-loggaren inte upp mycket känsliga personliga eller organisatoriska data. Biblioteket ger möjlighet att aktivera loggning av personliga och organisatoriska data om du bestämmer dig för detta.

## <a name="logging-in-msalnet"></a>Logga in MSAL.NET

 > [!NOTE]
 > Mer information om MSAL.NET finns i [MSAL.net-wikin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki). Hämta exempel på MSAL.NET-loggning och mer.
 
I MSAL 3. x anges loggning per program när appen skapas med hjälp av `.WithLogging` Builder-modifieraren. Den här metoden kräver valfria parametrar:

- \- *Nivå* kan du bestämma vilken loggnings nivå du vill ha. Om du ställer in det på fel får du bara fel meddelanden
- Med *PiiLoggingEnabled* kan du logga personliga och organisatoriska data om värdet är true. Som standard är detta inställt på falskt, så att programmet inte loggar personliga data.
- *LogCallback* har angetts till ett ombud som utför loggningen. Om *PiiLoggingEnabled* är true får den här metoden meddelandena två gånger: en gång med parametern *containsPii* är lika med falskt och meddelandet utan personliga data, och en andra gång med *containsPii* -parametern lika med sant och meddelandet kan innehålla personliga data. I vissa fall (när meddelandet inte innehåller personliga data) är meddelandet samma.
- *DefaultLoggingEnabled* aktiverar standard loggning för plattformen. Som standard är det falskt. Om du anger värdet till sant används händelse spårning i Desktop/UWP-program, NSLog på iOS och logcat på Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Logga in MSAL. js

 Du kan aktivera loggning i MSAL. js genom att skicka ett loggnings objekt under konfigurationen för att `UserAgentApplication` skapa en instans. Detta loggnings objekt har följande egenskaper:

- `localCallback`: en callback-instans som kan tillhandahållas av utvecklaren för att använda och publicera loggar på ett anpassat sätt. Implementera localCallback-metoden beroende på hur du vill dirigera om loggarna.

- `level`(valfritt): den konfigurerbara logg nivån. De logg nivåer som stöds är: Fel, varning, info, verbose. Standardvärdet är info.

- `piiLoggingEnabled`(valfritt): gör att du kan logga personliga och organisatoriska data om värdet är true. Som standard är detta inställt på falskt så att programmet inte loggar personliga data. Personliga data loggar skrivs aldrig till standardutdata som konsol, logcat eller NSLog. Standardvärdet är inställt på falskt.

- `correlationId`(valfritt): en unik identifierare som används för att mappa begäran med svar på fel söknings syfte. Standardvärdet är RFC4122 version 4 GUID (128 bitar).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
     system: {
             logger: new Msal.Logger(
                                loggerCallback ,{
                                     level: Msal.LogLevel.Verbose,
                                     piiLoggingEnabled: false,
                                     correlationId: '1234'
                                }
                        )
     }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="logging-in-msal-for-ios-and-macos"></a>Logga in MSAL för iOS och macOS

Ange ett återanrop för att avbilda MSAL-loggning och införliva den i ditt eget programs loggning. Signaturen för återanropet ser ut så här:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Exempel:

Objective-C
```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

SWIFT
```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-identifiable-information-pii"></a>Personlig identifierbar information (PII)

Som standard samlar MSAL inte in eller loggar all personligt identifierbar information. Med biblioteket kan utvecklare aktivera detta genom en egenskap i MSALLogger-klassen. Genom att aktivera personligt identifierbar information tar appen ansvar för säker hantering av mycket känsliga data och följande regler.

Objective-C
```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

SWIFT
```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Loggnings nivåer

Använd något av följande värden om du vill ange loggnings nivå när du loggar med MSAL för iOS och macOS:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standard nivå skrivs bara ut information när fel uppstår |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteks start punkter, med parametrar och olika nyckel rings åtgärder |
|`MSALLogLevelVerbose`     |  API-spårning       |

Exempel:

Objective-C
```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```
 
 SWIFT
```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID: n och tidsstämplar är användbara för att spåra problem. Information om tidsstämpel-och korrelations-ID finns i logg meddelandet. Den enda tillförlitliga platsen för att hämta dem är från MSAL loggnings meddelanden.
