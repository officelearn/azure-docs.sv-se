---
title: Logga in MSAL-program | Azure
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
ms.date: 04/22/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f52ca8062ed1ed196a67d25385b712451afe8ae
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532723"
---
# <a name="logging"></a>Loggning
MSAL-appar (Microsoft Authentication Library) för att generera logg meddelanden som kan hjälpa till att diagnostisera problem och ange information. En app kan konfigurera loggning med några rader kod och har anpassad kontroll över detalj nivån och huruvida personliga och organisatoriska data loggas. Vi rekommenderar att du ställer in ett återanrop för MSAL-loggning och ger användarna möjlighet att skicka loggar när de har problem med autentiseringen.

## <a name="logging-levels"></a>Loggnings nivåer

MSAL: s loggar gör det möjligt att samla flera detalj nivåer:

- Fel: Indikerar att något har gått fel och ett fel har genererats. Använd för fel sökning och identifiering av problem.
- Varning: Händelser som är av fråga och appen behöver mer information om. Det har inte nödvändigt vis uppstått något fel eller fel, men är avsett för diagnostik och att hitta problem.
- Statusinformation MSAL kommer att logga händelser som är avsedda för informations ändamål som inte nödvändigt vis är avsedda för fel sökning.
- Utförlig Standard. MSAL kommer att logga en stor mängd information och ge fullständig information om vilka bibliotek som fungerar.

## <a name="personal-and-organizational-data"></a>Personliga och organisatoriska data
Som standard fångar MSAL-loggaren inte in mycket känsliga personliga eller organisatoriska data. Biblioteket ger dig möjlighet att aktivera loggning av personliga och organisatoriska data om du väljer att göra det.

## <a name="logging-in-msalnet"></a>Logga in MSAL.NET

 > [!NOTE]
 > Mer information om MSAL.NET finns i [MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)-wikin. Hämta exempel på MSAL.NET-loggning och mer. 
 
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

- *localCallback*: en callback-instans som kan tillhandahållas av utvecklaren för att använda och publicera loggar på ett anpassat sätt. Implementera localCallback-metoden beroende på hur du vill dirigera om loggarna.

- *nivå* (valfritt): den konfigurerbara logg nivån. De logg nivåer som stöds är: Fel, varning, info, verbose. Standardvärdet är info.

- *piiLoggingEnabled* (valfritt): gör att du kan logga personliga och organisatoriska data om värdet är true. Som standard är detta inställt på falskt så att programmet inte loggar personliga data. Personliga data loggar skrivs aldrig till standardutdata som konsol, logcat eller NSLog. Standardvärdet är inställt på falskt.

- *correlationId* (valfritt): en unik identifierare som används för att mappa begäran med svar på fel söknings syfte. Standardvärdet är RFC4122 version 4 GUID (128 bitar).

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
