---
title: Konfigurera ett programs publisher-domän | Azure
description: Lär dig hur du konfigurerar ett programs publisher-domän för att informera användarna om där deras information skickas.
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.author: celested
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efbf448770bfcf797d6bf01cd3c28dc98023adff
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502285"
---
# <a name="how-to-configure-an-applications-publisher-domain-preview"></a>Anvisningar: Konfigurera ett programs publisher-domän (förhandsversion)

Ett programs publisher-domän visas för användarna på den [medgivandetext för programmets](application-consent-experience.md) att informera användarna om där deras information skickas. Program med flera klienter som är registrerade efter 21 maj 2019 som inte har en utgivardomän visas som **overifierade**. Program för flera innehavare är program som stöder konton utanför en enda organisationens katalog. till exempel stöd för alla Azure AD-konton eller stöd för alla Azure AD-konton och personliga Microsoft-konton.

## <a name="new-applications"></a>Nya program

När du registrerar en ny app kan publisher-domän för din app anges till ett standardvärde. Värdet beror på där appen har registrerats, särskilt om appen har registrerats i en klient och om klienten har klient kontrollerade domäner.

Om det finns klient kontrollerade domäner, appens publisher-domän som standard till primära verifierade domän i klienten. Om det finns ingen klient verifierat domäner (vilket är fallet när programmet inte har registrerats i en klient), appens utgivardomän anges till null.

I följande tabell sammanfattas standardbeteendet för domänvärdet utgivare.  

| Klient-kontrollerade domäner | Standardvärdet för publisher-domän |
|-------------------------|----------------------------|
| null | null |
| *.onmicrosoft.com | *.onmicrosoft.com |
| -*. onmicrosoft.com<br/>-domain1.com<br/>-domain2.com (primär) | domain2.com |

Om ett program med flera innehavare publisher-domän inte anges, eller om den är inställd på en domän som slutar med. onmicrosoft.com, appens medgivande visas **overifierade** i stället för publisher-domän.

## <a name="grandfathered-applications"></a>Grandfathered program

Om din app har registrerats innan den 21 maj 2019 medgivandetext för ditt program visas inte **overifierade** om du inte har en publisher-domän. Vi rekommenderar att du anger utgivaren domänvärdet så att användare kan se den här informationen i din app medgivande.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurera publiceringsdomän med hjälp av Azure portal

Följ dessa steg för att ställa in appens publisher-domän.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.

1. Om ditt konto finns i mer än en Azure AD-klient:
   1. Välj din profil på menyn i det övre högra hörnet på sidan och sedan **växla katalog**.
   1. Ändra din session till Azure AD-klient där du vill skapa ditt program.

1. Gå till [Azure Active Directory > App-registreringar](https://go.microsoft.com/fwlink/?linkid=2083908) att söka efter och välj den app som du vill konfigurera.

   När du har valt appen visas appens **översikt** sidan.

1. Från appens **översikt** väljer den **varumärkesriktlinjer** avsnittet.

1. Hitta den **utgivardomän** fältet och väljer något av följande alternativ:

   - Välj **konfigurera en domän** om du inte redan konfigurerat en domän.
   - Välj **uppdateringsdomän** om en domän har redan konfigurerats.

Om din app har registrerats i en klient, visas två flikar för att välja från: **Välj en verifierad domän** och **verifiera en ny domän**.

Om din app inte har registrerats i en klient, visas bara alternativet att verifiera en ny domän för ditt program.

### <a name="to-verify-a-new-domain-for-your-app"></a>Verifiera en ny domän för din app

1. Skapa en fil med namnet `microsoft-identity-association.json` och klistra in följande JSON-kodfragmentet.

   ```json
   {
      "associatedApplications": [
        {
           "applicationId": "{YOUR-APP-ID-HERE}"
        }
      ]
    }
   ```

1. Ersätt platshållaren *{YOUR-APP-ID-HERE}* med program (klient)-ID som motsvarar din app.

1. Vara värd för filen: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Ersätt platshållaren *{YOUR-DOMAIN-HERE}* så att de matchar en verifierad domän.

1. Klicka på den **verifiera och spara domän** knappen.

### <a name="to-select-a-verified-domain"></a>Att välja en verifierad domän

- Om din klient har verifierat domäner, väljer du något av domänerna från den **väljer en verifierad domän** listrutan.

## <a name="implications-on-the-app-consent-prompt"></a>Konsekvenser för appen medgivandetext

Konfigurera publisher-domän har en inverkan på vad användarna ser på medgivandetext för appen. För att helt förstå komponenterna i medgivandetext, se [förstå programtillåtelser inträffar](application-consent-experience.md).

I följande tabell beskrivs beteendet för program som skapats före den 21 maj 2019.

![Medgivandetext för appar som skapats före den 21 maj 2019](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Beteendet för nya program som skapats efter den 21 maj 2019 beror på publisher-domän och vilken typ av program. I följande tabell beskrivs de ändringar som du bör visas med de olika kombinationerna av konfigurationer.

![Medgivandetext för appar som skapats efter den 21 maj 2019](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Konsekvenser för omdirigerings-URI: er

Program som loggar in användare med eventuella arbets- eller skolkonto, eller personliga Microsoft-konton ([flera innehavare](single-and-multi-tenant-apps.md)) är föremål för några begränsningar när du anger omdirigerings-URI: er.

### <a name="single-root-domain-restriction"></a>Begränsning för enskild rot-domän

När värdet för publisher-domän för appar för flera klienter anges till null, appar är begränsade till att dela en enda rotdomän för omdirigerings-URI: er. Till exempel följande kombinationer av värden är inte tillåten eftersom rotdomänen, contoso.com, matchar inte fabrikam.com.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Underdomän begränsningar

Underdomäner tillåts, men du måste uttryckligen registrera rotdomänen. Till exempel när följande URI: er delar en enda rotdomän, tillåts kombinationen inte.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Om utvecklaren lägger uttryckligen rotdomänen, tillåts kombinationen.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Undantag

Följande fall inte omfattas av begränsning för enskild rot-domän:

- Enskild klient appar eller appar som är riktade till konton i en enskild katalog
- Användning av localhost som omdirigerings-URI: er
- Omdirigerings-URI: er med anpassade scheman (icke-HTTP eller HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Konfigurera publiceringsdomän programmässigt

Det finns för närvarande inga REST API eller PowerShell-stöd för att konfigurera utgivardomän programmässigt.
