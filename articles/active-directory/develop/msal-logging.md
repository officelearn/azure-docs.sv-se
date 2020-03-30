---
title: Logga in MSAL-appar | Azure
titleSuffix: Microsoft identity platform
description: Läs mer om hur du loggar in i MSAL-program (Microsoft Authentication Library).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 58697cc535357710c6889f05060b5e04e129ae7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084888"
---
# <a name="logging-in-msal-applications"></a>Logga in i MSAL-program

Msal-appar (Microsoft Authentication Library) genererar loggmeddelanden som kan hjälpa till att diagnostisera problem. En app kan konfigurera loggning med några rader kod och ha anpassad kontroll över detaljnivån och om personliga och organisatoriska data loggas eller inte. Vi rekommenderar att du skapar en MSAL-loggningsåterring och ger användarna ett sätt att skicka loggar när de har autentiseringsproblem.

## <a name="logging-levels"></a>Loggningsnivåer

MSAL ger flera nivåer av loggning detalj:

- Fel: Anger att något har gått fel och ett fel har genererats. Används för felsökning och identifiering av problem.
- Varning: Det har inte nödvändigtvis skett ett fel eller fel, men är avsedda för diagnostik och identifiera problem.
- Info: MSAL loggar händelser som är avsedda för informationsändamål som inte nödvändigtvis är avsedda för felsökning.
- Utförlig: Standard. MSAL loggar fullständig information om bibliotekets beteende.

## <a name="personal-and-organizational-data"></a>Personliga och organisatoriska data

Som standard samlar MSAL-loggern inte in några mycket känsliga personliga eller organisatoriska data. Biblioteket ger möjlighet att logga personliga och organisatoriska data om du bestämmer dig för att göra det.

Om du vill ha mer information om ATT MSAL loggar in på ett visst språk väljer du fliken som matchar ditt språk:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Logga in MSAL.NET

 > [!NOTE]
 > Se [den MSAL.NET wikin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) för exempel på MSAL.NET loggning med mera.

I MSAL 3.x ställs loggning in per `.WithLogging` program när appen skapas med hjälp av byggmodifieraren. Den här metoden tar valfria parametrar:

- `Level`kan du bestämma vilken loggningsnivå du vill ha. Om du ställer in den på fel får du bara fel
- `PiiLoggingEnabled`kan du logga personliga och organisatoriska data om de är inställda på true. Som standard är detta inställt på false, så att ditt program inte loggar personuppgifter.
- `LogCallback`är inställt på ett ombud som gör loggningen. Om `PiiLoggingEnabled` det är sant kommer den här metoden `containsPii` att ta emot meddelandena två gånger: en gång `containsPii` med parametern är lika med falskt och meddelandet utan personuppgifter, och en andra gång med parametern är lika med true och meddelandet kan innehålla personuppgifter. I vissa fall (när meddelandet inte innehåller personuppgifter) kommer meddelandet att vara detsamma.
- `DefaultLoggingEnabled`möjliggör standardloggning för plattformen. Som standard är det falskt. Om du ställer in den på true använder den Händelsespårning i Desktop/UWP-program, NSLog på iOS och logcat på Android.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Logga in MSAL för Android med Java

Aktivera inloggning när appen skapas genom att skapa en loggningsåterring. Motringningen tar dessa parametrar:

- `tag`är en sträng som skickas till motringningen av biblioteket. Den är associerad med loggposten och kan användas för att sortera loggningsmeddelanden.
- `logLevel`kan du bestämma vilken loggningsnivå du vill ha. De loggnivåer som `Error` `Warning`stöds `Info`är: , , och `Verbose`.
- `message`är innehållet i loggposten.
- `containsPII`anger om meddelanden som innehåller personuppgifter eller organisationsdata loggas. Som standard är detta inställt på falskt, så att ditt program inte loggar personuppgifter. Om `containsPII` `true`är , kommer denna metod att ta `containsPII` emot `false` meddelanden `message` två gånger: en gång med `containsPii` parametern `true` inställd på och utan personuppgifter, och en andra gång med parametern inställd på och meddelandet kan innehålla personuppgifter. I vissa fall (när meddelandet inte innehåller personuppgifter) kommer meddelandet att vara detsamma.

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

Som standard kommer MSAL logger inte fånga någon personlig identifierbar information eller organisatorisk identifierbar information.
Så här möjliggör du loggning av personlig identifierbar information eller organisatorisk identifierbar information:

```java
Logger.getInstance().setEnablePII(true);
```

Så här inaktiverar du loggning av personuppgifter och organisationsdata:

```java
Logger.getInstance().setEnablePII(false);
```

