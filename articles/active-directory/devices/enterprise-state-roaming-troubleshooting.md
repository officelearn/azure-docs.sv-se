---
title: Felsöka roaming i företagstillstånd i Azure Active Directory
description: Ger svar på några frågor som IT-administratörer kan ha om inställningar och synkronisering av appdata.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8ce24aeb665a7f99326e83dbe18d020e1b6196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672356"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Felsöka roaminginställningar för företagstillstånd i Azure Active Directory

Det här avsnittet innehåller information om hur du felsöker och diagnostiserar problem med Roaming i företagstillstånd och innehåller en lista över kända problem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Den här artikeln gäller den HTML-baserade webbläsaren Microsoft Edge Legacy som lanserades med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge Chromium-baserade webbläsaren som släpptes den 15 januari 2020. Mer information om synkroniseringsbeteendet för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Inledande steg för felsökning 

Innan du börjar felsöka kontrollerar du att användaren och enheten har konfigurerats korrekt och att alla krav i Företagstillståndsroaming uppfylls av enheten och användaren. 

1. Windows 10, med de senaste uppdateringarna och en minsta version 1511 (OS Build 10586 eller senare) är installerat på enheten. 
1. Enheten är Azure AD-ansluten eller hybrid Azure AD-ansluten. Mer information finns i [hur du får en enhet under kontroll av Azure AD](overview.md).
1. Kontrollera att **Företagstillståndsroaming** är aktiverat för klienten i Azure AD enligt beskrivningen i [Så här aktiverar Du centralen för företag.](enterprise-state-roaming-enable.md) Du kan aktivera roaming för alla användare eller endast för en vald grupp av användare.
1. Användaren tilldelas en Azure Active Directory Premium-licens.  
1. Enheten måste startas om och användaren måste logga in igen för att komma åt Roaming-funktioner i företagtillstånd.

## <a name="information-to-include-when-you-need-help"></a>Information som ska inkluderas om du behöver hjälp
Om du inte kan lösa ditt problem med vägledningen nedan kan du kontakta våra supporttekniker. När du kontaktar dem ska du inkludera följande information:

* **Allmän beskrivning av felet**: Finns det felmeddelanden ses av användaren? Om det inte fanns något felmeddelande beskriver du det oväntade beteendet du märkte i detalj. Vilka funktioner är aktiverade för synkronisering och vad förväntar sig användaren att synkronisera? Synkroniseras inte flera funktioner eller är de isolerade till en?
* **Användare som påverkas** – Fungerar/misslyckas synkroniseringen för en användare eller flera användare? Hur många enheter är inblandade per användare? Är alla av dem inte synkronisera eller är några av dem synkronisering och vissa inte synkronisera?
* **Information om användaren** – Vilken identitet använder användaren för att logga in på enheten? Hur loggar användaren in på enheten? Är de en del av en vald säkerhetsgrupp som tillåts synkronisera? 
* **Information om enheten** – Är den här enheten Azure AD-ansluten eller domänansluten? Vad bygger enheten på? Vilka är de senaste uppdateringarna?
* **Datum / Tid / Tidszon** - Vad var det exakta datum och tid du såg felet (inkludera tidszon)?

Genom att inkludera denna information kan vi lösa ditt problem så snabbt som möjligt.

## <a name="troubleshooting-and-diagnosing-issues"></a>Felsökning och diagnostisera problem

I det här avsnittet får du förslag på hur du felsöker och diagnostiserar problem relaterade till Roaming i företagstillstånd.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Verifiera synkronisering och inställningssidan "Synkronisera dina inställningar" 

1. När du har anslutit din Windows 10-dator till en domän som är konfigurerad för att tillåta Roaming i företagstillstånd loggar du in med ditt arbetskonto. Gå till **Inställningar** > **Konton** > **Synkronisera dina inställningar** och bekräfta att synkronisering och de enskilda inställningarna är på, och att den övre sidan av inställningssidan visar att du synkroniserar med ditt arbetskonto. Bekräfta att samma konto också används som ditt inloggningskonto i **Inställningar** > **konton** > **din info**. 
1. Kontrollera att synkroniseringen fungerar på flera datorer genom att göra vissa ändringar på den ursprungliga datorn, till exempel flytta Aktivitetsfältet till höger eller övre sidan av skärmen. Titta på ändringen sprida till den andra maskinen inom fem minuter. 

   * Låsa och låsa upp skärmen (Win + L) kan hjälpa utlösa en synkronisering.
   * Du måste logga in med samma konto på båda datorerna för att synkronisering ska fungera – eftersom Företagsstat Roaming är kopplat till användarkontot och inte datorkontot.

