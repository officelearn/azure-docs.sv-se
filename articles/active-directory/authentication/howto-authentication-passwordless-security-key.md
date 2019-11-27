---
title: Lösen ord för lösen ords skydd (för hands version) – Azure Active Directory
description: Aktivera inloggning med lösen ord utan lösen ord till Azure AD med hjälp av säkerhets nycklar för FIDO2 (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f87f1b2561b65590dfe29d7d2c8d1318e3d35e1
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381843"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Aktivera lösen ords skydd med lösen ords skydd (för hands version)

För företag som använder lösen ord idag och har en delad dator miljö, är säkerhets nycklar ett sömlöst sätt för arbetare att autentisera utan att ange användar namn eller lösen ord. Säkerhets nycklar ger förbättrad produktivitet för anställda och har bättre säkerhet.

Det här dokumentet fokuserar på att aktivera säkerhets nyckel baserad lösen ords lös autentisering. I slutet av den här artikeln kan du logga in till webbaserade program med ditt Azure AD-konto med hjälp av en säkerhets nyckel för FIDO2.

|     |
| --- |
| FIDO2 säkerhets nycklar är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna|
|     |

## <a name="requirements"></a>Krav

- [Azure-Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Kombinerad för hands version av säkerhets informations registrering](concept-registration-mfa-sspr-combined.md)
- Kompatibla [säkerhets nycklar för FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Webauthn kräver Windows 10 version 1809 eller högre * *

Om du vill använda säkerhets nycklar för att logga in på webbappar och tjänster måste du ha en webbläsare som stöder webauthn-protokollet. Det kan vara Microsoft Edge, Chrome, Firefox och Safari.

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för för hands version

Enheter som du ska pilot med måste köra Windows 10 version 1809 eller senare. Den bästa upplevelsen är i Windows 10 version 1903 eller senare.

## <a name="enable-passwordless-authentication-method"></a>Aktivera autentiseringsmetod för lösen ord

### <a name="enable-the-combined-registration-experience"></a>Aktivera kombinerad registrerings upplevelse

Registrerings funktioner för metoder för lösen ords kryptering förlitar sig på den kombinerade förhands granskningen. Följ anvisningarna i artikeln [Aktivera kombinerad registrering av säkerhets information (för hands version)](howto-registration-mfa-sspr-combined.md)för att aktivera den kombinerade förhands granskningen.

### <a name="enable-fido2-security-key-method"></a>Aktivera FIDO2 säkerhets nyckel metod

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **säkerhets** > **autentiseringsmetoder** > **principen för autentiserings metoden (för hands version)** .
1. Under metod **FIDO2 säkerhets nyckel**väljer du följande alternativ:
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – alla användare eller Välj användare
1. **Spara** konfigurationen.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Användar registrering och hantering av säkerhets nycklar för FIDO2

1. Bläddra till [https://myprofile.microsoft.com](https://myprofile.microsoft.com).
1. Logga in om du inte redan gjort det.
1. Klicka på **säkerhets information**.
   1. Om användaren redan har minst en registrerad Azure-Multi-Factor Authentication kan de omedelbart registrera en säkerhets nyckel för FIDO2.
   1. Om de inte har minst en registrerad Azure-Multi-Factor Authentication måste de lägga till en.
1. Lägg till en säkerhets nyckel för FIDO2 genom att klicka på **Lägg till metod** och välja **säkerhets nyckel**.
1. Välj **USB-enhet** eller **NFC-enhet**.
1. Låt nyckeln vara klar och välj **Nästa**.
1. En ruta visas och ber användaren att skapa/ange en PIN-kod för din säkerhets nyckel och sedan utföra den begärda gesten för nyckeln, antingen bio metrisk eller touch.
1. Användaren kommer att returneras till den kombinerade registrerings upplevelsen och uppmanas att ange ett beskrivande namn för nyckeln så att användaren kan identifiera vilken av dem som har flera. Klicka på **Nästa**.
1. Slutför processen genom att klicka på **klar** .

## <a name="sign-in-with-passwordless-credential"></a>Logga in med autentiseringsuppgifter för lösen ord

I exemplet nedan har en användare redan har etablerad sin FIDO2-säkerhetsnyckel. Användaren kan välja att logga in på webben med sin FIDO2 säkerhets nyckel i en webbläsare som stöds i Windows 10 version 1809 eller senare.

![Inloggning av säkerhets nyckel Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Fel sökning och feedback

Om du vill dela feedback eller om du får problem med att för hands Visa den här funktionen kan du dela via Windows Feedback Hub-appen.

1. Starta **feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   1. Kategori: säkerhet och sekretess
   1. Under kategori: FIDO
1. För att avbilda loggar använder du alternativet: **återskapa mitt problem**

## <a name="known-issues"></a>Kända problem

### <a name="security-key-provisioning"></a>Etablering av säkerhets nyckel

Administratörs etablering och avetablering av säkerhets nycklar är inte tillgängligt i den offentliga för hands versionen.

### <a name="upn-changes"></a>UPN-ändringar

Om en användares UPN-ändringar ändras kan du inte längre ändra FIDO2-säkerhetsnycklar för att ändra konto för ändringen. Lösningen är att återställa enheten och användaren måste registrera sina FIDO2-säkerhetsnycklar på nytt.

## <a name="next-steps"></a>Nästa steg

[FIDO2 säkerhets nyckel Windows 10-inloggning](howto-authentication-passwordless-security-key-windows.md)

[Aktivera FIDO2-autentisering för lokala resurser](howto-authentication-passwordless-security-key-on-premises.md)

[Läs mer om enhets registrering](../devices/overview.md)

[Läs mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
