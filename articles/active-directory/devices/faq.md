---
title: Vanliga frågor och svar om hantering av Azure Active Directory-enheter | Microsoft-dokument
description: Vanliga frågor och svar om hantering av Azure Active Directory-enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497531"
---
# <a name="azure-active-directory-device-management-faq"></a>Vanliga frågor och svar om hantering av Azure Active Directory-enheter

## <a name="general-faq"></a>Vanliga frågor och svar

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>F: Jag registrerade enheten nyligen. Varför kan jag inte se enheten under min användarinformation i Azure-portalen? Eller varför är enhetsägaren markerad som N/A för Azure Active Directory (Azure AD) anslutna enheter?

**A.** Windows 10-enheter som är hybrid-Azure AD-anslutna visas inte under **USER-enheter**.
Använd vyn **Alla enheter** i Azure-portalen. Du kan också använda en PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet.

Endast följande enheter visas under **USER-enheter:**

- Alla personliga enheter som inte är hybrid Azure AD gick. 
- Alla enheter som inte är Windows 10- eller Windows Server 2016-enheter.
- Alla enheter som inte är Windows-enheter. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>F: Hur vet jag vad enhetens registreringstillstånd för klienten är?

**A.** Gå till **alla enheter**i Azure-portalen . Sök efter enheten med hjälp av enhets-ID: et. Kontrollera värdet under kolumnen kopplingstyp. Ibland kan enheten återställas eller göras om. Så det är viktigt att även kontrollera enhetens registreringstillstånd på enheten:

- För Windows 10- och Windows Server 2016- eller senareenheter kör du `dsregcmd.exe /status`.
- För operativsystemets versioner `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`på lägre nivå kör du .