**Potentiellt problem**: Om kontrollerna på sidan **Inställningar** inte är tillgängliga och meddelandet "Vissa Windows-funktioner är bara tillgängliga om du använder ett Microsoft-konto eller ett arbetskonto". Det här problemet kan uppstå för enheter som har konfigurerats för att vara domänanslutna och registrerade i Azure AD, men enheten har ännu inte autentiserats till Azure AD. En möjlig orsak är att enhetsprincipen måste tillämpas, men det här programmet sker asynkront och kan fördröjas med några timmar. 

### <a name="verify-the-device-registration-status"></a>Verifiera enhetens registreringsstatus

Företagstillståndsroaming kräver att enheten registreras med Azure AD. Även om det inte är specifikt för Företagstillståndsroaming kan du följa instruktionerna nedan hjälpa dig att bekräfta att Windows 10-klienten är registrerad och bekräfta tumavtryck, Url för Azure AD-inställningar, NGC-status och annan information.

1. Öppna kommandotolken som inte är igerad. För att göra detta i Windows, öppna Run launcher (Win + R) och skriv "cmd" för att öppna.
1. När kommandotolken är öppen skriver du "*dsregcmd.exe /status*".
1. För förväntad utdata ska fältet **azureAdJoined-värdet** vara "JA", **fältvärdet WamDefaultSet** ska vara "JA" och fältvärdet **WamDefaultGUID** ska vara ett GUID med "(AzureAd)" i slutet.

**Potentiella problem:** **WamDefaultSet** och **AzureAdJoined** har båda "NEJ" i fältvärdet, enheten domänansluts och registrerades med Azure AD och enheten synkroniseras inte. Om den visar detta kan enheten behöva vänta på att principen ska tillämpas eller autentiseringen för enheten misslyckades när du ansluter till Azure AD. Användaren kan behöva vänta några timmar på att principen ska tillämpas. Andra felsökningssteg kan vara att försöka autoregistration igen genom att logga ut och in igen eller starta aktiviteten i Schemaläggaren. I vissa fall kan det vara till hjälp med problemet att köra "*dsregcmd.exe /leave*" i ett upphöjt kommandotolksfönster, starta om och försöka registrera igen.

**Potentiellt problem**: Fältet för **SettingsUrl** är tomt och enheten synkroniseras inte. Användaren kan ha loggat in senast på enheten innan Enterprise State Roaming aktiverades i Azure Active Directory Portal. Starta om enheten och ha användarens inloggning. Du kan också prova att låta IT-administratören navigera till **Azure Active Directory** > **Devices** > **Enterprise State Roaming** inaktivera och aktivera om användare kan synkronisera inställningar **och appdata mellan enheter**. När du har aktiverat den igen startar du om enheten och har användarinloggningen. Om detta inte löser problemet kan **SettingsUrl** vara tomt om det finns ett felaktigt enhetscertifikat. I det här fallet kan det vara till hjälp med det här problemet att köra "*dsregcmd.exe /leave*" i ett upphöjt kommandotolksfönster, starta om och försöka registrera igen.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming i företagstillstånd och multifaktorautentisering 

