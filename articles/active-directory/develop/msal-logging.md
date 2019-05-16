---
title: Logga in MSAL program | Azure
description: Lär dig om att logga in Microsoft Authentication Library (MSAL) program.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/22/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58f18995d46ca61ae68a7b226bbfc9a286e73a0b
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544101"
---
# <a name="logging"></a>Loggning
Microsoft Authentication Library (MSAL) appar att generera loggmeddelanden som kan hjälpa dig att diagnostisera problem och ange information. En app kan konfigurera loggning med några rader kod och anpassad kontroll över information och huruvida privata och organisationens data loggas. Vi rekommenderar att du ange ett återanrop i MSAL loggning och gör det möjligt för användare att skicka loggar när de har problem med autentisering.

## <a name="logging-levels"></a>Loggningsnivåer

MSALS loggaren möjliggör flera olika detaljnivåer ska avbilda:

- Fel: Anger har något gått fel och ett fel genereras. Använd för att felsöka och identifiera problem.
- Varning: Händelser som har en fråga och appen behöver mer information på. Det har inte nödvändigtvis har ett fel eller fel, men avsett för diagnostik- och lokalisera problem.
- Info: MSAL loggar händelser som är avsedd för endast i informationssyfte inte alltid är avsett för felsökning.
- utförlig: Standard. MSAL ska logga en stor mängd information och ge fullständig information till vilka bibliotek beteende.

## <a name="personal-and-organizational-data"></a>Privata och organisationens data
Som standard in MSAL loggaren inte alla mycket känslig personlig eller organisationens data. Biblioteket ger dig möjlighet att aktivera loggning privata och organisationens data om du vill göra detta.

## <a name="logging-in-msalnet"></a>Logga in MSAL.NET
I MSAL 3.x loggning anges per program vid skapas med hjälp av den `.WithLogging` builder modifieraren. Den här metoden tar följande valfria parametrar:

- *Nivå* kan du bestämma vilken nivå av loggning som du vill. Ange den till fel får endast fel
- *PiiLoggingEnabled* kan du logga privata och organisationens data om värdet är true. Detta är som standard till false, så att ditt program inte loggar personliga data.
- *LogCallback* har angetts till ett ombud som utför loggning. Om *PiiLoggingEnabled* är sant, den här metoden tar emot meddelanden två gånger: en gång med den *containsPii* parametern är lika med false och ett meddelande utan att personliga data och en andra gång med *containsPii* parametern är lika med SANT och meddelandet kan innehålla personuppgifter. I vissa fall (när meddelandet innehåller inga personliga data), kommer meddelandet att vara samma.
- *DefaultLoggingEnabled* aktiverar Standardloggning för plattformen. Som standard är FALSKT. Om du ställer in värdet till SANT det du använder händelsespårning i Desktop/UWP program, NSLog på iOS- och logcat på Android.

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
                                             .ExecuteAsnc();
  }
 }
 ```

 ## <a name="logging-in-msaljs"></a>Logga in MSAL.js

 Du kan aktivera loggning i MSAL.js genom att skicka en logger-objekt under konfiguration för att skapa en `UserAgentApplication` instans. Det här logger-objektet har följande egenskaper:

- *localCallback*: en motringningsinstansen som kan tillhandahållas av utvecklaren att förbruka och publicera loggar på ett anpassat sätt. Implementera metoden localCallback beroende på hur du vill omdirigera loggar.

- *nivå* (valfritt): konfigurerbara loggningsnivån. Stöds Loggnivåerna är: Fel, varning, information, utförlig. Standardvärdet är information.

- *piiLoggingEnabled* (valfritt): gör att du kan logga privata och organisationens data om värdet är true. Som standard är detta inställt på FALSKT så att ditt program inte loggar personliga data. Personliga data loggarna skrivs aldrig till standard-utdata som konsolen, Logcat eller NSLog. Standard är inställd på false.

- *correlationId* (valfritt): en unik identifierare som används för att mappa förfrågan med svar för felsökning. Standardvärdet är guid för RFC4122 version 4 (128 bitar).

```javascript

function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”,
    },
    system: {
        logger: {
            localCallback: loggerCallback,
            level: Msal.LogLevel.Verbose,
            piiLoggingEnabled: false,
            correlationId: '1234'
        }
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```
