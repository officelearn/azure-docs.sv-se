---
title: Azure Active Directory inloggning med lösen ord (för hands version)
description: Läs om alternativ för lösen ords lös inloggning för att Azure Active Directory med hjälp av FIDO2-säkerhetsnycklar eller Microsoft Authenticator-appen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffe3d359dffea3278337de00bea42e0b959e10f6
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269522"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Lösen ords verifierings alternativ för Azure Active Directory

Multi-Factor Authentication (MFA) är ett bra sätt att skydda din organisation, men användare kan ofta bli frustrerade med det extra säkerhets skiktet som krävs för att komma ihåg sina lösen ord. Metoder för snabbare autentisering är mer praktiska eftersom lösen ordet tas bort och ersätts med något som du har, plus något du känner till.

|   | Något du har | Något som du är eller vet |
| --- | --- | --- |
| Lösenordsfri | Windows 10-enhet, telefon eller säkerhets nyckel | Bio metrisk eller PIN-kod |

Varje organisation har olika behov när den kommer till autentiseringen. Microsoft erbjuder följande tre alternativ för Lösenordssynkronisering:

- Windows Hello för företag
- Microsoft Authenticator-appen
- FIDO2 säkerhets nycklar

![Autentisering: säkerhet jämfört med bekvämlighet](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello för företag

Windows Hello för företag är idealiskt för informations anställda som har sin egen angivna Windows-dator. Bio måttet och PIN-koden är direkt knutna till användarens dator, vilket förhindrar åtkomst från någon annan än ägaren. Med PKI-integrering (Public Key Infrastructure) och inbyggt stöd för enkel inloggning (SSO), är Windows Hello för företag en smidig metod för sömlös åtkomst till företagets resurser lokalt och i molnet.

[Planerings guiden](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) för Windows Hello för företag kan användas för att fatta beslut om en typ av Windows Hello för företag-distribution och vilka alternativ du behöver tänka på.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator app

Tillåt din anställdas telefon att bli en metod för lösen ords lös autentisering. Du kanske redan använder Microsoft Authenticator-appen som ett bekvämt alternativ för Multi-Factor Authentication förutom ett lösen ord. Du kan också använda Authenticator-appen som ett lösen ords alternativ.

![Logga in på Microsoft Edge med Microsoft Authenticator-appen](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Authenticator-appen förvandlar en iOS-eller Android-telefon till en stark, lösen ords rik autentiseringsuppgift. Användare kan logga in på vilken plattform eller webbläsare som helst genom att få ett meddelande till sin telefon, matcha ett nummer som visas på skärmen till det som visas på telefonen och sedan använda sina bio mått (touch eller FACET) eller PIN-kod för att bekräfta.

## <a name="fido2-security-keys"></a>FIDO2 säkerhets nycklar

FIDO2-säkerhetsnycklar är en unphishable standard-baserad autentiseringsmetod för lösen ords skydd som kan komma i vilken form som helst. Snabb identitet Online (FIDO) är en öppen standard för lösen ords mässig autentisering. Med FIDO kan användare och organisationer utnyttja standard för att logga in på sina resurser utan användar namn eller lösen ord med hjälp av en extern säkerhets nyckel eller en plattforms nyckel som är inbyggd i en enhet.

För en offentlig för hands version kan anställda använda säkerhets nycklar för att logga in på sina Azure AD-eller hybrid Azure AD-anslutna Windows 10-enheter och få enkel inloggning till sina moln resurser och lokala resurser. Användare kan också logga in i webbläsare som stöds. FIDO2-säkerhetsnycklar är ett bra alternativ för företag som är mycket känsliga eller har scenarier eller anställda som inte är villiga eller kan använda sin telefon som en andra faktor.

![Logga in på Microsoft Edge med en säkerhets nyckel](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Även om det finns många nycklar som är FIDO2-certifierade av FIDO-alliansen, kräver Microsoft vissa valfria tillägg för FIDO2-specifikationen (Client-to-Authenticator Protocol) som ska implementeras av leverantören för att säkerställa maximal säkerhet och bästa möjliga praktik.

En säkerhets nyckel **måste** implementera följande funktioner och tillägg från FIDO2 CTAP-protokollet som ska vara Microsoft-kompatibelt:

| # | Funktion/tillägg-förtroende | Varför krävs den här funktionen eller tillägget? |
| --- | --- | --- |
| 1 | Resident nyckel | Den här funktionen gör att säkerhets nyckeln kan vara portabel, där autentiseringsuppgifterna lagras på säkerhets nyckeln. |
| 2 | Klientens PIN-kod | Med den här funktionen kan du skydda dina autentiseringsuppgifter med en andra faktor och gälla säkerhets nycklar som inte har något användar gränssnitt. |
| 3 | HMAC-hemlighet | Det här tillägget garanterar att du kan logga in på enheten när den är offline eller i flyg Plans läge. |
| 4 | Flera konton per RP | Den här funktionen garanterar att du kan använda samma säkerhets nyckel för flera tjänster som Microsoft-konto och Azure Active Directory. |

Följande leverantörer ger FIDO2 säkerhets nycklar för olika form faktorer som är kända för att vara kompatibla med den lösen ords fri upplevelsen. Vi rekommenderar att du utvärderar säkerhets egenskaperna för dessa nycklar genom att kontakta leverantören och FIDO Alliance.

| Leverantör | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Om du köper och planerar att använda NFC-baserade säkerhets nycklar behöver du en NFC-läsare som stöds för säkerhets nyckeln. NFC-läsaren är inte ett Azure-krav eller en begränsning. Kontakta leverantören av en NFC-baserad säkerhets nyckel för en lista över NFC-läsare som stöds.

Kontakta [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)om du är en leverantör och vill få din enhet på den här listan över enheter som stöds.

## <a name="what-scenarios-work-with-the-preview"></a>Vilka scenarier fungerar med för hands versionen?

- Administratörer kan aktivera metoder för lösen ords kryptering för sina klienter
- Administratörer kan rikta alla användare eller välja användare/grupper inom sin klient organisation för varje metod
- Slutanvändare kan registrera och hantera dessa metoder för lösen ords kryptering i sin konto Portal
- Slutanvändare kan logga in med dessa metoder för lösen ords kryptering
   - Microsoft Authenticator app: fungerar i scenarier där Azure AD-autentisering används, inklusive i alla webbläsare, under installationen av OOBE-installationen (Windows 10 out of Box) och med integrerade mobilappar på alla operativ system.
   - Säkerhets nycklar: arbeta på Lås skärmen för Windows 10 och webben i webbläsare som stöds, t. ex. Microsoft Edge (både äldre och nya kanter).

## <a name="next-steps"></a>Nästa steg

[Aktivera FIDO2 säkerhets nyckel passwordlesss alternativ i din organisation](howto-authentication-passwordless-security-key.md)

[Aktivera telefonbaserade lösen ords alternativ i din organisation](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externa länkar

[FIDO Alliance](https://fidoalliance.org/)

[FIDO2 CTAP-specifikation](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
