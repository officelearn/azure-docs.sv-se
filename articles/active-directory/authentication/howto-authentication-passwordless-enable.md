---
title: Konfigurera Azure Active Directory lösenordslös inloggning (förhandsversion)
description: Aktivera konfiguration av lösenordsfri inloggning på Azure AD med FIDO2 säkerhetsnycklar eller Microsoft Authenticator-appen (förhandsversion)
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
ms.openlocfilehash: 5ba2545467ebfbd032408aeee25b82b92a628f2a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712077"
---
# <a name="enable-passwordless-sign-in-for-azure-ad-preview"></a>Aktivera konfiguration av lösenordsfri inloggning för Azure AD (förhandsversion)

## <a name="requirements"></a>Krav

* Azure Multi-Factor Authentication
* Kombinerade registrerings-förhandsversion
* Viktiga förhandsversion av FIDO2 säkerhet kräver kompatibla FIDO2 säkerhetsnycklar
* WebAuthN kräver Microsoft Edge i Windows 10 version 1809 eller senare
* FIDO2 baserat Windows inloggningen kräver Azure AD-anslutna Windows 10 version 1809 eller senare

## <a name="prepare-devices-for-preview"></a>Förbereda enheter för förhandsgranskning

Enheter som du kommer testkörning av med måste köra Windows 10 version 1809 eller högre. Den bästa upplevelsen är Windows 10 version 1903 eller högre.

## <a name="enable-security-keys-for-windows-sign-in"></a>Aktivera säkerhetsnycklar för Windows-logga in

Organisationer kan välja att använda en eller flera av följande metoder för att aktivera användningen av säkerhetsnycklar för Windows logga in.

### <a name="enable-credential-provider-via-intune"></a>Aktivera provider för autentiseringsuppgifter via Intune

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhetsregistrering** > **Windows-registrering** > **Windows Hello för företag** > **egenskaper**.
1. Under **inställningar** ange **använda säkerhetsnycklar för att logga in** till **aktiverad**.

Konfigurationen av säkerhetsnycklar för inloggning, är inte beroende av att konfigurera Windows Hello för företag.

#### <a name="enable-targeted-intune-deployment"></a>Aktivera riktad distribution av Intune

Om du vill rikta specifika Enhetsgrupper för att aktivera provider för autentiseringsuppgifter, använder du följande anpassade inställningar via Intune. 

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till **Microsoft Intune** > **enhetskonfiguration** > **profiler** > **skapa profil**.
1. Konfigurera den nya profilen med följande inställningar
   1. Namn: Säkerhetsnycklar för Windows-inloggning
   1. Beskrivning: Aktiverar FIDO säkerhetsnycklar som ska användas när Windows-inloggning
   1. Plattform: Windows 10 och senare
   1. Plattformstyp: Anpassat
   1. Anpassade OMA-URI-inställningar:
      1. Namn: Aktivera FIDO säkerhetsnycklar för Windows-inloggning
      1. OMA-URI: ./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. Datatyp: Integer
      1. Värde: 1 
