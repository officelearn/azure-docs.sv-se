---
title: Azure Active Directory-enhetshantering vanliga frågor och svar | Microsoft Docs
description: Azure Active Directory enhetshantering vanliga frågor och svar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f41e18b0ab546da87ea7a4a6d53bad370fefe670
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351753"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory-enhetshantering vanliga frågor och svar

###<a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>F: Jag har registrerat enheten nyligen. Varför visas inte enheten under Mina användarinformation i Azure-portalen? Eller varför enhetens ägare markeras som ej tillämpligt för hybrid Azure Active Directory (Azure AD)-anslutna enheter?

**S:** Windows 10-enheter som är hybrid Azure AD-anslutna inte visas **användarenheter**.
Använd den **alla enheter** vyn i Azure-portalen. Du kan också använda ett PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet.

Endast följande enheter visas under **användarenheter**:

- Alla personliga enheter som inte är hybrid Azure AD-anslutna. 
- Alla Windows 10 eller Windows Server 2016-enheter.
- Alla icke-Windows-enheter. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>F: Hur vet jag enhetstillstånd för registrering av klienten är?

**S:** I Azure-portalen går du till **alla enheter**. Sök efter enheter med hjälp av enhets-ID. Kontrollera värdet under typen kopplingskolumn. Ibland kan enheten återställas eller avbildning återställs. Det är därför viktigt att även kontrollera registreringstillstånd för enheten på enheten:

