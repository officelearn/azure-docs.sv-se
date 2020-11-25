---
title: Distribuera Azure-hanterade arbets stationer – Azure Active Directory
description: Lär dig hur du distribuerar säkra, Azure-hanterade arbets stationer för att minska risken för intrång på grund av felaktig konfiguration eller kompromisser.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56cd23494f65b1c74e44868496855c6e4a32bf7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95974093"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuera en säker, Azure-hanterad arbets Station

Nu när du [förstår säkra arbets stationer](concept-azure-managed-workstation.md)är det dags att påbörja distributions processen. Med den här vägledningen använder du definierade profiler för att skapa en arbets station som är säkrare från start.

![Distribution av en säker arbets Station](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Välj en profil innan du distribuerar lösningen. Du kan använda flera profiler samtidigt i en distribution och tilldela dem till taggar eller grupper.

> [!NOTE]
> Använd någon av profilerna efter behov. Du kan flytta till en annan profil genom att tilldela den i Microsoft Intune.

| Profil | Låg | Optimerad | Hög | Specialiserade | Skyddad | Isolerad |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Användare i Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Intune-hanterad | Ja | Ja | Ja | Ja | Ja | Ja |
| Enhet – Azure AD har registrerats | Yes |  |  |  |  | |   |
| Enhet – Azure AD-ansluten |   | Ja | Ja | Ja | Ja | Ja |
| Säkerhets bas linje för Intune tillämpad |   | Yes <br> Förbättring | Yes <br> (HighSecurity) | Yes <br> (NCSC) | Yes <br> Ordentligt | NA |
| Maskin vara uppfyller säkra Windows 10-standarder |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP aktiverat |   | Ja  | Ja | Ja | Ja | Ja |
| Borttagning av administratörs behörighet |   |   | Ja  | Ja | Ja | Ja |
| Distribution med Microsoft autopilot |   |   | Ja  | Ja | Ja | Ja |
| Appar som installeras endast av Intune |   |   |   | Ja | Ja |Ja |
| URL: er begränsade till godkänd lista |   |   |   | Ja | Ja |Ja |
| Blockerad Internet (inkommande/utgående) |   |   |   |  |  |Yes |

> [!NOTE]
> I guiderna för säker arbets Station **kommer att tilldelas** profiler och principer. Användarna har inte tillämpat principerna direkt, vilket gör att enhets delning (delade enheter) börjar gälla. Om en säker arbets Station inte delas i din distribution, eller om enskilda användar principer krävs, kan tilldelning av användar princip profiler tilldelas till användaren och enheten. 

## <a name="license-requirements"></a>Licenskrav

Begreppen som beskrivs i den här guiden förutsätter att du har Microsoft 365 Enterprise E5 eller motsvarande SKU. Några av rekommendationerna i den här hand boken kan implementeras med lägre SKU: er. Mer information finns i [Microsoft 365 Enterprise licensiering](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Om du vill automatisera licens etablering bör du överväga [gruppbaserad licensiering](../enterprise-users/licensing-groups-assign.md) för dina användare.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory konfiguration

Azure Active Directory (Azure AD) hanterar användare, grupper och enheter för administratörs arbets stationerna. Aktivera identitets tjänster och funktioner med ett [administratörs konto](../roles/permissions-reference.md).

När du skapar det skyddade administratörs kontot för arbets stationen exponerar du kontot för din aktuella arbets Station. Se till att du använder en känd säker enhet för att utföra den här inledande konfigurationen och all global konfiguration. Överväg att följa [rikt linjerna för att förhindra infektion av skadlig kod](/windows/security/threat-protection/intelligence/prevent-malware-infection)för att minska risken för angrepp vid första tiden.

Kräv Multi-Factor Authentication, minst för dina administratörer. Se [distribuera MOLNBASERAD MFA](../authentication/howto-mfa-getstarted.md) för implementerings vägledning.

### <a name="azure-ad-users-and-groups"></a>Azure AD-användare och -grupper

1. Från Azure Portal bläddrar du till **Azure Active Directory**  >  **användare**  >  **ny användare**.
1. Skapa din enhets administratör genom att följa stegen i [själv studie kursen skapa användare](/Intune/quickstart-create-user).
1. Ange:

   * **Namn** – säker arbets Stations administratör
   * **Användar namn** - `secure-ws-admin@identityitpro.com`
   * **Katalog roll**  -  **Begränsad administratör** och välj rollen **Intune-administratör** .

1. Välj **Skapa**.

Därefter skapar du två grupper: arbets Stations användare och arbets Stations enheter.

Från Azure Portal bläddrar du till **Azure Active Directory**  >  **grupper**  >  **ny grupp**.

1. För gruppen användare av arbets stationer kan du vilja konfigurera [gruppbaserad licensiering](../enterprise-users/licensing-groups-assign.md) för att automatisera etablering av licenser för användare.
1. För gruppen användare av arbets stationer anger du:

   * **Typ av grupp** – säkerhet
   * **Grupp namn** – säkra arbets Stations användare
   * **Medlemskaps typ** – tilldelad

1. Lägg till administratören för en säker arbets Stations administratör: `secure-ws-admin@identityitpro.com`
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

1. Gå till **Azure Active Directory** > **Enheter** > **Enhetsinställningar**.
1. Välj **markerad** under **användare kan ansluta enheter till Azure AD** och välj sedan gruppen "skydda arbets Stations användare".

#### <a name="removal-of-local-admin-rights"></a>Borttagning av lokal administratörs behörighet

Den här metoden kräver att användare av de virtuella datorerna VIP, DevOps och Secure-Level inte har någon administratörs behörighet på sina datorer. Så här konfigurerar du den här inställningen från Azure Portal:

1. Gå till **Azure Active Directory** > **Enheter** > **Enhetsinställningar**.
1. Välj **ingen** under **ytterligare lokala administratörer på Azure AD-anslutna enheter**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Kräv Multi-Factor Authentication för att ansluta enheter

För att ytterligare förstärka processen med att ansluta enheter till Azure AD:

1. Gå till **Azure Active Directory** > **Enheter** > **Enhetsinställningar**.
1. Välj **Ja** under **Kräv Multi-factor auth för att ansluta enheter**.
1. Välj **Spara**.

#### <a name="configure-mobile-device-management"></a>Konfigurera hantering av mobila enheter

Från Azure-portalen:

1. Bläddra till **Azure Active Directory**  >  **Mobility (MDM och MAM)**  >  **Microsoft Intune**.
1. Ändra inställningen för **användar omfång för MDM** till **alla**.
1. Välj **Spara**.

Med de här stegen kan du hantera alla enheter med Intune. Mer information finns i [Intune snabb start: Konfigurera automatisk registrering för Windows 10-enheter](/Intune/quickstart-setup-auto-enrollment). Du skapar Intune-konfiguration och efterlevnadsprinciper i ett kommande steg.

#### <a name="azure-ad-conditional-access"></a>Villkorsstyrd åtkomst i Azure AD

Villkorlig åtkomst i Azure AD kan hjälpa till att begränsa privilegierade administrativa uppgifter till kompatibla enheter. Fördefinierade medlemmar i gruppen **säker arbets Station användare** krävs för att utföra Multi-Factor Authentication vid inloggning till moln program. Ett bra tips är att undanta åtkomst konton för nöd situationer från principen. Mer information finns i [Hantera åtkomst konton för nöd situationer i Azure AD](../roles/security-emergency-access.md).

## <a name="intune-configuration"></a>Intune-konfiguration

### <a name="configure-enrollment-status"></a>Konfigurera registrerings status

Det är viktigt att se till att din säkra arbets Station är en betrodd ren enhet. När du köper nya enheter kan du insistera att de har ställts in på [Windows 10 Pro i S-läge](/Windows/deployment/Windows-10-pro-in-s-mode), vilket begränsar exponeringen för sårbarheter under hanteringen av leverans kedjan. När du har fått en enhet från leverantören kan du använda autopiloten för att ändra den från S-läge. Följande vägledning innehåller information om hur du använder omvandlings processen.

För att säkerställa att enheterna är fullständigt konfigurerade innan de används, kan Intune **blockera enhets användning tills alla appar och profiler är installerade**.

Från **Azure Portal**:

1. Gå till **Microsoft Intune**  >  **enhets registrering**  >  **Windows-registrering**  >  Inställningar för **registrerings status sidan**  >  **standardinställningar**  >  **Settings**.
1. Ställ in **Visa installations förloppet för appens profil** till **Ja**.
1. Ange att **blockering av enhet ska användas tills alla appar och profiler har installerats** på **Ja**.

### <a name="create-an-autopilot-deployment-profile"></a>Skapa en Autopilot-distributionsprofil

När du har skapat en enhets grupp måste du skapa en distributions profil för att konfigurera autopilot-enheterna.

I Intune i Azure Portal:

1. Välj **enhets registrering**  >  **Windows-registrering**  >  **profiler**  >  **Skapa profil**.
1. Ange:

   * Namn – **distributions profil för säker arbets Station**.
   * Beskrivning – **distribution av säkra arbets stationer**.
   * Ange **Omvandla alla målenheter till Autopilot** som **Ja**. Den här inställningen ser till att alla enheter i listan blir registrerade med Autopilots distributionstjänst. Det kan ta upp till 48 timmar för registreringen att bearbetas.

1. Välj **Nästa**.

   * För **distributions läge** väljer du **själv distribution (för hands version)**. Enheter med den här profilen är associerade med den användare som registrerar enheten. Autentiseringsuppgifter krävs för att registrera enheten. Det är viktigt att Observera att om du distribuerar en enhet i **själv distributions** läge kan du distribuera bärbara datorer i en delad modell. Ingen användar tilldelning sker förrän enheten tilldelas till en användare för första gången. Det innebär att alla användar principer som BitLocker inte aktive ras förrän en användar tilldelning har slutförts. Mer information om hur du loggar in på en säker enhet finns i [valda profiler](/intune/device-profile-assign).
   * Rutan **Anslut till Azure AD as** ska visa **Azure AD-ansluten** och vara nedtonad.
   * Välj ditt språk (region), användar konto typ **standard**. 

1. Välj **Nästa**.

   * Välj en omfattnings tagg om du har förkonfigurerat en.

1. Välj **Nästa**.
1. Välj **tilldelningar**  >  **tilldela till**  >  **valda grupper**. I **Välj grupper att inkludera** väljer du **säkra arbets stationer**.
1. Välj **Nästa**.
1. Välj **Skapa** för att skapa profilen. Nu kan Autopilot-distributionsprofilen tilldelas till enheter.

Enhets registrering i autopilot ger en annan användar upplevelse baserat på enhets typ och roll. I vårt distributions exempel illustrerar vi en modell där de skyddade enheterna är Mass distribuerade och kan delas, men när den används för första gången är enheten tilldelad till en användare. Mer information finns i [Intune autopilot-enhets registrering](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurera Windows Update

Att hålla Windows 10 uppdaterat är ett av de viktigaste saker du kan göra. För att upprätthålla Windows i ett säkert tillstånd distribuerar du en uppdaterings ring för att hantera takten att uppdateringar tillämpas på arbets stationer. 

Den här vägledningen rekommenderar att du skapar en ny uppdaterings ring och ändrar följande standardinställningar:

I Azure-portalen:

1. Gå till **Microsoft Intune**  >  **program uppdateringar**  >  **Windows 10 uppdaterings ringar**.
1. Ange:

   * Namn – **Azure-hanterade arbets Stations uppdateringar**
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

Mer information om Windows Update-principer finns i [princip CSP-Update](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integrering

Windows Defender ATP och Microsoft Intune samar beta för att hjälpa till att förhindra säkerhets överträdelser. De kan också begränsa effekten av överträdelser. ATP-funktioner ger real tids identifiering av hot samt möjliggör omfattande granskning och loggning av slut punkts enheter.

Om du vill konfigurera integrering av Windows Defender ATP och Intune går du till Azure Portal.

1. Bläddra till **Microsoft Intune**  >  **enhetens kompatibilitet**  >  **Windows Defender ATP**.
1. I steg 1 under **Konfigurera Windows Defender ATP** väljer **du Anslut Windows defender ATP till Microsoft Intune i Windows Defender Security Center**.
1. I Windows Defender Säkerhetscenter:

   1. Välj **Inställningar** > **Avancerade funktioner**.
   1. För **Microsoft Intune anslutning** väljer du **på**.
   1. Välj **Spara inställningar**.

1. När en anslutning har upprättats går du tillbaka till Intune och väljer **Uppdatera** högst upp.
1. Ändra **Anslut Windows-enheter version 10.0.15063 och senare till Windows Defender ATP9** till **På**.
1. Välj **Spara**.

Mer information finns i [Windows Defender Avancerat skydd](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Slutför härdning av arbets Stations profil

Slutför lösningen genom att ladda ned och köra lämpligt skript. Hitta nedladdnings länkarna för önskad **profil nivå**:

| Profil | Hämtnings plats | Sökväg |
| --- | --- | --- |
| Låg säkerhet | Saknas | Saknas |
| Förbättrad säkerhet | https://aka.ms/securedworkstationgit | Enhanced-Workstation-windows10-(1809). ps1 |
| Hög säkerhet | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-windows10-(1809). ps1 |
| Specialiserade | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-windows10 (1803) SecurityBaseline.ps1 |
| Specialiserad kompatibilitet * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-windows10 (1803). ps1 |
| Skyddad | https://aka.ms/securedworkstationgit | Secure-Workstation-windows10-(1809) -SecurityBaseline.ps1 |

\* Specialiserad kompatibilitet är ett skript som tillämpar den specialiserade konfigurationen i NCSC windows10 SecurityBaseline.

När skriptet har körts kan du göra uppdateringar av profiler och principer i Intune. Skripten för förbättrade och säkra profiler skapar principer och profiler åt dig, men du måste tilldela en princip till enhets gruppen **säker arbets Station** .

* Här hittar du de Intune-enhetens konfigurations profiler som skapas av skripten: **Azure Portal**  >  **Microsoft Intune**  >  **enhets konfigurations**  >  **profiler**.
* Här kan du hitta efterlevnadsprinciper för Intune-enheter som skapats av skripten: **Azure Portal**  >  **Microsoft Intune**  >  **efterlevnadsprinciper för enheter**  >  **Policies**.

Om du vill granska ändringar som gjorts av skripten kan du exportera profilerna. På så sätt kan du bestämma ytterligare härdningar som kan krävas enligt beskrivningen i SECCON- [dokumentationen](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Kör Intune-skriptet för data export `DeviceConfiguration_Export.ps1` från [DeviceConfiguration GiuHub-lagringsplatsen](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) för att exportera alla aktuella Intune-profiler.

## <a name="additional-configurations-and-hardening-to-consider"></a>Ytterligare konfigurationer och härdning att överväga

Genom att följa anvisningarna här har du distribuerat en säker arbets Station. Du bör dock även överväga ytterligare kontroller. Exempel:

* begränsa åtkomsten till alternativa webbläsare
* Tillåt utgående HTTP
* blockera utvalda webbplatser
* Ange behörigheter för att köra anpassade PowerShell-skript

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ange regler i providern för brand Väggs konfigurations tjänsten (CSP)

Du kan göra ytterligare ändringar i hanteringen av både inkommande och utgående regler efter behov för tillåtna och blockerade slut punkter. När du fortsätter att torka den säkra arbets stationen kan du lossa begränsningen som nekar all inkommande och utgående trafik. Du kan lägga till tillåtna utgående platser för att inkludera vanliga och betrodda webbplatser. Mer information finns i [brand Väggs konfigurations tjänsten](/Windows/client-management/mdm/firewall-csp).

Begränsad URL för trafik hantering omfattar:

* Neka all inkommande trafik som angetts i skriptet för den säkra arbets Stations profilen.
* Neka alla utgående utom valda Azure-och Microsoft-tjänster, inklusive Azure Cloud Shell och möjligheten att tillåta återställning av Azure-lösenord.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Om du vill ge mer detaljerad information om dina spärrnings regler kan du referera till det spamhaus-projekt som upprätthåller [listan över domän block (dubbelt)](https://www.spamhaus.org/dbl/): en bra resurs som ska användas som en avancerad uppsättning regler som ska implementeras för att blockera platser.

### <a name="manage-local-applications"></a>Hantera lokala program

Den säkra arbets stationen flyttas till ett verkligt härdnings tillstånd när lokala program tas bort, inklusive produktivitets program. Här lägger du till Chrome som standard webbläsare och använder Intune för att begränsa en användares möjlighet att ändra webbläsaren och dess plugin-program.

#### <a name="deploy-applications-using-intune"></a>Distribuera program med Intune

I vissa situationer krävs program som en Google Chrome-webbläsare på den säkra arbets stationen. Följande exempel innehåller instruktioner för att installera Chrome på enheter i säkerhets gruppen **säkra arbets stationer**.

1. Ladda ned installations [paketet för offline installer för Windows 64-bitars](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahera filerna och Anteckna platsen för `GoogleChromeStandaloneEnterprise64.msi` filen.
1. I **Azure Portal** Bläddra till **Microsoft Intune**  >  **klient Apps**  >  **appar**  >  **Lägg till**.
1. Välj **verksamhets nivå** under **typ av app**.
1. Under **app Package-fil** väljer du `GoogleChromeStandaloneEnterprise64.msi` filen från den extraherade platsen och väljer **OK**.
1. Under **app-information** anger du en beskrivning och en utgivare. Välj **OK**.
1. Välj **Lägg till**.
1. På fliken **tilldelningar** väljer du **tillgänglig för registrerade enheter** under **tilldelnings typ**.
1. Lägg till gruppen **säkra arbets stationer** under **inkluderade grupper**.
1. Välj **OK** och välj sedan **Spara**.

Mer information om hur du konfigurerar Chrome-inställningar finns i [hantera Chrome-webbläsare med Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurera företags portalen för anpassade appar

I ett skyddat läge är programinstallationen begränsad till Intune företags Portal. Installation av portalen kräver dock åtkomst till Microsoft Store. I din säkrade lösning kan du göra företags portalen tillgänglig för alla enheter via ett offline-läge.

En Intune-hanterad kopia av [företagsportal](/Intune/store-apps-company-portal-app) ger dig åtkomst på begäran till ytterligare verktyg som du kan skicka vidare till användare av de säkra arbets stationerna.

Du kan behöva installera Windows 32-bitars appar eller andra appar vars distribution kräver särskilda förberedelser. I sådana fall kan [förberedelse verktyget för Microsoft Win32-innehåll](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) tillhandahålla en format fil som är färdig att använda `.intunewin` för installation.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Villkorlig åtkomst tillåter endast säker arbets Stations möjlighet att komma åt Azure Portal

Azure AD ger möjlighet att hantera och begränsa, vem och vad som har åtkomst till din Azure Cloud Management-Portal. Genom att aktivera [villkorlig åtkomst](../conditional-access/overview.md) garanterar du att endast din säkra arbets Station kan hantera eller ändra resurser. Det är viktigt att när du distribuerar den här funktionen bör du överväga om funktionen för [nödfalls åtkomst](../roles/security-emergency-access.md) kan eller bara ska användas för extrema fall och kontot som hanteras via principen.

> [!NOTE]
> Du måste skapa en användar grupp och ta med din nödfalls användare som kan kringgå principen för villkorlig åtkomst. I vårt exempel har vi en säkerhets grupp med namnet **Emergency BreakGlass**

1. Bläddra till den **Azure Portal**  >  **Microsoft Intune**  >  **villkorliga åtkomst principer**  >  **ny princip**.
1. Ange ett **namn** för principen.
1. Välj användare och **grupper**  >  **Välj användare och grupper** 
1. Välj **Inkludera**  >  **katalog roller** > Välj roller > global administratör, privilegie rad roll administratör, privilegie rad autentisering administratör, säkerhets administratör, efterlevnadsprincip administratör, villkorlig åtkomst administratör, program administratör, moln program administratör, Intune-tjänsteadministratör
1. Välj **exkludera** > Välj **användare och grupper** > Välj **Välj exkluderade användare** > välj din **BreakGlass** -grupp.
1. Välj **molnappar eller åtgärder** > Välj **alla molnappar**
1. Välj **villkor** > Välj **enhets plattformar** > Välj Konfigurera **Ja** > Välj **Välj enhets plattform** Välj **Windows**
1. Välj **åtkomst kontroller** > Välj **bevilja åtkomst** **Ja** > Välj **Kräv att enheten ska markeras som kompatibel**. 
1. Välj **Aktivera princip**  >  **på**
 
Den här princip uppsättningen säkerställer att administratörerna måste använda en kompatibel Windows-enhet, som anges av Intune och WDATP. 

Organisationer bör också överväga att blockera bakåtkompatibla autentiseringsprotokoll i sina miljöer. Det finns flera sätt att utföra den här uppgiften. mer information om hur du blockerar äldre autentiseringsprotokoll finns i artikeln [så här: blockera äldre autentisering till Azure AD med villkorlig åtkomst](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Använd PowerShell för att skapa anpassade inställningar

Du kan också använda PowerShell för att utöka värd hanterings funktionerna. Det här exempel skriptet konfigurerar en standard bakgrund på värden. Det är en funktion som också är tillgänglig via Azure Portal och profiler. Exempel skriptet fungerar bara för att illustrera PowerShell-funktionen.

Du kan behöva konfigurera vissa anpassade kontroller och inställningar på dina säkra arbets stationer. Det här exemplet ändrar arbets stationens bakgrund genom att använda PowerShell: s möjlighet att enkelt identifiera enheten som en färdig att använda, säker arbets Station.

Med [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) -skriptet från Microsoft Scripting Center kan Windows läsa in den här [kostnads fria, allmänna bakgrunds avbildningen](https://i.imgur.com/OAJ28zO.png) vid start.

1. Ladda ned skriptet till en lokal enhet.
1. Uppdatera customerXXXX och nedladdnings platsen för bakgrunds bilden. I vårt exempel ersätter vi customerXXXX med bakgrunder.
1. Bläddra till **Azure Portal**  >  **Microsoft Intune**  >  **enhets konfiguration**  >  **PowerShell-skript**  >  **Lägg till**.
1. Ange ett **namn** på skriptet och ange sökvägen till **skriptet**.
1. Välj **Konfigurera**.
   1. Ange **Kör det här skriptet med de inloggade autentiseringsuppgifterna** till **Ja**.
   1. Välj **OK**.
1. Välj **Skapa**.
1. Välj **tilldelningar**  >  **Välj grupper**.
   1. Lägg till säkerhets gruppen **säkra arbets stationer**.
   1. Välj **Spara**.

## <a name="enroll-and-validate-your-first-device"></a>Registrera och verifiera din första enhet

1. För att registrera din enhet behöver du följande information:
   * **Serie nummer** – finns på enhetens chassi.
   * **Windows produkt-ID** – finns under **system**  >  **om** på menyn Windows-inställningar.
   * Du kan köra [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) för att hämta en CSV-hash-fil med all nödvändig information för enhets registrering.
   
     Kör `Get-WindowsAutoPilotInfo – outputfile device1.csv` för att spara informationen som en CSV-fil som du kan importera till Intune.

     > [!NOTE]
     > Skriptet kräver förhöjd behörighet. Den körs som fjärr signerad. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`Kommandot tillåter att skriptet körs på rätt sätt.

   * Du kan samla in den här informationen genom att logga in på en enhet med Windows 10 version 1809 eller senare. Din maskin varu åter försäljare kan också tillhandahålla den här informationen.
1. I **Azure Portal** går du till **Microsoft Intune**  >  **enhets registrering**  >  **Windows registrerings**  >  **enheter – hantera Windows autopilot-enheter**.
1. Välj **Importera** och välj CSV-fil.
1. Lägg till enheten i säkerhets gruppen **säker arbets Station** .
1. På den Windows 10-enhet som du vill konfigurera går du till **Windows-inställningar**  >  **uppdatering & säkerhets**  >  **återställning**.
   1. Välj **Kom igång** under **Återställ den här datorn**.
   1. Följ anvisningarna för att återställa och konfigurera om enheten med profil-och efterlevnadsprinciper konfigurerade.

När du har konfigurerat enheten slutför du en granskning och kontrollerar konfigurationen. Bekräfta att den första enheten är korrekt konfigurerad innan du fortsätter med distributionen.

## <a name="assign-devices"></a>Tilldela enheter

Om du vill tilldela enheter och användare måste du mappa de [valda profilerna](/intune/device-profile-assign) till din säkerhets grupp. Alla nya användare som behöver behörighet till tjänsten måste också läggas till i säkerhets gruppen.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Använda Sentinel och Windows Defender ATP för att övervaka och svara på säkerhets incidenter

Övervakning av en säker arbets Stations distribution kan åstadkommas genom att aktivera [Sentinel] och utnyttja [hot-och sårbarhets hantering](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) vägledningen ger ingen uttömmande hot jakt, men ger bra vanliga ansträngningar för att övervaka och svara på potentiella säkerhets incidenter.

Vi kommer att använda **Azure Sentinel** för att: 

* Samla in data från Intune, Azure Portal och Azure AD för användar-och enhets övervakning
* Hjälp till att undersöka misstänkt aktivitet för användar-och enhets konfiguration
* Och driva möjlighet att utforska säkerhets undersökningar med WDATP

Kontroll övervakning kräver att anslutningar till dina data källor, till exempel Azure AD konfigureras.

1. I **Azure Portal** går du till **Azure Sentinel (för hands version)** > Välj **Lägg till**
1. I fönstret **Välj en arbets yta att lägga till i Azure Sentinel väljer du** **skapa en ny arbets yta**
1. Ange:
   * **Log Analytics arbets yta** -"säker arbets Stations övervakning"
   * **Prenumeration** – välj din aktiva prenumeration
   * **Resurs grupp** – Välj * * Skapa ny * * > säker arbets station RG > **OK**
   * **Plats** – Välj den plats som är geografiskt anpassad för distributionen
   * **Pris nivå** – Välj **Per GB (2018)**
1. Välj **OK**.

Nu ska vi ansluta Tillgängliga data källor för säker arbets station till övervakningen.

1. I **Azure Portal** går du till **Azure Sentinel-arbetsytan** > Välj arbets yta för **säker arbets Stations övervakning**
1. Välj **data kopplingar**
1. Välj **Azure Active Directory** > öppna kopplings sidan > efter att ha granskat förutsättningen. Fortsätt till konfigurationen och välj **Anslut** för både inloggnings loggar för Azure AD, samt gransknings loggar för Azure AD.
1. Välj **Azure Activity** > sidan öppna koppling > efter att ha granskat förutsättningen. Fortsätt med att konfigurera Azure-aktivitets loggar > välj din prenumeration > Välj **Anslut**

När data samlas in av Sentinel kommer du att kunna se aktivitet genom att välja **Azure Portal**, gå till **Översikt över Azure Sentinel** 

Vi använder **Windows Defender ATP (WDATP)** för att:

* Övervaka och övervaka säkerhets risker och felkonfigurationer kontinuerligt
* Använd WDATP för att prioritera dynamiska hot i vilda
* Öka riskerna för sårbarheter med aviseringar för slut punkts identifiering och svar (EDR)
* Använd instrument panelen för att identifiera sårbarhet på dator nivå under undersökningar
* Skicka ut reparationer till Intune

Konfigurera din [Defender ATP-instrumentpanel](https://securitycenter.windows.com/machines). [Översikt över instrument panelen för hot & sårbarhets hantering](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Övervaka program aktivitet med Microsoft Monitoring Agent (MMA)
Från och med den specialiserade arbets stationen är app Locker aktive rad för övervakning av program aktivitet på en arbets Station. För att övervakningen ska integreras i din Log Analytics arbets yta måste en MMA-agent och-konfiguration följas. 

> [!NOTE]
> Den specialiserade arbets Stations profilen innehåller AppLockers övervaknings principer. Distribution av principer krävs för att övervaka program aktivitet på en klient

Distribuera MMA-agenten med Intune PowerShell-skriptet

1. Ladda ned installations [skriptet till en lokal enhet](https://aka.ms/securedworkstationgit).
1. Uppdatera parametrarna **$WorkSpaceID** och **$WorkSpaceKey**
1. Bläddra till **Azure Portal**  >  **Microsoft Intune**  >  **enhets konfiguration**  >  **PowerShell-skript**  >  **Lägg till**.
1. Ange ett **namn** på skriptet och ange sökvägen till **skriptet**.
1. Välj **Konfigurera**.
   1. Ange **Kör det här skriptet med de inloggade autentiseringsuppgifterna** till **Ja**.
   1. Välj **OK**.
1. Välj **Skapa**.
1. Välj **tilldelningar**  >  **Välj grupper**.
   1. Lägg till säkerhets gruppen **säkra arbets stationer**.
   1. Välj **Spara**.

Härnäst måste du konfigurera Log Analytics för att ta emot de nya loggarna
1. I **Azure Portal** går du till **Log Analytics arbets yta** > Select-"säker arbets Stations övervakning"
1. Välj **Avancerade inställningar**  >  **data**  >  **Windows händelse loggar**
1. I **samla in händelser från följande händelse loggar** 
1. Ange:
   * "Microsoft-Windows-AppLocker/EXE och DLL" > avmarkerar **information**
   * "Microsoft-Windows-AppLocker/MSI och skript" > avmarkerar **information**
   * "Microsoft-Windows-AppLocker/paketerad app-Deployment" > avmarkerar **information**
   * "Microsoft-Windows-AppLocker/paketerad app-Execution" > avmarkerar **information**
1. Välj **Spara**

Program loggningen är tillgänglig i den valda Log Analytics-arbetsytan.

## <a name="monitoring"></a>Övervakning

* Lär dig att [identifiera hot med Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md)
* [Undersök incidenter med Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
* [Konfigurera automatiska hot svar i Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
* Förstå hur du granskar [exponerings poängen](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Granska [säkerhets rekommendation](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Hantera säkerhets [åtgärder](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation)
* Hantera [slut punkts identifiering och-svar](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Övervaka profiler med [Intune-profil övervakning](/intune/device-profile-monitor).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Microsoft Intune](/intune/index).
* Förstå [Azure AD](../index.yml).
* Arbeta med [Microsoft Defender Avancerat skydd](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Identifiera [Azure Sentinel](../../sentinel/index.yml)