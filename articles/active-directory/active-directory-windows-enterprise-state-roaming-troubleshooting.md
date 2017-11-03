---
title: "Felsökning av Enterprise tillstånd centrala inställningar i Azure Active Directory | Microsoft Docs"
description: "Ger svar på frågor IT-administratörer kan ha om inställningar och data appsynkronisering."
services: active-directory
keywords: "Enterprise tillstånd centrala inställningar för windows-moln, vanliga frågor och svar för enterprise tillstånd centrala"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: ed25e6b922321fd4d8852860ad8817dc318d89ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Felsökning av Enterprise tillstånd centrala inställningar i Azure Active Directory

Det här avsnittet innehåller information om hur du felsöker och diagnostisera problem med Enterprise tillstånd centrala och visar en lista över kända problem.

## <a name="preliminary-steps-for-troubleshooting"></a>Förberedande steg för felsökning 
Kontrollera att användare och enhet har konfigurerats korrekt och att alla krav för Enterprise tillstånd nätverksväxling uppfylls av enheten och användaren innan du startar felsökning. 

1. Windows 10 installeras med de senaste uppdateringarna och en lägsta Version 1511 (OS-version 10586 eller senare) på enheten. 
2. Enheten är Azure AD ansluten eller Azure AD-hybridlösning anslutna. Mer information finns i [hur du registrerar en enhet under kontroll av Azure AD](device-management-introduction.md).
3. Se till att **Enterprise tillstånd centrala** har aktiverats för klienten i Azure AD som beskrivs i [att aktivera Enterprise tillstånd centrala](active-directory-windows-enterprise-state-roaming-enable.md). Du kan aktivera centrala för alla användare eller för en markerad grupp med användare.
4. Användaren måste redan tilldelats en Azure Active Directory Premium-licens.  
25. Du måste starta om enheten och användaren måste logga in igen på åtkomst till företagets tillstånd centrala funktioner.

## <a name="information-to-include-when-you-need-help"></a>Information som ska inkluderas när du behöver hjälp
Om du inte kan lösa problemet med riktlinjerna nedan kan du kontakta vår supportpersonal. När du kontaktar dem med följande information:

* **Allmän beskrivning av felet**: finns det felmeddelanden som visas för användaren? Om det fanns inget felmeddelande, Beskriv de oväntade resultat som du tänkt i detalj. Vilka funktioner är aktiverade för synkronisering och vad är den användare som väntar på att synkronisera? Är den isolerat till en eller flera funktioner inte synkroniserar?
* **Användare som påverkas** – är synkronisering fungerar/misslyckas för en användare eller flera användare? Hur många enheter ingår per användare? Dem inte synkroniserar alla eller några av dem synkroniserar och vissa inte synkroniserar?
* **Information om användaren** – vilka identiteten är användaren använder för att logga in på enheten? Hur användaren loggar in på enheten? De är en del av en vald säkerhetsgrupp som tillåts synkronisera? 
* **Information om enheten** – är Azure AD-ansluten eller ansluten till domänen för den här enheten? Vilken version är enheten på? Vilka är de senaste uppdateringarna?
- **Datum / tid / tidszonen** – vad var exakt datum och tid som du såg felet (inklusive tidszonen)?

Inklusive den här informationen hjälper oss att lösa problemet så snabbt som möjligt.

## <a name="troubleshooting-and-diagnosing-issues"></a>Felsökning och diagnos av problem
Det här avsnittet innehåller förslag på hur du felsöker och diagnostisera problem relaterade till Enterprise tillstånd nätverksväxling.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Kontrollera synkronisering och inställningssidan ”synkronisera dina inställningar” 

1. När du ansluter till din Windows 10-dator till en domän som är konfigurerad för att tillåta företag tillstånd centrala måste logga in med ditt arbetskonto. Gå till **inställningar** > **konton** > **din synkroniseringsinställningar** och bekräfta att synkronisera och de enskilda inställningarna finns på och att upp på inställningssidan indikerar att du synkroniserar med ditt arbetskonto. Bekräfta samma konto används också som ditt inloggningskonto i **inställningar** > **konton** > **din Info**. 
2. Kontrollera att synkronisering fungerar på flera datorer genom att göra några ändringar på den ursprungliga datorn, till exempel flytta Aktivitetsfältet till höger eller övre sida av skärmen. Titta på ändringen sprids till den andra datorn inom fem minuter. 
  * Låsa och låsa upp skärmen (Win + L) hjälper till att utlösa en synkronisering.
  * Du måste vara att logga in med samma konto på båda datorerna för synkronisering fungera – som Enterprise tillstånd nätverksväxling är knutna till användarkontot och inte datorkontot.

