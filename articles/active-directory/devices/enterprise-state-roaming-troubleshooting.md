---
title: Felsöka Enterprise State Roaming-inställningar i Azure Active Directory | Microsoft Docs
description: Ger svar på frågor IT-administratörer kan ha om inställningar och data appsynkronisering.
services: active-directory
keywords: Enterprise state roaminginställningarna, windows-molnet, vanliga frågor och svar på enterprise tillståndsväxling
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: f1547e424805358cf700930f087fb4c900998f02
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094010"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Felsöka Enterprise State Roaming-inställningar i Azure Active Directory

Det här avsnittet innehåller information om hur du felsöker och diagnostisera problem med Enterprise State Roaming och innehåller en lista över kända problem.

## <a name="preliminary-steps-for-troubleshooting"></a>Preliminära steg för felsökning 

Innan du startar felsökning, kontrollera att användare och enhet har konfigurerats korrekt och att alla krav hos Enterprise State Roaming uppfylls av enheten och användaren. 

1. Windows 10, som med de senaste uppdateringarna och en lägsta Version 1511 (OS skapa 10586 eller senare) är installerad på enheten. 
1. Enheten är Azure AD ansluten eller Azure AD-anslutna. Mer information finns i [så här hämtar du en enhet som kontrolleras av Azure AD](overview.md).
1. Se till att **Enterprise State Roaming** är aktiverat för klienten i Azure AD, enligt beskrivningen i [att aktivera Enterprise Tillståndsväxling](enterprise-state-roaming-enable.md). Du kan aktivera centrala för alla användare eller för en vald grupp av användare.
1. Användaren måste redan ha tilldelats en Azure Active Directory Premium-licens.  
1. Du måste starta om enheten och användaren måste logga in igen att komma åt Enterprise State Roaming funktionerna.

## <a name="information-to-include-when-you-need-help"></a>Information som ska ingå när du behöver hjälp
Om du inte kan lösa problemet med riktlinjerna nedan kan du kontakta vår support-tekniker. När du kontaktar dem ska du inkludera följande information:

* **Allmän beskrivning av felet**: Finns det några felmeddelanden som ses av användaren? Om det fanns inget felmeddelande, Beskriv de oväntade resultat som du såg i detalj. Vilka funktioner är aktiverade för synkronisering och vad är den användare som förväntar sig att synkronisera? Flera funktioner inte synkroniserar eller isolerade till en?
* **Användare som påverkas** – är sync fungerande/misslyckas för en användare eller flera användare? Hur många enheter ingår per användare? Dem inte synkroniserar alla eller vissa av dem synkroniseras och vissa inte synkroniserar?
* **Information om användaren** – vilka identiteten är användaren med att logga in på enheten? Hur användaren loggar in på enheten? De är en del av en vald säkerhetsgrupp som tillåts synkronisera? 
* **Information om enheten** – är den här enheten av Azure AD-anslutna eller domänansluten? Vilken build är enheten på? Vilka är de senaste uppdateringarna?
- **Datum / tid / tidszon** – vad var exakt datum och tid som du såg felet (inkludera tidszonen)?

Inklusive den här informationen hjälper oss att lösa problemet så snabbt som möjligt.

## <a name="troubleshooting-and-diagnosing-issues"></a>Felsökning och diagnostisera problem
Det här avsnittet innehåller förslag på hur du felsöker och diagnostisera problem som rör Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Kontrollera synkronisering och inställningssidan ”synkronisera dina inställningar” 

1. När du ansluter din Windows 10-dator till en domän som är konfigurerad för att tillåta Enterprise State Roaming, logga in med ditt arbetskonto. Gå till **inställningar** > **konton** > **Your synkroniseringsinställningar** och bekräfta att synkronisera och de enskilda inställningarna finns på, och att överst i inställningssidan anger att du synkroniserar med ditt arbetskonto. Bekräfta samma konto används också som ett inloggningskonto i **inställningar** > **konton** > **Your Info**. 
1. Kontrollera att synkroniseringen fungerar över flera datorer genom att göra några ändringar på den ursprungliga datorn, till exempel flytta Aktivitetsfältet till höger eller längst upp på skärmen. Titta på ändringen sprids till den andra datorn inom fem minuter. 

  * Låsa och låsa upp skärmen (Win + L) kan utlösa en synkronisering.
  * Du måste logga in med samma konto på båda datorerna för synkronisering fungera – som Enterprise State Roaming är knuten till användarkontot och inte datorkontot.