Under vissa förhållanden kan Företagstillståndsroamering misslyckas med att synkronisera data om Azure Multi Factor-autentisering har konfigurerats. Mer information om dessa symptom finns i supportdokumentet [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potentiellt problem:** Om enheten är konfigurerad för att kräva multifaktorautentisering på Azure Active Directory-portalen kan du misslyckas med att synkronisera inställningar när du loggar in på en Windows 10-enhet med ett lösenord. Den här typen av multifaktorautentiseringskonfiguration är avsedd att skydda ett Azure-administratörskonto. Administratörsanvändare kan fortfarande synkronisera genom att logga in på sina Windows 10-enheter med sin Microsoft Passport for Work-PIN-kod eller genom att slutföra multifaktorautentisering när de öppnar andra Azure-tjänster som Office 365.

**Potentiellt problem**: Synkronisering kan misslyckas om administratören konfigurerar principen Multifaktorautentisering för flerafaktorsautentiseringstjänster i Active Directory Federation Services och åtkomsttoken på enheten upphör att gälla. Se till att du loggar in och loggar ut med PIN-koden för Microsoft Passport for Work eller slutför multifaktorautentisering när du öppnar andra Azure-tjänster som Office 365.

### <a name="event-viewer"></a>Loggboken

För avancerad felsökning kan Loggboken användas för att hitta specifika fel. Dessa dokumenteras i tabellen nedan. Händelserna finns under Loggboken > program- och tjänstloggar > **Microsoft** > **Windows** > **SettingSync-Azure** och för identitetsrelaterade problem med synkronisering **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Kända problem

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synkronisering fungerar inte på enheter som har appar sida-laddad med MDM-programvara

Påverkar enheter som kör Windows 10 Anniversary Update (version 1607). I Loggboken under SettingSync-Azure-loggarna visas händelse-ID 6013 med fel 80070259 ofta.

**Rekommenderad åtgärd**  
Kontrollera att Windows 10 v1607-klienten har den kumulativa uppdateringen 23 augusti 2016 ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Favoriter i Internet Explorer synkroniseras inte

Påverkar enheter som kör Windows 10 November Update (Version 1511).

**Rekommenderad åtgärd**  
Kontrollera att Windows 10 v1511-klienten har den kumulativa uppdateringen för juli 2016[(KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Temat synkroniseras inte, liksom data som skyddas med Windows Information Protection 

För att förhindra dataläckage synkroniseras inte data som är skyddade med [Windows InformationSskydd](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) via Företagsstat Roaming för enheter som använder Windows 10 Anniversary Update.

**Rekommenderad åtgärd**  
Inga. Framtida uppdateringar av Windows kan lösa problemet.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Inställningarna för datum, tid och region synkroniseras inte på domänansluten enhet 
  
Enheter som är domäntill anslutna kommer inte att uppleva synkronisering för inställningen Datum, Tid och Region: automatisk tid. Om du använder automatisk tid kan det åsidosätta de andra inställningarna för datum, tid och region och dessa inställningar inte synkroniseras. 

**Rekommenderad åtgärd**  
Inga. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC-uppmaningar vid synkronisering av lösenord

Påverkar enheter som kör Windows 10 November Update (Version 1511) med ett trådlöst nätverkskort som är konfigurerat för att synkronisera lösenord.

**Rekommenderad åtgärd**  
Kontrollera att Windows 10 v1511-klienten har den kumulativa uppdateringen[(KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synkronisering fungerar inte på enheter som använder smartkort för inloggning

Om du försöker logga in på din Windows-enhet med ett smartkort eller ett virtuellt smartkort slutar synkroniseringen av inställningarna att fungera.     

**Rekommenderad åtgärd**  
Inga. Framtida uppdateringar av Windows kan lösa problemet.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Domänansluten enhet synkroniseras inte efter att ha lämnat företagsnätverket     

Domänanslutna enheter som är registrerade i Azure AD kan uppleva synkroniseringsfel om enheten är från plats under längre tidsperioder och domänautentisering inte kan slutföras.

**Rekommenderad åtgärd**  
Anslut enheten till ett företagsnätverk så att synkroniseringen kan återupptas.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD-anslutna enheten synkroniseras inte och användaren har ett blandat ärende Användarnamn.

Om användaren har ett blandat ärende UPN (till exempel Användarnamn i stället för användarnamn) och användaren finns på en Azure AD-ansluten enhet, som har uppgraderat från Windows 10 Build 10586 till 14393, kan användarens enhet inte synkroniseras. 

**Rekommenderad åtgärd**  
Användaren måste ta av sig och ansluta enheten till molnet igen. För att göra detta, logga in som den lokala administratören användaren och unjoin enheten genom att gå till **Inställningar** > **System** > **Om** och välj "Hantera eller koppla från arbete eller skola". Rensa filerna nedan och sedan Azure AD Gå med i enheten igen i **Inställningar** > **System** > **Om** och välja "Anslut till arbete eller skola". Fortsätt att ansluta till enheten till Azure Active Directory och slutföra flödet.

Rensa följande filer i rensningssteget:
- Settings.dat i`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Alla filer under mappen`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Händelse-ID 6065: 80070533 Den här användaren kan inte logga in eftersom det här kontot är inaktiverat  

I Loggboken under logerna SettingSync/Debug kan det här felet visas när användarens autentiseringsuppgifter har upphört att gälla. Dessutom kan det inträffa när klienten inte automatiskt har AzureRMS-etablerat. 

**Rekommenderad åtgärd**  
I det första fallet ska användaren uppdatera sina autentiseringsuppgifter och logga in på enheten med de nya autentiseringsuppgifterna. Lös AzureRMS-problemet genom att gå vidare med stegen i [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Händelse-ID 1098: Fel: 0xCAA5001C Token broker-åtgärden misslyckades  

I Loggboken under AAD/Operational logs kan det här felet ses med Händelse 1104: AAD Cloud AP plugin-anrop Hämta token returnerat fel: 0xC000005F. Det här problemet uppstår om det saknas behörigheter eller ägarskapsattribut.  

**Rekommenderad åtgärd**  
Fortsätt med stegen i listan [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Nästa steg

En översikt finns i [roaming översikt över företagstillstånd](enterprise-state-roaming-overview.md).
