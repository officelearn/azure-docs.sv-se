---
title: Återställning av lösenord för självbetjäning för Windows – Azure Active Directory
description: Så här aktiverar du återställning av lösenord med självbetjäning med glömt lösenord på Inloggningsskärmen i Windows
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743339"
---
# <a name="how-to-enable-password-reset-from-the-windows-login-screen"></a>Så här aktiverar du återställning av lösenord från Inloggningsskärmen i Windows

För maskiner som kör Windows 7, 8, 8.1 och 10 kan du göra det möjligt för användare att återställa sitt lösenord på Windows-inloggningsskärmen. Användare behöver inte längre hitta en enhet med en webbläsare för att komma åt [SSPR-portalen](https://aka.ms/sspr).

![Exempel på Windows 7 och 10 inloggningsskärmar med SSPR-länk visad](./media/howto-sspr-windows/windows-reset-password.png)

## <a name="general-limitations"></a>Allmänna begränsningar

- Återställning av lösenord stöds för närvarande inte från ett fjärrskrivbord eller från hyper-V-förbättrade sessioner.
- Vissa autentiseringsuppgifter från tredje part är kända för att orsaka problem med den här funktionen.
- Inaktivera UAC via ändring av [EnableLUA registernyckel](https://docs.microsoft.com/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec) är känt för att orsaka problem.
- Den här funktionen fungerar inte för nätverk med 802.1x nätverksautentisering distribueras och alternativet "Utför omedelbart före användarens inloggning". Nätverk med nätverksautentiseringen 802.1x distribuerad rekommenderas att använda datorautentisering för att aktivera funktionen.
- Hybrid Azure AD-anslutna datorer måste ha nätverksanslutningslinje till en domänkontrollant för att kunna använda det nya lösenordet och uppdatera cachelagrade autentiseringsuppgifter.
- Om du använder en avbildning, innan du kör sysprep, se till att webbcachen är rensad för den inbyggda administratören innan du utför CopyProfile-steget. Mer information om det här steget finns i supportartikeln [Prestandafattigt när du använder anpassad standardanvändarprofil](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile).
- Följande inställningar är kända för att störa möjligheten att använda och återställa lösenord på Windows 10-enheter
    - Om Ctrl+Alt+Del krävs av principen i versioner av Windows 10 före v1809 fungerar inte **återställningslösenordet.**
    - Om meddelanden för låsskärmen är avstängda fungerar inte **Återställ lösenord**.
    - HideFastUserSwitching har angetts på aktiverat eller 1
    - DontDisplayLastUserName har angetts på aktiverat eller 1
    - NoLockScreen har angetts på aktiverat eller 1
    - EnableLostMode har angetts på enheten
    - Explorer.exe har ersatts med ett anpassat gränssnitt
- Kombinationen av följande specifika tre inställningar kan leda till att den här funktionen inte fungerar.
    - Interaktiv inloggning: Kräver inte CTRL+ALT+DEL = Inaktiverad
    - InaktiveraLockScreenAppNotifications = 1 eller Aktiverad
    - Windows SKU är inte Hem eller Professional edition

## <a name="windows-10-password-reset"></a>Återställning av lösenord i Windows 10

### <a name="windows-10-prerequisites"></a>Förutsättningar för Windows 10

- En administratör måste aktivera Azure AD-självbetjäningslösenordsåterställning från Azure-portalen.
- **Användare måste registrera sig för SSPR innan de använder den här funktionen**
- Krav på nätverksproxy
   - Windows 10-enheter 
       - Port 443 `passwordreset.microsoftonline.com` till och`ajax.aspnetcdn.com`
       - Windows 10-enheter stöder endast proxykonfiguration på maskinnivå
- Kör minst Windows 10, version April 2018 Update (v1803), och enheterna måste vara antingen:
    - Azure AD-ansluten
    - Hybrid Azure AD-ansluten

### <a name="enable-for-windows-10-using-intune"></a>Aktivera för Windows 10 med Intune

Att distribuera konfigurationsändringen för att aktivera lösenordsåterställning från inloggningsskärmen med Intune är den mest flexibla metoden. Med Intune kan du distribuera konfigurationsändringen till en särskild grupp av datorer som du definierar. Den här metoden kräver Intune-registrering av enheten.

#### <a name="create-a-device-configuration-policy-in-intune"></a>Skapa en princip för enhetskonfiguration i Intune

1. Logga in på [Azure-portalen](https://portal.azure.com) och klicka på **Intune**.
1. Skapa en ny enhetskonfigurationsprofil genom att gå till Skapa profil för **enhetskonfigurationsprofiler** > **Profiles** > **Create Profile**
   - Ge profilen ett beskrivande namn
   - Du kan också ange en beskrivning av profilen
   - Plattform **Windows 10 och senare**
   - Profiltyp **Anpassad**
1. Konfigurera **inställningar**
   - **Lägg till** följande OMA-URI-inställning för att aktivera länken Återställ lösenord
      - Ange ett beskrivande namn som förklarar vad inställningen gör
      - Du kan också ange en beskrivning av inställningen
      - **OMA-URI** inställt på `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **Datatyp** inställt på **Integer**
      - **Värde** inställt på **1**
      - Klicka på **OK**
   - Klicka på **OK**
1. Klicka på **Skapa**
1. Den här principen kan tilldelas specifika användare, enheter eller grupper. Mer information finns i artikeln [Tilldela användar- och enhetsprofiler i Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign).

### <a name="enable-for-windows-10-using-the-registry"></a>Aktivera för Windows 10 med registret

1. Logga in på Windows-datorn med autentiseringsuppgifterna för administratören
1. Kör **regedit** som administratör
1. Ange följande registernyckel
   - `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      - `"AllowPasswordReset"=dword:00000001`

#### <a name="troubleshooting-windows-10-password-reset"></a>Felsöka lösenordsåterställning av Windows 10

Azure AD-granskningsloggen innehåller information om IP-adressen och klienttypen där lösenordsåterställningen har gjorts.

![Exempel på lösenordsåterställning i Windows 7 i Azure AD-granskningsloggen](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

När användare återställer sitt lösenord från inloggningsskärmen för en Windows `defaultuser1` 10-enhet skapas ett tillfälligt konto med låg behörighet som kallas. Det här kontot används för att skydda processen för lösenordsåterställning. Själva kontot har ett slumpmässigt genererat lösenord, visas inte för enhetsinloggning och tas automatiskt bort när användaren har återställt sitt lösenord. Flera `defaultuser` profiler kan finnas men kan ignoreras på ett säkert sätt.

## <a name="windows-7-8-and-81-password-reset"></a>Lösenordsåterställning av Windows 7, 8 och 8.1

### <a name="windows-7-8-and-81-prerequisites"></a>Förutsättningar för Windows 7, 8 och 8.1

- En administratör måste aktivera Azure AD-självbetjäningslösenordsåterställning från Azure-portalen.
- **Användare måste registrera sig för SSPR innan de använder den här funktionen**
- Krav på nätverksproxy
   - Windows 7-, 8- och 8.1-enheter
       - Port 443 till`passwordreset.microsoftonline.com`
- Lappat Operativsystem för Windows 7 eller Windows 8.1.
- TLS 1.2 aktiverat med hjälp av vägledningen i [registerinställningarna Transport Layer Security (TLS).](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12)
- Om mer än en autentiseringsleverantör från tredje part är aktiverad på din dator ser användarna mer än en användarprofil på inloggningsskärmen.

> [!WARNING]
> TLS 1.2 måste aktiveras, inte bara inställd på automatisk förhandling

### <a name="install"></a>Installera

1. Hämta rätt installationsprogram för den version av Windows som du vill aktivera.
   - Programvara finns på Microsofts nedladdningscenter på[https://aka.ms/sspraddin](https://aka.ms/sspraddin)
1. Logga in på datorn där du vill installera och kör installationsprogrammet.
1. Efter installationen rekommenderas en omstart.
1. Efter omstarten väljer du en användare på inloggningsskärmen och klickar på "Glömt lösenord?" för att initiera arbetsflödet för återställning av lösenord.
1. Slutför arbetsflödet genom att följa stegen på skärmen för att återställa lösenordet.

![Exempel på Windows 7 klickade på "Glömt lösenord?" SSPR-flöde](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>Tyst installation

- För tyst installation använder du kommandot "msiexec /i SsprWindowsLogon.PROD.msi /qn"
- För tyst avinstallation använder du kommandot "msiexec /x SsprWindowsLogon.PROD.msi /qn"

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>Felsöka lösenordsåterställning av Windows 7, 8 och 8.1

Händelser loggas både på datorn och i Azure AD. Azure AD-händelser innehåller information om IP-adressen och ClientType där återställningen av lösenordet inträffade.

![Exempel på lösenordsåterställning i Windows 7 i Azure AD-granskningsloggen](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

Om ytterligare loggning krävs kan en registernyckel på datorn ändras för att aktivera utförlig loggning. Aktivera detaljerad loggning endast för felsökning.

`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}`

- Om du vill aktivera utförlig loggning skapar du en `REG_DWORD: "EnableLogging"`och ställer in den på 1.
- Om du vill inaktivera utförlig loggning ändrar du `REG_DWORD: "EnableLogging"` till 0.

## <a name="what-do-users-see"></a>Vad ser användarna

Nu när du har konfigurerat återställning av lösenord för dina Windows-enheter, vilka ändringar för användaren? Hur vet de att de kan återställa sitt lösenord på inloggningsskärmen?

![Exempel på Windows 7 och 10 inloggningsskärmar med SSPR-länk visad](./media/howto-sspr-windows/windows-reset-password.png)

När användare försöker logga in ser de nu länken **Återställ lösenord** eller **Glömt lösenord** som öppnar självbetjäningsupplevelsen för återställning av lösenord på inloggningsskärmen. Via den här funktionen kan användarna återställa sina lösenord utan att de behöver använda en annan enhet för att få åtkomst till webbläsaren.

Dina användare får hjälp med att använda funktionen i [Reset your work or school password](../user-help/active-directory-passwords-update-your-own-password.md) (Återställa ditt arbets- eller skollösenord)

## <a name="next-steps"></a>Nästa steg

[Planera autentiseringsmetoder för att tillåta](concept-authentication-methods.md)

[Konfigurera Windows 10](https://docs.microsoft.com/windows/configuration/)
