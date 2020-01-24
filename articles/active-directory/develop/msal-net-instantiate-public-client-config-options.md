---
title: Instansiera en offentlig klient app (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du instansierar ett offentligt klient program med konfigurations alternativ med hjälp av Microsoft Authentication Library för .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c07ffb77a838a91bd7eed4b9e40b39050450981c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695508"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instansiera ett offentligt klient program med konfigurations alternativ med MSAL.NET

Den här artikeln beskriver hur du instansierar ett [offentligt klient program](msal-client-applications.md) med hjälp av Microsoft Authentication Library för .net (MSAL.net).  Programmet instansieras med konfigurations alternativ som definierats i en inställnings fil.

Innan du initierar ett program måste du först [Registrera](quickstart-register-app.md) det så att din app kan integreras med Microsoft Identity Platform. Efter registreringen kan du behöva följande information (som du hittar i Azure Portal):

- Klient-ID (en sträng som representerar ett GUID)
- Identitets leverantörens URL (kallas instansen) och inloggnings gruppen för ditt program. Dessa två parametrar kallas gemensamt för utfärdaren.
- Klient-ID: t om du skriver ett affärs program enbart för din organisation (även kallat program med en enda klient).
- För webbappar, och ibland för offentliga klient program (särskilt när appen behöver använda en Service Broker), har du också angett redirectUri där identitets leverantören ska kontakta ditt program igen med säkerhetstoken.


Ett .NET Core-konsolprogram kan ha följande *appSettings. JSON* -konfigurations fil:

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

Följande kod läser filen med .NET Configuration Framework:

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

Följande kod skapar ditt program med hjälp av konfigurationen från inställnings filen:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

