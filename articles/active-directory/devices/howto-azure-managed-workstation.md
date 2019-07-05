---
title: Distribuera Azure-hanterade arbetsstationer - Azure Active Directory
description: Lär dig mer om att distribuera säkra, Azure-hanterade arbetsstationer för att minska risken för intrång på grund av felaktig konfiguration eller kompromisser.
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
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550491"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuera en säker, Azure-hanterade arbetsstation

Du [förstå säkra arbetsstationer](concept-azure-managed-workstation.md), är det dags att starta processen för distribution. Med den här guiden ska använda du definierade profiler för att skapa en arbetsstation som är säkrare från början.

![Distribution av en säker arbetsstation](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Du måste välja en profil innan du kan distribuera lösningen. Du kan använda flera profiler samtidigt i en distribution och tilldela dem med taggar eller grupper.
> [!NOTE]
> Använda ett profilerna vid behov genom att dina behov. Du kan flytta till en annan profil genom att tilldela den i Intune.

| Profil | Låg | Optimerad | Hög | Specialiserade | Skyddas | Isolerad |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Användare i Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Intune-hanterade | Ja | Ja | Ja | Ja | Ja | Ja |
| Enhet – Azure AD-registrerad | Ja |  |  |  |  | |   |
| Enhet – Azure AD-ansluten |   | Ja | Ja | Ja | Ja | Ja |
| Intune säkerhetsbaslinje tillämpas |   | Ja <br> (Utökat) | Ja <br> (HighSecurity) | Ja <br> (NCSC) | Ja <br> (Skyddad) |  Ej tillämpligt |
| Maskinvaran uppfyller säker standarder för Windows 10 |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP-aktiverad |   | Ja  | Ja | Ja | Ja | Ja |
| Borttagning av administratörsrättigheter |   |   | Ja  | Ja | Ja | Ja |
| Distributionen med hjälp av Microsoft Autopilot |   |   | Ja  | Ja | Ja | Ja |
| Appar som installerats av Intune |   |   |   | Ja | Ja |Ja |
| URL: er som är begränsad till lista godkända |   |   |   | Ja | Ja |Ja |
| Internet blockeras (inkommande/utgående) |   |   |   |  |  |Ja |

## <a name="license-requirements"></a>Licenskrav

Begrepp i den här guiden förutsätter att du har Microsoft 365 Enterprise E5 eller en motsvarande SKU. Några av rekommendationerna i den här guiden kan implementeras med lägre SKU: er. Mer information finns i [Microsoft 365 Enterprise licensiering](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Om du vill automatisera etablering av licens kan du överväga att [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) för dina användare.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory-konfiguration

Azure Active Directory (Azure AD) hanterar användare, grupper och enheter för din administratörsdatorerna. Du måste aktivera identitetstjänster och funktioner med en [administratörskontot](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

När du skapar administratörskontot säker arbetsstation exponera kontot till din aktuella arbetsstation. Kontrollera att du använder en känd säker enhet för att utföra den här inledande konfiguration och alla global konfiguration. Om du vill minska exponeringen för attacker för första gången upplevelse, Överväg följande den [vägledning för att förhindra att infekteras av skadlig kod](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Du bör även kräva multifaktorautentisering, minst för administratörerna. Se [distribuera molnbaserad MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) för vägledning för implementering.

### <a name="azure-ad-users-and-groups"></a>Azure AD-användare och grupper

1. Från Azure-portalen bläddrar du till **Azure Active Directory** > **användare** > **ny användare**.
1. Skapa din enhetsadministratören genom att följa stegen i den [skapa användaren självstudien](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Ange:
   * **Namn på** – säker arbetsstation administratör
   * **Användarnamn** - `secure-ws-admin@identityitpro.com`
   * **Katalogroll** - **begränsad administratör** och välj den **Intune-administratör** roll.
1. Välj **Skapa**.

Därefter skapar du två grupper: arbetsstationsanvändare och enheter för arbetsstationen.

Från Azure-portalen bläddrar du till **Azure Active Directory** > **grupper** > **ny grupp**.

1. För gruppen användare arbetsstation, kanske du vill konfigurera [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) att automatisera etableringen av licenser till användare.
1. För gruppen arbetsstation användare, anger du:
   * **Grupptyp** -säkerhet
   * **Gruppnamn** -användare för säkra arbetsstationer
   * **Medlemstyp** – tilldelade
1. Lägg till säker arbetsstation-administratör: `secure-ws-admin@identityitpro.com`
1. Du kan lägga till andra användare som kommer att hantera säkra arbetsstationer.
1. Välj **Skapa**.

1. Ange för gruppen arbetsstation enheter:
   * **Grupptyp** -säkerhet
   * **Gruppnamn** -säkra arbetsstationer
   * **Medlemstyp** – tilldelade
1. Välj **Skapa**.

### <a name="azure-ad-device-configuration"></a>Azure AD-enhetskonfiguration

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Ange vem som kan ansluta enheter till Azure AD

Konfigurera dina enheter ställer i Active Directory så att din administrativa säkerhetsgrupp ansluta enheter till din domän. Konfigurera inställningen från Azure portal:

1. Gå till **Azure Active Directory** > **enheter** > **Enhetsinställningar**.
1. Välj **valda** under **användare kan ansluta enheter till Azure AD**, och välj sedan ”säker arbetsstationsanvändare”-gruppen.

#### <a name="removal-of-local-admin-rights"></a>Borttagning av lokala administratörsrättigheter

Den här metoden kräver att användarna VIP, DevOps och skydda nivå arbetsstationer har ingen behörighet som administratör på sina datorer. Konfigurera inställningen från Azure portal:

1. Gå till **Azure Active Directory** > **enheter** > **Enhetsinställningar**.
1. Välj **ingen** under **ytterligare lokala administratörer på Azure AD-anslutna enheter**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Kräv multifaktorautentisering ansluta enheter

Att ytterligare stärka processen med att ansluta enheter till Azure AD:

1. Gå till **Azure Active Directory** > **enheter** > **Enhetsinställningar**.
1. Välj **Ja** under **kräver Multi-Factor Authentication för att ansluta enheter**.
1. Välj **Spara**.

#### <a name="configure-mdm"></a>Konfigurera MDM

Från Azure-portalen:

1. Bläddra till **Azure Active Directory** > **Mobility (MDM och MAM)**  > **Microsoft Intune**.
1. Ändra den **MDM-användaromfattning** att ställa in **alla**.
1. Välj **Spara**.

De här stegen kan du hantera alla enheter med Intune. Mer information finns i [Intune Quickstart: Konfigurera automatisk registrering för Windows 10-enheter](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Du skapar Intune-principer för konfiguration och efterlevnad i ett kommande steg.

#### <a name="azure-ad-conditional-access"></a>Azure AD Conditional Access

Azure AD villkorlig åtkomst kan hjälpa att begränsa Privilegierade administrativa uppgifter till kompatibla enheter. Fördefinierade medlemmar i den **säker arbetsstationsanvändare** grupp krävs för att utföra multifaktorautentisering vid inloggning till program i molnet. Ett bra tips är att undanta för åtkomst vid akutfall från principen. Mer information finns i [hantera åtkomst vid akutfall i Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Konfigurera villkorlig åtkomst i Azure Portal:

1. Gå till **Azure Active Directory** > **villkorlig åtkomst** > **ny princip**.
1. Ange:
   * **Namn på** – säker enhet krävs för principen
   * Tilldelningar
     * **Användare och grupper**
       * Inkludera - **användare och grupper** – Välj den **säker arbetsstationsanvändare** gruppen som skapades tidigare.
       * Exkludera, **användare och grupper** – Välj din organisations åtkomst vid akutfall konton.
     * **Molnappar** -inkludera **alla molnappar**.
    * Åtkomstkontroller
      * **Bevilja** – Välj **bevilja åtkomst** alternativknappen.
        * **Kräv multifaktorautentisering**.
        * **Kräv att enheten är markerad som kompatibel**.
        * För flera kontroller - **kräver de valda kontrollerna**.
    * Aktivera princip – **på**.

Du har möjlighet att skapa principer som blockerar länder/regioner där användare inte kan komma åt företagets resurser. Mer information om principer för IP-plats-baserad villkorlig åtkomst finns i [platsvillkoret i Azure Active Directory villkorsstyrd åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Intune-konfiguration

### <a name="configure-enrollment-status"></a>Konfigurera registreringsstatus

Det är viktigt att säkerställa att din säker arbetsstation är en betrodd ren enhet. När du köper nya enheter, kan du se till att de är factory inställt [Windows 10 Pro i S läge](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), vilket begränsar exponeringen för säkerhetsproblem vid hantering av leverantörskedja. Du kan använda Autopilot för att ändra den från S läge när du har fått en enhet från leverantören. Vägledningen innehåller information om hur du använder omvandling processen.

För att säkerställa att enheter är helt konfigurerade före användning, Intune erbjuder ett sätt att **blockera enhetsanvändning tills alla appar och profiler är installerade**.

Från den **Azure-portalen**:
1. Gå till **Microsoft Intune** > **enhetsregistrering** > **Windows-registrering** > **registreringsstatus Sidan** > **standard** > **inställningar**.
1. Ange **visa Installationsförlopp för app-profil** till **Ja**.
1. Ange **blockera enhetsanvändning tills alla appar och profiler är installerade** till **Ja**.

### <a name="create-an-autopilot-deployment-profile"></a>Skapa en Autopilot-distributionsprofil

När du har skapat en enhetsgrupp, måste du skapa en distributionsprofil för att konfigurera Autopilot-enheter.

I Intune i Azure portal:

1. Välj **enhetsregistrering** > **Windows-registrering** > **Distributionsprofiler** > **skapa profil** .
1. Ange:
   * Namn – **säker arbetsstation distributionsprofil**.
   * Beskrivning – **distribution av säkra arbetsstationer**.
   * Ange **konvertera alla målriktade enheter att Autopilot** till **Ja**. Den här inställningen ser till att alla enheter i listan över registrera dig med distributionen Autopilot-tjänsten. Tillåt 48 timmar för att registreringen ska bearbetas.
1. Välj **Nästa**.
   * För **distributionsläget**, Välj **lokal distribution (förhandsversion)** . Enheter med den här profilen är kopplad till den användare som registrerar enheten. Autentiseringsuppgifter krävs för att registrera enheten.
   * Den **Anslut till Azure AD som** box ska visa **Azure AD-anslutna** och är nedtonade.
   * Välj din Langugage (Region), användarkontotyp **standard**. 
1. Välj **Nästa**.
   * Välj en omfångstagg om du har förkonfigurerat något.
1. Välj **Nästa**.
1. Välj **tilldelningar** > **tilldelas** > **valda grupper**. I **Välj grupper att ta**, Välj **säker arbetsstationsanvändare**.
1. Välj **Nästa**.
1. Välj **Skapa** för att skapa profilen. Autopilot-distributionsprofilen är nu tillgängligt att tilldela till enheter.

### <a name="configure-windows-update"></a>Konfigurera Windows Update

Uppdatera Windows 10 kontinuerligt är en av de viktigaste sakerna som du kan göra. Om du vill underhålla Windows i ett säkert tillstånd kan du distribuera en uppdateringsring för att hantera takt att uppdateringar har tillämpats på arbetsstationer. 

Den här vägledningen rekommenderar att du skapar en ny uppdateringsring och ändra följande standardinställningar:

På Azure Portal:

1. Gå till **Microsoft Intune** > **programuppdateringar** > **Windows 10-Uppdateringsringar**.
1. Ange:
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

1. Välj **Skapa**.
1. På den **tilldelningar** fliken, lägga till den **säkra arbetsstationer** grupp.

Ytterligare information om Windows Update-principer finns i [princip-CSP - uppdatering](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integrering

Windows Defender ATP- och Microsoft Intune fungerar tillsammans för att förhindra säkerhetsintrång. De kan också begränsa effekten av överträdelser. ATP funktioner ger hotidentifiering i realtid samt aktivera omfattande granskning och loggning av slutpunkt-enheter.

Om du vill konfigurera integreringen av Windows Defender ATP- och Intune går du till Azure-portalen.

1. Bläddra till **Microsoft Intune** > **enhetsefterlevnad** > **Windows Defender ATP**.
1. I steg 1 under **konfigurera Windows Defender ATP**väljer **ansluta Windows Defender ATP i Microsoft Intune i Windows Defender Security Center**.
1. I Windows Defender Security Center:
   1. Välj **inställningar** > **avancerade funktioner**.
   1. För **Microsoft Intune-anslutningen**, Välj **på**.
   1. Välj **Spara inställningar**.
1. När en anslutning har upprättats, gå tillbaka till Intune och välj **uppdatera** högst upp.
1. Ange **ansluta Windows enheter version 10.0.15063 och senare till Windows Defender ATP** till **på**.
1. Välj **Spara**.

Mer information finns i [Windows Defender Avancerat skydd](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Slut arbetsstation profil appstyrning

För att slutföra Härdning av lösningen, hämta och kör det aktuella skriptet. Hitta länkar för nedladdning för din önskade **profilera nivå**:

| Profil | Hämtningsplats | Filename |
| --- | --- | --- |
| Med låg säkerhet | Gäller inte |  Gäller inte |
| Förbättrad säkerhet | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Hög säkerhet  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specialiserade | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Specialiserad efterlevnad * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Skyddas | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Specialiserad efterlevnad är ett skript som tillämpar specialiserade konfigurationen i Windows 10-SecurityBaseline NCSC.

När skriptet har körs, kan du göra uppdateringar profiler och-principer i Intune. Skript för utökad och Secure profiler skapa principer och profiler för dig, men du måste tilldela principen till din **säkra arbetsstationer** grupp.

* Här är där du kan hitta Intune-enhetens konfigurationsprofiler skapats av skripten: **Azure-portalen** > **Microsoft Intune** > **enhetskonfiguration** > **profiler**.
* Här är där du kan hitta intunes principer för enhetsefterlevnad som skapats av skripten: **Azure-portalen** > **Microsoft Intune** > **Enhetsefterlevnad** > **principer**.

Du kan exportera profilerna för att granska ändringar som gjorts av skripten. På så sätt kan du fastställa ytterligare härdning som kan krävas som beskrivs i dokumentationen för SECCON.

Kör skriptet Intune data export `DeviceConfiguration_Export.ps1` från den [DeviceConfiguration GiuHub databasen](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) att exportera alla aktuella Intune-profiler.

## <a name="additional-configurations-and-hardening-to-consider"></a>Ytterligare konfigurationer och härdning att tänka på

Du har distribuerat en säker arbetsstation genom att följa riktlinjerna här. Men bör även du överväga ytterligare kontroller. Exempel:

* begränsa åtkomsten till alternativa webbläsare
* Tillåt utgående HTTP
* blockera väljer webbplatser
* Ange behörigheter för att köra anpassade PowerShell-skript

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ange regler i brandväggen configuration service provider (CSP)

Du kan göra ytterligare ändringar till hantering av både inkommande och utgående regler som behövs för dina tillåtna och blockerade slutpunkter. När du fortsätter att kunna härda säker arbetsstation lossa du den begränsning som nekar all inkommande och utgående trafik. Du kan lägga till tillåtna utgående platser om du vill inkludera vanliga och betrodda webbplatser. Mer information finns i [brandväggen konfigurationstjänsten](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Är standard begränsad rekommendationer:

* Neka alla inkommande
* Neka alla utgående

Projektet Spamhaus underhåller [domän Block lista (dubbelt)](https://www.spamhaus.org/dbl/): en bra resurs som ska användas som utgångspunkt för webbplatser.

### <a name="manage-local-applications"></a>Hantera lokala program

Säker arbetsstation flyttas till ett helt strikt läge när lokala program tas bort, inklusive produktivitetsprogram. Här kan du lägga till Chrome som standardwebbläsare och använda Intune för att begränsa en användares möjlighet att ändra webbläsaren och dess plugin-program.

#### <a name="deploy-applications-using-intune"></a>Distribuera program med Intune

I vissa situationer kan krävs program som webbläsaren Google Chrome på säker arbetsstation. I följande exempel innehåller instruktioner för att installera Chrome till enheter i gruppen **säkra arbetsstationer**.

1. Hämta offlineinstallationsprogrammet [Chrome-paket för Windows 64-bitars](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahera filerna och Anteckna platsen för den `GoogleChromeStandaloneEnterprise64.msi` filen.
1. I den **Azure-portalen** bläddrar du till **Microsoft Intune** > **klientappar** > **appar**  >  **Lägga till**.
1. Under **apptyp**, Välj **Line-of-business**.
1. Under **appaketfil**väljer den `GoogleChromeStandaloneEnterprise64.msi` från extraherade plats och väljer **OK**.
1. Under **appinformation**, ange en beskrivning och en utgivare. Välj **OK**.
1. Välj **Lägg till**.
1. På den **tilldelningar** fliken **tillgänglig för registrerade enheter** under **Tilldelningstyp**.
1. Under **inkluderade grupper**, lägga till den **säkra arbetsstationer** grupp.
1. Välj **OK**, och välj sedan **spara**.

Mer information om hur du konfigurerar inställningar för Chrome Se [hantera webbläsaren Chrome med Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurera Företagsportalen för anpassade appar

Programinstallationen är begränsad till Intune-Företagsportalen i ett skyddat läge. Installerar portalen kräver dock åtkomst till Microsoft Store. I din säker lösning du Företagsportalen tillgänglig för alla enheter via en offline-läge.

En Intune-hanterade kopia av den [Företagsportalen](https://docs.microsoft.com/Intune/store-apps-company-portal-app) ger åtkomst på begäran till ytterligare verktyg som du kan push-teknik för användare av de säkra arbetsstationerna.

Du kan behöva installera Windows 32-bitars appar eller andra appar vars distribution måste ha speciella förberedelser. I sådana fall kan den [Microsoft content Förberedelseverktyg för win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) kan ge en klar att använda `.intunewin` formatfilen för installation.

### <a name="use-powershell-to-create-custom-settings"></a>Använd PowerShell för att skapa anpassade inställningar

Du kan också använda PowerShell för att utöka värd hanteringsfunktioner. Det här exempelskriptet konfigurerar en standardbakgrund på värden. Det är en funktion som är också tillgänglig via Azure-portalen och profiler. Exempelskriptet fungerar endast för att illustrera PowerShell-funktioner.

Du kan behöva ställa in vissa anpassade kontroller och inställningar på din säkra arbetsstationer. Det här exemplet ändrar bakgrunden på arbetsstationen med hjälp av PowerShells möjlighet att enkelt identifiera enheten som är klara att använda och säker arbetsstation.

Den [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) skriptet från Microsoft Scripting Center tillåter Windows att läsa in detta [kostnadsfria, allmän bakgrundsbilden](https://i.imgur.com/OAJ28zO.png) på Starta.

1. Ladda ned skriptet till en lokal enhet.
1. Uppdatera customerXXXX och hämtningsplatsen för bakgrundsbilden. I vårt exempel ersätter vi customerXXXX till bakgrunder.  
1. Bläddra till den **Azure-portalen** > **Microsoft Intune** > **enhetskonfiguration** > **PowerShell skript** > **Lägg till**.
1. Ange en **namn** för skriptet och ange den **skriptplats**.
1. Välj **Konfigurera**.
   1. Ange **köra detta skript med de inloggade autentiseringsuppgifter** till **Ja**.
   1. Välj **OK**.
1. Välj **Skapa**.
1. Välj **tilldelningar** > **Välj grupper**.
   1. Lägger till säkerhetsgruppen **säkra arbetsstationer**.
   1. Välj **Spara**.

## <a name="enroll-and-validate-your-first-device"></a>Registrera och verifiera din första enhet

1. Om du vill registrera din enhet, behöver du följande information:
   * **Serienummer** – finns under chassi för enheten.
   * **Windows produkt-ID** – hittades under **System** > **om** på menyn Windows-inställningar.
   * Du kan köra [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) att hämta en CSV-hash-fil med all information som krävs för registrering av enheter.
   
     Kör `Get-WindowsAutoPilotInfo – outputfile device1.csv` att mata ut information som en CSV-fil som du kan importera i Intune.

     > [!NOTE]
     > Skriptet kräver förhöjd behörighet. Den körs som fjärranslutna signerade. Den `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` kommandot tillåter skriptet kan köras korrekt.

   * Du kan samla in den här informationen genom att logga in till en version av Windows 10 1809 eller senare enhet. Maskinvara-återförsäljare kan också ange den här informationen.
1. I den **Azure-portalen**går du till **Microsoft Intune** > **enhetsregistrering** > **Windows-registrering**  >  **Enheter – hantera Windows Autopilot-enheter**.
1. Välj **Import** och välj din CSV-fil.
1. Lägger till enheten de **säkra arbetsstationer** säkerhetsgrupp.
1. På Windows 10-enhet som du vill konfigurera, går du till **Windows-inställningar** > **uppdatering och säkerhet** > **Recovery**.
   1. Välj **börjar** under **återställa den här datorn**.
   1. Följ anvisningarna för att återställa och konfigurera om enheten med profilen och efterlevnad principerna som konfigureras.

När du har konfigurerat enheten, utför en granskning och kontrollera konfigurationen. Bekräfta att den första enheten är korrekt konfigurerad innan du fortsätter din distribution.

## <a name="assign-and-monitor"></a>Tilldela och övervaka

Om du vill tilldela enheter och användare, måste du koppla den [valt profiler](https://docs.microsoft.com/intune/device-profile-assign) till din säkerhetsgrupp. Alla nya användare som behöver behörigheter till tjänsten måste läggas till i säkerhetsgruppen.

Du kan övervaka profiler med [övervakning av Intune-profil](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Förstå [Azure AD](https://docs.microsoft.com/azure/active-directory/index).
