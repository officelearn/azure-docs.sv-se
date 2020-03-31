---
title: Konfigurationsfil för Android MSAL | Azure
titleSuffix: Microsoft identity platform
description: En översikt över konfigurationsfilen för MSAL (Android Microsoft Authentication Library), som representerar ett programs konfiguration i Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050381"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Konfigurationsfil för Microsoft-autentiseringsbibliotek i Android

Android Microsoft Authentication Library (MSAL) levereras med en [standardkonfiguration JSON-fil](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) som du anpassar för att definiera beteendet för din offentliga klientapp för saker som standardutfärdaren, vilka myndigheter du ska använda och så vidare.

Den här artikeln hjälper dig att förstå de olika inställningarna i konfigurationsfilen och hur du anger den konfigurationsfil som ska användas i din MSAL-baserade app.

## <a name="configuration-settings"></a>Konfigurationsinställningar

### <a name="general-settings"></a>Allmänna inställningar

| Egenskap | Datatyp | Krävs | Anteckningar |
|-----------|------------|-------------|-------|
| `client_id` | String | Ja | Appens klient-ID från [sidan För registrering av program](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | String | Ja | Appens Omdirigera URI från [sidan För registrering av program](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | Lista\<myndighet> | Inga | Listan över myndigheter som appen behöver |
| `authorization_user_agent` | AuthorizationAgent (uppräkning) | Inga | Möjliga `DEFAULT`värden: `BROWSER`,`WEBVIEW` |
| `http` | HttpKonfigurering | Inga | Konfigurera `HttpUrlConnection` `connect_timeout` och`read_timeout` |
| `logging` | LoggningKonfigurering | Inga | Anger loggningsdetaljerna. Valfria konfigurationer `pii_enabled`inkluderar: , som tar `log_level`ett `ERROR`booleskt värde och , som tar , `WARNING`, `INFO`eller `VERBOSE`. |

### <a name="client_id"></a>client_id

Klient-ID eller app-ID som skapades när du registrerade ditt program.

### <a name="redirect_uri"></a>redirect_uri

Den omdirigera URI du registrerade när du registrerade ditt program. Om omdirigerings-URI:n är till en mäklarapp läser du [Omdirigera URI för offentliga klientappar för](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) att säkerställa att du använder rätt omdirigerings-URI-format för din mäklarapp.

### <a name="authorities"></a>Myndigheterna

Listan över myndigheter som är kända och betrodda av dig. Förutom de myndigheter som anges här, frågar MSAL också Microsoft för att få en lista över moln och myndigheter som är kända för Microsoft. I den här listan över myndigheter anger du typen av `"audience"`myndighet och eventuella ytterligare valfria parametrar, till exempel , som ska anpassas till målgruppen för din app baserat på appens registrering. Följande är en exempellista över myndigheter:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mappa AAD-behörighet & målgrupp till slutpunkter för Microsoft-identitetsplattform

| Typ | Målgrupp | Klient-ID:t | Authority_Url | Resulterande slutpunkt | Anteckningar |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftKonto | | | `https://login.microsoftonline.com/common` | `common`är ett klientalias där kontot finns. Till exempel en specifik Azure Active Directory-klientorganisation eller Microsoft-kontosystemet. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Endast konton som finns i contoso.com kan hämta en token. Alla verifierade domäner, eller klient-GUID, kan användas som klient-ID. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | Endast Azure Active Directory-konton kan användas med den här slutpunkten. Microsoft-konton kan vara medlemmar i organisationer. Om du vill hämta en token med ett Microsoft-konto för en resurs i en organisation anger du den organisationsklient som du vill ha token från. |
| AAD | PersonalMicrosoftKonto | | | `https://login.microsoftonline.com/consumers` | Endast Microsoft-konton kan använda den här slutpunkten. |
| B2C | | | Se resulterande slutpunkt | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Endast konton som finns i contoso.onmicrosoft.com klienten kan hämta en token. I det här exemplet är B2C-principen en del av url-sökvägen för myndigheten. |

> [!NOTE]
> Myndighetsvalidering kan inte aktiveras och inaktiveras i MSAL.
> Myndigheter är antingen kända för dig som utvecklare enligt konfigurationen eller kända för Microsoft via metadata.
> Om MSAL tar emot en begäran om `MsalClientException` en `UnknownAuthority` token till en okänd myndighet, resultat av typ.

#### <a name="authority-properties"></a>Egenskaper för myndighet

| Egenskap | Datatyp  | Krävs | Anteckningar |
|-----------|-------------|-----------|--------|
| `type` | String | Ja | Speglar målgruppen eller kontotypen dina appmål. Möjliga värden: `AAD`,`B2C` |
| `audience` | Objekt | Inga | Gäller endast när`AAD`typ= . Anger vilken identitet appen riktar sig till. Använda värdet från din appregistrering |
| `authority_url` | String | Ja | Krävs endast när`B2C`typ= . Anger vilken myndighets-URL eller princip som appen ska använda  |
| `default` | boolean | Ja | En `"default":true` enda krävs när en eller flera myndigheter anges. |

#### <a name="audience-properties"></a>Målgruppsegenskaper

| Egenskap | Datatyp  | Krävs | Anteckningar |
|-----------|-------------|------------|-------|
| `type` | String | Ja | Anger vilken målgrupp appen vill rikta in sig på. Möjliga `AzureADandPersonalMicrosoftAccount`värden: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, ,`AzureADMyOrg` |
| `tenant_id` | String | Ja | Krävs endast `"type":"AzureADMyOrg"`när . Valfritt `type` för andra värden. Detta kan vara en `contoso.com`klientdomän som , `72f988bf-86f1-41af-91ab-2d7cd011db46`eller ett klient-ID som ) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Anger om du vill använda en inbäddad webbvy eller standardwebbläsaren på enheten när du loggar in på ett konto eller ger åtkomst till en resurs.

Möjliga värden:
- `DEFAULT`: Föredrar systembläddraren. Använder den inbäddade webbvyn om en webbläsare inte är tillgänglig på enheten.
- `WEBVIEW`: Använd den inbäddade webbvyn.
- `BROWSER`: Använder standardwebbläsaren på enheten.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

För klienter som stöder flera `true`nationella moln anger du . Microsofts identitetsplattform omdirigeras sedan automatiskt till rätt nationellt moln under auktorisering och tokeninlösen. Du kan bestämma det nationella molnet för det inloggade `AuthenticationResult`kontot genom att undersöka den myndighet som är associerad med . Observera att `AuthenticationResult` den inte ger den nationella molnspecifika slutpunktsadressen för den resurs som du begär en token för.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

En boolesk som anger om du använder en Microsoft Identity-mäklare kompatibel i mäklare omdirigera URI. Ställ `false` in på om du inte vill använda mäklaren i din app.

Om du använder AAD-myndigheten med `"MicrosoftPersonalAccount"`målgrupp inställd på används inte mäklaren.

### <a name="http"></a>http

Konfigurera globala inställningar för HTTP-timeout, till exempel:

| Egenskap | Datatyp | Krävs | Anteckningar |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Inga | Tid i millisekunder |
| `read_timeout` | int | Inga | Tid i millisekunder |

### <a name="logging"></a>loggning

Följande globala inställningar är för loggning:

| Egenskap | Datatyp  | Krävs | Anteckningar |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | Inga | Om personuppgifter ska avs. |
| `log_level`   | boolean | Inga | Vilka loggmeddelanden som ska matas ut |
| `logcat_enabled` | boolean | Inga | Om du vill skriva ut för att logga katt utöver loggningsgränssnittet |

### <a name="account_mode"></a>account_mode

Anger hur många konton som kan användas i appen åt gången. Möjliga värden är:

- `MULTIPLE`(Standard)
- `SINGLE`

Om du `PublicClientApplication` konstruerar ett kontoläge som inte matchar den här inställningen blir det ett undantag.

Mer information om skillnaderna mellan enstaka och flera konton finns i [Appar för ett och flera konton](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

En tillåt-lista över webbläsare som är kompatibla med MSAL. Dessa webbläsare hanterar omdirigeringar till anpassade avsikter på rätt sätt. Du kan lägga till i den här listan. Standardvärdet finns i standardkonfigurationen som visas nedan.
``
## <a name="the-default-msal-configuration-file"></a>Standardkonfigurationsfilen för MSAL

Standardkonfigurationen för MSAL som levereras med MSAL visas nedan. Du kan se den senaste versionen på [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Den här konfigurationen kompletteras med värden som du anger. De värden som du anger åsidosätter standardvärdena.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Exempel på grundläggande konfiguration

Följande exempel illustrerar en grundläggande konfiguration som anger klient-ID, omdirigera URI, om en mäklare omdirigera är registrerad och en lista över myndigheter.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Så här använder du en konfigurationsfil

1. Skapa en konfigurationsfil. Vi rekommenderar att du skapar `res/raw/auth_config.json`din anpassade konfigurationsfil i . Men du kan lägga den var du vill.
2. Tala om för MSAL var du `PublicClientApplication`ska leta efter konfigurationen när du konstruerar . Ett exempel:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
