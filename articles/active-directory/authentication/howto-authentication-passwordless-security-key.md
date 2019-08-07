---
title: Aktivera lösen ords skydds nyckel inloggning för Azure AD (för hands version) – Azure Active Directory
description: Aktivera lösen ords lös säkerhets nyckel logga in på Azure AD med hjälp av FIDO2-säkerhetsnycklar (för hands version)
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
ms.openlocfilehash: a063891339a46366490447b7c7a7a1a14fd81be6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828922"
---
# <a name="enable-passwordless-security-key-sign-in-for-azure-ad-preview"></a>Aktivera lösen ords skydds nyckel inloggning för Azure AD (för hands version)

## <a name="requirements"></a>Krav

* Azure Multi-Factor Authentication
* Kombinerad förhands granskning med användare aktiverade för SSPR
* För hands versionen av FIDO2 för säkerhets nyckel krävs kompatibla FIDO2 säkerhets nycklar
* Webauthn kräver Microsoft Edge på Windows 10 version 1809 eller senare
* FIDO2-baserad Windows-inloggning kräver Azure AD-ansluten Windows 10 version 1809 eller senare

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för för hands version

Enheter som du ska pilot med måste köra Windows 10 version 1809 eller senare. Den bästa upplevelsen är i Windows 10 version 1903 eller senare.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivera säkerhets nycklar för Windows-inloggning

Organisationer kan välja att använda en eller flera av följande metoder för att aktivera användning av säkerhets nycklar för Windows-inloggning.

### <a name="enable-credential-provider-via-intune"></a>Aktivera autentiseringsprovider via Intune

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhets registrering** > **Windows-registrering** > **Egenskaper** **för Windows Hello för företag** > .
1. Under **Inställningar** anger du **använda säkerhets nycklar för inloggning** till **aktive rad**.

Konfiguration av säkerhets nycklar för inloggning, är inte beroende av att konfigurera Windows Hello för företag.

#### <a name="enable-targeted-intune-deployment"></a>Aktivera riktad Intune-distribution

Om du vill använda specifika enhets grupper för att aktivera Credential-providern använder du följande anpassade inställningar via Intune. 

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhets konfiguration** > **profiler** > **Skapa profil**.
1. Konfigurera den nya profilen med följande inställningar
   1. Namn: Säkerhets nycklar för Windows-inloggning
   1. Beskrivning: Aktiverar FIDO-säkerhetsnycklar som ska användas under Windows-inloggning
   1. Plattform: Windows 10 och senare
   1. Plattforms typ: Anpassat
   1. Anpassade OMA-URI-inställningar:
      1. Namn: Aktivera säkerhets nycklar för FIDO för Windows-inloggning
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Datatyp: Integer
      1. Värde: 1 
