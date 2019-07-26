---
title: Distribuera Azure-hanterade arbets stationer – Azure Active Directory
description: Lär dig hur du distribuerar säkra, Azure-hanterade arbets stationer för att minska risken för intrång på grund av felaktig konfiguration eller kompromisser.
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
ms.openlocfilehash: be9e6374d92fbb7bb1c4b5a2a9e154119c5baf87
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377490"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuera en säker, Azure-hanterad arbets Station

Nu när du [förstår säkra arbets stationer](concept-azure-managed-workstation.md)är det dags att påbörja distributions processen. Med den här vägledningen använder du definierade profiler för att skapa en arbets station som är säkrare från start.

![Distribution av en säker arbets Station](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Du måste välja en profil innan du kan distribuera lösningen. Du kan använda flera profiler samtidigt i en distribution och tilldela dem till taggar eller grupper.
> [!NOTE]
> Använd någon av profilerna efter behov. Du kan flytta till en annan profil genom att tilldela den i Intune.

| Profil | Låg | Optimerad | Hög | Specialiserade | Säkrad | Isolerad |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Användare i Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Intune-hanterad | Ja | Ja | Ja | Ja | Ja | Ja |
| Enhet – Azure AD har registrerats | Ja |  |  |  |  | |   |
| Enhet – Azure AD-ansluten |   | Ja | Ja | Ja | Ja | Ja |
| Säkerhets bas linje för Intune tillämpad |   | Ja <br> Förbättring | Ja <br> (HighSecurity) | Ja <br> (NCSC) | Ja <br> Ordentligt |  Ej tillämpligt |
| Maskin vara uppfyller säkra Windows 10-standarder |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP aktiverat |   | Ja  | Ja | Ja | Ja | Ja |
| Borttagning av administratörs behörighet |   |   | Ja  | Ja | Ja | Ja |
| Distribution med Microsoft autopilot |   |   | Ja  | Ja | Ja | Ja |
| Appar som installeras endast av Intune |   |   |   | Ja | Ja |Ja |
| URL: er begränsade till godkänd lista |   |   |   | Ja | Ja |Ja |
| Blockerad Internet (inkommande/utgående) |   |   |   |  |  |Ja |

## <a name="license-requirements"></a>Licenskrav

Begreppen som beskrivs i den här guiden förutsätter att du har Microsoft 365 Enterprise E5 eller motsvarande SKU. Några av rekommendationerna i den här hand boken kan implementeras med lägre SKU: er. Mer information finns i [Microsoft 365 Enterprise licensiering](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Om du vill automatisera licens etablering bör du överväga [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) för dina användare.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory konfiguration

Azure Active Directory (Azure AD) hanterar användare, grupper och enheter för administratörs arbets stationerna. Du måste aktivera identitets tjänster och funktioner med ett [administratörs konto](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

När du skapar det skyddade administratörs kontot för arbets stationen exponerar du kontot för din aktuella arbets Station. Se till att du använder en känd säker enhet för att utföra den här inledande konfigurationen och all global konfiguration. Överväg att följa [rikt linjerna för att förhindra infektion av skadlig kod](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)för att minska risken för angrepp vid första tiden.

Du bör också kräva Multi-Factor Authentication, minst för dina administratörer. Se [distribuera MOLNBASERAD MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) för implementerings vägledning.

### <a name="azure-ad-users-and-groups"></a>Azure AD-användare och-grupper

1. Från Azure Portal bläddrar du till **Azure Active Directory** > **användare** > **ny användare**.
1. Skapa din enhets administratör genom att följa stegen i [själv studie kursen skapa användare](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Går
   * **Namn** – säker arbets Stations administratör
   * **Användar namn** - `secure-ws-admin@identityitpro.com`
   * **Begränsad katalog Rolls** - **administratör** och välj rollen **Intune-administratör** .
1. Välj **Skapa**.

Därefter skapar du två grupper: arbets Stations användare och arbets Stations enheter.

Från Azure Portal bläddrar du till **Azure Active Directory** > **grupper** > **ny grupp**.

1. För gruppen användare av arbets stationer kan du vilja konfigurera [gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) för att automatisera etablering av licenser för användare.
1. För gruppen användare av arbets stationer anger du:
   * **Typ av grupp** – säkerhet
   * **Grupp namn** – säkra arbets Stations användare
   * **Medlemskaps typ** – tilldelad
1. Lägg till administratören för en säker arbets Stations administratör:`secure-ws-admin@identityitpro.com`
1. Du kan lägga till andra användare som ska hantera säkra arbets stationer.
1. Välj **Skapa**.

1. För arbets Stations enhets gruppen anger du:
   * **Typ av grupp** – säkerhet
   * **Grupp namn** – säkra arbets stationer
   * **Medlemskaps typ** – tilldelad
1. Välj **Skapa**.

### <a name="azure-ad-device-configuration"></a>Konfiguration av Azure AD-enhet

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Ange vem som kan ansluta enheter till Azure AD

Konfigurera inställningarna för enheter i Active Directory så att din administrativa säkerhets grupp kan ansluta enheter till din domän. Så här konfigurerar du den här inställningen från Azure Portal:

1. Gå till**enhets inställningarna**för **Azure Active Directory** > **enheter** > .
1. Välj **markerad** under **användare kan ansluta enheter till Azure AD**och välj sedan gruppen "skydda arbets Stations användare".

#### <a name="removal-of-local-admin-rights"></a>Borttagning av lokal administratörs behörighet

Den här metoden kräver att användare av de virtuella datorerna VIP, DevOps och Secure-Level inte har någon administratörs behörighet på sina datorer. Så här konfigurerar du den här inställningen från Azure Portal:

1. Gå till**enhets inställningarna**för **Azure Active Directory** > **enheter** > .
1. Välj **ingen** under **ytterligare lokala administratörer på Azure AD-anslutna enheter**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Kräv Multi-Factor Authentication för att ansluta enheter

För att ytterligare förstärka processen med att ansluta enheter till Azure AD:

1. Gå till**enhets inställningarna**för **Azure Active Directory** > **enheter** > .
1. Välj **Ja** under **Kräv Multi-factor auth för att ansluta enheter**.
1. Välj **Spara**.

#### <a name="configure-mdm"></a>Konfigurera MDM

Från Azure Portal:

1. Bläddra till **Azure Active Directory** > **Mobility (MDM och MAM)**  > **Microsoft Intune**.
1. Ändra inställningen för **användar omfång för MDM** till **alla**.
1. Välj **Spara**.

Med de här stegen kan du hantera alla enheter med Intune. Mer information finns i [Intune snabb start: Konfigurera automatisk registrering för Windows 10-enheter](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Du skapar Intune-konfiguration och efterlevnadsprinciper i ett kommande steg.

#### <a name="azure-ad-conditional-access"></a>Azure AD Conditional Access

Villkorlig åtkomst i Azure AD kan hjälpa till att begränsa privilegierade administrativa uppgifter till kompatibla enheter. Fördefinierade medlemmar i gruppen **säker arbets Station användare** krävs för att utföra Multi-Factor Authentication vid inloggning till moln program. Ett bra tips är att undanta åtkomst konton för nöd situationer från principen. Mer information finns i [Hantera åtkomst konton för nöd situationer i Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Så här konfigurerar du villkorlig åtkomst från Azure Portal:

1. Gå till **Azure Active Directory** > **villkorlig åtkomst** > **ny princip**.
1. Går
   * **Namn** – säker enhet som krävs för principen
   * Tilldelningar
     * **Användare och grupper**
       * Inkludera- **användare och grupper** – Välj gruppen **skydda arbets Stations användare** som skapades tidigare.
       * Exkludera – **användare och grupper** – välj din organisations konto för nöd åtkomst.
     * **Molnappar** – inkludera **alla molnappar**.
    * Åtkomstkontroller
      * **Grant** -Välj alternativ knappen **bevilja åtkomst** .
        * **Kräv Multi-Factor Authentication**.
        * **Kräv att enheten ska markeras som kompatibel**.
        * **Kräv alla markerade kontroller**för flera kontroller.
    * Aktivera princip – **på**.

Du har möjlighet att skapa principer som blockerar länder där användare inte kommer åt företagets resurser. Mer information om IP-plats-baserade principer för villkorlig åtkomst finns i [plats villkor i Azure Active Directory villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Intune-konfiguration

### <a name="configure-enrollment-status"></a>Konfigurera registrerings status

Det är viktigt att se till att din säkra arbets Station är en betrodd ren enhet. När du köper nya enheter kan du insistera att de har ställts in på [Windows 10 Pro i S-läge](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), vilket begränsar exponeringen för sårbarheter under hanteringen av leverans kedjan. När du har fått en enhet från leverantören kan du använda autopiloten för att ändra den från S-läge. Följande vägledning innehåller information om hur du använder omvandlings processen.

För att säkerställa att enheterna är fullständigt konfigurerade innan de används, kan Intune **blockera enhets användning tills alla appar och profiler är installerade**.

Från **Azure Portal**:
1. Gå till **Microsoft Intune** > **enhets registrering** > **Windows-registrering** > **Inställningar**för**registrerings status sidan** > **standardinställningar** > .
1. Ställ in **Visa installations förloppet för appens profil** till **Ja**.
1. Ange att **blockering av enhet ska användas tills alla appar och profiler har installerats** på **Ja**.

### <a name="create-an-autopilot-deployment-profile"></a>Skapa en distributions profil för autopilot

När du har skapat en enhets grupp måste du skapa en distributions profil för att konfigurera autopilot-enheterna.

I Intune i Azure Portal:

1. Välj **enhets registrering** > **Windows-registrering** > **profiler** > **Skapa profil**.
1. Går
   * Namn – **distributions profil för säker arbets Station**.
   * Beskrivning – **distribution av säkra arbets stationer**.
   * Ställ in **konvertera alla riktade enheter till autopilot** till **Ja**. Den här inställningen säkerställer att alla enheter i listan registreras med autopilot-distributions tjänsten. Tillåt 48 timmar för att registreringen ska bearbetas.
1. Välj **Nästa**.
   * För **distributions läge**väljer du **själv distribution (för hands version)** . Enheter med den här profilen är associerade med den användare som registrerar enheten. Användarautentiseringsuppgifter krävs för att registrera enheten. Det är viktigt att Observera att om du distribuerar en enhet i **själv distributions** läge kan du distribuera bärbara datorer i en delad modell. Ingen användar tilldelning sker förrän enheten tilldelas till en användare för första gången. Det innebär att alla användar principer som BitLocker inte aktive ras förrän en användar tilldelning har slutförts. Mer information om hur du loggar in på en säker enhet finns i [valda profiler](https://docs.microsoft.com/intune/device-profile-assign).
   * Rutan **Anslut till Azure AD as** ska visa **Azure AD-ansluten** och vara nedtonad.
   * Välj din Langugage (region), användar konto typ **standard**. 
1. Välj **Nästa**.
   * Välj en omfattnings tagg om du har förkonfigurerat en.
1. Välj **Nästa**.
1. Välj **tilldelningar** > **tilldela till** > **valda grupper**. I **Välj grupper att inkludera**väljer du **skydda arbets Stations användare**.
1. Välj **Nästa**.
1. Välj **Skapa** för att skapa profilen. Den autopilot-distributions profilen är nu tillgänglig för att tilldelas enheter.

Enhets registrering i autopilot ger en annan användar upplevelse baserat på enhets typ och roll. I vårt distributions exempel illustrerar vi en modell där de skyddade enheterna är Mass distribuerade och kan delas, men när den används för första gången är enheten tilldelad till en användare. Mer information finns i [Intune autopilot-enhets registrering](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurera Windows Update

Att hålla Windows 10 uppdaterat är ett av de viktigaste saker du kan göra. För att upprätthålla Windows i ett säkert tillstånd distribuerar du en uppdaterings ring för att hantera takten att uppdateringar tillämpas på arbets stationer. 

Den här vägledningen rekommenderar att du skapar en ny uppdaterings ring och ändrar följande standardinställningar:

På Azure Portal:

1. Gå till **Microsoft Intune** > **program uppdateringar** > **Windows 10 uppdaterings ringar**.
1. Går
   * Namn – **Azure Managed Stations uppdateringar**
   * Service kanal – **Windows Insider – fast**
   * Avstängning av kvalitets uppdatering (dagar)- **3**
   * Uppskjutande period för funktions uppdatering (dagar)- **3**
   * Beteende för automatisk uppdatering – **installera automatiskt och starta om utan slut användar kontroll**
   * Blockera användare från att pausa Windows-uppdateringar – **blockera**
   * Kräv att användarens godkännande ska starta om utanför arbets tid – **krävs**
   * Tillåt användaren att starta om (interaktiv omstart) – **krävs**
   * Överföra användare till interaktiv omstart efter en automatisk omstart (dagar)- **3**
   * Påminnelse om omstart av vilo läge (dagar)- **3**
   * Ange tids gräns för väntande omstarter (dagar)- **3**

1. Välj **Skapa**.
1. På fliken **tilldelningar** lägger du till gruppen **skyddade arbets stationer** .

Mer information om Windows Update-principer finns i [princip CSP-Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integrering

Windows Defender ATP och Microsoft Intune samar beta för att hjälpa till att förhindra säkerhets överträdelser. De kan också begränsa effekten av överträdelser. ATP-funktioner ger real tids identifiering av hot samt möjliggör omfattande granskning och loggning av slut punkts enheter.

Om du vill konfigurera integrering av Windows Defender ATP och Intune går du till Azure Portal.

1. Bläddra till **Microsoft Intune** > **enhetens kompatibilitet** > **Windows Defender ATP**.
1. I steg 1 under **Konfigurera Windows Defender ATP**väljer **du Anslut Windows defender ATP till Microsoft Intune i Windows Defender Security Center**.
1. I Windows Defender Security Center:
   1. Välj **Inställningar** > **avancerade funktioner**.
   1. För **Microsoft Intune anslutning**väljer du **på**.
   1. Välj **Spara inställningar**.
1. När en anslutning har upprättats går du tillbaka till Intune och väljer **Uppdatera** högst upp.
1. Ange **Anslut Windows-enheter version 10.0.15063 och högre till Windows Defender ATP** till **på**.
1. Välj **Spara**.

Mer information finns i [Windows Defender Avancerat skydd](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Slutför härdning av arbets Stations profil

Slutför lösningen genom att ladda ned och köra lämpligt skript. Hitta nedladdnings länkarna för önskad **profil nivå**:

| Profil | Hämtningsplats | Filnamn |
| --- | --- | --- |
| Låg säkerhet | Gäller inte |  Gäller inte |
| Förbättrad säkerhet | https://aka.ms/securedworkstationgit | Enhanced-Workstation-windows10-(1809). ps1 |
| Hög säkerhet  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specialiserade | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-windows10 (1803) SecurityBaseline. ps1 |
| Specialiserad kompatibilitet * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Säkrad | https://aka.ms/securedworkstationgit | Secure-Workstation-windows10-(1809)-SecurityBaseline. ps1 |

\*Specialiserad kompatibilitet är ett skript som tillämpar den specialiserade konfigurationen i NCSC windows10 SecurityBaseline.

När skriptet har körts kan du göra uppdateringar av profiler och principer i Intune. Skripten för förbättrade och säkra profiler skapar principer och profiler åt dig, men du måste tilldela principen till din **säkra arbets Stations** grupp.

* Här kan du hitta de enhets konfigurations profiler för Intune som skapats av skripten: **Azure Portal** **Microsoft Intune**enhetskonfigurations > profiler. >  > 
* Här kan du hitta efterlevnadsprinciper för Intune-enheter som skapats av skripten: **Azure Portal** **Microsoft Intune**enhetensefterlevnadsprinciper > . >  > 

Om du vill granska ändringar som gjorts av skripten kan du exportera profilerna. På så sätt kan du bestämma ytterligare härdningar som kan krävas enligt beskrivningen i SECCON- [dokumentationen](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Kör Intune-skriptet `DeviceConfiguration_Export.ps1` för data export från [DeviceConfiguration GiuHub-lagringsplatsen](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) för att exportera alla aktuella Intune-profiler.

## <a name="additional-configurations-and-hardening-to-consider"></a>Ytterligare konfigurationer och härdning att överväga

Genom att följa anvisningarna här har du distribuerat en säker arbets Station. Du bör dock även överväga ytterligare kontroller. Exempel:

* begränsa åtkomsten till alternativa webbläsare
* Tillåt utgående HTTP
* blockera utvalda webbplatser
* Ange behörigheter för att köra anpassade PowerShell-skript

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ange regler i providern för brand Väggs konfigurations tjänsten (CSP)

Du kan göra ytterligare ändringar i hanteringen av både inkommande och utgående regler efter behov för tillåtna och blockerade slut punkter. När du fortsätter att torka den säkra arbets stationen kan du lossa begränsningen som nekar all inkommande och utgående trafik. Du kan lägga till tillåtna utgående platser för att inkludera vanliga och betrodda webbplatser. Mer information finns i [brand Väggs konfigurations tjänsten](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Standard begränsade rekommendationer är:

* Neka alla inkommande
* Neka alla utgående

Spamhaus-projektet underhåller [listan över domän block (dubbelt)](https://www.spamhaus.org/dbl/): en lämplig resurs som används som utgångs punkt för att blockera platser.

### <a name="manage-local-applications"></a>Hantera lokala program

Den säkra arbets stationen flyttas till ett verkligt härdnings tillstånd när lokala program tas bort, inklusive produktivitets program. Här lägger du till Chrome som standard webbläsare och använder Intune för att begränsa en användares möjlighet att ändra webbläsaren och dess plugin-program.

#### <a name="deploy-applications-using-intune"></a>Distribuera program med Intune

I vissa situationer krävs program som en Google Chrome-webbläsare på den säkra arbets stationen. Följande exempel innehåller instruktioner för att installera Chrome på enheter i säkerhets gruppen **säkra arbets stationer**.

1. Ladda ned installations [paketet för offline installer för Windows 64-bitars](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahera filerna och Anteckna platsen för `GoogleChromeStandaloneEnterprise64.msi` filen.
1. I **Azure Portal** Bläddra till **Microsoft Intune** > **klient Apps** > **appar** > **Lägg till**.
1. Välj **verksamhets nivå**under **typ av app**.
1. Under **app Package-fil**väljer du `GoogleChromeStandaloneEnterprise64.msi` filen från den extraherade platsen och väljer **OK**.
1. Under **app-information**anger du en beskrivning och en utgivare. Välj **OK**.
1. Välj **Lägg till**.
1. På fliken **tilldelningar** väljer du **tillgänglig för registrerade enheter** under tilldelnings **typ**.
1. Lägg till gruppen **säkra arbets stationer** under **inkluderade grupper**.
1. Välj **OK**och välj sedan **Spara**.

Mer information om hur du konfigurerar Chrome-inställningar finns i [hantera Chrome-webbläsare med Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurera företags portalen för anpassade appar

I ett skyddat läge är programinstallationen begränsad till Intune företags Portal. Installation av portalen kräver dock åtkomst till Microsoft Store. I din säkrade lösning kan du göra företags portalen tillgänglig för alla enheter via ett offline-läge.

En Intune-hanterad kopia av [företagsportal](https://docs.microsoft.com/Intune/store-apps-company-portal-app) ger dig åtkomst på begäran till ytterligare verktyg som du kan skicka vidare till användare av de säkra arbets stationerna.

Du kan behöva installera Windows 32-bitars appar eller andra appar vars distribution kräver särskilda förberedelser. I sådana fall kan [förberedelse verktyget för Microsoft Win32-innehåll](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) tillhandahålla en format fil som är färdig `.intunewin` att använda för installation.

### <a name="use-powershell-to-create-custom-settings"></a>Använd PowerShell för att skapa anpassade inställningar

Du kan också använda PowerShell för att utöka värd hanterings funktionerna. Det här exempel skriptet konfigurerar en standard bakgrund på värden. Det är en funktion som också är tillgänglig via Azure Portal och profiler. Exempel skriptet fungerar bara för att illustrera PowerShell-funktionen.

Du kan behöva konfigurera vissa anpassade kontroller och inställningar på dina säkra arbets stationer. Det här exemplet ändrar arbets stationens bakgrund genom att använda PowerShell: s möjlighet att enkelt identifiera enheten som en färdig att använda, säker arbets Station.

Med skriptet [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) från Microsoft Scripting Center kan Windows läsa in den här [kostnads fria, allmänna](https://i.imgur.com/OAJ28zO.png) bakgrunds avbildningen vid start.

1. Ladda ned skriptet till en lokal enhet.
1. Uppdatera customerXXXX och nedladdnings platsen för bakgrunds bilden. I vårt exempel ersätter vi customerXXXX med bakgrunder.  
1. Bläddra till **Azure Portal** > **Microsoft Intune** > enhets konfiguration PowerShell-skript > Lägg till. > 
1. Ange ett **namn** på skriptet och ange sökvägen till **skriptet**.
1. Välj **Konfigurera**.
   1. Ange **Kör det här skriptet med de inloggade autentiseringsuppgifterna** till **Ja**.
   1. Välj **OK**.
1. Välj **Skapa**.
1. Välj **tilldelningar** > **Välj grupper**.
   1. Lägg till säkerhets gruppen **säkra arbets stationer**.
   1. Välj **Spara**.

## <a name="enroll-and-validate-your-first-device"></a>Registrera och verifiera din första enhet

1. För att registrera din enhet behöver du följande information:
   * **Serie nummer** – finns på enhetens chassi.
   * **Windows produkt-ID** – finns under **system** > **om** på menyn Windows-inställningar.
   * Du kan köra [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) för att hämta en CSV-hash-fil med all nödvändig information för enhets registrering.
   
     Kör `Get-WindowsAutoPilotInfo – outputfile device1.csv` för att spara informationen som en CSV-fil som du kan importera till Intune.

     > [!NOTE]
     > Skriptet kräver förhöjd behörighet. Den körs som fjärr signerad. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` Kommandot tillåter att skriptet körs på rätt sätt.

   * Du kan samla in den här informationen genom att logga in på en enhet med Windows 10 version 1809 eller senare. Din maskin varu åter försäljare kan också tillhandahålla den här informationen.
1. I **Azure Portal**går du till **Microsoft Intune** > **enhets registrering** > **Windows registrerings** > **enheter – hantera Windows autopilot-enheter**.
1. Välj **Importera** och välj CSV-fil.
1. Lägg till enheten i säkerhets gruppen **säker arbets Station** .
1. På den Windows 10-enhet som du vill konfigurera går du **till Windows-inställningar** > **uppdatering & säkerhets** > **återställning**.
   1. Välj **Kom igång** under **Återställ den här datorn**.
   1. Följ anvisningarna för att återställa och konfigurera om enheten med profil-och efterlevnadsprinciper konfigurerade.

När du har konfigurerat enheten slutför du en granskning och kontrollerar konfigurationen. Bekräfta att den första enheten är korrekt konfigurerad innan du fortsätter med distributionen.

## <a name="assign-and-monitor"></a>Tilldela och övervaka

Om du vill tilldela enheter och användare måste du mappa de [valda](https://docs.microsoft.com/intune/device-profile-assign) profilerna till din säkerhets grupp. Alla nya användare som behöver behörighet till tjänsten måste också läggas till i säkerhets gruppen.

Du kan övervaka profiler med [övervakning av Intune-profiler](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Förstå [Azure AD](https://docs.microsoft.com/azure/active-directory/index).