**A.** Information om felsökning finns i följande artiklar:
- [Felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md)
- [Felsöka hybrid-Azure Active Directory-anslutna till Windows 10- och Windows Server 2016-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid-Azure Active Directory-anslutna enheter på lägre nivå](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>F: Jag ser enhetsposten under USER-informationen i Azure-portalen. Och jag ser staten som registrerad på enheten. Har jag konfigurerat villkorlig åtkomst på rätt sätt?

**A.** Enhetskopplingstillståndet, som visas av **deviceID,** måste matcha tillståndet på Azure AD och uppfylla alla utvärderingskriterier för villkorlig åtkomst. Mer information finns i [Kräv hanterade enheter för molnappåtkomst med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>F: Varför visas ett felmeddelande om att "Din organisation har tagit bort enheten" eller "Din organisation har inaktiverat enheten" på sina Windows 10-enheter?

**A.** På Windows 10-enheter som är anslutna till eller registrerade med Azure AD får användarna en [primär uppdateringstoken (PRT)](concept-primary-refresh-token.md) som möjliggör enkel inloggning. Prt-enhetens giltighet baseras på själva enhetens giltighet. Användare ser det här meddelandet om enheten antingen tas bort eller inaktiveras i Azure AD utan att initiera åtgärden från själva enheten. En enhet kan tas bort eller inaktiveras i Azure AD något av följande scenarier: 

- Användaren inaktiverar enheten från portalen Mina appar. 
- En administratör (eller användare) tar bort eller inaktiverar enheten i Azure-portalen eller med hjälp av PowerShell
- Endast Hybrid Azure AD gick med: En administratör tar bort enheternas organisationsenhet utanför synkroniseringsomfånget, vilket resulterar i att enheterna tas bort från Azure AD
- Uppgradera Azure AD-anslutning till version 1.4.xx.x. [Förstå Azure AD Connect 1.4.xx.x och enhetens försvinnande](/azure/active-directory/hybrid/reference-connect-device-disappearance).


Se nedan om hur dessa åtgärder kan rättas till.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>F: Jag har inaktiverat eller tagit bort min enhet i Azure-portalen eller med Windows PowerShell. Men den lokala staten på enheten säger att den fortfarande är registrerad. Vad ska jag göra?

**A.** Den här åtgärden är avsiktligt. I det här fallet har enheten inte åtkomst till resurser i molnet. Administratörer kan utföra den här åtgärden för inaktuella, förlorade eller stulna enheter för att förhindra obehörig åtkomst. Om den här åtgärden utfördes oavsiktligt måste du återaktivera eller registrera om enheten enligt beskrivningen nedan

- Om enheten har inaktiverats i Azure AD kan en administratör med tillräckliga privilegier aktivera den från Azure AD-portalen  
  > [!NOTE]
  > Om du synkroniserar enheter med Azure AD Connect aktiveras hybrid-Azure AD-anslutna enheter automatiskt under nästa synkroniseringscykel. Så om du behöver inaktivera en hybrid Azure AD-ansluten enhet måste du inaktivera den från din lokala AD

 - Om enheten tas bort i Azure AD måste du registrera om enheten. Om du vill registrera om måste du vidta en manuell åtgärd på enheten. Se nedan för instruktioner för omregistrering baserat på enhetens tillstånd. 

      Så här registrerar du hybrid-Azure AD-enheter för Windows 10 och Windows Server 2016/2019:

      1. Öppna kommandotolken som administratör.
      1. Ange `dsregcmd.exe /debug /leave`.
      1. Logga ut och logga in för att utlösa den schemalagda aktiviteten som registrerar enheten igen med Azure AD. 

      För Windows OS-versioner på lägre nivå som är hybrid-Azure AD-anslutna:

      1. Öppna kommandotolken som administratör.
      1. Ange `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Ange `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      För Azure AD-anslutna enheter Windows 10-enheter gör du följande:

      1. Öppna kommandotolken som administratör
      1. Ange `dsregcmd /forcerecovery` (Obs: Du måste vara administratör för att utföra den här åtgärden).
      1. Klicka på "Logga in" i dialogrutan som öppnas och fortsätt med inloggningsprocessen.
      1. Logga ut och logga in tillbaka till enheten för att slutföra återställningen.

      För Azure AD-registrerade Windows 10-enheter gör du följande:

      1. Gå till **Inställningar** > **Konton** > **Access Work eller School**. 
      1. Välj kontot och välj **Koppla från**.
      1. Klicka på "+ Anslut" och registrera enheten igen genom att gå igenom inloggningsprocessen.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>F: Varför visas dubblettenhetsposter i Azure-portalen?

**A:**

- För Windows 10 och Windows Server 2016 kan upprepade försök att ta bort glädje och ansluta till samma enhet orsaka dubblettposter. 
- Varje Windows-användare som använder **Lägg till arbets- eller skolkonto** skapar en ny enhetspost med samma enhetsnamn.
- För windows os-versioner på lägre nivå som är lokala Azure Directory-domän anslutna skapar automatisk registrering en ny enhetspost med samma enhetsnamn för varje domänanvändare som loggar in på enheten. 
- En Azure AD-ansluten dator som har rensats, installerats om och återansluts med samma namn visas som en annan post med samma enhetsnamn.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>F: Stöder Windows 10-enhetsregistrering i Azure AD TPMs i FIPS-läge?

**A.** Registrering av Windows 10-enheter stöds endast för FIPS-kompatibel TPM 2.0 och stöds inte för TPM 1.2. Om dina enheter har FIPS-kompatibel TPM 1.2 måste du inaktivera dem innan du fortsätter med Azure AD-anslutning eller Hybrid Azure AD-anslutning. Microsoft tillhandahåller inga verktyg för att inaktivera FIPS-läge för TPMs eftersom det är beroende av TPM-tillverkaren. Kontakta maskinvaru-OEM-tillverkaren för support. 

---

**F: Varför kan en användare fortfarande komma åt resurser från en enhet som jag har inaktiverat i Azure-portalen?**

**A.** Det tar upp till en timme innan en återkallad tillämpas från den tidpunkt då Azure AD-enheten markeras som inaktiverad.

>[!NOTE] 
>För registrerade enheter rekommenderar vi att du rensar enheten för att se till att användarna inte kan komma åt resurserna. Mer information finns i [Vad är enhetsregistrering?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>F: Varför markeras enheter som "Väntande" under kolumnen REGISTRERAD i Azure-portalen?

**S**: Väntar anger att enheten inte är registrerad. Det här tillståndet anger att en enhet har synkroniserats med Azure AD-anslutning från en lokal AD och är klar för enhetsregistrering. Dessa enheter har JOIN TYPE inställd på "Hybrid Azure AD-ansluten". Läs mer om [hur du planerar implementeringen av hybrid-Azure Active Directory-anslutning](hybrid-azuread-join-plan.md).

>[!NOTE]
>En enhet kan också ändras från att ha ett registrerat tillstånd till "Väntande"
>* Om en enhet tas bort från Azure AD först och synkroniseras om från en lokal AD.
>* Om en enhet tas bort från ett synkroniseringsomfång på Azure AD Connect och läggs tillbaka.
>
>I båda fallen måste du registrera om enheten manuellt på var och en av dessa enheter. Om du vill granska om enheten tidigare har registrerats kan du [felsöka enheter med kommandot dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Vanliga frågor och svar om Azure AD-koppling

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>F: Hur tar jag loss en Azure AD-ansluten enhet lokalt på enheten?

**A.** För rena Azure AD-anslutna enheter kontrollerar du att du har ett lokalt administratörskonto offline eller skapar ett. Du kan inte logga in med några Azure AD-användarautentiseringsuppgifter. Gå sedan till **Inställningar** > **Konton** > **Access Work eller School**. Välj ditt konto och välj **Koppla från**. Följ anvisningarna och ange de lokala administratörsautentiseringsuppgifterna när du uppmanas att göra det. Starta om enheten för att slutföra unjoin-processen.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>F: Kan mina användare logga in på Azure AD-anslutna enheter som tas bort eller inaktiveras i Azure AD?

**S:** Ja. Windows har en cachelagrad användarnamn och lösenord kapacitet som tillåter användare som loggat in tidigare för att komma åt skrivbordet snabbt även utan nätverksanslutning. 

När en enhet tas bort eller inaktiveras i Azure AD är det inte känt för Windows-enheten. Så användare som loggat in tidigare fortsätter att komma åt skrivbordet med det cachelagrade användarnamnet och lösenordet. Men eftersom enheten tas bort eller inaktiveras kan användarna inte komma åt några resurser som skyddas av enhetsbaserad villkorlig åtkomst. 

Användare som inte loggat in tidigare kan inte komma åt enheten. Det finns inget cachelagrat användarnamn och lösenord aktiverat för dem. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>F: Kan en inaktiverad eller borttagen användare logga in på en Azure AD-anslutna enheter

**A.** Ja, men bara under en begränsad tid. När en användare tas bort eller inaktiveras i Azure AD är det inte omedelbart känt för Windows-enheten. Så användare som loggat in tidigare kan komma åt skrivbordet med cachelagrade användarnamn och lösenord. 

Vanligtvis är enheten medveten om användartillståndet på mindre än fyra timmar. Sedan blockerar Windows dessa användares åtkomst till skrivbordet. När användaren tas bort eller inaktiveras i Azure AD återkallas alla deras token. Så de kan inte komma åt några resurser. 

Borttagna eller inaktiverade användare som inte loggat in tidigare kan inte komma åt en enhet. Det finns inget cachelagrat användarnamn och lösenord aktiverat för dem. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>F: Varför har mina användare problem på Azure AD-anslutna enheter efter att de har ändrat sitt UPN?

**A.** För närvarande stöds inte UPN-ändringar fullt ut på Azure AD-anslutna enheter. Så deras autentisering med Azure AD misslyckas efter deras UPN-ändringar. Som ett resultat har användarna SSO och villkorlig åtkomst problem på sina enheter. För närvarande måste användarna logga in på Windows via panelen "Annan användare" med hjälp av sitt nya UPN för att lösa problemet. Vi arbetar för närvarande med att ta itu med denna fråga. Användare som loggar in med Windows Hello för företag tar dock inte upp det här problemet. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>F: Mina användare kan inte söka på skrivare från Azure AD-anslutna enheter. Hur aktiverar jag utskrift från dessa enheter?

**A.** Information om hur du distribuerar skrivare för Azure [AD-anslutna](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)enheter finns i Distribuera Windows Server Hybrid Cloud Print med förautentisering . Du behöver en lokal Windows Server för att distribuera hybridmolnutskrift. För närvarande är molnbaserad utskriftstjänst inte tillgänglig. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>F: Hur ansluter jag till en fjärrenhet som anslöt till Azure AD?

**A.** Se [Anslut till fjärr-Azure Active Directory-ansluten dator](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>F: Varför ser mina användare *Du kan inte komma dit härifrån?*

**A.** Har du konfigurerat vissa regler för villkorlig åtkomst för att kräva ett visst enhetstillstånd? Om enheten inte uppfyller kriterierna blockeras användarna och de ser meddelandet. Utvärdera principreglerna för villkorlig åtkomst. Kontrollera att enheten uppfyller kriterierna för att undvika meddelandet.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>F: Varför får inte några av mina användare Azure Multi-Factor Authentication-uppmaningar på Azure AD-anslutna enheter?

**A.** En användare kan gå med i eller registrera en enhet med Azure AD med hjälp av multifaktorautentisering. Då själva enheten blir en betrodd andra faktor för den användaren. När samma användare loggar in på enheten och kommer åt ett program, anser Azure AD enheten som en andra faktor. Det gör det möjligt för användaren att sömlöst komma åt program utan ytterligare multifaktorautentiseringsmeddelanden. 

Detta beteende:

- Gäller azure AD-anslutna och Azure AD-registrerade enheter - men inte för hybrid Azure AD-anslutna enheter.
- Gäller inte för någon annan användare som loggar in på den enheten. Så alla andra användare som kommer åt den enheten får en multifaktorautentisering utmaning. Sedan kan de komma åt program som kräver multifaktorautentisering.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>F: Varför får jag ett *användarnamn eller lösenord är felaktigt* meddelande för en enhet som jag just gick med i Azure AD?

**A.** Vanliga orsaker till det här scenariot är följande:

- Dina användaruppgifter är inte längre giltiga.
- Datorn kan inte kommunicera med Azure Active Directory. Sök efter problem med nätverksanslutningen.
- Federerade inloggningar kräver att federationsservern stöder WS-Trust-slutpunkter som är aktiverade och tillgängliga. 
- Du har aktiverat direktautentisering. Så ditt tillfälliga lösenord måste ändras när du loggar in.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>F: Varför ser jag *Oops ... ett fel uppstod!* när jag försöker Azure AD ansluta till min dator?

**A.** Det här felet inträffar när du konfigurerar Azure Active Directory-registrering med Intune. Kontrollera att användaren som försöker ansluta till Azure AD har rätt Intune-licens tilldelad. Mer information finns i [Konfigurera registrering för Windows-enheter](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>F: Varför misslyckades mitt försök att Azure AD ansluta till en dator, även om jag inte fick någon felinformation?

**A.** En trolig orsak är att du loggade in på enheten med hjälp av det lokala inbyggda administratörskontot. Skapa ett annat lokalt konto innan du använder Azure Active Directory-koppling för att slutföra installationen. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>F:Vilka ms-organisations-P2P-Access-certifikat finns på våra Windows 10-enheter?

**A.** MS-Organization-P2P-Access-certifikaten utfärdas av Azure AD till båda, Azure AD-anslutna och hybrid-Azure AD-anslutna enheter. Dessa certifikat används för att aktivera förtroende mellan enheter i samma klient för fjärrskrivbordsscenarier. Ett certifikat utfärdas till enheten och ett annat utfärdas till användaren. Enhetscertifikatet finns `Local Computer\Personal\Certificates` i och gäller i en dag. Det här certifikatet förnyas (genom att utfärda ett nytt certifikat) om enheten fortfarande är aktiv i Azure AD. Användarcertifikatet finns `Current User\Personal\Certificates` i och det här certifikatet är också giltigt i en dag, men det utfärdas på begäran när en användare försöker en fjärrskrivbordssession till en annan Azure AD-ansluten enhet. Det förnyas inte vid utgången. Båda dessa certifikat utfärdas med ms-Organization-P2P-Access-certifikatet `Local Computer\AAD Token Issuer\Certificates`som finns i . Det här certifikatet utfärdas av Azure AD under enhetsregistrering. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>F: Varför ser jag flera utgångna certifikat som utfärdats av MS-Organization-P2P-Access på våra Windows 10-enheter? Hur tar jag bort dem?

**A.** Det uppstod ett problem som identifierades i Windows 10 version 1709 och lägre där utgångna MS-Organization-P2P-Access-certifikat fortsatte att finnas i datorarkivet på grund av kryptografiska problem. Användarna kan få problem med nätverksanslutningen om du använder vpn-klienter (till exempel Cisco AnyConnect) som inte kan hantera det stora antalet utgångna certifikat. Det här problemet åtgärdades i Windows 10 1803-versionen för att automatiskt ta bort sådana utgångna MS-Organization-P2P-Access-certifikat. Du kan lösa problemet genom att uppdatera dina enheter till Windows 10 1803. Om du inte kan uppdatera kan du ta bort dessa certifikat utan någon negativ inverkan.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Vanliga frågor och svar om Hybrid Azure AD-koppling

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>F: Hur tar jag loss en Hybrid Azure AD-ansluten enhet lokalt på enheten?

**A.** För hybrid Azure AD-anslutna enheter, se till att stänga av automatisk registrering. Då registrerar inte den schemalagda aktiviteten enheten igen. Öppna sedan en kommandotolk som `dsregcmd.exe /debug /leave`administratör och ange . Eller kör det här kommandot som ett skript över flera enheter för att ta bort glädje i grupp.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>F: Var hittar jag felsökningsinformation för att diagnostisera hybridfel för Azure AD-koppling?

**A.** Information om felsökning finns i följande artiklar:

- [Felsöka hybrid-Azure Active Directory-anslutna till Windows 10- och Windows Server 2016-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid-Azure Active Directory-anslutna enheter på lägre nivå](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>F: Varför ser jag en dubblett-Registrerad Azure AD-post för min Windows 10 hybrid Azure AD-anslutna enhet i Listan över Azure AD-enheter?

**A.** När användarna lägger till sina konton i appar på en domänansluten enhet kan de bli tillfrågade med **Lägg till konto i Windows?** Om de anger **Ja** i prompten registreras enheten med Azure AD. Förtroendetypen är markerad som Azure AD registrerad. När du har aktiverat hybrid-Azure AD-koppling i din organisation får enheten också hybrid Azure AD ansluten. Sedan visas två enhetstillstånd för samma enhet. 

Hybrid Azure AD-koppling har företräde framför det Azure AD-registrerade tillståndet. Så din enhet anses hybrid Azure AD gick för autentisering och villkorlig åtkomst utvärdering. Du kan ta bort Azure AD-registrerade enhetsposten från Azure AD-portalen. Lär dig att [undvika eller rensa det här dubbla tillståndet på Windows 10-datorn](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>F: Varför har mina användare problem på Azure AD-enheter med Windows 10 hybrid-anslutna enheter efter att de har ändrat sitt UPN?

**A.** För närvarande stöds inte UPN-ändringar fullt ut med hybrid Azure AD-anslutna enheter. Användare kan logga in på enheten och komma åt sina lokala program, men autentisering med Azure AD misslyckas efter en UPN-ändring. Som ett resultat har användarna SSO och villkorlig åtkomst problem på sina enheter. För närvarande måste du ta av dig enheten från Azure AD (kör "dsregcmd /leave" med förhöjda privilegier) och gå med (sker automatiskt) för att lösa problemet. Vi arbetar för närvarande med att ta itu med denna fråga. Användare som loggar in med Windows Hello för företag tar dock inte upp det här problemet. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>F: Kräver Windows 10 hybrid Azure AD-anslutna enheter siktlinje till domänkontrollanten för att få åtkomst till molnresurser?

**A.** Nej, förutom när användarens lösenord ändras. När Windows 10 hybrid Azure AD-anslutning är klar och användaren har loggat in minst en gång, kräver enheten inte siktlinje till domänkontrollanten för att komma åt molnresurser. Windows 10 kan få enkel inloggning till Azure AD-program var som helst med en internetanslutning, utom när ett lösenord ändras. Användare som loggar in med Windows Hello för företag fortsätter att få enkel inloggning till Azure AD-program även efter en lösenordsändring, även om de inte har siktlinje till sin domänkontrollant. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>F: Vad händer om en användare ändrar sitt lösenord och försöker logga in på sin Windows 10 hybrid Azure AD-anslutna enhet utanför företagsnätverket?

**A.** Om ett lösenord ändras utanför företagsnätverket (till exempel med hjälp av Azure AD SSPR) misslyckas användaren med det nya lösenordet. För hybrid Azure AD-anslutna enheter är lokal Active Directory den primära behörigheten. När en enhet inte har siktlinje till domänkontrollanten kan den inte validera det nya lösenordet. Så användaren måste upprätta anslutning med domänkontrollanten (antingen via VPN eller att vara i företagets nätverk) innan de kan logga in på enheten med sitt nya lösenord. Annars kan de bara logga in med sitt gamla lösenord på grund av cachelagrade inloggningsfunktioner i Windows. Det gamla lösenordet ogiltigförklaras dock av Azure AD under tokenbegäranden och förhindrar därför enkel inloggning och misslyckas med alla enhetsbaserade principer för villkorlig åtkomst. Det hÃ¤r problemet uppstÃ¥r inte om du anrÃ¤nder Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Vanliga frågor och svar om Azure AD-register

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>F: Hur tar jag bort ett Azure AD-registrerat tillstånd för en enhet lokalt?

**A:** 
- För Windows 10 Azure AD-registrerade enheter går du till **Inställningar** > **Konton** > **Access Work eller School**. Välj ditt konto och välj **Koppla från**. Enhetsregistreringen är per användarprofil i Windows 10.
- För iOS och Android kan du använda Microsoft Authenticator-programmet **Inställningar** > **Enhetsregistrering** och välja **Avregistrera enheten**.
- För macOS kan du använda Microsoft Intune Company Portal-programmet för att avregistrera enheten från hantering och ta bort alla registreringar. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>F: Hur kan jag blockera användare från att lägga till ytterligare arbetskonton (Azure AD-registrerade) på mina Windows 10-enheter för företag?

**A.** Aktivera följande register för att blockera användarna från att lägga till ytterligare arbetskonton till din företagsdomän gick, Azure AD gick med i eller hybrid Azure AD gick med i Windows 10-enheter. Den här principen kan också användas för att blockera domänanslutna datorer från att oavsiktligt få Azure AD registrerad med samma användarkonto. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>F: Kan jag registrera Android- eller iOS BYOD-enheter?

**A.** Ja, men bara med Azure-enhetsregistreringstjänsten och för hybridkunder. Det stöds inte med den lokala enhetsregistreringstjänsten i Active Directory Federation Services (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>F: Hur registrerar jag en macOS-enhet?

**A.** Gör följande:

1.    [Skapa en efterlevnadsprincip](/intune/compliance-policy-create-mac-os)
1.    [Definiera en princip för villkorlig åtkomst för macOS-enheter](../active-directory-conditional-access-azure-portal.md) 

**Anmärkningar:**

- De användare som ingår i principen villkorlig åtkomst behöver en [version av Office för macOS som stöds för](../conditional-access/concept-conditional-access-conditions.md) att komma åt resurser. 
- Under det första åtkomstförsöket uppmanas användarna att registrera enheten med hjälp av företagsportalen.

---
## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure AD-registrerade enheter](concept-azure-ad-register.md)
- Läs mer om [Azure AD-anslutna enheter](concept-azure-ad-join.md)
- Läs mer om [hybrid-Azure AD-anslutna enheter](concept-azure-ad-join-hybrid.md)
