---
title: Inloggning av lösenordslös säkerhetsnyckel Windows - Azure Active Directory
description: Lär dig hur du aktiverar inloggning av lösenordslös säkerhetsnyckel till Azure Active Directory med FIDO2-säkerhetsnycklar (förhandsversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6ef244a887e75a0d8b9bb663d5325a33cd1e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263912"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>Aktivera inloggning av lösenordslös säkerhetsnyckel till Windows 10-enheter med Azure Active Directory (förhandsversion)

Det här dokumentet fokuserar på att aktivera FIDO2-säkerhetsnyckelbaserad lösenordslös autentisering med Windows 10-enheter. I slutet av den här artikeln kan du logga in på både dina Azure AD- och hybrid Azure AD-anslutna Windows 10-enheter med ditt Azure AD-konto med hjälp av en FIDO2-säkerhetsnyckel.

|     |
| --- |
| FIDO2-säkerhetsnycklar är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för användning för Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>Krav

| Enhetstyp | Azure AD-ansluten | Hybrid Azure AD-ansluten |
| --- | --- | --- |
| [Azure multifaktorautentisering](howto-mfa-getstarted.md) | X | X |
| [Förhandsgranskning av kombinerad registrering av säkerhetsinformation](concept-registration-mfa-sspr-combined.md) | X | X |
| Kompatibla [FIDO2-säkerhetsnycklar](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN kräver Windows 10 version 1809 eller senare | X | X |
| [Azure AD-anslutna enheter](../devices/concept-azure-ad-join.md) kräver Windows 10 version 1903 eller senare | X |   |
| [Hybrid Azure AD-anslutna enheter](../devices/concept-azure-ad-join-hybrid.md) kräver Windows 10 Insider Build 18945 eller senare |   | X |
| Fullständigt korrigerade Windows Server 2016/2019-domänkontrollanter. |   | X |
| [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) version 1.4.32.0 eller senare |   | X |
| [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) (valfritt) | X | X |
| Etableringspaket (valfritt) | X | X |
| Grupprincip (valfritt) |   | X |

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds

Följande scenarier stöds inte:

- Windows Server Active Directory Domain Services (AD DS) domänansluten (endast lokala enheter) distribution.
- RDP-, VDI- och Citrix-scenarier med hjälp av en säkerhetsnyckel.
- S/MIME med hjälp av en säkerhetsnyckel.
- "Kör som" med hjälp av en säkerhetsnyckel.
- Logga in på en server med hjälp av en säkerhetsnyckel.
- Om du inte har använt säkerhetsnyckeln för att logga in på enheten när du är online kan du inte använda den för att logga in eller låsa upp offline.
- Logga in eller låsa upp en Windows 10-enhet med en säkerhetsnyckel som innehåller flera Azure AD-konton. Det här scenariot använder det senaste kontot som lagts till i säkerhetsnyckeln. WebAuthN tillåter användare att välja det konto de vill använda.
- Lås upp en enhet som kör Windows 10 version 1809. För bästa möjliga upplevelse, använd Windows 10 version 1903 eller senare.

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för förhandsgranskning

Azure AD-anslutna enheter som du lotsar under förhandsgranskningen av funktionen med måste köra Windows 10 version 1809 eller senare. Den bästa upplevelsen är på Windows 10 version 1903 eller högre.

Hybrid Azure AD-anslutna enheter måste köra Windows 10 Insider Build 18945 eller nyare.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivera säkerhetsnycklar för Windows-inloggning

Organisationer kan välja att använda en eller flera av följande metoder för att aktivera användning av säkerhetsnycklar för Windows-inloggning baserat på organisationens krav:

- [Aktivera med Intune](#enable-with-intune)
- [Riktad Intune-distribution](#targeted-intune-deployment)
- [Aktivera med ett etableringspaket](#enable-with-a-provisioning-package)
- [Aktivera med grupprincip (endast Hybrid Azure AD-anslutna enheter)](#enable-with-group-policy)

> [!IMPORTANT]
> Organisationer med **hybrid Azure AD-anslutna enheter** måste **också** slutföra stegen i artikeln, [Aktivera FIDO2-autentisering till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md) innan autentisering av windows 10 FIDO2-säkerhetsnyckel fungerar.
>
> Organisationer med **Azure AD-anslutna enheter** måste göra detta innan deras enheter kan autentisera lokala resurser med FIDO2-säkerhetsnycklar.

### <a name="enable-with-intune"></a>Aktivera med Intune

Så här aktiverar du användningen av säkerhetsnycklar med Intune:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till Registrering av Windows **Intune-enhetsregistrering** > **Device enrollment** > **Windows** > **Hello for Business.** > **Properties**
1. Under **Inställningar**anger du **Använd säkerhetsnycklar för inloggning** på **Aktiverad**.

Konfiguration av säkerhetsnycklar för inloggning är inte beroende av att konfigurera Windows Hello för företag.

### <a name="targeted-intune-deployment"></a>Riktad Intune-distribution

Om du vill rikta in dig på specifika enhetsgrupper för att aktivera autentiseringsleverantören använder du följande anpassade inställningar via Intune:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Bläddra till**konfigurationsprofiler för** >  **Microsoft Intune-enhet** > **Device configuration** > **Skapa**.
1. Konfigurera den nya profilen med följande inställningar:
   - Namn: Säkerhetsnycklar för Windows-inloggning
   - Beskrivning: Aktiverar FIDO-säkerhetsnycklar som ska användas vid inloggning i Windows
   - Plattform: Windows 10 och senare
   - Profiltyp: Anpassad
   - Anpassade OMA-URI-inställningar:
      - Namn: Aktivera FIDO-säkerhetsnycklar för Windows-inloggning
      - OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      - Datatyp: Heltal
      - Värde: 1
1. Den här principen kan tilldelas specifika användare, enheter eller grupper. Mer information finns [i Tilldela användar- och enhetsprofiler i Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Skapa intune-princip för anpassad enhetskonfiguration](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>Aktivera med ett etableringspaket

För enheter som inte hanteras av Intune kan ett etableringspaket installeras för att aktivera funktionen. Appen Windows Configuration Designer kan installeras från [Microsoft Store](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22). Gör så här för att skapa ett etableringspaket:

1. Starta Konfigurationsdesignern för Windows.
1. Välj **Arkiv** > **Nytt projekt**.
1. Ge projektet ett namn och anteckna sökvägen där projektet skapas och välj sedan **Nästa**.
1. Lämna *etableringspaketet* markerat som **det valda projektarbetsflödet** och välj **Nästa**.
1. Välj *Alla Windows-skrivbordsutgåvor* under **Välj vilka inställningar som ska visas och konfigureras**och välj sedan **Nästa**.
1. Välj **Slutför**.
1. I det nyskapade projektet bläddrar du till **Runtime-inställningar** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Ange **UseSecurityKeyForSignIn** till *Aktiverad*.
1. Välj **Exportera** > **etableringspaket**
1. Lämna standardvärdena i fönstret **Skapa** under **Beskriv etableringspaketet**och välj sedan **Nästa**.
1. Lämna standardvärdena i fönstret **Bygg** under **Välj säkerhetsinformation för etableringspaketet** och välj **Nästa**.
1. Anteckna eller ändra sökvägen i fönstren **Skapa** under **Välj var etableringspaketet ska sparas** och välj **Nästa**.
1. Välj **Skapa** på sidan **Skapa etableringspaketet.**
1. Spara de två filer som skapats *(ppkg* och *katt)* på en plats där du kan tillämpa dem på maskiner senare.
1. Information om hur du installerar etableringspaketet som du skapade finns i [Tillämpa ett etableringspaket](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package).

> [!NOTE]
> Enheter som kör Windows 10 Version 1809 måste också aktivera delat pc-läge *(EnableSharedPCMode*). Mer information om hur du aktiverar den här funktionen finns i [Konfigurera en delad dator eller gästdator med Windows 10](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc).

### <a name="enable-with-group-policy"></a>Aktivera med grupprincip

För **hybrid Azure AD-anslutna enheter**kan organisationer konfigurera följande grupprincipinställning för att aktivera FIDO-registrering av säkerhetsnyckel. Inställningen finns under **Datorkonfiguration** > **Administrativa mallar** > **Systeminloggning** > **System** > **Aktivera säkerhetsnyckel inloggning:**

- Om du ställer in den här principen på **Aktiverad** kan användare logga in med säkerhetsnycklar.
- Om du anger den här principen till **Inaktiverad** eller **inte konfigurerad** stoppas användare från att logga in med säkerhetsnycklar.

Den här grupprincipinställningen `credentialprovider.admx` kräver en uppdaterad version av grupprincipmallen. Den här nya mallen är tillgänglig med nästa version av Windows Server och med Windows 10 20H1. Den här inställningen kan hanteras med en enhet som kör en av dessa nyare versioner av Windows eller centralt genom att följa vägledningen i supportavsnittet, [Så här skapar och hanterar du administrativa centralbutiken i Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="sign-in-with-fido2-security-key"></a>Logga in med FIDO2-säkerhetsnyckel

I exemplet nedan har en användare med namnet Bala Sandhu redan etablerat sin FIDO2-säkerhetsnyckel med hjälp av stegen i föregående artikel, [Aktivera lösenordslös säkerhetsnyckel logga in](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys). För hybrid-Azure AD-anslutna enheter kontrollerar du att du även har [aktiverat lösenordslös säkerhetsnyckel inloggning till lokala resurser](howto-authentication-passwordless-security-key-on-premises.md). Bala kan välja säkerhetsnyckelautentiseringsprovidern från låsskärmen i Windows 10 och infoga säkerhetsnyckeln för att logga in i Windows.

![Inloggning för säkerhetsnyckel på låsskärmen för Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Hantera säkerhetsnyckeln biometrisk, PIN-kod eller återställ säkerhetsnyckel

* Windows 10 version 1903 eller senare
   * Användare kan öppna **Windows-inställningar** på sin enhet > **konton** > **säkerhetsnyckel**
   * Användare kan ändra sin PIN-kod, uppdatera biometri eller återställa sin säkerhetsnyckel

## <a name="troubleshooting-and-feedback"></a>Felsökning och feedback

Om du vill dela feedback eller stöta på problem när du förhandsgranskar den här funktionen delar du via Appen Windows Feedback Hub med följande steg:

1. Starta **Feedback Hub** och se till att du är inloggad.
1. Skicka feedback under följande kategorisering:
   - Kategori: Säkerhet och sekretess
   - Underkategori: FIDO
1. Om du vill fånga in loggar använder du alternativet återskapa **mitt problem**

## <a name="next-steps"></a>Nästa steg

[Aktivera åtkomst till lokala resurser för Azure AD- och hybrid-Azure AD-anslutna enheter](howto-authentication-passwordless-security-key-on-premises.md)

[Läs mer om enhetsregistrering](../devices/overview.md)

[Läs mer om Azure Multi-Factor-autentisering](../authentication/howto-mfa-getstarted.md)
