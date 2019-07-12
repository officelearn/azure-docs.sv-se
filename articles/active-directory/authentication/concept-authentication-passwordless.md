---
title: Azure Active Directory lösenordslös inloggning (förhandsversion)
description: Konfiguration av lösenordsfri inloggning på Azure AD med FIDO2 säkerhetsnycklar eller Microsoft Authenticator-appen (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712090"
---
# <a name="what-is-passwordless"></a>Vad är lösenordslös?

Multifaktorautentisering (MFA) är ett bra sätt att skydda din organisation, men användarna frustrerade med extra lager ovanpå att behöva komma ihåg sina lösenord. Konfiguration av lösenordsfri autentiseringsmetoder är enklare eftersom lösenordet tas bort och ersätts med något användaren har plus något du är eller något användaren vet.

|   | Något du har | Något du eller vet |
| --- | --- | --- |
| Lösenordsfri | Telefon eller säkerhet | Biometrisk eller PIN-kod |

Vi känner igen att varje organisation har olika behov när det gäller autentisering. Microsoft erbjuder för närvarande Windows Hello, våra premier lösenordslös upplevelse för Windows-dator. Vi lägger till nya autentiseringsuppgifter i lösenordslös familjen: Microsoft Authenticator-appen och FIDO2 säkerhetsnycklar.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Tillåt att en anställd telefon för att bli en lösenordslös autentiseringsmetod. Du kanske redan använder Microsoft Authenticator-appen som en praktisk multifaktorautentisering alternativ utöver ett lösenord. Men nu är tillgängligt som ett alternativ för konfiguration av lösenordsfri.

Det blir iOS- eller Android-telefon autentiseringsuppgift för stark, konfiguration av lösenordsfri genom att tillåta användare att logga in på valfri plattform eller en webbläsare genom att få ett meddelande till telefonen, matchar ett tal som visas på skärmen för att en på sin telefon och sedan använda sina biometriska ( touch eller står inför) eller PIN-kod för att bekräfta.

## <a name="fido2-security-keys"></a>FIDO2 säkerhetsnycklar

FIDO2 säkerhetsnycklar är en unphishable standardbaserad lösenordslös autentiseringsmetod som kan finnas i vilken anslutningstyp. Snabb identitet Online (FIDO) är en öppen standard för konfiguration av lösenordsfri autentisering. Det gör att användare och organisationer utnyttja standarden att logga in på sina resurser utan användarnamn och lösenord med en extern säkerhetsnyckel eller en platform-nyckel som är inbyggda i en enhet.

För en förhandsversion, anställda använda externa säkerhetsnycklar för att logga in på sina Azure Active Directory anslutna Windows 10-datorer (som kör version 1809 eller senare) och få enkel inloggning på sina molnresurser. De kan också logga in på webbläsare som stöds.

Det finns många nycklar som är FIDO2 som certifierats av FIDO-Alliance, kräver Microsoft att vissa valfria tillägg i specifikationen FIDO2 CTAP implementeras av leverantören att säkerställa maximal säkerhet och den bästa upplevelsen.

En säkerhetsnyckel **måste** implementerar följande funktioner och tillägg från FIDO2 CTAP protokoll ska vara kompatibel med Microsoft:

| # | Funktionen / lita på tillägg | Varför krävs detta? |
| --- | --- | --- |
| 1 | Fasta nyckel | Den här funktionen gör det möjligt för säkerhetsnyckel kan flyttas, där dina autentiseringsuppgifter lagras på säkerhetsnyckeln. |
| 2 | Klienten PIN-kod | Den här funktionen kan du skydda dina autentiseringsuppgifter med en andra faktor och gäller för säkerhetsnycklar som inte har ett användargränssnitt. |
| 3 | hmac-secret | Det här tillägget säkerställer att du kan logga in på din enhet när den är offline eller i Flygplansläge. |
| 4 | Flera konton per RP | Den här funktionen ser till att du kan använda samma säkerhetsnyckeln för flera tjänster som Account och Azure Active Directory. |

Följande leverantörer tillhandahåller FIDO2 säkerhetsnycklar för olika formfaktorer som har visat sig vara kompatibel med paswordless-upplevelse. Microsoft uppmuntrar kunder säkerhetsegenskaperna för de här nycklarna genom att kontakta leverantören samt FIDO Alliance.

| Leverantör | Kontakta |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Om du är en leverantör och vill få din enhet i listan kan du kontakta [ Fido2Request@Microsoft.com ](mailto:Fido2Request@Microsoft.com).

FIDO2 säkerhetsnycklar är ett bra alternativ för företag som är mycket säkerhet som är känsliga eller har scenarier eller medarbetare som inte är villig eller använda telefonen som en andra faktor.

## <a name="what-scenarios-work-with-the-preview"></a>Vilka scenarier som fungerar med förhandsversionen?

1. Administratörer kan aktivera lösenordslös autentiseringsmetoder för klienten
1. Administratörer kan fokusera på alla användare eller Välj användare/grupper i klientorganisationen för varje metod
1. Slutanvändare kan registrera och hantera dessa lösenordslös autentiseringsmetoder i sina-kontoportalen
1. Slutanvändarna kan logga in med dessa lösenordslös autentiseringsmetoder
   1. Microsoft Authenticator-appen: Inklusive i alla webbläsare, kommer arbete i alla scenarier där Azure AD-autentisering används under Windows 10 ut (OOBE) installationen och med integreras mobilappar på alla operativsystem.
   1. Säkerhetsnycklarna: Fungerar på låsskärmen för Windows 10 version 1809 eller senare och webb i webbläsare som stöds som Microsoft Edge.

## <a name="next-steps"></a>Nästa steg

[Aktivera lösenordslös alternativ i din organisation](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Externa länkar

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP-specifikation](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