**Potentiella problem**: Om kontrollerna i den **inställningar** sidan är inte tillgängliga och du ser meddelandet ”vissa Windows-funktioner är endast tillgängliga om du använder ett Microsoft-konto eller ett arbetskonto”. Det här problemet kan uppstå för enheter som ska vara domänansluten och registrerad till Azure AD, men enheten har inte ännu har autentiserats med Azure AD. En möjlig orsak är att Enhetsprincipen måste användas, men det här programmet sker asynkront och kan fördröjas med några timmar. 

### <a name="verify-the-device-registration-status"></a>Kontrollera status på enhetsregistreringen

Enterprise State Roaming kräver att enheten registreras med Azure AD. Även om det är inte specifik för Enterprise State Roaming, följa anvisningarna nedan kan hjälpa att bekräfta att Windows 10-klient är registrerad och bekräfta tumavtryck URL för Azure AD-inställningar, NGC status och annan information.

1.  Öppna Kommandotolken utan behörighet. Om du vill göra detta i Windows, öppna kör startprogrammet (Win + R) och Skriv ”cmd” öppna.
2.  När Kommandotolken är öppet, skriver du ”*dsregcmd.exe/status*”.
3.  För utdata som förväntas i **AzureAdJoined** fältvärdet ska vara ”Ja” **WamDefaultSet** fältvärdet ska vara ”Ja” och **WamDefaultGUID** fältvärdet ska vara ett GUID med ”(AzureAd)” i slutet.

**Potentiella problem**: **WamDefaultSet** och **AzureAdJoined** både har ”Nej” i fältvärdet enheten var ansluten till domänen och registrerad med Azure AD och synkroniserar inte enheten. Om den visar detta, enheten kan behöva vänta på att principen tillämpas det gick inte att autentiseringen för enheten när du ansluter till Azure AD Användaren kan behöva vänta några timmar innan principen tillämpas. Andra åtgärder för felsökning kan omfatta försöker automatisk registrering genom att logga ut och in igen eller starta om aktiviteten i Schemaläggaren. I vissa fall kan köra ”*dsregcmd.exe /leave*” i en upphöjd kommandotolk, starta om och försök registrera igen kan bidra med det här problemet.


**Potentiella problem**: Fältet för **SettingsUrl** är tom och synkroniserar inte enheten. Användaren kan ha senast inloggad till enheten innan Enterprise State Roaming aktiverades i Azure Active Directory-portalen. Starta om enheten och har användarinloggning. Du kan också prova att gå till IT-administratören i portalen **Azure Active Directory** > **enheter** > **Enterprise State Roaming** inaktivera och återaktivera **användarna kan synkronisera inställningar och AppData på enheter**. En gång återaktiveras, starta om enheten och har användarinloggning. Om detta inte löser problemet, **SettingsUrl** kan vara tom när det gäller ett felaktigt certifikat. I det här fallet kör ”*dsregcmd.exe /leave*” i en upphöjd kommandotolk, starta om och försök registrera igen kan bidra med det här problemet.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming och Multi-Factor Authentication 