**Potentiella problem**: Om kontrollerna i den **inställningar** sidan är inte tillgängliga och meddelandet ”vissa Windows-funktioner är endast tillgängliga om du använder ett Microsoft-konto eller arbetskonto”. Det här problemet kan uppstå för enheter som ska vara domänansluten och registrerad på Azure AD, men enheten har inte ännu har autentiserats till Azure AD. En möjlig orsak är att enhetsprincip måste användas, men det här programmet sker asynkront, och kan vara fördröjd med några timmar. 

### <a name="verify-the-device-registration-status"></a>Kontrollera registreringsstatus enhet
Enterprise tillstånd centrala kräver att enheten registreras med Azure AD. Även om det är inte specifik för Enterprise tillstånd Roaming, följande anvisningar hjälper dig att bekräfta att Windows 10-klient är registrerad och bekräfta tumavtrycket URL för Azure AD-inställningar, ngc, Rapportera status och annan information.

1.  Öppna Kommandotolken upphöjt. Öppna kör programstart (Win + R) för att göra detta i Windows, och Skriv ”cmd” att öppna.
2.  När det är öppna Kommandotolken, Skriv ”*dsregcmd.exe/status*”.
3.  För utdata som förväntas av **AzureAdJoined** fältvärdet ska vara ”Ja” **WamDefaultSet** fältvärdet ska vara ”YES”, och **WamDefaultGUID** fältvärdet ska vara ett GUID med ”(AzureAd)” i slutet.

**Potentiella problem**: **WamDefaultSet** och **AzureAdJoined** både har ”ingen” i fältvärdet enheten var ansluten till domänen och registrerad med Azure AD och synkroniserar inte enheten. Om det visas den här enheten kan behöva vänta på att principen tillämpas eller autentiseringen för enheten misslyckades vid anslutning till Azure AD. Användaren kan behöva vänta några timmar för principen ska tillämpas. Andra felsökningssteg kan omfatta du försöker automatisk registrering genom att logga ut och in igen eller starta om aktiviteten i Schemaläggaren. I vissa fall, kör ”*dsregcmd.exe /leave*” i ett Kommandotolk-fönster startas om och försök registrera igen får hjälp med problemet.


