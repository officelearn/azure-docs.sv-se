---
title: Azure Active Directory lösen ords utan lösen ord (för hands version)
description: Lösen ords lös inloggning till Azure AD med hjälp av FIDO2-säkerhetsnycklar eller Microsoft Authenticator app (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ae8f6854241240249cb3b7494872cbbd8fd41e6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823751"
---
# <a name="what-is-passwordless"></a>Vad är lösen ord?

Multi-Factor Authentication (MFA) är ett bra sätt att skydda din organisation, men användarna kan bli frustrerade med det extra lagret ovanpå att de behöver komma ihåg sina lösen ord. Metoder för lösen ords kryptering är bekvämare eftersom lösen ordet tas bort och ersätts med något som du har, samt något du känner till.

|   | Något du har | Något som du är eller vet |
| --- | --- | --- |
| Lösenordsfri | Telefon-eller säkerhets nyckel | Bio metrisk eller PIN-kod |

Varje organisation har olika behov när den kommer till autentiseringen. Microsoft erbjuder för närvarande Windows Hello, våra för Windows-datorer. Vi lägger till Microsoft Authenticator-appen och FIDO2-säkerhetsnycklarna i den lösenordsskyddade familjen.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-appen

Tillåt din anställdas telefon att bli en metod för lösen ords lös autentisering. Du kanske redan använder Microsoft Authenticator-appen som ett bekvämt alternativ för Multi-Factor Authentication förutom ett lösen ord. Men nu är det tillgängligt som ett lösen ords utan alternativ.

![Logga in på Microsoft Edge med Microsoft Authenticator-appen](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Den vänder sig till en stark och lösenordsskyddad autentiseringsuppgift genom att tillåta att användare loggar in på valfri plattform eller webbläsare genom att få ett meddelande till telefonen, som matchar ett tal som visas på skärmen till det som visas på telefonen och sedan använder sina bio metriska mått ( touch eller FACET) eller fäst för att bekräfta.

## <a name="fido2-security-keys"></a>FIDO2 säkerhets nycklar

FIDO2-säkerhetsnycklar är en unphishable standard-baserad autentiseringsmetod för lösen ords skydd som kan komma i vilken form som helst. Snabb identitet Online (FIDO) är en öppen standard för lösen ords mässig autentisering. Det gör att användare och organisationer kan använda standard för att logga in på sina resurser utan användar namn eller lösen ord med hjälp av en extern säkerhets nyckel eller en plattforms nyckel som är inbyggd i en enhet.

För en offentlig för hands version kan anställda använda externa säkerhets nycklar för att logga in på sina Azure Active Directory anslutna Windows 10-datorer (som kör version 1809 eller senare) och få enkel inloggning till deras moln resurser. De kan också logga in i webbläsare som stöds.

![Logga in på Microsoft Edge med en säkerhets nyckel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Även om det finns många nycklar som är FIDO2-certifierade av FIDO Alliance, kräver Microsoft några valfria tillägg till FIDO2 CTAP-specifikationen som ska implementeras av leverantören för att säkerställa maximal säkerhet och bästa möjliga upplevelse.

En säkerhets nyckel **måste** implementera följande funktioner och tillägg från FIDO2 CTAP-protokollet som ska vara Microsoft-kompatibelt:

| # | Funktion/tillägg-förtroende | Varför krävs den här funktionen eller tillägget? |
| --- | --- | --- |
| 1 | Resident nyckel | Den här funktionen gör att säkerhets nyckeln kan vara portabel, där autentiseringsuppgifterna lagras på säkerhets nyckeln. |
| 2 | Klientens PIN-kod | Med den här funktionen kan du skydda dina autentiseringsuppgifter med en andra faktor och gälla säkerhets nycklar som inte har något användar gränssnitt. |
| 3 | HMAC-hemlighet | Det här tillägget garanterar att du kan logga in på enheten när den är offline eller i flyg Plans läge. |
| 4 | Flera konton per RP | Den här funktionen garanterar att du kan använda samma säkerhets nyckel för flera tjänster som Microsoft-konto och Azure Active Directory. |

Följande leverantörer ger FIDO2 säkerhets nycklar för olika form faktorer som är kända för att vara kompatibla med paswordless-upplevelsen. Microsoft uppmuntrar kunderna att utvärdera säkerhets egenskaperna för dessa nycklar genom att kontakta leverantören och FIDO Alliance.

| Leverantör | Kontakta |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Om du är en leverantör och vill få din enhet i den här listan kan du [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)kontakta.

FIDO2-säkerhetsnycklar är ett bra alternativ för företag som är mycket känsliga eller har scenarier eller anställda som inte är villiga eller kan använda sin telefon som en andra faktor.

## <a name="what-scenarios-work-with-the-preview"></a>Vilka scenarier fungerar med för hands versionen?

- Administratörer kan aktivera metoder för lösen ords kryptering för sina klienter
- Administratörer kan rikta alla användare eller välja användare/grupper inom sin klient organisation för varje metod
- Slutanvändare kan registrera och hantera dessa metoder för lösen ords kryptering i sin konto Portal
- Slutanvändare kan logga in med dessa metoder för lösen ords kryptering
   - Microsoft Authenticator app: Fungerar i scenarier där Azure AD-autentisering används, inklusive i alla webbläsare, under installationen av Windows 10 out-of-Box (OOBE) och med integrerade mobilappar på alla operativ system.
   - Säkerhets nycklar: Fungerar på Lås skärmen för Windows 10 version 1809 eller senare och webben i webbläsare som stöds, t. ex. Microsoft Edge.

## <a name="next-steps"></a>Nästa steg

[Aktivera FIDO2 säkerhets nyckel passwordlesss alternativ i din organisation](howto-authentication-passwordless-security-key.md)

[Aktivera telefonbaserade lösen ords alternativ i din organisation](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externa länkar

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP-specifikation](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