1. Den här principen kan tilldelas till specifika användare, enheter eller grupper. Mer information finns i artikeln [tilldela användar- och enhetsprofiler i Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

![Intune configuration-skapa anpassade principer](./media/howto-authentication-passwordless-enable/intune-custom-profile.png)

### <a name="enable-credential-provider-via-provisioning-package"></a>Aktivera provider för autentiseringsuppgifter via konfigurationspaket

För enheter som inte hanteras av Intune, kan ett konfigurationspaket installeras om du vill aktivera funktionen. Windows Configuration Designer-appen kan installeras från den [Microsoft Store](https://www.microsoft.com/store/apps/9nblggh4tx22).

1. Starta Windows Configuration Designer.
1. Välj **filen** > **nytt projekt**.
1. Namnge ditt projekt och anteckna sökvägen där du har skapat ditt projekt.
1. Välj **Nästa**.
1. Lämna **etablering paketet** valda som den **valda projektarbetsflöde** och välj **nästa**.
1. Välj **alla Windows-skrivbord versioner** under **välja vilka inställningar som du vill visa och konfigurera** och välj **nästa**.
1. Välj **Slutför**.
1. I det nyskapade projektet bläddrar du till **körningsinställningar** > **WindowsHelloForBusiness** > **SecurityKeys**  >  **UseSecurityKeyForSignIn**.
1. Ange **UseSecurityKeyForSignIn** till **aktiverat**.
1. Välj **exportera** > **etablering paket**
1. Lämna standardvärdena i den **skapa** fönstret, under **beskriver konfigurationspaketet** och välj **nästa**.
1. Lämna standardvärdena i den **skapa** fönstret, under **Välj säkerhetsinformation om konfigurationspaketet** och välj **nästa**.
1. Anteckna eller ändra sökvägen i den **skapa** windows under **välja var du vill spara konfigurationspaketet** och välj **nästa**.
1. Välj **skapa** på den **skapa konfigurationspaketet** sidan.
1. Spara de två filerna som har skapats (ppkg och cat) till en plats där du kan använda dem till datorer senare.
1. Följ anvisningarna i artikeln [tillämpa ett konfigurationspaket](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package), för att tillämpa konfigurationspaketet som du skapade.

## <a name="obtain-fido2-security-keys"></a>Hämta FIDO2 säkerhetsnycklar

Se avsnittet FIDO2 säkerhetsnycklar, i artikeln [vad är lösenordslös?](concept-authentication-passwordless.md) för mer information om stöds nycklar och tillverkare.

> [!NOTE]
> Om du köper och planerar att använda NFC baserat säkerhetsnycklar måste en NFC-läsare som stöds.

## <a name="enable-passwordless-authentication-methods"></a>Aktivera lösenordslös autentiseringsmetoder

### <a name="enable-the-combined-registration-experience"></a>Aktivera kombinerade registrerings-upplevelsen

Registrering av funktionerna för FIDO2 säkerhetsnycklar är beroende av kombinerade registrerings-förhandsversionen. Följ stegen nedan för att aktivera kombinerade registrerings-förhandsversionen.

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Bläddra till **Azure Active Directory** > **användarinställningar**
   1. Klicka på **hantera inställningar för åtkomst till panelen förhandsversionsfunktioner**
   1. Under **användare kan använda förhandsversionsfunktioner för att registrera och hantera säkerhetsinformation - förbättrad**.
      1. Välj **valda** och välj en grupp av användare som ska delta i förhandsversionen.
      1. Eller välj **alla** ska aktiveras för alla i din katalog.
1. Klicka på **Spara**

### <a name="enable-new-passwordless-authentication-methods"></a>Aktivera nya lösenordslös autentiseringsmetoder

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Bläddra till **Azure Active Directory** > **autentiseringsmetoder** > **autentiseringsprincip metod (förhandsversion)**
1. Under varje **metoden**, väljer du följande
   1. **Aktivera** – Ja eller Nej
   1. **Target** – alla användare eller Välj användare
1. **Spara** varje metod

> [!WARNING]
> FIDO2 som ”nyckel policys för begränsning av” inte fungerar ännu. Den här funktionen kommer att finnas före den allmänt tillgängliga, ändra inte de här principerna från standardvärdet.

> [!NOTE]
> Du behöver inte välja att båda lösenordslös metoder (om du vill förhandsgranska bara en konfiguration av lösenordsfri metod du kan aktivera endast den metoden). Vi rekommenderar att du kan prova att använda båda metoderna eftersom de båda har sina egna fördelar.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>Användarregistrering och hantering av FIDO2 säkerhetsnycklar

1. Bläddra till [https://myprofile.microsoft.com](https://myprofile.microsoft.com)
1. Logga in om den inte redan
1. Klicka på **säkerhetsinformation**
   1. Om användaren redan har minst en Azure Multi-Factor Authentication-metod som är registrerad, kan de omedelbart registrera en FIDO2 säkerhetsnyckel.
   1. Om de inte har minst en Azure Multi-Factor Authentication-metod som är registrerad kan de måste lägga till en.
1. Lägg till en FIDO2 säkerhetsnyckel genom att klicka på **tilläggsmetod** och välja **Säkerhetsnyckel**
1. Välj **USB-enhet** eller **NFC-enhet**
1. Har nyckeln är redo och välj **nästa**
1. En ruta ska visas och be dig att skapa/ange en PIN-kod för din säkerhetsnyckel och sedan utföra nödvändiga gest för din nyckel antingen biometriska eller touch.
1. Du kommer tillbaka till kombinerade registrerings-upplevelsen och uppmanas att ange ett beskrivande namn för din token så att du kan identifiera vilken som om du har flera. Klicka på **Nästa**.
1. Klicka på **klar** ska slutföras

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>Hantera säkerhetsnyckel biometrisk, PIN-kod eller återställa Säkerhetsnyckel

* Windows 10 version 1809
   * Tillhörande programvara från security viktiga leverantören krävs
* Windows 10 version 1903 eller senare
   * Användare kan öppna **Windows-inställningar** på sin enhet > **konton** > **Säkerhetsnyckel**
   * Användare kan ändra sina PIN-kod, uppdatera biometrik eller återställa sina Säkerhetsnyckel

## <a name="user-registration-and-management-of-microsoft-authenticator-app"></a>Användarregistrering och hantering av Microsoft Authenticator-appen

För att konfigurera Microsoft Authenticator-appen för telefoninloggning, följer du anvisningarna i artikeln [logga in på dina konton med hjälp av Microsoft Authenticator-appen](../user-help/user-help-auth-app-sign-in.md).

## <a name="sign-in-with-passwordless-credentials"></a>Logga in med autentiseringsuppgifter för konfiguration av lösenordsfri

### <a name="sign-in-at-the-lock-screen"></a>Logga in på låsskärm

I exemplet nedan en användare har Bala Sandhu redan har etablerat sina FIDO2 säkerhetsnyckel. Bala väljer nyckelautentiseringsuppgift säkerhetsprovider från Windows 10-låsskärmen och infoga Säkerhetsnyckel för att logga in på Windows.

![Säkerhetsnyckel logga in på Windows 10-låsskärmen](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="sign-in-on-the-web"></a>Logga in på webben

I exemplet nedan en användare har redan har etablerat sina FIDO2 säkerhetsnyckel. Användaren kan välja att logga in på webben med sina FIDO2 säkerhetsnyckel i Microsoft Edge-webbläsaren på Windows 10 version 1809 eller högre.

![Viktiga logga i Microsoft Edge](./media/howto-authentication-passwordless-enable/fido2-windows-10-1903-edge-sign-in.png)

Information om inloggningen med hjälp av Microsoft Authenticator-appen finns i artikeln [logga in på dina konton med hjälp av Microsoft Authenticator-appen](../user-help/user-help-auth-app-sign-in.md).

## <a name="known-issues"></a>Kända problem

### <a name="fido2-security-keys"></a>FIDO2 säkerhetsnycklar

#### <a name="security-key-provisioning"></a>Säkerhet viktiga etablering

Administratören aktivering och inaktivering av säkerhetsnycklar är inte tillgänglig i den offentliga förhandsversionen.

#### <a name="hybrid-azure-ad-join"></a>Hybrid Azure Active Directory-anslutning

Användare som förlitar sig på WIA SSO och som använder hanterade autentiseringsuppgifter som FIDO2 säkerhetsnycklar eller lösenordslös logga in med Microsoft Authenticator-appen behöver att Hybrid delta i Windows 10 för att få fördelarna med enkel inloggning. Dock säkerhetsnycklar endast arbete för Azure Active Directory-anslutna datorer nu. Vi rekommenderar att du bara testa FIDO2 säkerhetsnycklar för Windows-låsskärmen på ren Azure Active Directory-anslutna datorer. Den här begränsningen gäller inte för webben.

#### <a name="upn-changes"></a>UPN-ändringar

Vi arbetar med stöd för en funktion som kan ändra UPN på hybrid AADJ och AADJ enheter. Om en användares UPN ändras kan ändra du inte längre FIDO2 säkerhetsnycklar för som. Så här är det enda sättet att återställa enheten och användaren måste registrera igen.

### <a name="authenticator-app"></a>Autentiseringsapp

#### <a name="ad-fs-integration"></a>AD FS-integrering

När en användare har aktiverat Microsoft Authenticator lösenordslös autentiseringsuppgifter, autentisering för den användaren alltid som standard skickar ett meddelande om godkännande. Den här logiken som förhindrar att användare i en hybrid-klient dirigeras till ADFS för verifieringen av inloggningen utan att användaren tar ytterligare ett steg till klickar du på ”Använd ditt lösenord istället”. Den här processen kommer också kringgå alla principer för villkorlig åtkomst av lokala och direkt autentiseringsflöden. Undantag till den här processen är om en login_hint är anges en användare vara automatiskt till AD FS och kringgå alternativet att använda lösenordslös autentiseringsuppgifterna.

#### <a name="azure-mfa-server"></a>Azure MFA-servern

Slutanvändare som är aktiverade för MFA via en organisations lokal Azure MFA server kan fortfarande skapa och använda en enda lösenordslös phone inloggning i autentiseringsuppgifter. Om du försöker uppgradera flera installationer (5 +) av Microsoft Authenticator med autentiseringsuppgifterna, kan den här ändringen resultera i ett fel.  

#### <a name="device-registration"></a>Enhetsregistrering

En av kraven för att skapa den här nya, starka autentiseringsuppgifter är att enheten där det finns är registrerad i Azure AD-klient till en enskild användare. På grund av begränsningar för registrering av enheter kan endast en enhet registreras i en enda klient. Den här gränsen innebär att endast ett arbets- eller skolkonto konto i Microsoft Authenticator-appen kan aktiveras för telefoninloggning.

## <a name="next-steps"></a>Nästa steg

[Lär dig mer om enhetsregistrering](../devices/overview.md)

[Lär dig mer om Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
