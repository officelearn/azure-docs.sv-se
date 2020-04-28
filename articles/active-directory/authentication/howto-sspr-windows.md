---
title: Lösen ords återställning via självbetjäning för Windows-Azure Active Directory
description: Aktivera självbetjäning för återställning av lösen ord med glömt lösen ord på Windows inloggnings skärm
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4f08161daf1d9c1a4431d9e3fba3ca741d88b16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80743339"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Gör så här: Aktivera lösen ords återställning från Windows inloggnings skärm

För datorer som kör Windows 7, 8, 8,1 och 10 kan du göra det möjligt för användare att återställa sina lösen ord på Windows inloggnings skärm. Användare behöver inte längre hitta en enhet med en webbläsare för att få åtkomst till [SSPR-portalen](https://aka.ms/sspr).

![Exempel på Windows 7-och 10 inloggnings skärmar med SSPR-länk visas](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Allmänna begränsningar

- Lösen ords återställning stöds för närvarande inte från ett fjärr skrivbord eller från utökade Hyper-V-sessioner.
- Vissa leverantörer av autentiseringsuppgifter för tredje part är kända för att orsaka problem med den här funktionen.
- Att inaktivera UAC via ändring av [register nyckeln EnableLUA](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) är känt för att orsaka problem.
- Den här funktionen fungerar inte för nätverk med 802.1 x-nätverksautentisering distribuerad och alternativet "utför omedelbart före användar inloggning". Nätverk med nätverksautentiseringen 802.1x distribuerad rekommenderas att använda datorautentisering för att aktivera funktionen.
- Hybrid Azure AD-anslutna datorer måste ha en nätverks anslutning till en domänkontrollant för att kunna använda det nya lösen ordet och uppdatera cachelagrade autentiseringsuppgifter.
- Om du använder en avbildning innan du kör Sysprep kontrollerar du att webbcachen har rensats för den inbyggda administratören innan du utför CopyProfile-steget. Mer information om det här steget finns i Support artikeln [dåliga prestanda när du använder anpassad standard användar profil](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Följande inställningar är kända för att störa möjligheten att använda och återställa lösen ord på Windows 10-enheter
    - Om Ctrl + Alt + del krävs av en princip i versioner av Windows 10 innan v1809, kommer **Återställ lösen ord** inte att fungera.
    - Om meddelanden för låsskärmen är avstängda fungerar inte **Återställ lösenord**.
    - HideFastUserSwitching har angetts på aktiverat eller 1
    - DontDisplayLastUserName har angetts på aktiverat eller 1
    - NoLockScreen har angetts på aktiverat eller 1
    - EnableLostMode har angetts på enheten
    - Explorer.exe har ersatts med ett anpassat gränssnitt
- Kombinationen av följande tre inställningar kan orsaka att funktionen inte fungerar.
    - Interaktiv inloggning: Kräv inte CTRL + ALT + DEL = inaktive rad
    - DisableLockScreenAppNotifications = 1 eller aktive rad
    - Windows SKU är inte Home eller Professional Edition

## <a name="windows-10-password-reset"></a>Lösen ords återställning för Windows 10

### <a name="windows-10-prerequisites"></a>Krav för Windows 10

- En administratör måste aktivera lösen ords återställning via självbetjäning i Azure AD från Azure Portal.
- **Användarna måste registrera sig för SSPR innan de kan använda den här funktionen**
- Krav för nätverks proxy
   - Windows 10-enheter 
       - Port 443 till `passwordreset.microsoftonline.com` och`ajax.aspnetcdn.com`
       - Windows 10-enheter stöder endast konfiguration av proxykonfigurationen på dator nivå
- Kör minst Windows 10, version april 2018 Update (v1803) och enheterna måste vara antingen:
    - Azure AD-ansluten
    - Hybrid Azure AD-ansluten

### <a name="enable-for-windows-10-using-intune"></a>Aktivera för Windows 10 med Intune

Att distribuera konfigurationsändringen för att aktivera lösenordsåterställning från inloggningsskärmen med Intune är den mest flexibla metoden. Med Intune kan du distribuera konfigurationsändringen till en särskild grupp av datorer som du definierar. Den här metoden kräver Intune-registrering av enheten.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Skapa en princip för enhetskonfiguration i Intune

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Intune**.
1. Skapa en ny enhets konfigurations profil genom att gå till **enhets konfiguration** > **profiler** > **Skapa profil**
   - Ge profilen ett beskrivande namn
   - Du kan också ange en beskrivning av profilen
   - Plattform **Windows 10 och senare**
   - Profiltyp **Anpassad**
1. Konfigurera **Inställningar**
   - **Lägg till** följande OMA-URI-inställning för att aktivera länken Återställ lösenord
      - Ange ett beskrivande namn som förklarar vad inställningen gör
      - Du kan också ange en beskrivning av inställningen
      - **OMA-URI** inställt på `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Datatyp** inställt på **Integer**
      - **Värde** inställt på **1**
      - Klicka på **OK**
   - Klicka på **OK**
1. Klicka på **skapa**
1. Den här principen kan tilldelas till vissa användare, enheter eller grupper. Mer information hittar du i artikeln [Tilldela användar-och enhets profiler i Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Aktivera för Windows 10 med hjälp av registret

1. Logga in på Windows-datorn med autentiseringsuppgifterna för administratören
1. Kör **regedit** som administratör
1. Ange följande registernyckel
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Fel sökning av lösen ords återställning i Windows 10

Azure AD-granskningsloggen innehåller information om IP-adressen och klienttypen där lösenordsåterställningen har gjorts.

![Exempel på lösen ords återställning i Windows 7 i Azure AD-gransknings loggen](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

När användarna återställer sina lösen ord från inloggnings skärmen på en Windows 10-enhet skapas ett tillfälligt konto med låg `defaultuser1` behörighet som heter. Det här kontot används för att skydda processen för lösenordsåterställning. Själva kontot har ett slumpmässigt genererat lösen ord, visas inte för enhets inloggning och tas automatiskt bort när användaren återställer lösen ordet. Det `defaultuser` kan finnas flera profiler men de kan ignoreras på ett säkert sätt.

## <a name="windows-7-8-and-81-password-reset"></a>Lösen ords återställning för Windows 7, 8 och 8,1

### <a name="windows-7-8-and-81-prerequisites"></a>Krav för Windows 7, 8 och 8,1

- En administratör måste aktivera lösen ords återställning via självbetjäning i Azure AD från Azure Portal.
- **Användarna måste registrera sig för SSPR innan de kan använda den här funktionen**
- Krav för nätverks proxy
   - Windows 7-, 8-och 8,1-enheter
       - Port 443 till`passwordreset.microsoftonline.com`
- Operativ systemet Windows 7 eller Windows 8,1 har korrigerats.
- TLS 1,2 aktiverat med hjälp av rikt linjer som finns i [register inställningarna för Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12).
- Om fler än en leverantör av autentiseringsuppgifter för tredje part är aktive rad på datorn visas fler än en användar profil på inloggnings skärmen.

> [!WARNING]
> TLS 1,2 måste vara aktiverat, inte bara inställt på automatisk förhandling

### <a name="install"></a>Installera

1. Hämta lämpligt installations program för den Windows-version som du vill aktivera.
   - Program vara är tillgänglig på Microsoft Download Center på[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Logga in på den dator där du vill installera och kör installations programmet.
1. Efter installationen rekommenderas du starkt att starta om.
1. Efter omstarten väljer du en användare på inloggnings skärmen och klickar på "glömt lösen ord?" för att initiera arbets flödet för lösen ords återställning.
1. Slutför arbets flödet genom att följa anvisningarna på skärmen för att återställa lösen ordet.

![Exempel Windows 7 klickade på "glömt lösen ord?" SSPR-flöde](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Tyst installation

- För tyst installation använder du kommandot "msiexec/i SsprWindowsLogon. PROD. msi/qn"
- Vid tyst avinstallation använder du kommandot "msiexec/x SsprWindowsLogon. PROD. msi/qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Fel sökning av lösen ords återställning för Windows 7, 8 och 8,1

Händelser loggas både på datorn och i Azure AD. Azure AD-händelser innehåller information om IP-adressen och ClientType där lösen ords återställningen utfördes.

![Exempel på lösen ords återställning i Windows 7 i Azure AD-gransknings loggen](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Om ytterligare loggning krävs kan en register nyckel på datorn ändras för att aktivera utförlig loggning. Aktivera utförlig loggning enbart för fel söknings syfte.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Om du vill aktivera utförlig loggning skapar du `REG_DWORD: "EnableLogging"`en och anger den till 1.
- Om du vill inaktivera utförlig loggning ändrar `REG_DWORD: "EnableLogging"` du till 0.

## <a name="what-do-users-see"></a>Vad ser användarna

Nu när du har konfigurerat lösen ords återställning för dina Windows-enheter, vilka ändringar av användaren? Hur vet de att de kan återställa sitt lösenord på inloggningsskärmen?

![Exempel på Windows 7-och 10 inloggnings skärmar med SSPR-länk visas](./media/howto-sspr-windows/windows-reset-password.png)

När användarna försöker logga in ser de nu länken **Återställ lösen ord** eller **glömt lösen** ord som öppnar självbetjäningen för återställning av lösen ord på inloggnings skärmen. Via den här funktionen kan användarna återställa sina lösenord utan att de behöver använda en annan enhet för att få åtkomst till webbläsaren.

Dina användare får hjälp med att använda funktionen i [Reset your work or school password](../user-help/active-directory-passwords-update-your-own-password.md) (Återställa ditt arbets- eller skollösenord)

## <a name="next-steps"></a>Nästa steg

[Planera autentiseringsmetoder som ska tillåtas](concept-authentication-methods.md)

[Konfigurera Windows 10](https://docs.microsoft.com/windows/configuration/)
