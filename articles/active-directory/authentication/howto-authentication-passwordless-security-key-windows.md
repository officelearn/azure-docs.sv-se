---
title: Aktivera lösen ords skydd för inloggning med lösen ord för Azure AD (för hands version) – Azure Active Directory
description: Aktivera inloggning med lösen ord utan lösen ord till Azure AD med hjälp av säkerhets nycklar för FIDO2 (för hands version)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5758b1fbb9d311219e3dc4dd483691f6c9d80c1
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172166"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>Aktivera lösen ords lös säkerhets nyckel logga in på Windows 10-enheter (för hands version)

Det här dokumentet fokuserar på att aktivera FIDO2-säkerhetsnyckel baserad lösenordsautentisering med Windows 10-enheter. I slutet av den här artikeln kan du logga in på både webbaserade program och dina Azure AD-anslutna Windows 10-enheter med ditt Azure AD-konto med hjälp av en FIDO2 säkerhets nyckel.

|     |
| --- |
| FIDO2 säkerhets nycklar är en offentlig förhands gransknings funktion i Azure Active Directory. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versionerna|
|     |

## <a name="requirements"></a>Krav

| Enhets typ | Azure AD-ansluten | Hybrid Azure AD-ansluten |
| --- | --- | --- |
| [Azure-Multi-Factor Authentication](howto-mfa-getstarted.md) | X | X |
| [Kombinerad för hands version av säkerhets informations registrering](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibla [säkerhets nycklar för FIDO2](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| Webauthn kräver Windows 10 version 1809 eller senare | X | X |
| [Azure AD-anslutna enheter](../devices/concept-azure-ad-join.md) kräver Windows 10 version 1809 eller senare | X |   |
| [Hybrid Azure AD-anslutna enheter](../devices/concept-azure-ad-join-hybrid.md) kräver Windows 10 Insider build 18945 eller högre |   | X |
| Fullständigt korrigerade Windows Server 2016/2019-domänkontrollanter. |   | X |
| Uppgradera till den senaste versionen av [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (valfritt) | X | X |
| Etablerings paket (valfritt) | X | X |
| Grupprincip (valfritt) |   | X |

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

- Windows Server Active Directory Domain Services (AD DS)-domänanslutna (endast lokala enheter) **stöds inte**.
- RDP-, VDI-och Citrix-scenarier **stöds inte** med hjälp av säkerhets nyckel.
- S/MIME **stöds inte** med säkerhets nyckeln.
- "Kör som" **stöds inte** med hjälp av säkerhets nyckeln.
- Det finns **inte stöd**för att logga in på en server med hjälp av säkerhets nyckel.
- Om du inte har använt din säkerhets nyckel för att logga in på enheten när du är online kan du inte använda den för att logga in eller låsa upp offline.

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för för hands version

Azure AD-anslutna enheter som du ska pilot med måste köra Windows 10 version 1809 eller senare. Den bästa upplevelsen är i Windows 10 version 1903 eller senare.

Hybrid Azure AD-anslutna enheter som du ska pilot med måste köra Windows 10 Insider build 18945 eller senare.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivera säkerhets nycklar för Windows-inloggning

Organisationer kan välja att använda en eller flera av följande metoder för att aktivera användning av säkerhets nycklar för Windows-inloggning baserat på deras organisations krav.

- [Aktivera med Intune](#enable-with-intune)
   - [Riktad Intune-distribution](#targeted-intune-deployment)
- [Aktivera med ett konfigurations paket](#enable-with-a-provisioning-package)
- [Aktivera med grupprincip (endast hybrid Azure AD-anslutna enheter)](#enable-with-group-policy)

> [!IMPORTANT]
> Organisationer med **hybrid Azure AD-anslutna enheter** måste **också** följa stegen i artikeln, [Aktivera FIDO2-autentisering till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md) innan autentisering med Windows 10 FIDO2-säkerhetsnyckeln fungerar.
>
> Organisationer med **Azure AD-anslutna enheter** måste göra detta innan deras enheter kan autentisera till lokala resurser med FIDO2 säkerhets nycklar.

### <a name="enable-with-intune"></a>Aktivera med Intune

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhets registrering** > **Windows-registrering** > **Windows Hello för företag** > **Egenskaper**.
1. Under **Inställningar** anger du **använda säkerhets nycklar för inloggning** till **aktive rad**.

Konfiguration av säkerhets nycklar för inloggning, är inte beroende av att konfigurera Windows Hello för företag.

#### <a name="targeted-intune-deployment"></a>Riktad Intune-distribution

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

För enheter som inte hanteras av Intune kan ett konfigurations paket installeras för att aktivera funktionen. Windows Configuration designer-appen kan installeras från [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

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

### <a name="enable-with-group-policy"></a>Aktivera med grupprincip

För organisationer med **hybrid Azure AD-anslutna enheter** kan du konfigurera följande grupprincip inställning för att aktivera Fido-inloggning för säkerhets nycklar.

Du hittar inställningen under **dator konfiguration** > **Administrativa mallar** > **system** > **inloggning** > **Aktivera inloggning med säkerhets nycklar**.

- Om du anger den här principen till **aktive rad** kan användarna logga in med säkerhets nycklar.
- Om du ställer in den här principen på **inaktive rad** eller **inte konfigurerad** stoppas användare från att logga in med säkerhets nycklar.

Den här grupprincip inställningen kräver en uppdaterad version av `credentialprovider.admx` grupprincip-mallen. Den här nya mallen är tillgänglig med nästa version av Windows Server och Windows 10 20H1. Den här inställningen kan hanteras med en enhet som kör någon av dessa nyare versioner av Windows eller centralt genom att följa anvisningarna i support avsnittet [så här skapar och hanterar du den centrala butiken för grupprincip administrativa mallar i Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Logga in med säkerhets nyckeln FIDO2

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

## <a name="next-steps"></a>Nästa steg

[Aktivera åtkomst till lokala resurser för Azure AD och hybrid Azure AD-anslutna enheter](howto-authentication-passwordless-security-key-on-premises.md)

[Läs mer om enhets registrering](../devices/overview.md)

[Läs mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
