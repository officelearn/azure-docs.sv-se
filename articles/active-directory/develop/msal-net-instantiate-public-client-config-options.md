---
title: Instansiera en offentlig klientapp (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du instansierar ett offentligt klientprogram med konfigurationsalternativ med hjälp av Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083591"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instansiera ett offentligt klientprogram med konfigurationsalternativ med MSAL.NET

I den hÃ¤r artikeln beskrivs hur du instansierar ett [offentligt klientprogram](msal-client-applications.md) med Microsoft Authentication Library for .NET (MSAL.NET).  Programmet instansieras med konfigurationsalternativ som definieras i en inställningsfil.

Innan du initierar ett program måste du först [registrera](quickstart-register-app.md) det så att din app kan integreras med Microsofts identitetsplattform. Efter registreringen kan du behöva följande information (som finns i Azure-portalen):

- Klient-ID (en sträng som representerar ett GUID)
- Identitetsleverantörens URL (namngiven instans) och inloggningsmålgruppen för ditt program. Dessa två parametrar kallas gemensamt myndigheten.
- Klient-ID om du skriver en branschprogram endast för din organisation (även namnet single-tenant application).
- För webbappar och ibland för offentliga klientappar (särskilt när appen behöver använda en mäklare) har du också angett omdirigeuri där identitetsleverantören kontaktar tillbaka ditt program med säkerhetstoken.


Ett .NET Core-konsolprogram kan ha följande *appsettings.json-konfigurationsfil:*

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

Följande kod läser den här filen med .NET-konfigurationsramverket:

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

Följande kod skapar ditt program med hjälp av konfigurationen från inställningsfilen:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

