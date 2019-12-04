---
title: Lösen ords skydd med lösen ords skydd i Windows – Azure Active Directory
description: Aktivera inloggning med lösen ord utan lösen ord till Azure AD med hjälp av säkerhets nycklar för FIDO2 (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05230e39175e71f4eec2c99cd6cbd2f44f05df30
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766369"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Aktivera lösen ords lös säkerhets nyckel logga in på Windows 10-enheter (för hands version)

Det här dokumentet fokuserar på att aktivera FIDO2-säkerhetsnyckel baserad lösenordsautentisering med Windows 10-enheter. I slutet av den här artikeln kan du logga in på både webbaserade program och dina Azure AD-anslutna Windows 10-enheter med ditt Azure AD-konto med hjälp av en FIDO2 säkerhets nyckel.

|     |
| --- |
| FIDO2 säkerhets nycklar är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna|
|     |

## <a name="requirements"></a>Krav

- [Azure-Multi-Factor Authentication](howto-mfa-getstarted.md)
- [Kombinerad för hands version av säkerhets informations registrering](concept-registration-mfa-sspr-combined.md)
- Kompatibla [säkerhets nycklar för FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- Webauthn kräver Windows 10 version 1809 eller senare
- [Azure AD-anslutna enheter](../devices/concept-azure-ad-join.md) kräver Windows 10 version 1809 eller senare
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (valfritt)
- Etablerings paket (valfritt)

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

- Windows Server Active Directory Domain Services (AD DS)-domänanslutna (endast lokala enheter) **stöds inte**.
- RDP-, VDI-och Citrix-scenarier **stöds inte** med hjälp av säkerhets nyckel.
- S/MIME **stöds inte** med säkerhets nyckeln.
- "Kör som" **stöds inte** med hjälp av säkerhets nyckeln.
- Det finns **inte stöd**för att logga in på en server med hjälp av säkerhets nyckel.
- Om du inte har använt din säkerhets nyckel för att logga in på enheten när du är online kan du inte använda den för att logga in eller låsa upp offline.
- Logga in eller låsa upp en Windows 10-enhet med en säkerhets nyckel som innehåller flera Azure AD-konton. I det här scenariot används det senaste kontot som lades till i säkerhets nyckeln. Med webauthn kan användarna välja det konto de vill använda.

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för för hands version

Azure AD-anslutna enheter som du ska pilot med måste köra Windows 10 version 1809 eller senare. Den bästa upplevelsen är i Windows 10 version 1903 eller senare.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivera säkerhets nycklar för Windows-inloggning

Organisationer kan välja att använda en eller flera av följande metoder för att aktivera användning av säkerhets nycklar för Windows-inloggning baserat på deras organisations krav.

- [Aktivera med Intune](#enable-with-intune)
- [Riktad Intune-distribution](#targeted-intune-deployment)
- [Aktivera med ett konfigurations paket](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>Aktivera med Intune

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhets registrering** > **Windows-registrering** > **Windows Hello för företag** > **Egenskaper**.
1. Under **Inställningar** anger du **använda säkerhets nycklar för inloggning** till **aktive rad**.

Konfiguration av säkerhets nycklar för inloggning, är inte beroende av att konfigurera Windows Hello för företag.

### <a name="targeted-intune-deployment"></a>Riktad Intune-distribution

Om du vill använda specifika enhets grupper för att aktivera Credential-providern använder du följande anpassade inställningar via Intune.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhets konfiguration** > **profiler** > **Skapa profil**.
1. Konfigurera den nya profilen med följande inställningar
   1. Namn: säkerhets nycklar för Windows-inloggning
   1. Beskrivning: aktiverar FIDO-säkerhetsnycklar som ska användas vid inloggning i Windows
   1. Plattform: Windows 10 och senare
   1. Profil typ: anpassad
   1. Anpassade OMA-URI-inställningar:
      1. Namn: Aktivera FIDO säkerhets nycklar för Windows-inloggning
      1. OMA-URI:./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Datatyp: heltal
      1. Värde: 1
1. Den här principen kan tilldelas till vissa användare, enheter eller grupper. Mer information hittar du i artikeln [Tilldela användar-och enhets profiler i Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Skapa anpassad enhets konfigurations princip för Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Aktivera med ett konfigurations paket

För enheter som inte hanteras av Intune kan ett konfigurations paket installeras för att aktivera funktionen. Windows Configuration designer-appen kan installeras från [Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22).

1. Starta Windows Configuration designer.
1. Välj **fil** > **nytt projekt**.
1. Ge projektet ett namn och anteckna sökvägen där projektet skapas.
1. Välj **Nästa**.
1. Lämna **etablerings paketet** valt som det **valda projekt arbets flödet** och välj **Nästa**.
1. Välj **alla versioner av Windows-skrivbordet** under **Välj vilka inställningar du vill visa och konfigurera** och välj **Nästa**.
1. Välj **Slutför**.
1. I det nyskapade projektet bläddrar du till **körnings inställningar** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Ange **UseSecurityKeyForSignIn** till **aktive rad**.
1. Välj **exportera** > **etablerings paket**
1. Lämna standardvärdena i fönstret **build** under **Beskriv etablerings paketet** och välj **Nästa**.
1. Lämna standardvärdena i fönstret **build** under **Välj säkerhets information för etablerings paketet** och välj **Nästa**.
1. Anteckna eller ändra sökvägen i **build** Windows under **Välj var du vill spara etablerings paketet** och välj **Nästa**.
1. Välj **build** på sidan **bygga etablerings paket** .
1. Spara de två filerna som skapats (ppkg och Cat) till en plats där du kan tillämpa dem på datorer senare.
1. Följ anvisningarna i artikeln [tillämpa ett konfigurations paket](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)för att tillämpa det konfigurations paket som du har skapat.

> [!NOTE]
> Enheter som kör Windows 10 version 1809 måste också aktivera Shared PC Mode (EnableSharedPCMode). Information om hur du aktiverar den här funktioner finns i artikeln [Konfigurera en delad eller gäst dator med Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>Logga in på Windows med en säkerhets nyckel för FIDO2

I exemplet nedan har en användare Bala Sandhu redan har tillhandahållit sin FIDO2-säkerhetsnyckel med hjälp av stegen i föregående artikel, [Aktivera lösen ords skydds nyckel inloggning](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). Bala kan välja providern för säkerhets nyckel autentiseringsuppgifter från Lås skärmen för Windows 10 och infoga säkerhets nyckeln för att logga in på Windows.

![Säkerhets nyckel logga in på Lås skärmen i Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Hantera säkerhets nyckel bio metrisk, PIN-kod eller återställning säkerhets nyckel

* Windows 10 version 1903 eller senare
   * Användare kan öppna **Windows-inställningar** på sina enheter > **konton** > **säkerhets nyckel**
   * Användare kan ändra sin PIN-kod, uppdatera biometrik eller återställa sin säkerhets nyckel

## <a name="troubleshooting-and-feedback"></a>Fel sökning och feedback

Om du vill dela feedback eller om du får problem med att för hands Visa den här funktionen kan du dela via Windows Feedback Hub-appen.

1. Starta **feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   1. Kategori: säkerhet och sekretess
   1. Under kategori: FIDO
1. För att avbilda loggar använder du alternativet: **återskapa mitt problem**

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="does-this-work-in-my-on-premises-environment"></a>Fungerar det här i min lokala miljö?

Den här funktionen fungerar inte för en ren lokal Active Directory Domain Services (AD DS)-miljö.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Min organisation kräver två Factor Authentication för att få åtkomst till resurser, vad kan jag göra för att stödja det här kravet?

Säkerhets nycklar kommer i en mängd olika form faktorer. Kontakta enhets tillverkaren för att diskutera hur deras enheter kan aktive ras med en PIN-kod eller bio metrisk som en andra faktor.

### <a name="can-admins-set-up-security-keys"></a>Administratörer kan konfigurera säkerhets nycklar?

Vi arbetar på den här funktionen för allmän tillgänglighet (GA) för den här funktionen.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Var kan jag hitta kompatibla säkerhets nycklar?

[FIDO2 säkerhets nycklar](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Vad gör jag om jag tappar bort min säkerhets nyckel?

Du kan ta bort nycklar från Azure Portal genom att gå till sidan säkerhets information och ta bort säkerhets nyckeln.

## <a name="next-steps"></a>Nästa steg

[Läs mer om enhets registrering](../devices/overview.md)

[Läs mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
