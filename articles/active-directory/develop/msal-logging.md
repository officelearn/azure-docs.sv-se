---
title: Logga in MSAL-appar | Azure
titleSuffix: Microsoft identity platform
description: Lär dig mer om loggning i MSAL-program (Microsoft Authentication Library).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: cba213824cba86b4ea54e852b7e2f1ede83abfea
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695816"
---
# <a name="logging-in-msal-applications"></a>Logga in MSAL-program

MSAL-appar (Microsoft Authentication Library) genererar logg meddelanden som kan hjälpa dig att diagnostisera problem. En app kan konfigurera loggning med några rader kod och har anpassad kontroll över detalj nivån och huruvida personliga och organisatoriska data loggas. Vi rekommenderar att du skapar ett återanrop för MSAL-loggning och ger användarna möjlighet att skicka loggar när de har problem med autentiseringen.

## <a name="logging-levels"></a>Logging levels

MSAL tillhandahåller flera nivåer av loggnings information:

- Fel: indikerar att något har gått fel och ett fel genererades. Använd för fel sökning och identifiering av problem.
- Varning! det har inte nödvändigt vis uppstått något fel eller fel, men är avsedda för diagnostik och att hitta problem.
- Info: MSAL kommer att logga händelser som är avsedda för informations ändamål som inte nödvändigt vis är avsedda för fel sökning.
- Verbose: standard. MSAL loggar fullständig information om biblioteks beteendet.

## <a name="personal-and-organizational-data"></a>Personliga och organisatoriska data

Som standard fångar MSAL-loggaren inte upp mycket känsliga personliga eller organisatoriska data. Biblioteket ger möjlighet att aktivera loggning av personliga och organisatoriska data om du bestämmer dig för detta.

Om du vill ha mer information om MSAL-loggning på ett visst språk väljer du fliken som matchar ditt språk:

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Logga in MSAL.NET

 > [!NOTE]
 > Se [MSAL.net-wikin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) för exempel på MSAL.net-loggning med mera.

I MSAL 3. x anges loggning per program när appen skapas med hjälp av `.WithLogging` Builder-modifieraren. Den här metoden kräver valfria parametrar:

- med `Level` kan du bestämma vilken loggnings nivå du vill ha. Om du ställer in det på fel får du bara fel meddelanden
- med `PiiLoggingEnabled` kan du logga personliga och organisatoriska data om värdet är true. Som standard är detta inställt på falskt, så att programmet inte loggar personliga data.
- `LogCallback` har angetts till ett ombud som utför loggningen. Om `PiiLoggingEnabled` har värdet True får den här metoden meddelandena två gånger: en gång med parametern `containsPii` är lika med falskt och meddelandet utan personliga data, och en andra gång med `containsPii`-parametern lika med sant och meddelandet kan innehålla personliga data. In some cases (when the message does not contain personal data), the message will be the same.
- `DefaultLoggingEnabled` aktiverar standard loggning för plattformen. Som standard är det falskt. Om du anger värdet till sant används händelse spårning i Desktop/UWP-program, NSLog på iOS och logcat på Android.

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

## <a name="androidtabandroid"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Logga in MSAL för Android med Java

Aktivera loggning när appen skapas genom att skapa en loggning för motringning. Återanropet använder följande parametrar:

- `tag` är en sträng som skickas till återanropet av biblioteket. Den är kopplad till logg posten och kan användas för att sortera loggnings meddelanden.
- med `logLevel` kan du bestämma vilken loggnings nivå du vill ha. The supported log levels are: `Error`, `Warning`, `Info`, and `Verbose`.
- `message` is the content of the log entry.
- `containsPII` specifies whether messages containing personal data, or organizational data are logged. By default, this is set to false, so that your application doesn't log personal data. If `containsPII` is `true`, this method will receive the messages twice: once with the `containsPII` parameter set to `false` and the `message` without personal data, and a second time with the `containsPii` parameter set to `true` and the message might contain personal data. In some cases (when the message does not contain personal data), the message will be the same.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

By default, the MSAL logger will not capture any personal identifiable information or organizational identifiable information.
To enable the logging of personal identifiable information or organizational identifiable information:

```java
Logger.getInstance().setEnablePII(true);
```

To disable logging personal data and organization data:

```java
Logger.getInstance().setEnablePII(false);
```

By default logging to logcat is disabled. To enable:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

 Enable logging in MSAL.js (JavaScript) by passing a logger object during the configuration for creating a `UserAgentApplication` instance. This logger object has the following properties:

- `localCallback`: a Callback instance that can be provided by the developer to consume and publish logs in a custom manner. Implement the localCallback method depending on how you want to redirect logs.
- `level` (optional): the configurable log level. The supported log levels are: `Error`, `Warning`, `Info`, and `Verbose`. Standardvärdet är `Info`.
- `piiLoggingEnabled` (optional): if set to true, logs personal and organizational data. By default this is false so that your application doesn't log personal data. Personal data logs are never written to default outputs like Console, Logcat, or NSLog.
- `correlationId` (optional): a unique identifier, used to map the request with the response for debugging purposes. Defaults to RFC4122 version 4 guid (128 bits).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
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