- För Windows 10 och Windows Server 2016 eller senare enheter, kör `dsregcmd.exe /status`.
- OS-versioner äldre kör `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>F: Jag ser enhetspost under användarinformation i Azure-portalen. Och jag se tillståndet som registrerats på enheten. Är jag korrekt konfigurerad att använda villkorlig åtkomst?

**S:** Anslutningstillståndet enhet enligt **deviceID**, måste matcha tillståndet på Azure AD och uppfylla alla utvärderingskriterierna för villkorlig åtkomst. Mer information finns i [kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>F: Jag har tagit bort enheten i Azure portal eller med hjälp av Windows PowerShell. Men det lokala enhetens tillståndet står det att den fortfarande är registrerad.

**S:** Den här åtgärden är avsiktligt. Enheten har inte åtkomst till resurser i molnet. 

Om du vill registrera, måste du utföra en manuell åtgärd på enheten. 

Om du vill ta bort anslutningstillståndet från Windows 10 och Windows Server 2016 som är en lokal Active Directory-domänanslutna, gör du följande:

1.  Öppna Kommandotolken som administratör.

2.  Ange `dsregcmd.exe /debug /leave`.

3.  Logga ut och logga in att utlösa den schemalagda aktiviteten som registreras igen med Azure AD. 

För äldre Windows OS-versioner som är en lokal Active Directory-domänanslutna, gör du följande:

1.  Öppna Kommandotolken som administratör.
2.  Ange `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Ange `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>F: Varför ser jag dubblettenheten poster i Azure-portalen?

**S:**

-   För Windows 10 och Windows Server 2016 kan orsaka upprepade försöker frånkoppling från och återansluta till samma enhet dubblettposter. 

-   Varje Windows-användare som använder **Lägg till arbets- eller Skolkonto** skapar en ny enhetspost med samma enhetsnamn.

-   För äldre Windows OS-versioner som är en lokal Azure Directory domänanslutna skapar automatisk registrering en ny enhetspost med samma enhetsnamn för varje domänanvändare som loggar in på enheten. 

-   En domänansluten dator i Azure AD som har rensats, installeras om och återkopplas med samma namn som visas som en annan post med samma enhetsnamn.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>F: Stöder registrering av Windows 10-enheten i Azure AD TPM: er i FIPS-läge?

**S:** Nej, för närvarande enhetsregistrering i Windows 10 för alla enhetstillstånd - Hybrid Azure AD-anslutning, Azure AD-anslutning och Azure AD-registrerad - stöder inte TPM: er i FIPS-läge. Om du vill ansluta till eller registrera dig till Azure AD, måste FIPS-läge stängas av för TPM: er på dessa enheter

---

**F: Varför kan en användare fortfarande komma åt resurser från en enhet som jag har inaktiverats i Azure-portalen?**

**S:** Det tar till en timme innan en återkalla som ska användas.

>[!NOTE] 
>För registrerade enheter rekommenderar vi att du rensar enheten för att se till att användare inte kan komma åt resurserna. Mer information finns i [vad är enhetsregistrering?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Azure AD join vanliga frågor och svar

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>F: Hur jag frånkoppling från en Azure AD-domänansluten enhet lokalt på enheten?

**S:** 
- Anslutna enheter, se till att inaktivera automatisk registrering för Azure AD. Den schemalagda aktiviteten registrera inte sedan enheten igen. Öppna en kommandotolk som administratör och ange `dsregcmd.exe /debug /leave`. Eller kör detta kommando som ett skript på flera enheter vid frånkoppling från gruppvis.

- Anslutna enheter, kontrollera att du har ett lokalt administratörskonto som är offline eller skapa en för ren Azure AD. Du kan inte logga in med autentiseringsuppgifter för alla Azure AD-användare. Gå sedan till **inställningar** > **konton** > **åtkomst till arbete eller skola**. Välj ditt konto och välj **Disconnect**. Följ anvisningarna och ange autentiseringsuppgifterna som lokal administratör när du tillfrågas. Starta om enheten för att slutföra processen frånkoppling.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>F: Mina användare loggar in till Azure AD-anslutna enheter som ska tas bort eller inaktiveras i Azure AD?

**S:** Ja. Windows har en cachelagrad användarnamn och lösenord-funktion som gör att användare som loggat in tidigare komma åt skrivbordet snabbt även utan nätverksanslutning. 

När en enhet tas bort eller inaktiveras i Azure AD, är det inte känt för Windows-enheten. Så att användare som loggat in tidigare fortsätta komma åt skrivbordet med cachelagrade användarnamn och lösenord. Men när enheten tas bort eller inaktiveras, användare kan inte komma åt några resurser som skyddas av enhetsbaserad villkorlig åtkomst. 

Användare som inte har registrerat i tidigare inte åtkomst till enheten. Det finns inga cachelagrade användarnamn och lösenord som har aktiverats för dem. 

---

### <a name="q-can-disabled-or-deleted-users-sign-in-to-azure-ad-joined-devices"></a>F: Inaktiverade eller borttagna användare loggar in till Azure AD-anslutna enheter?

**S:** Ja, men endast under en begränsad tid. När en användare tas bort eller inaktiveras i Azure AD, kallas den inte direkt till Windows-enheten. Så kan användare som loggat in tidigare åt skrivbordet med cachelagrade användarnamn och lösenord. 

Enheten har vanligtvis om användarens tillstånd i mindre än fyra timmar. Sedan blockerar Windows dessa användare åtkomst till skrivbordet. När användaren tas bort eller inaktiveras i Azure AD, har alla token återkallats. Så det går inte att de åtkomst till resurser. 

Har tagits bort eller är inaktiverad användare som inte har registrerat i tidigare inte åtkomst till en enhet. Det finns inga cachelagrade användarnamn och lösenord som har aktiverats för dem. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>F: Varför Mina användare har problem på Azure AD-anslutna enheter när du har ändrat deras UPN?

**S:** För närvarande stöds UPN ändringar inte helt på Azure AD-anslutna enheter. Så misslyckas autentiseringen med Azure AD efter deras UPN-ändringar. Därför kan användare använda enkel inloggning och villkorlig åtkomst utfärdar på sina enheter. Användare måste logga in på Windows via panelen ”andra användare” som använder sina nya UPN-namnet för att lösa det här problemet just nu. Vi arbetar med att lösa problemet. Användare som loggar in med Windows Hello för företag dock inte stöter på problemet. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>F: Mina användare kan inte söka efter skrivare från Azure AD-anslutna enheter. Hur kan jag aktivera utskrift från dessa enheter?

**S:** Distribuera skrivare för Azure AD-anslutna enheter, se [distribuera Windows Server Hybrid Cloud utskrift med förautentisering](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Du behöver en lokal Windows Server att distribuera hybrid cloud utskrifts. Molnbaserad utskriftstjänster är för närvarande inte tillgängligt. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>F: Hur ansluter jag till en fjärransluten Azure AD domänansluten enhet?

**S:** Se [Anslut till Azure Active Directory-anslutna fjärrdator](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>F: Varför ser mina användare *du kan inte ta dig dit härifrån*?

**S:** Du konfigurera vissa regler för villkorlig åtkomst för att kräva en specifik enhetstillstånd? Om enheten inte uppfyller kriterierna kan användare blockeras och de finns i meddelandet. Utvärdera princip-regler för villkorlig åtkomst. Kontrollera att enheten uppfyller kriterierna för att undvika meddelandet.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>F: Varför inte några av Mina användare för att hämta Azure Multi-Factor Authentication anvisningarna på Azure AD-anslutna enheter?

**S:** En användare kan ansluta till eller registrera en enhet med Azure AD med hjälp av Multi-Factor Authentication. Själva enheten blir sedan en betrodd andra faktor för användaren. När samma användare loggar in på enheten och har åtkomst till ett program, Azure AD tar hänsyn till enheten som en andra faktor. Det gör att användaren sömlöst kan komma åt program utan ytterligare Multi-Factor Authentication-frågor. 

Det här beteendet:

- Är tillämpliga på Azure AD ansluten och Azure AD-registrerade enheter – men inte för hybrid Azure AD-anslutna enheter.

- Gäller inte för alla användare som loggar in på enheten. Så alla andra användare som har åtkomst till enheten får en Multi-Factor Authentication-utmaning. De kan sedan komma åt program som kräver Multifaktorautentisering.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>F: Varför får jag en *användarnamnet eller lösenordet är felaktigt* meddelande för en enhet som jag precis domänanslutna till Azure AD?

**S:** Vanliga orsaker till det här scenariot är följande:

- Dina autentiseringsuppgifter är inte längre giltig.

- Datorn kan inte kommunicera med Azure Active Directory. Sök efter eventuella problem med nätverksanslutningen.

- Federerad inloggningar kräver federationsservern som stöd för WS-Trust-slutpunkter som är aktiverade och tillgänglig. 

- Du har aktiverat direktautentisering. Därför måste din tillfälliga lösenordet ändras när du loggar in.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>F: Varför visas den *Oops... Det uppstod ett fel!* dialogrutan när jag försöker Azure AD join min dator?

**S:** Det här felet inträffar när du ställer in Azure Active Directory-registrering med Intune. Se till att den användare som försöker Azure AD-anslutning har rätt Intune-licens. Mer information finns i [konfigurera registrering för Windows-enheter](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>F: Varför Mina försöker Azure AD join en PC-växling vid fel, även om jag inte får någon information om fel?

**S:** En trolig orsak är att du loggat in på enheten med hjälp av det inbyggda lokala administratörskontot. Skapa ett annat lokala konto innan du använder Azure Active Directory-koppling för att slutföra installationen. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q: Vad är MS-organisation-P2P-Access-certifikat finns på vår Windows 10-enheter?

**S:** MS-organisation-P2P-Access-certifikat utfärdas av Azure AD för både Azure AD-anslutna och hybrid Azure AD-anslutna enheter. Dessa certifikat används för att aktivera förtroendet mellan enheter i samma klient för remote desktop scenarier. Ett certifikat har utfärdats till enheten och en annan utfärdas till användaren. Enhetens certifikat finns i `Local Computer\Personal\Certificates` och är giltig i en dag. Det här certifikatet förnyas (genom att utfärda ett nytt certifikat) om enheten är fortfarande är aktiv i Azure AD. Användarcertifikatet finns i `Current User\Personal\Certificates` och det här certifikatet gäller även för en dag, men det har utfärdats på begäran när en användare försöker en fjärrskrivbordssession till en annan Azure AD-domänansluten enhet. Det är inte förnya löper ut. Båda dessa certifikat utfärdas med MS-organisation-P2P-Access-certifikat finns i den `Local Computer\AAD Token Issuer\Certificates`. Det här certifikatet utfärdas av Azure AD vid enhetsregistrering. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why visas flera utgångna certifikat som utfärdats av MS-organisation-P2P-åtkomst på våra Windows 10-enheter? Hur kan jag ta bort dem?

**S:** Det uppstod ett problem som identifieras på Windows 10 version 1709 och lägre där utgångna certifikat för MS-organisation-P2P-åtkomst fortsätter finns på datorn på grund av kryptografiska problem. Användarna kunde stöter på problem med nätverksanslutningen, om du använder ingen VPN-klient (t.ex. Cisco AnyConnect) som inte kan hantera ett stort antal utgångna certifikat. Det här problemet har åtgärdats i Windows 10-1803 versionen automatiskt ta bort sådana utgångna certifikat MS-organisation-P2P-åtkomst. Du kan lösa det här problemet genom att uppdatera dina enheter till Windows 10-1803. Om det inte går att uppdatera kan du ta bort dessa certifikat utan någon negativ inverkan.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD-anslutning vanliga frågor och svar

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>F: Var hittar jag felsökning information att diagnostisera hybrid Azure AD join fel?

**S:** Information om felsökning finns i dessa artiklar:

- [Felsöka hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Felsöka hybrid Azure Active Directory-anslutna äldre enheter](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>F: Varför ser jag en duplicerad Azure AD registrerade post för min Windows 10 Azure AD ansluten enhet i enhetslistan för Azure AD?

**S:** När användarna lägger till sina konton i appar på en domänansluten enhet, uppmanas de med **Lägg till kontot i Windows?** Om de anger **Ja** i Kommandotolken, registrerar enheten med Azure AD. Förtroendetypen har markerats som Azure AD-registrerad. När du har aktiverat hybrid Azure AD-anslutning i din organisation, hämtar enheten också hybrid Azure AD-anslutna. Sedan två enhetstillstånd som visas för samma enhet. 

Hybrid Azure AD-anslutning har företräde framför Azure AD-registrerad tillstånd. Så anses enheten vara hybrid Azure AD-anslutna för autentisering och utvärderingen av villkorlig åtkomst. Du kan ta bort posten för Azure AD-registrerad enhet från Azure AD-portalen. Lär dig hur du [undvika eller rensa den här dubbla tillstånd på Windows 10-dator](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>F: Varför Mina användare har problem på Windows 10 hybrid Azure AD-anslutna enheter när du har ändrat deras UPN?

**S:** För närvarande UPN ändringar stöds fullständigt inte med hybrid Azure AD-anslutna enheter. Användare kan logga in på enheten och komma åt sina lokala program, misslyckas autentiseringen med Azure AD när ett UPN ändra. Därför kan användare använda enkel inloggning och villkorlig åtkomst utfärdar på sina enheter. För närvarande måste du frånkoppling från enheten från Azure AD (kör ”dsregcmd /leave” med utökade privilegier) och ansluta på nytt (sker automatiskt) att lösa problemet. Vi arbetar med att lösa problemet. Användare som loggar in med Windows Hello för företag dock inte stöter på problemet. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>F: Kräver Windows 10 hybrid Azure AD-anslutna enheter åtkomst till domänkontrollanten för att få åtkomst till resurser i molnet?

**S:** Allmänt Nej, utom när användarens lösenord har ändrats. Enare Windows 10 hybrid Azure AD join är klar och användaren har loggat in minst en gång, enheten kräver inte åtkomst till domänkontrollanten för att komma åt resurser i molnet. Windows 10 kan få enkel inloggning till Azure AD-program från var som helst med en Internetanslutning, utom när ett lösenord ändras. Användare som loggar in med Windows Hello för företag fortfarande få enkel inloggning till Azure AD-program även efter att en lösenordsändring även om de inte har åtkomst till sina domänkontrollant. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>F: Vad händer om en användare ändrar sitt lösenord och försöker att logga in på sina Windows 10-hybrid Azure AD ansluten enhet utanför företagsnätverket?

**S:** Om ett lösenord har ändrats utanför företagets nätverk (till exempel med hjälp av Azure AD SSPR), misslyckas användaren loggar in med det nya lösenordet. Lokala Active Directory är den primära utfärdaren för hybrid Azure AD-anslutna enheter. När en enhet inte har åtkomst till domänkontrollanten, är det inte att verifiera det nya lösenordet. Därför användare behöver upprätta anslutning med domänkontrollanten (antingen via VPN eller att den finns i företagsnätverket) innan de är kunna logga in till enheten med sitt nya lösenord. I annat fall kan de bara logga in med sina gamla lösenord på grund av cachelagrade inloggningsmöjlighet i Windows. Men det gamla lösenordet betraktas som inaktuella av Azure AD under tokenbegäranden och därför förhindrar enkel inloggning på och misslyckas alla principer för enhetsbaserad villkorlig åtkomst. Det här problemet inträffar inte om du använder Windows Hello för företag. 

---


## <a name="azure-ad-register-faq"></a>Registrera dig av vanliga frågor om med Azure AD

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>F: Kan jag registrera Android eller iOS BYOD-enheter?

**S:** Ja, men endast med enhetsregistreringstjänsten för Azure och för kunder med hybriddistributioner. Det stöds inte med den lokala enhetsregistreringstjänsten i Active Directory Federation Services (AD FS).

### <a name="q-how-can-i-register-a-macos-device"></a>F: Hur kan jag registrera en macOS-enhet?

**S:** Utför följande steg:

1.  [Skapa en efterlevnadsprincip](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiera principer för villkorlig åtkomst för macOS-enheter](../active-directory-conditional-access-azure-portal.md) 

**Anmärkning:**

- Användare som ingår i din princip för villkorlig åtkomst måste en [version av Office som stöds för macOS](../conditional-access/technical-reference.md#client-apps-condition) att komma åt resurser. 

- Under det första försöket för åtkomst uppmanas användarna att registrera enheten med hjälp av Företagsportalen.

