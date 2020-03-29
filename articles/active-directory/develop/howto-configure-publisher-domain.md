---
title: Konfigurera en apps utgivardomän | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar ett programs utgivardomän så att användarna vet var deras information skickas.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.openlocfilehash: 68040c8ee22454c300296493b6c840eabbca98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76697140"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Så här konfigurerar du ett programs utgivardomän

Ett programs utgivardomän visas för användare i [programmets samtyckesfråga](application-consent-experience.md) för att låta användarna veta var deras information skickas. Program med flera innehavare som har registrerats efter den 21 maj 2019 som inte har en utgivardomän visas som **overifierad**. Program med flera innehavare är program som stöder konton utanför en enda organisationskatalog. till exempel stödja alla Azure AD-konton eller stödja alla Azure AD-konton och personliga Microsoft-konton.

## <a name="new-applications"></a>Nya applikationer

När du registrerar en ny app kan utgivardomänen för din app vara inställd på ett standardvärde. Värdet beror på var appen är registrerad, särskilt om appen är registrerad i en klientorganisation och om klienten har verifierade domäner för klienten.

Om det finns klientverifierade domäner kommer appens utgivardomän som standard att den primära verifierade domänen för klienten. Om det inte finns några klientverifierade domäner (vilket är fallet när programmet inte är registrerat i en klient) anges appens utgivardomän till null.

I följande tabell sammanfattas standardbeteendet för utgivardomänvärdet.  

| Klientverifierade domäner | Standardvärde för utgivardomänen |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primär) | domain2.com |

Om ett utgivardomän för ett program med flera innehavare inte har angetts, eller om den är inställd på en domän som slutar i .onmicrosoft.com, visas appens medgivandefråga **overifierad** i stället för utgivardomänen.

## <a name="grandfathered-applications"></a>Grandfathered applikationer

Om din app registrerades före den 21 maj 2019 visas inte programmets samtyckesfråga **overifierad** om du inte har angett en utgivardomän. Vi rekommenderar att du anger värdet för utgivarens domän så att användarna kan se den här informationen i appens samtyckesfråga.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurera utgivardomän med Azure-portalen

Så här anger du appens utgivardomän.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.

1. Om ditt konto finns i mer än en Azure AD-klient:
   1. Välj din profil på menyn längst upp till höger på sidan och växla sedan **katalog**.
   1. Ändra din session till Azure AD-klienten där du vill skapa ditt program.

1. Navigera till [Azure Active Directory > Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) för att hitta och välja den app som du vill konfigurera.

   När du har valt appen visas appens **översiktssida.**

1. På appens **översiktssida** väljer du avsnittet **Branding.**

1. Leta reda på **fältet Publisher-domän** och välj något av följande alternativ:

   - Välj **Konfigurera en domän** om du inte redan har konfigurerat en domän.
   - Välj **Uppdatera domän** om en domän redan har konfigurerats.

Om din app är registrerad i en klientorganisation visas två flikar att välja mellan: **Välj en verifierad domän** och Verifiera en ny **domän**.

Om din app inte är registrerad i en klientorganisation visas bara alternativet att verifiera en ny domän för ditt program.

### <a name="to-verify-a-new-domain-for-your-app"></a>Så här verifierar du en ny domän för din app

1. Skapa en `microsoft-identity-association.json` fil med namnet och klistra in följande JSON-kodavsnitt.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Ersätt platshållaren *{YOUR-APP-ID-HERE}* med det program -(klient)-ID som motsvarar din app.

1. Värd för filen `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`på: . Ersätt platshållaren *{YOUR-DOMAIN-HERE}* så att den matchar den verifierade domänen.

1. Klicka på knappen **Verifiera och spara domän.**

### <a name="to-select-a-verified-domain"></a>Så här väljer du en verifierad domän

- Om din klient har verifierade domäner väljer du en av domänerna i **listrutan Välj en verifierad domän.**

>[!Note]
> Det förväntade "Content-Type"-huvudet som `application/json`ska returneras är . Du kan få ett felmeddelande som nämns nedan om du använder något annat som`application/json; charset=utf-8` 
> 
>``` "Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value. " ```
>

## <a name="implications-on-the-app-consent-prompt"></a>Konsekvenser för appmedgivande prompten

Konfigurera utgivardomänen påverkar vad användarna ser på appmedgivande prompten. Information om hur du till fullo förstår komponenterna i samtyckesprompten finns i [Förstå programgodkännandeupplevelserna](application-consent-experience.md).

I följande tabell beskrivs beteendet för program som skapats före den 21 maj 2019.

![Samtyckesfråga för appar som skapats före den 21 maj 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Beteendet för nya program som skapats efter den 21 maj 2019 beror på utgivardomänen och typen av program. I följande tabell beskrivs de ändringar som du bör förvänta dig att se med de olika kombinationerna av konfigurationer.

![Samtyckesfråga för appar som skapats efter 21 maj 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Konsekvenser för omdirigera URI:er

Program som loggar in användare med arbets- eller skolkonton eller personliga[Microsoft-konton](single-and-multi-tenant-apps.md)( flera innehavare ) omfattas av få begränsningar när de anger omdirigera URI:er.

### <a name="single-root-domain-restriction"></a>Begränsning av en enda rotdomän

När utgivardomänvärdet för appar med flera innehavare är inställt på null, är appar begränsade till att dela en enda rotdomän för omdirigerings-URI:er. Följande kombination av värden är till exempel inte tillåten eftersom rotdomänen, contoso.com, inte matchar fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Begränsningar för underdomän

Underdomäner är tillåtna, men du måste uttryckligen registrera rotdomänen. Även om följande URI:er till exempel delar en enda rotdomän är kombinationen inte tillåten.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Men om utvecklaren uttryckligen lägger till rotdomänen tillåts kombinationen.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Undantag

Följande fall omfattas inte av begränsningen av den enda rotdomänen:

- Enstaka klientappar eller appar som riktar sig till konton i en enda katalog
- Användning av localhost som omdirigerande URI:er
- Omdirigera URI:er med anpassade scheman (icke-HTTP eller HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Konfigurera utgivardomänen programmässigt

För närvarande finns det inget REST API eller PowerShell-stöd för att konfigurera utgivarens domän programmässigt.