## <a name="objective-ctabobjc"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL for iOS and macOS logging-ObjC

Set a callback to capture MSAL logging and incorporate it in your own application's logging. The signature for the callback looks like this:

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

Ett exempel:

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

### <a name="personal-data"></a>Personliga data

By default, MSAL doesn't capture or log any personal data (PII). The library allows app developers to turn this on through a property in the MSALLogger class. By turning on `pii.Enabled`, the app takes responsibility for safely handling highly sensitive data and following regulatory requirements.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Logging levels

To set the logging level when you log using MSAL for iOS and macOS, use one of the following values:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standard nivå skrivs bara ut information när fel uppstår |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteks start punkter, med parametrar och olika nyckel rings åtgärder |
|`MSALLogLevelVerbose`     |  API-spårning |

Ett exempel:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ett exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID: n och tidsstämplar är användbara för att spåra problem. Information om tidsstämpel-och korrelations-ID finns i logg meddelandet. Den enda tillförlitliga platsen för att hämta dem är från MSAL loggnings meddelanden.

## <a name="swifttabswift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL for iOS and macOS logging-Swift

Set a callback to capture MSAL logging and incorporate it in your own application's logging. The signature (represented in Objective-C) for the callback looks like this:

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

Ett exempel:

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

### <a name="personal-data"></a>Personliga data

By default, MSAL doesn't capture or log any personal data (PII). The library allows app developers to turn this on through a property in the MSALLogger class. By turning on `pii.Enabled`, the app takes responsibility for safely handling highly sensitive data and following regulatory requirements.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Logging levels

To set the logging level when you log using MSAL for iOS and macOS, use one of the following values:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standard nivå skrivs bara ut information när fel uppstår |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteks start punkter, med parametrar och olika nyckel rings åtgärder |
|`MSALLogLevelVerbose`     |  API-spårning |

Ett exempel:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Logg meddelande format

Meddelande delen av MSAL logg meddelanden har formatet `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ett exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID: n och tidsstämplar är användbara för att spåra problem. Information om tidsstämpel-och korrelations-ID finns i logg meddelandet. Den enda tillförlitliga platsen för att hämta dem är från MSAL loggnings meddelanden.

## <a name="javatabjava"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL för Java-loggning

Med MSAL för Java kan du använda det loggnings bibliotek som du redan använder med din app, så länge det är kompatibelt med SLF4J. MSAL for Java använder [fasad för enkel loggning för Java](http://www.slf4j.org/) (SLF4J) som en enkel fasad eller abstraktion för olika loggnings ramverk, till exempel [Java. util. logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [logback](http://logback.qos.ch/) och [log4j](https://logging.apache.org/log4j/2.x/). SLF4J gör att användaren kan ansluta till det önskade loggnings ramverket vid distributions tillfället.

Om du till exempel vill använda logback som loggnings ramverk i ditt program lägger du till logback-beroendet till maven Pom-filen för ditt program:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Lägg sedan till konfigurations filen logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J binder automatiskt till logback vid distributions tillfället. MSAL-loggar skrivs till-konsolen.

Instruktioner för hur du binder till andra loggnings ramverk finns i [hand boken för SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Personlig information och organisationsinformation

Som standard samlar MSAL inte in eller loggar personliga eller organisatoriska data. I följande exempel är loggning av personliga eller organisatoriska data inaktiverat som standard:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Aktivera personlig och organisatorisk data loggning genom att ställa in `logPii()` på klient programs Builder. Om du aktiverar personlig eller organisatorisk data loggning måste din app ta ansvar för säker hantering av mycket känsliga data och som uppfyller alla myndighets krav.

I följande exempel är loggning av personliga eller organisatoriska data aktiverade:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="pythontabpython"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL för python-loggning

Loggning i MSAL python använder standard funktionen python-loggning, till exempel `logging.info("msg")` du kan konfigurera MSAL-loggning på följande sätt (och se hur det fungerar i [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Aktivera fel söknings loggning för alla moduler

Som standard är loggningen i valfritt Python-skript inaktive rad. Om du vill aktivera fel söknings loggning för alla moduler i hela python-skriptet använder du:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Enbart tystnads MSAL loggning

Om du bara vill göra en MSAL-biblioteks loggning, samtidigt som du aktiverar fel söknings loggning i alla andra moduler i python-skriptet, inaktiverar du den loggning som används av MSAL python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Personliga och organisatoriska data i python

MSAL for python loggar inte personliga data eller organisations data. Det finns ingen egenskap för att aktivera eller inaktivera personlig eller organisations data inloggning.

Du kan använda standard-python-loggning för att logga vad du vill, men du är ansvarig för att säkert hantera känsliga data och följa regel krav.

Mer information om hur du loggar in python finns i python- [loggning howto](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
