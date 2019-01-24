---
title: Azure Active Directory-enhetshantering vanliga frågor och svar | Microsoft Docs
description: Azure Active Directory enhetshantering vanliga frågor och svar.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2010
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 916de2de6cdc19bfa1e3967661d40693d4be1e99
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852396"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory-enhetshantering vanliga frågor och svar

**F: Jag har registrerat enheten nyligen. Varför visas inte enheten under Mina användarinformation i Azure-portalen? Eller varför enhetens ägare markeras som ej tillämpligt för hybrid Azure Active Directory (Azure AD)-anslutna enheter?**

**A:** Windows 10-enheter som är hybrid Azure AD-anslutna inte visas **användarenheter**.
Använd den **alla enheter** vyn i Azure-portalen. Du kan också använda ett PowerShell [Get-MsolDevice](https://docs.microsoft.com/en-us/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet.

Endast följande enheter visas under **användarenheter**:

- Alla personliga enheter som inte är hybrid Azure AD-anslutna. 
- Alla Windows 10 eller Windows Server 2016-enheter.
- Alla icke-Windows-enheter. 

--- 

**F: Hur vet jag enhetstillstånd för registrering av klienten är?**

**A:** I Azure-portalen går du till **alla enheter**. Sök efter enheter med hjälp av enhets-ID. Kontrollera värdet under typen kopplingskolumn. Ibland kan enheten återställas eller avbildning återställs. Det är därför viktigt att även kontrollera registreringstillstånd för enheten på enheten:

- För Windows 10 och Windows Server 2016 eller senare enheter, kör `dsregcmd.exe /status`.
- OS-versioner äldre kör `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

**F: Jag ser enhetspost under användarinformation i Azure-portalen. Och jag se tillståndet som registrerats på enheten. Är jag korrekt konfigurerad att använda villkorlig åtkomst?**

**A:** Anslutningstillståndet enhet enligt **deviceID**, måste matcha tillståndet på Azure AD och uppfylla alla utvärderingskriterierna för villkorlig åtkomst. Mer information finns i [kräver hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

---

**F: Jag har tagit bort enheten i Azure portal eller med hjälp av Windows PowerShell. Men det lokala enhetens tillståndet står det att den fortfarande är registrerad.**

**A:** Den här åtgärden är avsiktligt. Enheten har inte åtkomst till resurser i molnet. 

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

**F: Varför ser jag dubblettenheten poster i Azure-portalen?**

**A:**

-   För Windows 10 och Windows Server 2016 kan orsaka upprepade försöker frånkoppling från och återansluta till samma enhet dubblettposter. 

-   Varje Windows-användare som använder **Lägg till arbets- eller Skolkonto** skapar en ny enhetspost med samma enhetsnamn.

-   För äldre Windows OS-versioner som är en lokal Azure Directory domänanslutna skapar automatisk registrering en ny enhetspost med samma enhetsnamn för varje domänanvändare som loggar in på enheten. 

-   En domänansluten dator i Azure AD som har rensats, installeras om och återkopplas med samma namn som visas som en annan post med samma enhetsnamn.

---

**F: Varför kan en användare fortfarande komma åt resurser från en enhet som jag har inaktiverats i Azure-portalen?**

**A:** Det tar till en timme innan en återkalla som ska användas.

>[!NOTE] 
>För registrerade enheter rekommenderar vi att du rensar enheten för att se till att användare inte kan komma åt resurserna. Mer information finns i [vad är enhetsregistrering?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Azure AD join vanliga frågor och svar

**F: Hur jag frånkoppling från en Azure AD-domänansluten enhet lokalt på enheten?**

**A:** 
- Anslutna enheter, se till att inaktivera automatisk registrering för Azure AD. Den schemalagda aktiviteten registrera inte sedan enheten igen. Öppna en kommandotolk som administratör och ange `dsregcmd.exe /debug /leave`. Eller kör detta kommando som ett skript på flera enheter vid frånkoppling från gruppvis.

- Anslutna enheter, kontrollera att du har ett lokalt administratörskonto som är offline eller skapa en för ren Azure AD. Du kan inte logga in med autentiseringsuppgifter för alla Azure AD-användare. Gå sedan till **inställningar** > **konton** > **åtkomst till arbete eller skola**. Välj ditt konto och välj **Disconnect**. Följ anvisningarna och ange autentiseringsuppgifterna som lokal administratör när du tillfrågas. Starta om enheten för att slutföra processen frånkoppling.

---

**F: Mina användare loggar in till Azure AD-anslutna enheter som ska tas bort eller inaktiveras i Azure AD?**

**A:** Ja. Windows har en cachelagrad användarnamn och lösenord-funktion som gör att användare som loggat in tidigare komma åt skrivbordet snabbt även utan nätverksanslutning. 

När en enhet tas bort eller inaktiveras i Azure AD, är det inte känt för Windows-enheten. Så att användare som loggat in tidigare fortsätta komma åt skrivbordet med cachelagrade användarnamn och lösenord. Men när enheten tas bort eller inaktiveras, användare kan inte komma åt några resurser som skyddas av enhetsbaserad villkorlig åtkomst. 

Användare som inte har registrerat i tidigare inte åtkomst till enheten. Det finns inga cachelagrade användarnamn och lösenord som har aktiverats för dem. 

---

**F: Inaktiverade eller borttagna användare loggar in till Azure AD-anslutna enheter?**

**A:** Ja, men endast under en begränsad tid. När en användare tas bort eller inaktiveras i Azure AD, kallas den inte direkt till Windows-enheten. Så kan användare som loggat in tidigare åt skrivbordet med cachelagrade användarnamn och lösenord. 

Enheten har vanligtvis om användarens tillstånd i mindre än fyra timmar. Sedan blockerar Windows dessa användare åtkomst till skrivbordet. När användaren tas bort eller inaktiveras i Azure AD, har alla token återkallats. Så det går inte att de åtkomst till resurser. 

Har tagits bort eller är inaktiverad användare som inte har registrerat i tidigare inte åtkomst till en enhet. Det finns inga cachelagrade användarnamn och lösenord som har aktiverats för dem. 

---

**F: Mina användare kan inte söka efter skrivare från Azure AD-anslutna enheter. Hur kan jag aktivera utskrift från dessa enheter?**

**A:** Distribuera skrivare för Azure AD-anslutna enheter, se [distribuera Windows Server Hybrid Cloud utskrift med förautentisering](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Du behöver en lokal Windows Server att distribuera hybrid cloud utskrifts. Molnbaserad utskriftstjänster är för närvarande inte tillgängligt. 

---

**F: Hur ansluter jag till en fjärransluten Azure AD domänansluten enhet?**

**A:** Se [Anslut till Azure Active Directory-anslutna fjärrdator](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

**F: Varför ser mina användare *du kan inte ta dig dit härifrån*?**

**A:** Du konfigurera vissa regler för villkorlig åtkomst för att kräva en specifik enhetstillstånd? Om enheten inte uppfyller kriterierna kan användare blockeras och de finns i meddelandet. Utvärdera princip-regler för villkorlig åtkomst. Kontrollera att enheten uppfyller kriterierna för att undvika meddelandet.

---

**F: Varför inte några av Mina användare för att hämta Azure Multi-Factor Authentication anvisningarna på Azure AD-anslutna enheter?**

**A:** En användare kan ansluta till eller registrera en enhet med Azure AD med hjälp av Multi-Factor Authentication. Själva enheten blir sedan en betrodd andra faktor för användaren. När samma användare loggar in på enheten och har åtkomst till ett program, Azure AD tar hänsyn till enheten som en andra faktor. Det gör att användaren sömlöst kan komma åt program utan ytterligare Multi-Factor Authentication-frågor. 

Det här beteendet gäller inte för alla användare som loggar in på enheten. Så alla andra användare som har åtkomst till enheten får en Multi-Factor Authentication-utmaning. De kan sedan komma åt program som kräver Multifaktorautentisering.

---

**F: Varför får jag en *användarnamnet eller lösenordet är felaktigt* meddelande för en enhet som jag precis domänanslutna till Azure AD?**

**A:** Vanliga orsaker till det här scenariot är följande:

- Dina autentiseringsuppgifter är inte längre giltig.

- Datorn kan inte kommunicera med Azure Active Directory. Sök efter eventuella problem med nätverksanslutningen.

- Federerad inloggningar kräver federationsservern som stöd för WS-Trust-slutpunkter som är aktiverade och tillgänglig. 

- Du har aktiverat direktautentisering. Därför måste din tillfälliga lösenordet ändras när du loggar in.

---

**F: Varför visas den *Oops … ett fel inträffade!* dialogrutan när jag försöker Azure AD join min dator?**

**A:** Det här felet inträffar när du ställer in Azure Active Directory-registrering med Intune. Se till att den användare som försöker Azure AD-anslutning har rätt Intune-licens. Mer information finns i [konfigurera registrering för Windows-enheter](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**F: Varför Mina försöker Azure AD join en PC-växling vid fel, även om jag inte får någon information om fel?**

**A:** En trolig orsak är att du loggat in på enheten med hjälp av det inbyggda lokala administratörskontot. Skapa ett annat lokala konto innan du använder Azure Active Directory-koppling för att slutföra installationen. 

---

## <a name="hybrid-azure-ad-join-faq"></a>Hybrid Azure AD-anslutning vanliga frågor och svar

**F: Var hittar jag felsökning information att diagnostisera hybrid Azure AD join fel?**

**A:** Information om felsökning finns i dessa artiklar:

- [Felsöka hybrid Azure Active Directory-anslutna enheter för Windows 10 och Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Felsöka hybrid Azure Active Directory-anslutna äldre enheter](troubleshoot-hybrid-join-windows-legacy.md)
 
**F: Varför ser jag en duplicerad Azure AD registrerade post för min Windows 10 Azure AD ansluten enhet i enhetslistan för Azure AD?**

**A:** När användarna lägger till sina konton i appar på en domänansluten enhet, uppmanas de med **Lägg till kontot i Windows?** Om de anger **Ja** i Kommandotolken, registrerar enheten med Azure AD. Förtroendetypen har markerats som Azure AD-registrerad. När du har aktiverat hybrid Azure AD-anslutning i din organisation, hämtar enheten också hybrid Azure AD-anslutna. Sedan två enhetstillstånd som visas för samma enhet. 

Hybrid Azure AD-anslutning har företräde framför Azure AD-registrerad tillstånd. Så anses enheten vara hybrid Azure AD-anslutna för autentisering och utvärderingen av villkorlig åtkomst. Du kan ta bort posten för Azure AD-registrerad enhet från Azure AD-portalen. Lär dig hur du [undvika eller rensa den här dubbla tillstånd på Windows 10-dator](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 

---

## <a name="azure-ad-register-faq"></a>Registrera dig av vanliga frågor om med Azure AD

**F: Kan jag registrera Android eller iOS BYOD-enheter?**

**A:** Ja, men endast med enhetsregistreringstjänsten för Azure och för kunder med hybriddistributioner. Det stöds inte med den lokala enhetsregistreringstjänsten i Active Directory Federation Services (AD FS).

**F: Hur kan jag registrera en macOS-enhet?**

**A:** Gör följande:

1.  [Skapa en efterlevnadsprincip](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiera principer för villkorlig åtkomst för macOS-enheter](../active-directory-conditional-access-azure-portal.md) 

**Anmärkning:**

- Användare som ingår i din princip för villkorlig åtkomst måste en [version av Office som stöds för macOS](../conditional-access/technical-reference.md#client-apps-condition) att komma åt resurser. 

- Under det första försöket för åtkomst uppmanas användarna att registrera enheten med hjälp av Företagsportalen.

---


**Q: Vad är MS-organisation-P2P-Access-certifikat finns på vår Windows 10-enheter?**

**A:** MS-organisation-P2P-Access-certifikat utfärdas av Azure AD för både Azure AD-anslutna och hybrid Azure AD-anslutna enheter. Dessa certifikat används för att aktivera förtroendet mellan enheter i samma klient för remote desktop scenarier. Ett certifikat har utfärdats till enheten och en annan utfärdas till användaren. Enhetens certifikat finns i `Local Computer\Personal\Certificates` och är giltig i en dag. Det här certifikatet förnyas (genom att utfärda ett nytt certifikat) om enheten är fortfarande är aktiv i Azure AD. Användarcertifikatet finns i `Current User\Personal\Certificates` och det här certifikatet gäller även för en dag, men det har utfärdats på begäran när en användare försöker en fjärrskrivbordssession till en annan Azure AD-domänansluten enhet. Det är inte förnya löper ut. Båda dessa certifikat utfärdas med MS-organisation-P2P-Access-certifikat finns i den `Local Computer\AAD Token Issuer\Certificates`. Det här certifikatet utfärdas av Azure AD vid enhetsregistrering. 

---

**Q:Why visas flera utgångna certifikat som utfärdats av MS-organisation-P2P-åtkomst på våra Windows 10-enheter? Hur kan jag ta bort dem?**

**A:** Det uppstod ett problem som identifieras på Windows 10 version 1709 och lägre där utgångna certifikat för MS-organisation-P2P-åtkomst fortsätter finns på datorn på grund av kryptografiska problem. Användarna kunde stöter på problem med nätverksanslutningen, om du använder ingen VPN-klient (t.ex. Cisco AnyConnect) som inte kan hantera ett stort antal utgångna certifikat. Det här problemet har åtgärdats i Windows 10-1803 versionen automatiskt ta bort sådana utgångna certifikat MS-organisation-P2P-åtkomst. Du kan lösa det här problemet genom att uppdatera dina enheter till Windows 10-1803. Om det inte går att uppdatera kan du ta bort dessa certifikat utan någon negativ inverkan.  
