---
title: Inloggning för lösenordslös säkerhetsnyckel (förhandsversion) – Azure Active Directory
description: Aktivera inloggning av lösenordslös säkerhetsnyckel till Azure AD med fido2-säkerhetsnycklar (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 066ab7892bed6e7505e7ee114ff37a7850ef5c9b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450929"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>Aktivera inloggning med lösenordslös säkerhetsnyckel (förhandsgranskning)

För företag som använder lösenord idag och har en delad datormiljö är säkerhetsnycklar ett smidigt sätt för arbetare att autentisera utan att ange ett användarnamn eller lösenord. Säkerhetsnycklar ger förbättrad produktivitet för arbetstagare och har bättre säkerhet.

Det här dokumentet fokuserar på att aktivera säkerhetsnyckelbaserad lösenordslös autentisering. I slutet av den här artikeln kan du logga in på webbaserade program med ditt Azure AD-konto med hjälp av en FIDO2-säkerhetsnyckel.

|     |
| --- |
| FIDO2-säkerhetsnycklar är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Krav

- [Azure Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Förhandsgranskning av kombinerad registrering av säkerhetsinformation](concept-registration-mfa-sspr-combined.md)
- Kompatibla [FIDO2-säkerhetsnycklar](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN kräver Windows 10 version 1809 eller senare**

Om du vill använda säkerhetsnycklar för att logga in på webbappar och webbtjänster måste du ha en webbläsare som stöder WebAuthN-protokollet. Dessa inkluderar Microsoft Edge, Chrome, Firefox och Safari.

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för förhandsgranskning

Azure AD-anslutna enheter som du styr med måste köra Windows 10 version 1809 eller senare. Den bästa upplevelsen är på Windows 10 version 1903 eller högre.

Hybrid Azure AD-anslutna enheter måste köra Windows 10 Insider Build 18945 eller nyare.

## <a name="enable-passwordless-authentication-method"></a>Aktivera lösenordslös autentiseringsmetod

### <a name="enable-the-combined-registration-experience"></a>Aktivera den kombinerade registreringsupplevelsen

Registreringsfunktioner för lösenordslösa autentiseringsmetoder är beroende av den kombinerade registreringsfunktionen. Följ stegen i artikeln [Aktivera kombinerad registrering av säkerhetsinformation (förhandsversion)](howto-registration-mfa-sspr-combined.md)för att aktivera kombinerad registrering.

### <a name="enable-fido2-security-key-method"></a>Aktivera fido2-säkerhetsnyckelmetod

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Azure Active Directory** > **Security** > **Authentication methods** > **Authentication method policy (Preview)**.
1. Under metoden **FIDO2 Security Key**väljer du följande alternativ:
   1. **Aktivera** - Ja eller Nej
   1. **Mål** - Alla användare eller Välj användare
1. **Spara** konfigurationen.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Registrering och hantering av FIDO2-säkerhetsnycklar

1. Bläddra [https://myprofile.microsoft.com](https://myprofile.microsoft.com)till .
1. Logga in om inte redan.
1. Klicka på **Säkerhetsinformation**.
   1. Om användaren redan har minst en Azure Multi-Factor Authentication-metod registrerad kan de omedelbart registrera en FIDO2-säkerhetsnyckel.
   1. Om de inte har minst en Azure Multi-Factor Autentiseringsmetod registrerad, måste de lägga till en.
1. Lägg till en FIDO2-säkerhetsnyckel genom att klicka på **Lägg till metod** och välja **Säkerhetsnyckel**.
1. Välj **USB-enhet** eller **NFC-enhet**.
1. Ha din nyckel redo och välj **Nästa**.
1. En ruta visas och be användaren att skapa/ange en PIN-kod för din säkerhetsnyckel och sedan utföra den nödvändiga gesten för nyckeln, antingen biometrisk eller touch.
1. Användaren kommer att returneras till den kombinerade registreringsupplevelsen och uppmanas att ange ett meningsfullt namn för nyckeln så att användaren kan identifiera vilken om de har flera. Klicka på **Nästa**.
1. Klicka på **Klar** för att slutföra processen.

## <a name="sign-in-with-passwordless-credential"></a>Logga in med lösenordslös autentiseringsuppgifter

I exemplet nedan har en användare redan etablerat sin FIDO2-säkerhetsnyckel. Användaren kan välja att logga in på webben med sin FIDO2-säkerhetsnyckel inuti en webbläsare som stöds i Windows 10 version 1809 eller senare.

![Inloggning av säkerhetsnyckel Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Felsökning och feedback

Om du vill dela feedback eller stöta på problem när du förhandsgranskar den här funktionen delar du via Appen Windows Feedback Hub med följande steg:

1. Starta **Feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   - Kategori: Säkerhet och sekretess
   - Underkategori: FIDO
1. Om du vill fånga in loggar använder du alternativet återskapa **mitt problem**

## <a name="known-issues"></a>Kända problem

### <a name="security-key-provisioning"></a>Etablering av säkerhetsnyckel

Administratörsetablering och avetablering av säkerhetsnycklar är inte tillgängligt i den offentliga förhandsversionen.

### <a name="upn-changes"></a>UPN-ändringar

Vi arbetar med att stödja en funktion som tillåter UPN-ändring på hybrid Azure AD-anslutna och Azure AD-anslutna enheter. Om en användares UPN ändras kan du inte längre ändra FIDO2-säkerhetsnycklar för att ta hänsyn till ändringen. Lösningen är att återställa enheten och användaren måste registrera om.

## <a name="next-steps"></a>Nästa steg

[FIDO2-säkerhetsnyckel Windows 10 loggar in](howto-authentication-passwordless-security-key-windows.md)

[Aktivera FIDO2-autentisering till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md)

[Läs mer om enhetsregistrering](../devices/overview.md)

[Läs mer om Azure Multi-Factor-autentisering](../authentication/howto-mfa-getstarted.md)