Enterprise State Roaming kan misslyckas under vissa förhållanden synkroniserar data om Azure Multi-Factor Authentication har konfigurerats. Mer information om problemen finns i dokumentet support [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potentiella problem**: Om enheten är konfigurerad för att kräva Multifaktorautentisering på Azure Active Directory-portalen, kan du inte synkronisera inställningar när du loggar in på en Windows 10-enheten med ett lösenord. Den här typen av Multi-Factor Authentication-konfigurationen är avsedd att skydda en Azure-administratörskonto. Administrativa användare kan fortfarande att kunna synkronisera genom att logga in på sina Windows 10-enheter med sina Microsoft Passport för arbete PIN-kod eller genom att fylla i Multi-Factor Authentication vid åtkomst till andra Azure-tjänster som Office 365.

**Potentiella problem**: Synkroniseringen kan misslyckas om en administratör konfigurerar principen för villkorlig åtkomst för Active Directory Federation Services Multi-Factor-autentisering och åtkomst-token på enheten går ut. Se till att du loggar in och logga ut med hjälp av Microsoft Passport för arbete PIN-kod eller slutföra Multifaktorautentisering vid åtkomst till andra Azure-tjänster som Office 365.

### <a name="event-viewer"></a>Loggboken

För avancerad felsökning, kan Loggboken användas för att hitta specifika fel. Dessa finns dokumenterade i tabellen nedan. Händelser finns i Loggboken > Applications and Services Logs > **Microsoft** > **Windows** > **SettingSync Azure** och identitetsrelaterade problem med synkronisering av **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Kända problem

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synkronisering fungerar inte på enheter som har appar som sidoladdad med hjälp av MDM-programvara

Påverkar enheter som kör Windows 10 Anniversary Update (Version 1607). Händelse-ID 6013 felmeddelande 80070259 visas ofta i Loggboken under SettingSync Azure-loggarna.

**Rekommenderad åtgärd**  
Kontrollera att klienten för Windows 10-v1607 har 23 augusti 2016 samlingsuppdatering ([KB3176934](https://support.microsoft.com/kb/3176934) OS skapa 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Favoriter i Internet Explorer är inte synkroniserade

Påverkar enheter som kör Windows 10 November Update (Version 1511).

**Rekommenderad åtgärd**  
Kontrollera att klienten för Windows 10-v1511 har juli 2016 samlingsuppdatering ([KB3172985](https://support.microsoft.com/kb/3172985) OS skapa 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Tema synkroniserar inte, samt data som skyddas med Windows informationsskydd 

Att förhindra dataläckor, data som skyddas med [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) kommer inte att synkronisera via Enterprise State Roaming för enheter med Windows 10 Anniversary Update.

**Rekommenderad åtgärd**  
Ingen. Framtida uppdateringar av Windows kan lösa problemet.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Inställningar för datum, tid och regionen är inte synkroniserade på domänansluten enhet 
  
Enheter som är anslutna till en domän får inte synkronisering för inställningen datum, tid och Region: automatiska tiden. Använda automatisk tid kan åsidosätta de andra datum, tid och Region inställningarna och orsaka dessa inställningar inte att synkronisera. 

**Rekommenderad åtgärd**  
Ingen. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC uppmanar när synkroniserar lösenord

Påverkar enheter som kör Windows 10 November Update (Version 1511) med ett trådlösa nätverkskort som är konfigurerad för att synkronisera lösenord.

**Rekommenderad åtgärd**  
Kontrollera att klienten för Windows 10-v1511 har den kumulativa uppdateringen ([KB3140743](https://support.microsoft.com/kb/3140743) OS skapa 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synkronisering fungerar inte på enheter som använder smartkort för inloggning

Om du försöker logga in på din Windows-enhet med ett smartkort eller virtuellt smartkort, att synkronisera inställningar för sluta fungera.     

**Rekommenderad åtgärd**  
Ingen. Framtida uppdateringar av Windows kan lösa problemet.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Domänansluten enhet synkroniserar inte när du lämnar företagets nätverk     

Domänanslutna enheter som registrerats till Azure AD kan uppstå synkroniseringfel vid om enheten är på annan plats för längre tid och domänautentisering kan inte slutföras.

**Rekommenderad åtgärd**  
Anslut enheten till ett företagsnätverk så att synkronisering kan återupptas.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD har anslutits enheten synkroniseras inte och användaren har en blandad fall UPN-namnet.

Om användaren har ett UPN (t.ex. användarnamn i stället för användarnamn) med blandat skiftläge och att användaren finns på en Azure AD har anslutits-enhet som har uppgraderats från Windows 10 skapa 10586 till 14393, misslyckas användarens enhet ska synkroniseras. 

**Rekommenderad åtgärd**  
Användaren behöver frånkoppling från och återansluta till enheten till molnet. Att göra det loggar du in som lokal administratör och frånkoppling från enheten genom att gå till **inställningar** > **System** > **om** och välj ”Hantera eller koppla från arbetsplats eller skola ”. Rensa filerna nedan och Azure AD Join enheten igen i **inställningar** > **System** > **om** och välja ”Anslut till arbete eller School ”. Fortsätta att ansluta enheten till Azure Active Directory och slutför flödet.

I steg rensning, rensa följande filer:
- Settings.dat i `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Alla filer i mappen `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Event ID 6065: 80070533 den här användaren kan inte logga in eftersom det här kontot är inaktiverat  

I Loggboken under SettingSync/felsökningsloggar, kan det här felet visas när användarens autentiseringsuppgifter har upphört att gälla. Dessutom kan det inträffa när klienten inte har automatiskt AzureRMS som etablerats. 

**Rekommenderad åtgärd**  
I det första fallet har användaren uppdatera sina autentiseringsuppgifter och logga in på enheten med de nya autentiseringsuppgifterna. För att lösa problemet AzureRMS, fortsätter du med stegen i [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Event ID 1098: Fel: 0xCAA5001C token broker-åtgärden misslyckades  

I Loggboken under AAD/Operational loggar visas det här felet med händelsen 1104: Asien och Stillahavsområdet för AAD-molnet plugin-programmet anropet Get token returnerade fel: 0xC000005F. Det här problemet uppstår om det saknar behörigheter eller ägarskap attribut.  

**Rekommenderad åtgärd**  
Fortsätter med stegen [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Nästa steg

En översikt finns i [företagsroaming översikt](enterprise-state-roaming-overview.md).
