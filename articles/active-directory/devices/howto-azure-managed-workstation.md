---
title: Distribuera Azure hanterade arbetsstationer - Azure Active Directory
description: Lär dig hur du distribuerar säkra Azure-hanterade arbetsstationer för att minska risken för intrång på grund av felaktig konfiguration eller kompromettering
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307234"
---
# <a name="deploy-a-secure-workstation"></a>Distribuera en säker arbetsstation

Nu när du förstår [varför det är viktigt att skydda arbetsstation åtkomst?](concept-azure-managed-workstation.md) är det dags att börja distributionen med hjälp av verktyg. Den här vägledningen använder de definierade profilerna för att skapa en arbetsstation som är säkrare från början.

![Distribution av en säker arbetsstation](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Den profil som du kommer att använda måste väljas innan du distribuerar lösningen. Det är viktigt att Observera att du kan använda någon av de valda profilerna och flytta till en annan genom att tilldela profil i Intune efter behov. Flera profiler kan användas samtidigt i en distribution och tilldela med hjälp av taggens eller grupp tilldelningar.

| Profil | Låg | Optimerad | Hög | Specialiserade | Skyddas | Isolerad |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Användare i Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Intune-hanterade | Ja | Ja | Ja | Ja | Ja | Ja |
| Azure AD-registrerade enheter | Ja |  |  |  |  | |   |
| Azure AD-anslutna enheter |   | Ja | Ja | Ja | Ja | Ja |
| Intune säkerhetsbaslinje tillämpas |   | Ja <br> (Utökat) | Ja <br> (HighSecurity) | Ja <br> (NCSC) | Ja <br> (Skyddad) |  Saknas |
| Maskinvaran uppfyller säker standarder för Windows 10 |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP-aktiverad |   | Ja  | Ja | Ja | Ja | Ja |
| Borttagning av administratörsrättigheter |   |   | Ja  | Ja | Ja | Ja |
| Distributionen med hjälp av Microsoft Autopilot |   |   | Ja  | Ja | Ja | Ja |
| Appar som installerats av Intune |   |   |   | Ja | Ja |Ja |
| URL: er som är begränsad till endast godkända lista |   |   |   | Ja | Ja |Ja |
| Internet (inkommande/utgående blockerad) |   |   |   |  |  |Ja |

## <a name="license-requirements"></a>Licenskrav

Begrepp i den här guiden förutsätter Microsoft 365 Enterprise E5 eller en motsvarande SKU. Några av rekommendationerna i den här guiden kan implementeras med lägre SKU: er. Mer information finns på [Microsoft 365 Enterprise licensiering](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Du kanske vill konfigurera [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) för dina användare att automatisera etableringen av licenser.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory-konfiguration

Konfigurera din Azure Active Directory (Azure AD)-katalog som ska hantera dina användare, grupper och enheter för din administratörsdatorerna kräver att du aktiverar identitetstjänster och funktioner med en [administratörskontot](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

När du skapar administratörskontot säker arbetsstation kan visar du kontot till din aktuella arbetsstation. Du bör du göra det här den initiala konfigurationen och alla globala konfigurationen från en känd säker enhet. Du kan tänka på för att [förhindra att infekteras av skadlig kod](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) att minska risken för exponering av först den första gång funktionen från angrepp.

Organisationer bör minst kräva multifaktorautentisering för administratören. Se [distribuera molnbaserad MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) för vägledning för implementering.

### <a name="azure-ad-users-and-groups"></a>Azure AD-användare och grupper

Från Azure-portalen bläddrar du till **Azure Active Directory** > **användare** > **ny användare**. [Skapa säker arbetsstation användaren](https://docs.microsoft.com/Intune/quickstart-create-user), som kommer att enhetsadministratören.

* **Namn på** – säker arbetsstation administratör
* **Användarnamn** - secure-ws-admin@identityitpro.com
* **Katalogroll** - **begränsad administratör** och välj följande administrativa roll
   * **Intune-administratör**
* **Skapa**

Vi ska skapa två grupper för användare arbetsstationer och för arbetsstationer själva. Från Azure-portalen bläddrar du till **Azure Active Directory** > **grupper** > **ny grupp**

Första gruppen för arbetsstationsanvändare. Du kanske vill konfigurera [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) för användare i den här gruppen för att automatisera etableringen av licenser till användare.

* **Grupptyp** -säkerhet
* **Gruppnamn** -användare för säkra arbetsstationer
* **Medlemstyp** – tilldelade
* Lägg till din säker arbetsstation administratörsanvändare i gruppen
   * secure-ws-admin@identityitpro.com
* Du kan lägga till andra användare som kommer att hantera säkra arbetsstationer i gruppen
* **Skapa**

Andra gruppen för arbetsstation enheter.

* **Grupptyp** -säkerhet
* **Gruppnamn** -säkra arbetsstationer
* **Medlemstyp** – tilldelade
* **Skapa**

### <a name="azure-ad-device-configuration"></a>Azure AD-enhetskonfiguration

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Ange vem som kan ansluta enheter till Azure AD

Konfigurera dina enheter ställer i Active Directory så att din administrativa säkerhetsgrupp ansluta enheter till din domän. För att konfigurera inställningen från Azure portal, bläddra till **Azure Active Directory** > **enheter** > **Enhetsinställningar**. Välj **valda** under **användare kan ansluta enheter till Azure AD** och välj ”säker arbetsstationsanvändare”-gruppen.

#### <a name="removal-of-local-admin-rights"></a>Borttagning av lokala administratörsrättigheter

Som en del av distributionen har arbetsstationer användare VIP, DevOps och säkra nivån arbetsstationer ingen behörighet som administratör på sina datorer. För att konfigurera inställningen från Azure portal, bläddra till **Azure Active Directory** > **enheter** > **Enhetsinställningar**. Välj **ingen** under **ytterligare lokala administratörer på Azure AD-anslutna enheter**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Kräv multifaktorautentisering ansluta enheter

För att ytterligare förbättra processen med att ansluta enheter till Azure AD, bläddra till **Azure Active Directory** > **enheter** > **Enhetsinställningar** Välj **Ja** under **kräver Multi-Factor Authentication för att ansluta enheter** därefter **spara**.

#### <a name="configure-mdm"></a>Konfigurera MDM

Från Azure-portalen bläddrar du till **Azure Active Directory** > **Mobility (MDM och MAM)**  > **Microsoft Intune**. Ändra inställningen **MDM-användaromfattning** till **alla** och välj **spara** som tillåter vi alla enheter som ska hanteras av Intune i det här scenariot. Mer information finns i artikeln [Intune Quickstart: Konfigurera automatisk registrering för Windows 10-enheter](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Vi skapar Intune-principer för konfiguration och efterlevnad i ett kommande steg.

#### <a name="azure-ad-conditional-access"></a>Azure AD villkorlig åtkomst

Azure AD villkorlig åtkomst kan du skydda dessa Privilegierade administrativa uppgifter på kompatibla enheter. Användare som vi har definierat som medlemmar i den **säker arbetsstationsanvändare** gruppen kommer att behöva utföra multifaktorautentisering vid inloggning till program i molnet. Vi följer bästa praxis riktlinjer och undanta vår konton för åtkomst vid akutfall från principen. Mer information finns i artikeln [hantera åtkomst vid akutfall i Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

För att konfigurera villkorlig åtkomst i Azure Portal, bläddra till **Azure Active Directory** > **villkorlig åtkomst** > **ny princip**.

* **Namn på** – säker enhet krävs för principen
* Tilldelningar
   * **Användare och grupper**
      * Inkludera - **användare och grupper** – Välj den **säker arbetsstationsanvändare** gruppen som skapades tidigare
      * Exkludera, **användare och grupper** – Välj din organisations konton för åtkomst vid akutfall
   * **Molnappar**
      * Inkludera - **alla molnappar**
* Åtkomstkontroller
   * **Bevilja** – Välj **bevilja åtkomst** alternativknapp
      * **Kräv multifaktorautentisering**
      * **Kräv att enheten är markerad som kompatibel**
      * För flera kontroller - **kräver de valda kontrollerna**
* Aktivera princip – **på**

Organisationer kan du skapa principer för att blockera länder/regioner där användare inte kan komma åt företagets resurser. Mer information om principer för IP-plats-baserad villkorlig åtkomst finns i artikeln [vad är platsvillkoret för villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Intune-konfiguration

### <a name="configure-enrollment-status"></a>Konfigurera registreringsstatus

Som beskrivs i hanteringen av leverantörskedja, se till att säker arbetsstation är en betrodd ren enhet rekommenderar vi som när du köper nya enheter som enheter vara factory inställd [Windows 10 Pro i S läge](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), vilket begränsar exponering och säkerhetsproblem vid hantering av leverantörskedja. När en enhet tas emot från leverantören, tas enheten bort från S läge med hjälp av Autopilot. Vägledningen innehåller information om hur du använder omvandling processen.

Vi vill säkerställa att enheter är helt konfigurerade före användning. Intune erbjuder ett sätt att **blockera enhetsanvändning tills alla appar och profiler är installerade**. 

1. Från den **Azure-portalen** navigerar du till:
1. **Microsoft Intune** > **enhetsregistrering** > **Windows-registrering** > **Registreringsstatussida (förhandsversion)**  >  **Standard** > **inställningar**.
1. Ange **visa Installationsförlopp för app-profil** till **Ja**.
1. Ange **blockera enhetsanvändning tills alla appar och profiler är installerade** till **Ja**.

### <a name="create-an-autopilot-deployment-profile"></a>Skapa en Autopilot-distributionsprofil

När du har skapat en enhetsgrupp, måste du skapa en distributionsprofil så att du kan konfigurera Autopilot-enheter.

1. I Intune på Azure-portalen, väljer **enhetsregistrering** > **Windows-registrering** > **Distributionsprofiler**  >   **Skapa profil**.
1. Namn, ange **säker arbetsstation distributionsprofil**. Beskrivning, ange **distribution av säkra arbetsstationer**.
1. Ange konvertera alla målriktade enheter till Autopilot på Ja. Den här inställningen ser till att alla enheter i listan över registrera dig med distributionen Autopilot-tjänsten.  Tillåt 48 timmar för att registreringen ska bearbetas.
1. Välj för distribution **lokal distribution (förhandsversion)** . Enheter med den här profilen är associerade med användaren registrerar enheten. Autentiseringsuppgifter krävs för att registrera enheten.
1. I kopplingen till Azure AD som rutan **Azure AD-anslutna** bör väljas och nedtonat.
1. Välj Out-of-box experience (OOBE), konfigurera följande alternativ och lämna andra inställt på standardvärdet och välj sedan **Ok**:
   1. Typ av användarkonto: **Standard**
1. Välj **Skapa** för att skapa profilen. Autopilot-distributionsprofilen är nu tillgängligt att tilldela till enheter.
1. Välj **tilldelningar** > **tilldelas** > **valda grupper**
   1. **Välj grupper att ta** -användare för säkra arbetsstationer

### <a name="configure-windows-update"></a>Konfigurera Windows Update

En av de viktigaste sakerna som en organisation kan göra är att hålla Windows 10 uppdaterade. För att upprätthålla Windows 10 i ett säkert tillstånd, distribuerar vi en uppdateringsring för att hantera den takt med vilken uppdateringar har tillämpats på arbetsstationer. Den här konfigurationen finns i den **Azure-portalen** > **Microsoft Intune** > **programuppdateringar**  >  **Windows 10-Uppdateringsringar**.

Vi kommer **skapa** en nya uppdateringsringen med följande inställningar har ändrats från standardvärdena.

* Namn – **Azure hanterade arbetsstation uppdateringar**
* Underhållskanal - **Windows Insider – snabb**
* Uppskjuten för uppdatering av kvalitet (dagar) - **3**
* Uppskjutningsperiod för funktionsuppdatering (dagar) - **3**
* Funktionssätt för automatisk uppdatering - **installera automatiskt och starta om utan användarkontroll**
* Blockera användare från pausa uppdateringar för Windows - **Block**
* Kräv användares godkännande för att starta om utanför arbetstid - **krävs**
* Tillåt användaren att starta om (engagerade omstart) - **krävs**
   * Övergång användare engagerade omstart efter en automatisk omstart (dagar) - **3**
   * Viloläge engagerade omstart påminnelse (dagar) - **3**
   * Ange tidsgräns för väntande startar om (dagar) - **3**

Klicka på **skapa** på den **tilldelningar** fliken Lägg till den **säkra arbetsstationer** som en inkluderad grupp.

Mer information om Windows Update-principer finns i [princip-CSP - uppdatering](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integrering

Windows Defender ATP- och Microsoft Intune fungerar tillsammans för att förhindra säkerhetsintrång och begränsa effekten av överträdelser. Funktionerna kan användas för identifiering i realtid. ATP ger även vår distribution omfattande granskning och loggning av slutpunkt-enheter.

För att konfigurera Intune för Windows Defender ATP-integrering i Azure portal, bläddra till **Microsoft Intune** > **enhetsefterlevnad** > **Windows Defender ATP** .

1. I steg 1 under **konfigurera Windows Defender ATP**, klickar du på **ansluta Windows Defender ATP i Microsoft Intune i Windows Defender Security Center**.
1. I Windows Defender Security Center:
   1. Välj **inställningar** > **avancerade funktioner**
   1. För **Microsoft Intune-anslutningen**, Välj **på**
   1. Välj **Spara inställningar**
1. När en anslutning har upprättats, gå tillbaka till Intune och klicka på **uppdatera** högst upp.
1. Ange **ansluta Windows enheter version 10.0.15063 och senare till Windows Defender ATP** till **på**.
1. **Spara**

Mer information finns i artikeln [Windows Defender Avancerat skydd](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Du har slutfört Härdning av profilen som arbetsstation

För att slutföra Härdning av lösningen, hämta och kör skriptet baserat på önskad **profilera nivå** från följande diagram.

| Profil | Hämtningsplats | Filename |
| --- | --- | --- |
| Med låg säkerhet | Gäller inte |  Gäller inte |
| Förbättrad säkerhet | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Hög säkerhet  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specialiserade | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Specialiserad efterlevnad * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Skyddas | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Specialiserad efterlevnad * är ett skript som tillämpar specialiserade konfigurationen i Windows 10-SecurityBaseline NCSC.

När det markerade skriptet har körs, kan uppdateringar av profilerna och principerna göras i Microsoft Intune. Skript för utökad och Secure profiler skapa principer och profiler för du men du måste tilldela principen till din **säkra arbetsstationer** grupp.

* Intune enhetskonfigurationsprofiler som skapats av skripten kan hittas i den **Azure-portalen** > **Microsoft Intune** > **enhetskonfiguration**  >  **Profiler**.
* Intunes enhetsefterlevnadsprinciper som skapats av skripten kan hittas i den **Azure-portalen** > **Microsoft Intune** > **Enhetsefterlevnad**  >  **Principer**.

Om du vill granska ändringarna kan du också exportera profiler, och när ändringarna skulle verkställas exportfilen som beskrivs i SECCON dokumentation baserat på och ytterligare härdning som krävs.

Kör Intune-data exportera skript `DeviceConfiguration_Export.ps1` från den [DeviceConfiguration GiuHub databasen](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) ger aktuella export av alla befintliga Intune-profiler.

## <a name="additional-configurations-and-hardening-to-consider"></a>Ytterligare konfigurationer och härdning att tänka på

Riktlinjerna har aktiverat en säker arbetsstation, ytterligare kontroller bör också övervägas, t.ex. alternativa webbläsare öppnar utgående HTTP tillåtna och blockerade webbplatser och möjligheten att köra anpassade PowerShell-skript.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Restriktiva inkommande och utgående regler i brandväggen configuration service provider (CSP)

Ytterligare hantering av både inkommande och utgående regler kan uppdateras för att återspegla din tillåtna och blockerade slutpunkter. När vi fortsätter att förstärka säker arbetsstation, vi flytta begränsningen till en neka alla inkommande och utgående som standard och Lägg till tillåtna platser för utgående för att återspegla vanliga och betrodda webbplatser. Ytterligare konfigurationsinformation för brandväggen configuration service provider finns i artikeln [brandväggen CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Är standard begränsad rekommendationer:

* Neka alla inkommande
* Neka alla utgående

Spamhaus-projektet har en bra lista som organisationer kan använda som en startpunkt för webbplatser som kallas [domän Block lista (dubbelt)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>Hantering av lokala program

Ta bort lokala program, flyttar inklusive borttagning av produktivitetsprogram säker arbetsstation till ett helt strikt läge. I vårt exempel ska vi lägga till Chrome som standardwebbläsare och begränsa möjligheten att ändra webbläsaren inklusive plugin-program med hjälp av Intune.

#### <a name="deploy-applications-using-intune"></a>Distribuera program med Intune

I vissa situationer kan krävs program som webbläsaren Google Chrome på säker arbetsstation. I följande exempel innehåller instruktioner för att installera Chrome till enheter i gruppen **säkra arbetsstationer** skapade tidigare.

1. Hämta offlineinstallationsprogrammet [Chrome-paket för Windows 64-bitars](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Extrahera filerna och Anteckna platsen för den `GoogleChromeStandaloneEnterprise64.msi` att installera med hjälp av Intune
1. I den **Azure-portalen** bläddrar du till **Microsoft Intune** > **klientappar** > **appar**  >  **Lägg till**
1. Under **apptyp**, Välj **Line-of-business**
1. Under **appaketfil**väljer den `GoogleChromeStandaloneEnterprise64.msi` i extraherade plats och klicka på **OK**
1. Under **appinformation**, ange en beskrivning och utgivare och välj **OK**
1. Klicka på **Lägg till**
1. På den **tilldelningar** Välj **tillgänglig för registrerade enheter** under **Tilldelningstyp**
1. Under **inkluderade grupper**, lägga till den **säkra arbetsstationer** gruppen som skapades tidigare
1. Klicka på **OK** sedan **spara**

Mer information om hur du konfigurerar inställningar för Chrome finns i deras support-artikeln [hantera webbläsaren Chrome med Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurera Företagsportalen för anpassade appar

I ett skyddat läge, ska installera program begränsas till Intune-Företagsportalen. Installerar portalen kräver dock åtkomst till Microsoft Store. I vår säker lösning, kommer att portalen tillgänglig för alla enheter med hjälp av en offline-läge för Företagsportalen.

Installera en Intune hanteras kopia av den [Företagsportalen](https://docs.microsoft.com/Intune/store-apps-company-portal-app) tillåter möjligheten att push-teknik ytterligare verktyg på begäran för användare av de säkra arbetsstationerna.

Vissa organisationer kan krävas för att installera Windows 32-bitars appar eller appar som kräver andra förberedelser för att distribuera. För dessa program i [Microsoft content Förberedelseverktyg för win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) ger en klar att använda `.intunewin` formatfilen för installation.

### <a name="setting-up-custom-settings-using-powershell"></a>Konfigurera anpassade inställningar med hjälp av PowerShell

Vi använder också ett exempel på använder PowerShell för att tillhandahålla utökningsbarhet i hanteringen av värden. Skriptet konfigurerar en standardbakgrund på värden. Den här funktionen är också tillgängligt i profiler och används bara för att illustrera kapaciteten.

I lösningen kan det finnas ett behov av att ställa in vissa anpassade kontroller och inställningar på säkra arbetsstationer. I vårt exempel ändrar vi bakgrunden på arbetsstationen med hjälp av Powershell för att kunna enkelt identifiera enheten som en säker arbetsstation som är redo att användas. Även om vårt exempel använder PowerShell för att genomföra den här uppgiften, kan den också utföras i Azure-portalen.

Vårt exempel använder följande [kostnadsfri allmän bakgrundsbilden](https://i.imgur.com/OAJ28zO.png) och [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) från Microsoft Scripting Center för att tillåta Windows att läsa in bakgrunden på start.

1. Ladda ned skriptet till en lokal enhet
1. Uppdatera customerXXXX och hämtningsplatsen för bakgrunden som du vill använda i skriptet för att återspegla bakgrundsfilen och mappen som du vill att distributionen ska använda. I vårt exempel ersätter vi customerXXXX till bakgrunder.  
1. Bläddra till den **Azure-portalen** > **Microsoft Intune** > **enhetskonfiguration** > **PowerShell skript** > **Lägg till**
1. Ange en **namn** för skriptet och ange den **skriptplats** där du har hämtat den till
1. Välj **konfigurera**
   1. Ange **köra detta skript med de inloggade autentiseringsuppgifter**till **Ja**
   1. Klicka på **OK**
1. Klicka på **Skapa**
1. Välj **tilldelningar** > **Välj grupper**
   1. Lägger till säkerhetsgruppen **säkra arbetsstationer** skapade tidigare och klicka på **spara**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>Med hjälp av förhandsversionen: MDM-Säkerhetsbaslinje för oktober 2018

Microsoft Intune har infört baslinje management säkerhetsfunktion ger administratörer ett enkelt sätt att tillämpa en gemensam baslinje säkerhetsposition. Baslinjen ger ett liknande sätt att uppnå en låst nedåt utökad profil arbetsstation.

Implementering av denna baslinje inte används som den är i konflikt med konfigureringen av säker distribution för en säker arbetsstation.

|   |   |   |
| :---: | :---: | :---: |
| Låst | Installation av enheten | Fjärrskrivbordstjänster |
| App-Runtime | Enhetslås | Fjärrhantering |
| Programhantering | Händelseloggtjänsten | Fjärrproceduranrop |
| Spela upp automatiskt | Upplevelse | Search |
| BitLocker | Exploit Guard | Smart skärm |
| Webbläsare | Utforskaren | Systemkrav|
| Anslutning | Internet Explorer | Wi-Fi |
| Delegering av autentiseringsuppgifter | Säkerhetsalternativ för lokala principer | Windows Anslutningshanteraren |
| Autentiseringsuppgifter för Användargränssnittet | Säkerhetsguiden för MS | Windows Defender|
| Dataskydd | MSS Legacy | Windows Ink Workspace |
| Enhetsskydd | Power | Windows PowerShell |

Mer information om den här förhandsgranskningsfunktionen finns i artikeln [Windows baslinje säkerhetsinställningar för Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Registrera och verifiera din första enhet

1. Om du vill registrera din enhet, behöver du följande information:
   * **Serienummer** – hittades på enheten chassi
   * **Windows produkt-ID** – hittades under **System** > **om** på menyn Windows-inställningar.
   * Kör [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) ger en CSV-fil för hash för registrering av enheter med all nödvändig information. 
      * Kör `Get-WindowsAutoPilotInfo – outputfile device1.csv` att mata ut information som en CSV-fil som kan importeras till Intune.

   > [!NOTE]
   > Skriptet kräver förhöjd behörighet och kör via fjärranslutning som signerats. Du kan använda följande kommando för att skriptet körs på rätt sätt. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Du kan samla in den här informationen genom att logga in till en version av Windows 10 1809 eller senare enhet för att samla in information, eller din återförsäljare för maskinvara kan ge den här informationen när du beställer nya enheter.
1. Samla in informationen som krävs och återgå till den **Azure-portalen**. Gå till **Microsoft Intune** > **enhetsregistrering** > **Windows-registrering** > **enheter – Hantera Windows Autopilot-enheter**väljer **Import**, och välj den CSV-fil som du har skapat eller har angetts.
1. Lägg till den nu registrerade enheten till i säkerhetsgruppen **säkra arbetsstationer** skapade tidigare.
1. Från Windows 10-enhet som du vill konfigurera, bläddra till **Windows-inställningar** > **uppdatering och säkerhet** > **Recovery**. Välj **börjar** under **återställa den här datorn** och följ anvisningarna för att återställa och konfigurera om den enhet som använder profilen och efterlevnad principerna som konfigureras.

När enheten har konfigurerats, utför en granskning och kontrollera konfigurationen. Bekräfta att den första enheten har konfigurerats korrekt innan du fortsätter din distribution.

## <a name="assignment-and-monitoring"></a>Tilldelning och övervakning

Tilldela enheter och användare kräver mappningen av den [valt profiler](https://docs.microsoft.com/intune/device-profile-assign) till din säkerhet i gruppen och alla nya användare som kommer att få behörighet att tjänsten kommer att behöva läggas till i säkerhetsgruppen.

Övervaka profiler till kan göras med hjälp av övervakning [Microsoft Intune-profiler](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Nästa steg

[Microsoft Intune](https://docs.microsoft.com/intune/index) dokumentation

[Azure AD](https://docs.microsoft.com/azure/active-directory/index) dokumentation