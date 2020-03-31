---
title: Distribuera Azure-hanterade arbetsstationer – Azure Active Directory
description: Lär dig hur du distribuerar säkra, Azure-hanterade arbetsstationer för att minska risken för intrång på grund av felkonfiguration eller kompromettering.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672609"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Distribuera en säker, Azure-hanterad arbetsstation

Nu när du [förstår säkra arbetsstationer](concept-azure-managed-workstation.md)är det dags att påbörja distributionsprocessen. Med den här vägledningen använder du definierade profiler för att skapa en arbetsstation som är säkrare från början.

![Driftsättning av en säker arbetsstation](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Välj en profil innan du distribuerar lösningen. Du kan använda flera profiler samtidigt i en distribution och tilldela dem med taggar eller grupper.

> [!NOTE]
> Använd någon av profilerna efter behov enligt dina krav. Du kan gå över till en annan profil genom att tilldela den i Microsoft Intune.

| Profil | Låg | Optimerad | Hög | Specialiserade | Säkrade | Isolerat |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Användare i Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Intune-hanterad | Ja | Ja | Ja | Ja | Ja | Ja |
| Enhet - Azure AD registrerad | Ja |  |  |  |  | |   |
| Enhet - Azure AD har anslutit |   | Ja | Ja | Ja | Ja | Ja |
| Intune-säkerhetsbaslinje tillämpad |   | Ja <br> (Förbättrad) | Ja <br> (Högsäkerhet) | Ja <br> (NCSC) | Ja <br> (Säkrad) | Ej tillämpligt |
| Maskinvara uppfyller säkra Windows 10-standarder |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP aktiverat |   | Ja  | Ja | Ja | Ja | Ja |
| Borttagning av administratörsrättigheter |   |   | Ja  | Ja | Ja | Ja |
| Distribution med Microsoft Autopilot |   |   | Ja  | Ja | Ja | Ja |
| Appar som endast installeras av Intune |   |   |   | Ja | Ja |Ja |
| Webbadresser som är begränsade till godkänd lista |   |   |   | Ja | Ja |Ja |
| Internet blockerat (inkommande/utgående) |   |   |   |  |  |Ja |

> [!NOTE]
> I den säkra arbetsstation vägledning **enheter** kommer att tilldelas med profiler och principer. Användarna kommer inte att ha de principer som tillämpas på dem direkt, vilket gör att enhetsdelning (delade enheter) är i kraft. Om en säker arbetsstation inte delas i distributionen eller om enskilda användarprinciper behövs, kan tilldelning av användarprincipprofiler tilldelas användaren och enheten. 

## <a name="license-requirements"></a>Licenskrav

Begreppen i den här guiden förutsätter att du har Microsoft 365 Enterprise E5 eller en motsvarande SKU. Några av rekommendationerna i den här guiden kan implementeras med lägre SKU: er. Mer information finns i [Microsoft 365 Enterprise-licensiering](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Om du vill automatisera licensetablering bör du överväga [gruppbaserad licensiering](../users-groups-roles/licensing-groups-assign.md) för användarna.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory-konfiguration

Azure Active Directory (Azure AD) hanterar användare, grupper och enheter för dina administratörsarbetsstationer. Aktivera identitetstjänster och funktioner med ett [administratörskonto](../users-groups-roles/directory-assign-admin-roles.md).

När du skapar det skyddade arbetsstationsadministratörskontot exponerar du kontot för din aktuella arbetsstation. Se till att du använder en känd säker enhet för att göra den här inledande konfigurationen och all global konfiguration. För att minska angreppsexponeringen för första gången, överväga att följa [vägledningen för att förhindra malware infektioner](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Kräv multifaktorautentisering, åtminstone för administratörerna. Se [Distribuera molnbaserad MFA](../authentication/howto-mfa-getstarted.md) för implementeringsvägledning.

### <a name="azure-ad-users-and-groups"></a>Azure AD-användare och -grupper

1. Bläddra till Azure Active Directory**Users** > **New-användare**från **Azure-portalen** > .
1. Skapa enhetsadministratören genom att följa stegen i [självstudiekursen skapa användare](/Intune/quickstart-create-user).
1. Skriv:

   * **Namn** - Säker arbetsstationsadministratör
   * **Användarnamn** - `secure-ws-admin@identityitpro.com`
   * **Katalogroll** - **Begränsad administratör** och välj rollen **Intune Administrator.**

1. Välj **Skapa**.

Därefter skapar du två grupper: arbetsstationsanvändare och arbetsstationsenheter.

Bläddra till **azure Active Directory** > **Groups** > **ny grupp**från Azure-portalen .

1. För arbetsgruppens användargrupp kanske du vill konfigurera [gruppbaserad licensiering](../users-groups-roles/licensing-groups-assign.md) för att automatisera etablering av licenser till användare.
1. För gruppen arbetsstationsanvändare anger du:

   * **Grupptyp** - Säkerhet
   * **Gruppnamn** - Användare av säkra arbetsstationer
   * **Medlemstyp** - Tilldelad

1. Lägg till din användare av den säkra arbetsstationsadministratören:`secure-ws-admin@identityitpro.com`
1. Du kan lägga till andra användare som hanterar säkra arbetsstationer.
1. Välj **Skapa**.
1. För gruppen arbetsstationsenheter anger du:

   * **Grupptyp** - Säkerhet
   * **Gruppnamn** - Säkra arbetsstationer
   * **Medlemstyp** - Tilldelad

1. Välj **Skapa**.

### <a name="azure-ad-device-configuration"></a>Konfiguration av Azure AD-enhet

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Ange vem som kan ansluta till enheter till Azure AD

Konfigurera enhetsinställningen i Active Directory så att den administrativa säkerhetsgruppen kan ansluta till enheter till domänen. Så här konfigurerar du den här inställningen från Azure-portalen:

1. Gå till Azure **Active Directory** > **Devices-enhetsinställningar****Devices** > .
1. Välj **Markerad** under **Användare kan ansluta enheter till Azure AD**och välj sedan gruppen "Säkra arbetsstationsanvändare".

#### <a name="removal-of-local-admin-rights"></a>Borttagning av lokala administratörsrättigheter

Den här metoden kräver att användare av VIP-, DevOps- och secure-level-arbetsstationer inte har några administratörsrättigheter på sina datorer. Så här konfigurerar du den här inställningen från Azure-portalen:

1. Gå till Azure **Active Directory** > **Devices-enhetsinställningar****Devices** > .
1. Välj **Ingen** under **Ytterligare lokala administratörer på Azure AD-anslutna enheter**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Kräv multifaktorautentisering för att ansluta till enheter

Så här ytterligare stärker du processen med att ansluta enheter till Azure AD:

1. Gå till Azure **Active Directory** > **Devices-enhetsinställningar****Devices** > .
1. Välj **Ja** under **Kräv multifaktorautentisering för att ansluta till enheter**.
1. Välj **Spara**.

#### <a name="configure-mobile-device-management"></a>Konfigurera hantering av mobila enheter

Från Azure-portalen:

1. Bläddra till **Azure Active Directory** > **Mobility (MDM och MAM)** > **Microsoft Intune**.
1. Ändra **mdm-användarens scopeinställning** till **Alla**.
1. Välj **Spara**.

Med de här stegen kan du hantera alla enheter med Intune. Mer information finns [i Intune Quickstart: Konfigurera automatisk registrering för Windows 10-enheter](/Intune/quickstart-setup-auto-enrollment). Du skapar Intune-konfigurations- och efterlevnadsprinciper i ett framtida steg.

#### <a name="azure-ad-conditional-access"></a>Villkorlig åtkomst för Azure AD

Azure AD-villkorlig åtkomst kan hjälpa till att begränsa privilegierade administrativa uppgifter till kompatibla enheter. Fördefinierade medlemmar i gruppen Användare av **säkra arbetsstationer** måste utföra multifaktorautentisering när de loggar in på molnprogram. En bästa praxis är att utesluta konton för nödåtkomst från principen. Mer information finns [i Hantera konton för nödåtkomst i Azure AD](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Intune-konfiguration

### <a name="configure-enrollment-status"></a>Konfigurera registreringsstatus

Det är viktigt att se till att din säkra arbetsstation är en betrodd ren enhet. När du köper nya enheter kan du insistera på att de är fabrikssatta i [Windows 10 Pro i S-läge](/Windows/deployment/Windows-10-pro-in-s-mode), vilket begränsar exponeringen för sårbarheter under hantering av försörjningskedjan. När du har fått en enhet från din leverantör kan du använda Autopilot för att ändra den från S-läge. Följande vägledning innehåller information om hur du tillämpar omvandlingsprocessen.

För att säkerställa att enheterna är helt konfigurerade före användning, erbjuder Intune ett sätt att **blockera enhetsanvändning tills alla appar och profiler är installerade**.

Från **Azure-portalen:**

1. Gå till**Enrollment Status Page** > **standardinställningar****Settings** >  > för registrering av Registrering av**Windows-registrering** >  **för Microsoft Intune-enhet** > **Device enrollment**Windows-registrering .
1. Ange Förlopp för **installation av appens profil** till **Ja**.
1. Ange **blockera enhetsanvändning tills alla appar och profiler är installerade på** **Ja**.

### <a name="create-an-autopilot-deployment-profile"></a>Skapa en Autopilot-distributionsprofil

När du har skapat en enhetsgrupp måste du skapa en distributionsprofil för att konfigurera Autopilot-enheterna.

I Intune i Azure-portalen:

1. Välj **Enhetsregistrering** > **Windows-registreringsdistributionsprofiler** > **Deployment Profiles** > Skapa**profil**.
1. Skriv:

   * Namn - **Säker distributionsprofil för arbetsstationer**.
   * Beskrivning - **Utplacering av säkra arbetsstationer**.
   * Ange **Omvandla alla målenheter till Autopilot** som **Ja**. Den här inställningen ser till att alla enheter i listan blir registrerade med Autopilots distributionstjänst. Det kan ta upp till 48 timmar för registreringen att bearbetas.

1. Välj **Nästa**.

   * För **distributionsläge**väljer du **Självdistribution (förhandsversion)**. Enheter med den här profilen är associerade med användaren som registrerar enheten. Autentiseringsuppgifter krävs för att registrera enheten. Det är viktigt att notera att distribuera en enhet i **självut distribuerande** läge gör att du kan distribuera bärbara datorer i en delad modell. Ingen användartilldelning sker förrän enheten tilldelas en användare för första gången. Därför aktiveras inte användarprinciper som BitLocker förrän en användartilldelning har slutförts. Mer information om hur du loggar in på en säker enhet finns i [valda profiler](/intune/device-profile-assign).
   * Rutan **Gå med till Azure AD som** ska visa Azure **AD-ansluten** och vara nedtonad.
   * Välj språk (region), Standard **för**användarkontotyp . 

1. Välj **Nästa**.

   * Välj en scopetagg om du har förkonfigurerat en.

1. Välj **Nästa**.
1. Välj **Tilldelningar** > **Tilldela till** > markerade**grupper**. I **Välj grupper som ska ingå**väljer du Säkra **arbetsstationer**.
1. Välj **Nästa**.
1. Välj **Skapa** för att skapa profilen. Nu kan Autopilot-distributionsprofilen tilldelas till enheter.

Enhetsregistrering i Autopilot ger en annan användarupplevelse baserat på enhetstyp och roll. I vårt distributionsexempel illustrerar vi en modell där de säkrade enheterna är massbe distribuerade och kan delas, men när den används för första gången tilldelas enheten till en användare. Mer information finns i [Registrering av Intune Autopilot-enhet](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurera Windows Update

Att hålla Windows 10 uppdaterat är en av de viktigaste sakerna du kan göra. Om du vill behålla Windows i ett säkert tillstånd distribuerar du en uppdateringsring för att hantera den takt som uppdateringar tillämpas på arbetsstationer. 

Den här vägledningen rekommenderar att du skapar en ny uppdateringsring och ändrar följande standardinställningar:

På Azure Portal:

1. Gå till **Microsoft Intune** > **Software updates** > **Windows 10 Update Rings**.
1. Skriv:

   * Namn - **Azure-hanterade arbetsstationsuppdateringar**
   * Servicekanal - **Windows Insider - Snabb**
   * Kvalitetsuppdatering uppskov (dagar) - **3**
   * Funktionsuppdatering uppskovsperiod (dagar) - **3**
   * Automatiskt uppdateringsbeteende - **Automatisk installation och omstart utan slutanvändarens kontroll**
   * Blockera användare från att pausa Windows-uppdateringar - **Blockera**
   * Kräv användarens godkännande för att starta om utanför arbetstid - **Obligatoriskt**
   * Tillåt användaren att starta om (aktiverad omstart) - **Krävs**
   * Övergång användare till engagerad omstart efter en automatisk omstart (dagar) - **3**
   * Snooze engagerad omstart påminnelse (dagar) - **3**
   * Ange tidsgräns för väntande omstarter (dagar) - **3**

1. Välj **Skapa**.
1. Lägg till gruppen Säkra **arbetsstationer på** fliken **Tilldelningar.**

Mer information om Windows Update-principer finns i [Princip CSP - Uppdatera](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integrering

Windows Defender ATP och Microsoft Intune arbetar tillsammans för att förhindra säkerhetsöverträdelser. De kan också begränsa effekterna av överträdelser. ATP-funktioner ger hotidentifiering i realtid samt möjliggör omfattande granskning och loggning av slutpunktsenheterna.

Om du vill konfigurera integrering av Windows Defender ATP och Intune går du till Azure-portalen.

1. Bläddra till **Microsoft Intune** > **Device compliance** > **Windows Defender ATP**.
1. I steg 1 under **Konfigurera Windows Defender ATP**väljer du **Anslut Windows Defender ATP till Microsoft Intune i Windows Defender Security Center**.
1. I Windows Defender Säkerhetscenter:

   1. Välj **Avancerade inställningar** > **Advanced features**.
   1. För **Microsoft Intune-anslutning**väljer du **På**.
   1. Välj **Spara inställningar**.

1. När en anslutning har upprättats går du tillbaka till Intune och väljer **Uppdatera** högst upp.
1. Ändra **Anslut Windows-enheter version 10.0.15063 och senare till Windows Defender ATP9** till **På**.
1. Välj **Spara**.

Mer information finns i [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Avsluta härdning av arbetsstationsprofil

För att slutföra härdningen av lösningen, ladda ner och köra rätt skript. Hitta nedladdningslänkarna för önskad **profilnivå:**

| Profil | Ladda ner plats | Filnamn |
| --- | --- | --- |
| Låg säkerhet | Ej tillämpligt | Ej tillämpligt |
| Förbättrad säkerhet | https://aka.ms/securedworkstationgit | Förbättrad arbetsstation-Windows10-(1809).ps1 |
| Hög säkerhet | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specialiserade | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline.ps1 |
| Specialiserad efterlevnad* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Säkrade | https://aka.ms/securedworkstationgit | Säker arbetsstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Specialiserad efterlevnad är ett skript som framtvingar den specialiserade konfigurationen som finns i NCSC Windows10 SecurityBaseline.

När skriptet har körs kan du göra uppdateringar av profiler och principer i Intune. Skripten för förbättrade och säkra profiler skapar principer och profiler åt dig, men du måste tilldela principen till **enhetsgruppen Säkra arbetsstationer.**

* Här hittar du konfigurationsprofilerna för Intune-enheten som skapats av skripten:**Konfigurationsprofiler** **för Azure portal** > **Microsoft Intune** > **Device** > .
* Här hittar du intune-principer för enhetsefterlevnad som skapats av skripten: **Azure portal** > **Microsoft Intune** > **Device Compliance** > **Policies**.

Om du vill granska ändringar som gjorts av skripten kan du exportera profilerna. På så sätt kan du bestämma ytterligare härdning som kan krävas enligt [SECCON-dokumentationen](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Kör Intune-dataexportskriptet `DeviceConfiguration_Export.ps1` från [DeviceConfiguration GiuHub-databasen](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) för att exportera alla aktuella Intune-profiler.

## <a name="additional-configurations-and-hardening-to-consider"></a>Ytterligare konfigurationer och härdning att överväga

Genom att följa vägledningen här har du distribuerat en säker arbetsstation. Men du bör också överväga ytterligare kontroller. Ett exempel:

* begränsa åtkomsten till alternativa webbläsare
* tillåta utgående HTTP
* blockera utvalda webbplatser
* ange behörigheter för att köra anpassade PowerShell-skript

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Ange regler i brandväggsoperatören (CSP)

Du kan göra ytterligare ändringar i hanteringen av både inkommande och utgående regler efter behov för tillåtna och blockerade slutpunkter. När du fortsätter att härda den säkra arbetsstationen kan du lossa begränsningen som nekar all inkommande och utgående trafik. Du kan lägga till tillåtna utgående webbplatser för att inkludera vanliga och betrodda webbplatser. Mer information finns i [Brandväggskonfigurationstjänst](/Windows/client-management/mdm/firewall-csp).

Restriktiv trafikhantering för webbadresser omfattar:

* Neka all inkommande trafik - Ange i skriptet för säker arbetsstationsprofil.
* Neka alla utgående utom valda Azure- och Microsoft-tjänster, inklusive Azure Cloud Shell och möjligheten att tillåt Azure Password Reset.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Om du funderar på att ge mer detaljerad information till dina blockeringsregler kan du hänvisa till Spamhaus Project som upprätthåller [Domänblocklistan (DBL):](https://www.spamhaus.org/dbl/)en bra resurs att använda som en avancerad uppsättning regler för att implementera för blockering av webbplatser.

### <a name="manage-local-applications"></a>Hantera lokala program

Den säkra arbetsstationen översätts till ett verkligt härdat tillstånd när lokala program tas bort, inklusive produktivitetsprogram. Här lägger du till Chrome som standardwebbläsare och använder Intune för att begränsa en användares möjlighet att ändra webbläsaren och dess plugin-program.

#### <a name="deploy-applications-using-intune"></a>Distribuera program med Intune

I vissa situationer krävs program som webbläsaren Google Chrome på den säkrade arbetsstationen. I följande exempel finns instruktioner för hur du installerar Chrome på enheter i säkerhetsgruppen **Secure Workstations**.

1. Ladda ned [Chrome-paketet för offlineinstallationsprogrammet för Windows 64-bitars](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahera filerna och notera platsen för `GoogleChromeStandaloneEnterprise64.msi` filen.
1. I **Azure-portalen** bläddra till **Microsoft Intune** > **Client apps** > **Apps Lägg** > **till**.
1. Under **Apptyp**väljer du **Line-of-business**.
1. Under **Apppaketfil**väljer `GoogleChromeStandaloneEnterprise64.msi` du filen från den extraherade platsen och väljer **OK**.
1. Under **Appinformation**anger du en beskrivning och en utgivare. Välj **OK**.
1. Välj **Lägg till**.
1. Välj **Tillgänglig för registrerade enheter** under **Tilldelningstyp**på fliken **Tilldelningar** .
1. Lägg till gruppen **Säkra arbetsstationer under** **Inkluderade grupper.**
1. Välj **OK**och välj sedan **Spara**.

Mer information om hur du konfigurerar Chrome-inställningar finns i [Hantera Webbläsaren Chrome med Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurera företagsportalen för anpassade appar

I ett säkert läge är programinstallationen begränsad till Intune-företagsportalen. Om du installerar portalen måste du dock få åtkomst till Microsoft Store. I din säkra lösning kan du göra företagsportalen tillgänglig för alla enheter via ett offlineläge.

En Intune-hanterad kopia av [företagsportalen](/Intune/store-apps-company-portal-app) ger dig åtkomst på begäran till ytterligare verktyg som du kan trycka ned till användare av de skyddade arbetsstationerna.

Du kan behöva installera Windows 32-bitarsappar eller andra appar vars distribution kräver särskilda förberedelser. I sådana fall kan [Microsoft win32-innehållsförberedelseverktyget](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) tillhandahålla en färdig `.intunewin` formatfil för installation.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Villkorlig åtkomst som endast tillåter säker arbetsstationskapacitet att komma åt Azure-portalen

Azure AD erbjuder möjligheten att hantera och begränsa, vem och vad som kan komma åt din Azure-molnhanteringsportal. Om du aktiverar [villkorlig åtkomst](../conditional-access/overview.md) kan endast din säkra arbetsstation hantera eller ändra resurser. Det är viktigt att när du distribuerar den här funktionen du överväga, om [nödåtkomst](../users-groups-roles/directory-emergency-access.md) funktionalitet kan eller bör användas endast för extrema fall och kontot hanteras via principen.

> [!NOTE]
> Du måste skapa en användargrupp och inkludera din nödanvändare som kan kringgå principen villkorlig åtkomst. Till vårt exempel har vi en säkerhetsgrupp som heter **Emergency BreakGlass**

1. Bläddra till **Azure Portal** > **Microsoft Intune** > Villkorlig åtkomst -**Principer** > **Ny princip**.
1. Ange ett **namn** för principen.
1. Välj **Användare och grupper** > **Välj användare och grupper** 
1. Välj **Inkludera** > **katalogroller** > Välj roller > global administratör, administratör för privilegierad roll, administratör för privilegierad autentisering, säkerhetsadministratör, efterlevnadsadministratör, administratör för villkorlig åtkomst, programadministratör, molnprogramadministratör, intune-tjänstadministratör
1. Välj **Uteslut** > Välj **användare och grupper** > Välj Utvalda **uteslutna användare** > Välj din grupp För **nödbrytarglas.**
1. Välj **Molnappar eller -åtgärder** > Markera **alla molnappar**
1. Välj **villkor** > Välj **enhetsplattformar** > Välj konfigurera **Ja** > Välj **Välj enhetsplattformar** Välj **Windows**
1. Välj **Access-kontroller** > Välj **Bevilja åtkomst** **Ja** > Välj **Kräv enhet som ska markeras som kompatibel**. 
1. Välj **Aktivera princip** > **på**
 
Den här principuppsättningen säkerställer att administratörerna måste använda en kompatibel Windows-enhet, som har angetts av Intune och WDATP. 

Organisationer bör också överväga att blockera äldre autentiseringsprotokoll i sina miljöer. Det finns flera sätt att utföra den här uppgiften, för mer information om hur du blockerar äldre autentiseringsprotokoll se artikeln, [Så här: Blockera äldre autentisering till Azure AD med villkorlig åtkomst](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Använda PowerShell för att skapa anpassade inställningar

Du kan också använda PowerShell för att utöka värdhanteringsfunktionerna. I det här exempelskriptet ställs en standardbakgrund på värden. Det är en funktion som också är tillgänglig via Azure-portalen och profiler. Exempelskriptet används bara för att illustrera PowerShell-funktionen.

Du kan behöva konfigurera vissa anpassade kontroller och inställningar på dina säkra arbetsstationer. Det här exemplet ändrar arbetsstationens bakgrund med hjälp av Powershells förmåga att enkelt identifiera enheten som en säker arbetsstation som är klar att använda.

[SetDesktopBackground.ps1-skriptet](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) från Microsoft Scripting Center gör att Windows kan läsa in den här [kostnadsfria, generiska bakgrundsbilden](https://i.imgur.com/OAJ28zO.png) vid start.

1. Ladda ned skriptet till en lokal enhet.
1. Uppdatera kundXXXX och hämtningsplatsen för bakgrundsbilden. I vårt exempel ersätter vi customerXXXX till bakgrunder.
1. Bläddra till **Azure Portal** > **Microsoft Intune** > **Device konfiguration** > **PowerShell-skript** > **Lägg till**.
1. Ange ett **namn** för skriptet och ange **skriptplatsen**.
1. Välj **Konfigurera**.
   1. Ange **Kör det här skriptet med de inloggade autentiseringsuppgifterna** till **Ja**.
   1. Välj **OK**.
1. Välj **Skapa**.
1. Välj **Tilldelningar** > **Välj grupper**.
   1. Lägg till säkerhetsgruppen **Säkra arbetsstationer**.
   1. Välj **Spara**.

## <a name="enroll-and-validate-your-first-device"></a>Registrera och validera din första enhet

1. För att registrera enheten behöver du följande information:
   * **Serienummer** - finns på enhetens chassi.
   * **Windows-produkt-ID** - hittades under **System** > **Om** på menyn Inställningar i Windows.
   * Du kan köra [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) för att få en CSV hash-fil med all nödvändig information för enhetsregistrering.
   
     Kör `Get-WindowsAutoPilotInfo – outputfile device1.csv` för att mata ut informationen som en CSV-fil som du kan importera till Intune.

     > [!NOTE]
     > Skriptet kräver förhöjda rättigheter. Den körs som fjärrsignerad. Kommandot `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` gör att skriptet kan köras korrekt.

   * Du kan samla in den här informationen genom att logga in på en Windows 10 version 1809 eller senare enhet. Maskinvaruåterförsäljaren kan också ange den här informationen.
1. Gå till Registreringsenheter för **Microsoft Intune** > Device i **Azure-portalen****Device enrollment** > **Windows enrollment** > **– Hantera Windows Autopilot-enheter**.
1. Välj **Importera** och välj csv-filen.
1. Lägg till enheten i säkerhetsgruppen **Säkra arbetsstationer.**
1. På den Windows 10-enhet som du vill konfigurera går du till **Windows Settings** > **Update & Security** > **Recovery**.
   1. Välj **Kom igång** under Återställ den här **datorn**.
   1. Följ anvisningarna för att återställa och konfigurera om enheten med de profil- och efterlevnadsprinciper som konfigurerats.

När du har konfigurerat enheten slutför du en granskning och kontrollerar konfigurationen. Kontrollera att den första enheten är korrekt konfigurerad innan du fortsätter distributionen.

## <a name="assign-devices"></a>Tilldela enheter

Om du vill tilldela enheter och användare måste du mappa de [valda profilerna](/intune/device-profile-assign) till din säkerhetsgrupp. Alla nya användare som behöver behörighet till tjänsten måste också läggas till i säkerhetsgruppen.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Använda Sentinel och Windows Defender ATP för att övervaka och svara på säkerhetsincidenter

Övervakning av säker arbetsstationsutplacering kan utföras genom att [Sentinel] och användning av [hot- och sårbarhetshantering](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) Vägledningen inte kommer att ge uttömmande hotjakt, utan ger sunt förnuft för att övervaka och reagera på potentiella säkerhetsincidenter.

Vi använder **Azure Sentinel** för att: 

* Samla in data från Intune, Azure-portalen och Azure AD för övervakning av användare och enheter
* Hjälp undersöka misstänkt aktivitet för användar- och enhetskonfiguration
* Och driva möjligheten att utforska säkerhetsutredningar med WDATP

Sentinel-övervakning kräver att kopplingar till dina datakällor som Azure AD ställs in.

1. Gå till **Azure Sentinel (Förhandsversion)** > Välj **Lägg till** i **Azure-portalen**
1. Skapa **en** ny arbetsyta på välj en arbetsyta som ska läggas till i Azure Sentinel Välj Skapa en ny **arbetsyta**
1. Skriv:
   * **Logganalys arbetsyta** - "Säker övervakning av arbetsstationer"
   * **Prenumeration** - Välj din aktiva prenumeration
   * **Resursgrupp** - välj ** Skapa ny** > säker arbetsstation RG > **Ok**
   * **Plats** - Välj den plats som är geografiskt bäst lämpad för distributionen
   * **Prisnivå** - Välj **per GB (2018)**
1. Välj **Ok**.

Därefter kommer vi att ansluta tillgängliga säkra arbetsstationsdatakällor till övervakningen.

1. Gå **Azure portal**till **Azure Sentinel-arbetsytan** > Välj arbetsyta **för övervakning av en säker arbetsstation**
1. Välj **dataanslutningar**
1. Välj **Azure Active Directory** > Öppna anslutningssidan > När du har granskat förutsättningen. Fortsätt till konfigurationen och välj **Anslut** för både Azure AD-inloggningsloggar samt Azure AD-granskningsloggar.
1. Välj **Azure-aktivitet** > öppna anslutningssidan > När du har granskat förutsättningen. Fortsätt att konfigurera Azure Activity Logs > Välj din prenumeration > Välj **Connect**

När data samlas in av Sentinel kan du observera aktivitet genom att välja **Azure-portal**, gå till **Azure Sentinel Översikt** 

Vi kommer att använda **Windows Defender ATP (WDATP)** för att:

* Observera och övervaka sårbarheter och felkonfigurationer kontinuerligt
* Använd WDATP för att prioritera dynamiska hot i naturen
* Driva korrelation av sårbarheter med edr-varningar (endpoint detection and response)
* Använd instrumentpanelen för att identifiera sårbarhet på maskinnivå under undersökningar
* Push out remediations till Intune

Konfigurera [din Defender ATP-instrumentpanel](https://securitycenter.windows.com/machines). Använda vägledning på [instrumentpanelen & & sårbarhetshantering](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Övervaka programaktivitet med Hjälp av Microsoft Monitoring Agent (MMA)
Från och med den specialiserade arbetsstationen är appskåp aktiverat för övervakning av applikationsaktivitet på en arbetsstation. För att övervakningen ska vara integrerad i logganalysarbetsytan måste en MMA-agent och konfiguration följas. 

> [!NOTE]
> Profilen specialiserad arbetsstation innehåller AppLocker-övervakningsprinciperna. Det krävs en distribution av principerna för övervakning av applikationsaktivitet på en klient

Distribuera MMA-agenten med Intune PowerShell-skript

1. Hämta [installationsskriptet till en lokal enhet](https://aka.ms/securedworkstationgit).
1. Uppdatera parametrarna, **$WorkSpaceID** och **$WorkSpaceKey**
1. Bläddra till **Azure Portal** > **Microsoft Intune** > **Device konfiguration** > **PowerShell-skript** > **Lägg till**.
1. Ange ett **namn** för skriptet och ange **skriptplatsen**.
1. Välj **Konfigurera**.
   1. Ange **Kör det här skriptet med de inloggade autentiseringsuppgifterna** till **Ja**.
   1. Välj **OK**.
1. Välj **Skapa**.
1. Välj **Tilldelningar** > **Välj grupper**.
   1. Lägg till säkerhetsgruppen **Säkra arbetsstationer**.
   1. Välj **Spara**.

Därefter måste du ställa in Log Analytics för att få de nya loggarna
1. Gå till **Log Analytics Workspace** > Select i **Azure-portalen**– säker övervakning av arbetsstationer
1. Välj **avancerade inställningar** > **Windows-händelseloggar för** **data** > 
1. I **Samla in händelser från följande händelseloggar** 
1. Skriv:
   * "Microsoft-Windows-AppLocker/EXE och DLL" > Oförsegla **information**
   * "Microsoft-Windows-AppLocker/MSI and Script" > Oförsegla **information**
   * "Microsoft-Windows-AppLocker/Paketerad appdistribution" > information om **Informational** olektektiv
   * "Microsoft-Windows-AppLocker/Packaged app-Execution" > Oförsegla **information**
1. Välj **Spara**

Programloggning är tillgängligt på den valda Log Analytics-arbetsytan.

## <a name="monitoring"></a>Övervakning

* Lär dig hur du [identifierar hot med Azure Sentinel](/azure/sentinel/tutorial-detect-threats)
* [Undersöka incidenter med Azure Sentinel](/azure/sentinel/tutorial-investigate-cases)
* [Konfigurera automatiska hotsvar i Azure Sentinel](/azure/sentinel/tutorial-respond-threats-playbook)
* Förstå hur du granskar din [exponeringspoäng](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Granska [rekommendationen säkerhet](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Hantera [säkerhetskorrigerande åtgärder](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation)
* Hantera [identifiering och svar på slutpunkt](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Övervaka profiler med [Intune-profilövervakning](/intune/device-profile-monitor).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Microsoft Intune](/intune/index).
* Förstå [Azure AD](../index.yml).
* Arbeta med [Microsoft Defender advanced threat protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Upptäck [Azure Sentinel](/azure/sentinel/)
