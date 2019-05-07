---
title: Skapa en instans av en offentlig klient-app med alternativ (Microsoft Authentication Library för .NET) | Azure
description: Lär dig mer om att skapa en instans av en offentlig klientprogram konfigurationsalternativ med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158707"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Skapa en instans av en offentlig klientprogram konfigurationsalternativ med hjälp av MSAL.NET

Den här artikeln beskriver hur du skapa en instans av en [offentliga klientprogram](msal-client-applications.md) med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).  Programmet instantieras konfigurationsalternativ som definierats i en fil med inställningar.

Innan du initierar ett program, måste du först [registrera](quickstart-register-app.md) den så att din app kan integreras med Microsoft identity-plattformen. Efter registreringen behöver du följande information (som finns i Azure portal):

- Klient-ID (en sträng som representerar ett GUID)
- Den URL för identitetsprovider (med namnet instansen) och logga in-målgruppen för ditt program. Dessa två parametrar kallas sammantaget behörighet.
- Klient-ID om du skriver ett verksamhetsspecifikt program endast för din organisation (som också namngivna enda klient program).
- För web apps, och ibland för offentlig klient apps (i synnerhet när din app behöver använda en asynkron meddelandekö), måste du också ställa redirectUri där identitetsprovidern kommer att kontakta tillbaka ditt program med säkerhetstoken.


Ett .NET Core-konsolprogram kan ha följande *appsettings.json* konfigurationsfil:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Följande kod läser den här filen med hjälp av .NET framework för konfiguration:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Följande kod skapar ditt program, med konfigurationen från filen:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