Som standard är loggning till logcat inaktiverad. Så här aktiverar du:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[Javascript](#tab/javascript)

 Aktivera loggning i MSAL.js (JavaScript) genom att skicka ett `UserAgentApplication` loggerobjekt under konfigurationen för att skapa en instans. Det här loggerobjektet har följande egenskaper:

- `localCallback`: En motringningsinstans som kan tillhandahållas av utvecklaren för att använda och publicera loggar på ett anpassat sätt. Implementera metoden localCallback beroende på hur du vill omdirigera loggar.
- `level`(valfritt): den konfigurerbara loggnivån. De loggnivåer som `Error` `Warning`stöds `Info`är: , , och `Verbose`. Standardvärdet är `Info`.
- `piiLoggingEnabled`(valfritt): Om värdet är true loggar du personliga och organisatoriska data. Som standard är detta falskt så att ditt program inte loggar personuppgifter. Personliga dataloggar skrivs aldrig till standardutdata som Konsol, Loggcat eller NSLog.
- `correlationId`(valfritt): en unik identifierare som används för att mappa begäran med svaret för felsökning. Standardvärdet för RFC4122 version 4 guid (128 bitar).

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

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL för iOS och macOS-loggning-ObjC

Ställ in en motringning för att fånga MSAL-loggning och införliva den i ditt eget programs loggning. Signaturen för motringningen ser ut så här:

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

### <a name="personal-data"></a>Personuppgifter

Som standard samlar MSAL inte in eller loggar några personuppgifter (PII). Biblioteket gör det möjligt för apputvecklare att aktivera detta via en egenskap i KLASSEN MSALLogger. Genom att `pii.Enabled`slå på tar appen ansvar för att hantera mycket känsliga data på ett säkert sätt och följa lagstadgade krav.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Loggningsnivåer

Om du vill ange loggningsnivån när du loggar med MSAL för iOS och macOS använder du något av följande värden:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standardnivå, skriver ut information endast när fel inträffar |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteksinmatningspunkter, med parametrar och olika nyckelringsoperationer |
|`MSALLogLevelVerbose`     |  API-spårning |

Ett exempel:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Loggmeddelandeformat

Meddelandedelen av MSAL-loggmeddelanden är i form av`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ett exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID och tidsstämplar är till hjälp för att spåra problem. Tidsstämpel- och korrelations-ID-information är tillgänglig i loggmeddelandet. Den enda tillförlitliga platsen att hämta dem är från MSAL loggning meddelanden.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL för iOS- och macOS-loggning-Swift

Ställ in en motringning för att fånga MSAL-loggning och införliva den i ditt eget programs loggning. Signaturen (representerad i Objective-C) för motringningen ser ut så här:

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

### <a name="personal-data"></a>Personuppgifter

Som standard samlar MSAL inte in eller loggar några personuppgifter (PII). Biblioteket gör det möjligt för apputvecklare att aktivera detta via en egenskap i KLASSEN MSALLogger. Genom att `pii.Enabled`slå på tar appen ansvar för att hantera mycket känsliga data på ett säkert sätt och följa lagstadgade krav.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Loggningsnivåer

Om du vill ange loggningsnivån när du loggar med MSAL för iOS och macOS använder du något av följande värden:

|Nivå  |Beskrivning |
|---------|---------|
| `MSALLogLevelNothing`| Inaktivera all loggning |
| `MSALLogLevelError` | Standardnivå, skriver ut information endast när fel inträffar |
| `MSALLogLevelWarning` | Varningar |
| `MSALLogLevelInfo` |  Biblioteksinmatningspunkter, med parametrar och olika nyckelringsoperationer |
|`MSALLogLevelVerbose`     |  API-spårning |

Ett exempel:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Loggmeddelandeformat

Meddelandedelen av MSAL-loggmeddelanden är i form av`TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Ett exempel:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Att tillhandahålla korrelations-ID och tidsstämplar är till hjälp för att spåra problem. Tidsstämpel- och korrelations-ID-information är tillgänglig i loggmeddelandet. Den enda tillförlitliga platsen att hämta dem är från MSAL loggning meddelanden.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL för Java-loggning

MSAL för Java kan du använda loggningsbiblioteket som du redan använder med din app, så länge det är kompatibelt med SLF4J. MSAL för Java använder [Simple Logging Facade för Java](http://www.slf4j.org/) (SLF4J) som en enkel fasad eller abstraktion för olika loggningsramverk, till exempel [java.util.logging,](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html) [Logback](http://logback.qos.ch/) och [Log4j](https://logging.apache.org/log4j/2.x/). SLF4J tillåter användaren att ansluta önskat loggningsramverk vid distributionen.

Om du till exempel vill använda Logback som loggningsramverk i ditt program lägger du till logback-beroendet i Maven pom-filen för ditt program:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Lägg sedan till konfigurationsfilen för logg tillbaka:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J binder automatiskt till Logback vid distributionen. MSAL-loggar skrivs till konsolen.

Instruktioner om hur du binder till andra loggningsramverk finns i [SLF4J-handboken](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Personlig information och organisationsinformation

Som standard samlar MSAL-loggning inte in eller loggar några personliga data eller organisationsdata. I följande exempel är loggning av personliga data eller organisationsdata inaktiverad som standard:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Aktivera personlig och organisatorisk dataloggning `logPii()` genom att ange på klientprogramverktyget. Om du aktiverar personlig eller organisatorisk dataloggning måste din app ta ansvar för att hantera mycket känsliga data på ett säkert sätt och uppfylla eventuella lagstadgade krav.

I följande exempel är loggning av personliga data eller organisationsdata aktiverat:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL för Python-loggning

Genom att logga in MSAL Python används standardmekanismen för Python-loggning, till exempel `logging.info("msg")` Du kan konfigurera MSAL-loggning enligt följande (och se den i aktion i [username_password_sample):](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)

### <a name="enable-debug-logging-for-all-modules"></a>Aktivera felsökningsloggning för alla moduler

Som standard är loggningen i ett Python-skript inaktiverat. Om du vill aktivera felsökningsloggning för alla moduler i hela Python-skriptet använder du:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Endast MSAL-loggning tystas

Om du bara vill tysta MSAL-biblioteksloggningen inaktiverar du loggern som används av MSAL Python när du aktiverar felsökningsloggning i alla andra moduler i Python-skriptet:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Personliga och organisatoriska data i Python

MSAL för Python loggar inte personliga data eller organisationsdata. Det finns ingen egendom som kan aktivera eller inaktivera personlig dataloggning.

Du kan använda standardiserad Python-loggning för att logga vad du vill, men du ansvarar för att hantera känsliga data på ett säkert sätt och följa lagstadgade krav.

Mer information om hur du loggar in i Python finns i Pythons [Loggnings HOWTO](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