**Potentiella problem**: fältet för **AzureAdSettingsUrl** är tom och synkroniserar inte enheten. Användaren kan ha senast loggade in till enheten innan Enterprise tillstånd nätverksväxling har aktiverats i Azure Active Directory-portalen. Starta om enheten och har användarinloggning. Försök eventuellt med IT-administratören inaktivera och återaktivera användare kan synkroniseringsinställningar och Data för Enterprise-App i portalen. En gång aktiveras, starta om enheten och har användarinloggning. Om detta inte löser problemet, **AzureAdSettingsUrl** kan vara tom när det gäller ett felaktigt certifikat. I det här fallet kör ”*dsregcmd.exe /leave*” i ett Kommandotolk-fönster startas om och försök registrera igen får hjälp med problemet.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise tillstånd centrala och Multifaktorautentisering 
Under vissa förhållanden Enterprise tillstånd centrala kan det hända att synkronisera data om Azure Multi-Factor Authentication har konfigurerats. För mer information om problemen finns stöd för dokumentet [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potentiella problem**: om enheten konfigureras för att kräva Multifaktorautentisering i Azure Active Directory-portalen kan du kanske inte kan synkronisera inställningar när du loggar in på en Windows 10-enhet med ett lösenord. Den här typen av Multi-Factor Authentication-konfigurationen är avsedd att skydda en administratör för Azure-konto. Administratörer kan fortfarande att kunna synkronisera genom att logga in till sina Windows 10-enheter med sina Microsoft Passport för arbetsplats PIN-kod eller genom att fylla i Multi-Factor Authentication vid åtkomst till andra Azure-tjänster som Office 365.

**Potentiella problem**: synkronisering kan misslyckas om administratören konfigurerar principen för villkorlig åtkomst för Active Directory Federation Services Multifaktorautentisering och åtkomsttoken på enheten upphör att gälla. Se till att du logga in och logga ut med Microsoft Passport för arbetsplats PIN-kod eller slutföra Multifaktorautentisering vid åtkomst till andra Azure-tjänster som Office 365.

###<a name="event-viewer"></a>Loggboken
För avancerad felsökning kan Loggboken användas för att söka efter specifika fel. Dessa beskrivs i tabellen nedan. Händelser kan hittas under Loggboken > program- och tjänstloggar > **Microsoft** > **Windows** > **SettingSync** och identity-relaterade problem med synkronisering av **Microsoft** > **Windows** > **Azure AD**.


## <a name="known-issues"></a>Kända problem

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synkronisering fungerar inte på enheter som har appar sidoladdad via MDM-programvara

Påverkar enheter som kör Windows 10 årsdagar Update (Version 1607). I Loggboken under SettingSync Azure loggar visas händelse-ID 6013 med fel 80070259 ofta.

**Rekommenderad åtgärd**  
Kontrollera att Windows 10 v1607-klient har 23 augusti 2016 kumulativa uppdateringen ([KB3176934](https://support.microsoft.com/kb/3176934) OS skapa 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Favoriter i Internet Explorer är inte synkroniserade

Påverkar enheter som kör Windows 10 November Update (Version 1511).

**Rekommenderad åtgärd**  
Kontrollera att Windows 10 v1511-klient har juli 2016 kumulativa uppdateringen ([KB3172985](https://support.microsoft.com/kb/3172985) OS skapa 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Temat synkroniserar inte, samt data som skyddas av Windows informationsskydd 

För att förhindra dataläckor, data som skyddas med [Windows informationsskydd](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) kommer inte att synkroniseras via Enterprise tillstånd Roaming för enheter med Windows 10 årsdagar Update.



**Rekommenderad åtgärd**  
Ingen. Framtida uppdateringar för Windows kanske kan lösa problemet.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Inställningar för datum, tid och regionen är inte synkroniserade på domänansluten enhet 
  
Enheter som är anslutna till en domän får inte synkronisering för datum, tid och Region: automatisk tid. Använda automatisk tid kan åsidosätta andra datum, tid och Region inställningarna och göra de inställningarna som inte ska synkroniseras. 

**Rekommenderad åtgärd**  
Ingen. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC uppmanar när synkroniserar lösenord

Påverkar enheter som kör Windows 10 November Update (Version 1511) med ett trådlöst nätverkskort som är konfigurerad för att synkronisera lösenord.

**Rekommenderad åtgärd**  
Kontrollera att Windows 10 v1511-klient har den kumulativa uppdateringen ([KB3140743](https://support.microsoft.com/kb/3140743) OS skapa 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synkronisering fungerar inte på enheter som använder smartkort för inloggning
Om du försöker logga in på din Windows-enhet med ett smartkort eller virtuellt smartkort att synkronisera inställningar för sluta fungera.     

**Rekommenderad åtgärd**  
Ingen. Framtida uppdateringar för Windows kanske kan lösa problemet.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Domänansluten enhet synkroniserar inte efter lämnar företagets nätverk     
Domänanslutna enheter som registrerats till Azure AD kan uppstå synkroniseringfel vid om enheten är utanför kontoret för längre tid och domänautentisering kan inte slutföras.

**Rekommenderad åtgärd**  
Ansluta enheten till ett företagsnätverk så att synkroniseringen kan återupptas.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD-ansluten enhet synkroniserar inte och användaren har en blandad case UPN-namnet.
 Om användaren har ett UPN (t.ex. användarnamn i stället för användarnamn) med blandat skiftläge och användaren är på en Azure AD-ansluten enhet som har uppgraderats från Windows 10 Build 10586 till 14393, misslyckas användarens enhet ska synkroniseras. 

**Rekommenderad åtgärd**  
Användaren behöver frånkoppling från och ansluta enheten till molnet. För att göra det loggar du in som lokal administratör och frånkoppling från enheten genom att gå till **inställningar** > **System** > **om** och välj ”hantera eller koppla från arbetet eller skolan”. Rensa filerna nedan och Azure AD Join enheten igen i **inställningar** > **System** > **om** och välja ”ansluta till arbetet eller skolan”. Fortsätta att ansluta enheten till Azure Active Directory och slutföra flödet.

I steget rensning rensa följande filer:
- Settings.dat i`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Alla filer under mappen`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Händelse-ID 6065:80070533 den här användaren inte logga in eftersom det här kontot är inaktiverat  
I Loggboken under SettingSync/Debug-loggar, kan det här felet visas när användarens autentiseringsuppgifter har upphört att gälla. Dessutom kan kan det uppstå när klienten inte har automatiskt AzureRMS etableras. 

**Rekommenderad åtgärd**  
I det första fallet behörighet att uppdatera sina autentiseringsuppgifter och logga in till enheten med de nya autentiseringsuppgifterna. För att lösa problemet AzureRMS, Fortsätt med steg som anges i [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Händelse-ID 1098: Fel: 0xCAA5001C Token broker åtgärden misslyckades  
I Loggboken under AAD/Operational loggar felet kan visas med händelsen 1104: AAD moln AP plugin-anropet Get-token returnerade fel: 0xC000005F. Det här problemet uppstår om det saknar behörighet eller ägarskap attribut.  

**Rekommenderad åtgärd**  
Fortsätt med steg visas [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>Nästa steg

- Använd den [User Voice forum](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) att ge feedback och förslag på hur vi kan förbättra Enterprise tillstånd nätverksväxling.

- Mer information finns i [företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Relaterade ämnen
* [Övergripande översikt över Enterprise tillstånd](active-directory-windows-enterprise-state-roaming-overview.md)
* [Aktivera företagsroaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Inställningar och dataroaming vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Gruppen principer och MDM-inställningar för synkronisering av inställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Centrala referens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