1. Den här principen kan tilldelas till vissa användare, enheter eller grupper. Mer information hittar du i artikeln [Tilldela användar-och enhets profiler i Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Skapa anpassad enhets konfigurations princip för Intune](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Aktivera autentiseringsprovider via ett etablerings paket

För enheter som inte hanteras av Intune kan ett konfigurations paket installeras för att aktivera funktionen. Windows Configuration designer-appen kan installeras från [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Starta Windows Configuration designer.
1. Välj **Arkiv** > **nytt projekt**.
1. Ge projektet ett namn och anteckna sökvägen där projektet skapas.
1. Välj **Nästa**.
1. Lämna **etablerings paketet** valt som det **valda projekt arbets flödet** och välj **Nästa**.
1. Välj **alla versioner av Windows-skrivbordet** under **Välj vilka inställningar du vill visa och konfigurera** och välj **Nästa**.
1. Välj **Slutför**.
1. I det nyskapade projektet bläddrar du till **körnings inställningar** > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**.
1. Ange **UseSecurityKeyForSignIn** till **aktive rad**.
1. Välj **Exportera** > **etablerings paket**
1. Lämna standardvärdena i fönstret **build** under **Beskriv etablerings paketet** och välj **Nästa**.
1. Lämna standardvärdena i fönstret **build** under **Välj säkerhets information för etablerings paketet** och välj **Nästa**.
1. Anteckna eller ändra sökvägen i **build** Windows under **Välj var du vill spara etablerings paketet** och välj **Nästa**.
1. Välj **build** på sidan **bygga etablerings paket** .
1. Spara de två filerna som skapats (ppkg och Cat) till en plats där du kan tillämpa dem på datorer senare.
1. Följ anvisningarna i artikeln [tillämpa ett konfigurations paket](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)för att tillämpa det konfigurations paket som du har skapat.

## <a name="obtain-fido2-security-keys"></a>Hämta säkerhets nycklar för FIDO2

Mer information om vilka nycklar och tillverkare som stöds finns i avsnittet om säkerhets nycklar för FIDO2 i artikeln [Vad är lösen ords skydd?](concept-authentication-passwordless.md)

> [!NOTE]
> Om du köper och planerar att använda NFC-baserade säkerhets nycklar behöver du en NFC-läsare som stöds.

## <a name="enable-passwordless-authentication-method"></a>Aktivera autentiseringsmetod för lösen ord

### <a name="enable-the-combined-registration-experience"></a>Aktivera kombinerad registrerings upplevelse

Registrerings funktioner för metoder för lösen ords kryptering förlitar sig på den kombinerade förhands granskningen. Följ anvisningarna i artikeln Aktivera kombinerad registrering av [säkerhets information (för hands version)](howto-registration-mfa-sspr-combined.md)för att aktivera den kombinerade förhands granskningen.

### <a name="enable-new-passwordless-authentication-method"></a>Aktivera ny autentiseringsmetod för lösen ord

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Bläddra till **Azure Active Directory** > **säkerhets** > **autentiseringsmetoder**metoderprincip > för**autentisering (för hands version)**
1. Under varje **metod**väljer du följande alternativ
   1. **Aktivera** – Ja eller Nej
   1. **Mål** – alla användare eller Välj användare
1. **Spara** varje metod

> [!WARNING]
> FIDO2 "Key begränsning policies" fungerar inte ännu. Den här funktionen kommer att vara tillgänglig före allmän tillgänglighet. Ändra inte dessa principer från standard.

> [!NOTE]
> Du behöver inte välja båda metoderna utan lösen ord (om du bara vill förhandsgranska en enda metod för lösen ords skydd kan du bara aktivera den metoden). Vi rekommenderar att du testar båda metoderna eftersom de båda har sina egna förmåner.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Användar registrering och hantering av säkerhets nycklar för FIDO2

1. Bläddra till [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Logga in om inte redan
1. Klicka på **säkerhets information**
   1. Om användaren redan har minst en Azure Multi-Factor Authentication-metod registrerad kan de omedelbart registrera en säkerhets nyckel för FIDO2.
   1. Om de inte har minst en Azure Multi-Factor Authentication-metod registrerad måste de lägga till en.
1. Lägg till en säkerhets nyckel för FIDO2 genom att klicka på **Lägg till metod** och välja **säkerhets nyckel**
1. Välj **USB-enhet** eller **NFC-enhet**
1. Låt din nyckel vara klar och välj **Nästa**
1. En ruta visas och du uppmanas att skapa/ange en PIN-kod för din säkerhets nyckel och sedan utföra den önskade gesten för din nyckel, antingen bio metrisk eller touch.
1. Du kommer tillbaka till den kombinerade registrerings upplevelsen och uppmanas att ange ett beskrivande namn för din token, så att du kan identifiera vilken som är en om du har flera. Klicka på **Nästa**.
1. Slutför processen genom att klicka på **klar**

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Hantera säkerhets nyckel bio metrisk, PIN-kod eller återställning säkerhets nyckel

* Windows 10 version 1809
   * Den medföljande program varan från leverantören av säkerhets nyckeln krävs
* Windows 10 version 1903 eller senare
   * Användare kan öppna **Windows-inställningar** på sina enheter >**säkerhets nyckel** för **konton** > 
   * Användare kan ändra sin PIN-kod, uppdatera biometrik eller återställa sin säkerhets nyckel

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Användar registrering och hantering av Microsoft Authenticator app

Om du vill konfigurera Microsoft Authenticator appen för telefon inloggning, följer du anvisningarna i artikeln [Logga in på dina konton med hjälp av Microsoft Authenticator-appen](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credential"></a>Logga in med autentiseringsuppgifter för lösen ord

### <a name="sign-in-at-the-lock-screen"></a>Logga in på Lås skärmen

I exemplet nedan har en användare Bala Sandhu redan har tillhandahållit sin FIDO2-säkerhetsnyckel. Bala kan välja providern för säkerhets nyckel autentiseringsuppgifter från Lås skärmen för Windows 10 och infoga säkerhets nyckeln för att logga in på Windows.

![Säkerhets nyckel logga in på Lås skärmen i Windows 10](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Logga in på webben

I exemplet nedan har en användare redan har etablerad sin FIDO2-säkerhetsnyckel. Användaren kan välja att logga in på webben med sin FIDO2 säkerhets nyckel i Microsoft Edge-webbläsaren på Windows 10 version 1809 eller senare.

![Inloggning av säkerhets nyckel i Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="known-issues"></a>Kända problem

### <a name="security-key-provisioning"></a>Etablering av säkerhets nyckel

Administratörs etablering och avetablering av säkerhets nycklar är inte tillgängligt i den offentliga för hands versionen.

### <a name="hybrid-azure-ad-join"></a>Hybrid Azure AD-anslutning

Användare som förlitar sig på WIA SSO som använder hanterade autentiseringsuppgifter som FIDO2 säkerhets nycklar eller lösen ords lös inloggning med Microsoft Authenticator appen behöver hybrid anslutning på Windows 10 för att få fördelarna med SSO. Säkerhets nycklar fungerar dock bara för Azure Active Directory anslutna datorer för tillfället. Vi rekommenderar att du bara provar FIDO2-säkerhetsnycklar för Windows Lås skärmen på rena Azure Active Directory anslutna datorer. Den här begränsningen gäller inte för webben.

### <a name="upn-changes"></a>UPN-ändringar

Vi arbetar med att stödja en funktion som tillåter UPN-ändring på Hybrid AADJ-och AADJ-enheter. Om användarens UPN-ändringar ändras, kan du inte längre ändra FIDO2-säkerhetsnycklar för det. Det enda sättet är att återställa enheten och användaren måste registrera den igen.

## <a name="next-steps"></a>Nästa steg

[Läs mer om enhets registrering](../devices/overview.md)

[Lär dig mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
