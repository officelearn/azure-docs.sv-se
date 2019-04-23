---
title: Använda befintliga NPS-servrar för att tillhandahålla funktioner för Azure MFA - Azure Active Directory
description: Lägga till molnbaserade tvåstegsverifiering verifiering funktioner i din befintliga infrastruktur för autentisering
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a945316df27460fef48a8bb4d43e46d412d2ae81
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009724"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication

Nätverksprincipserver (NPS)-tillägget för Azure MFA lägger till funktioner för molnbaserad MFA till din infrastruktur för autentisering med hjälp av befintliga servrar. Med NPS-tillägget du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering till ditt befintliga autentiseringsflödet utan att behöva installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda VPN-anslutningar utan att distribuera Azure MFA-servern. NPS-tillägget fungerar som ett kort mellan RADIUS- och molnbaserade Azure MFA för att tillhandahålla en andra faktor autentisering för federerade eller synkroniserade användare.

När du använder NPS-tillägget för Azure MFA innehåller autentiseringsflödet följande komponenter: 

1. **NAS/VPN-servern** tar emot förfrågningar från VPN-klienter och konverterar dem till RADIUS-förfrågningar till NPS-servrar. 
2. **NPS-Server** ansluter till Active Directory för att utföra den primära autentiseringen för RADIUS-förfrågningar och vid en lyckad distribution, skickar begäran till några installerade tillägg.  
3. **NPS-tillägget** utlöser en begäran om att Azure MFA för den sekundära autentiseringen. När tillägget tar emot svaret och om MFA-kontrollen utförs kan den är klar autentiseringsbegäran genom att tillhandahålla NPS-servern med säkerhetstoken som innehåller en MFA-anspråk kan utfärdas av Azure STS.  
4. **Azure MFA** kommunicerar med Azure Active Directory för att hämta användarens information och utför sekundära autentiseringen med hjälp av en verifieringsmetod som konfigurerats för användaren.

Följande diagram illustrerar det här övergripande autentiseringsflödet för begäran: 

![Flödesdiagram för autentisering](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planera distributionen

NPS-tillägget hanterar automatiskt redundans, så du inte behöver en särskild konfiguration.

Du kan skapa så många Azure MFA-aktiverade NPS-servrar som du behöver. Om du installerar flera servrar, bör du använda ett klientcertifikat för skillnaden för var och en av dem. Skapa ett certifikat för varje server innebär att du kan uppdatera varje cert individuellt och inte bekymra dig om avbrott på alla servrar.

VPN-servrar vidarebefordra autentiseringsbegäranden, så att de behöver känna till de nya Azure MFA-aktiverade NPS-servrarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

NPS-tillägget är avsedda att fungera med din befintliga infrastruktur. Kontrollera att du har följande krav innan du börjar.

### <a name="licenses"></a>Licenser

NPS-tillägget för Azure MFA är tillgängligt för kunder med [licenser för Azure Multi-Factor Authentication](multi-factor-authentication.md) (ingår i Azure AD Premium, EMS eller en fristående MFA-licens). Förbrukningsbaserad licenser för Azure MFA som per användare eller per autentisering licenser är inte kompatibla med NPS-tillägget. 

### <a name="software"></a>Programvara

Windows Server 2008 R2 SP1 eller senare.

### <a name="libraries"></a>Bibliotek

Dessa bibliotek installeras automatiskt med filnamnstillägget.

- [Visual C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-modulen för Windows PowerShell version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory-modulen för Windows PowerShell är installerad, om det inte redan är installerat, via ett konfigurationsskript som du kör som en del av installationen. Det finns ingen anledning att installera den här modulen förbereds i förväg om den inte redan är installerad.

### <a name="azure-active-directory"></a>Azure Active Directory

Alla som använder NPS-tillägget måste synkroniseras till Azure Active Directory med Azure AD Connect och måste vara registrerad för MFA.

När du installerar tillägget måste katalog-ID och administratörsautentiseringsuppgifter för Azure AD-klienten. Du kan hitta din katalog-ID i den [Azure-portalen](https://portal.azure.com). Logga in som administratör, väljer den **Azure Active Directory** ikonen till vänster, välj sedan **egenskaper**. Kopiera GUID i den **katalog-ID** och spara den. Du använder detta GUID som klient-ID när du installerar NPS-tillägget.

![Hitta din katalog-ID under Azure Active Directory-egenskaper](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Nätverkskrav

NPS-servern måste kunna kommunicera med följande webbadresser över portarna 80 och 443.

* https:\//adnotifications.windowsazure.com  
* https:\//login.microsoftonline.com

Dessutom kan anslutningen till följande URL: er för att slutföra den [installationen av kortet använder tillhandahållna PowerShell-skript](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Förbered din miljö

Innan du installerar NPS-tillägget som du vill förbereda dig miljö för att hantera fördelning.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Aktivera NPS-rollen på en domänansluten server

NPS-servern ansluter till Azure Active Directory och autentiserar MFA-begäranden. Välj en server för den här rollen. Vi rekommenderar att du väljer en server som inte hanterar förfrågningar från andra tjänster, eftersom NPS-tillägget genererar fel för alla förfrågningar som inte är RADIUS. NPS-servern måste konfigureras som den primära och sekundära authentication-servern för din miljö. Det går inte att proxy RADIUS-förfrågningar till en annan server.

1. På din server öppnar du den **guiden Lägg till roller och funktioner** Server Manager Quickstart-menyn.
2. Välj **rollbaserad eller funktionsbaserad installation** för din installationstyp av.
3. Välj den **nätverkspolicy och åtkomsttjänster** serverrollen. Ett fönster kan visas om nödvändiga funktioner för att köra den här rollen.
4. Fortsätt genom guiden tills bekräftelsesidan. Välj **Installera**.

Nu när du har en server som är avsedd för NPS, bör du också konfigurera den här servern för att hantera inkommande RADIUS-förfrågningar från VPN-lösning.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurera din VPN-lösning för att kommunicera med NPS-servern

Hur du konfigurerar din princip för RADIUS-autentisering varierar beroende på vilken VPN-lösning som du använder. Konfigurera den här principen så att den pekar till RADIUS-NPS-servern.

### <a name="sync-domain-users-to-the-cloud"></a>Synkronisera domänanvändare till molnet

Det här steget kan redan vara klar på din klient, men det är bra att kontrollera att Azure AD Connect har synkroniserat dina databaser nyligen.

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory** > **Azure AD Connect**
3. Kontrollera att din synkroniseringsstatus är **aktiverad** och att den senaste synkroniseringen har mindre än en timme sedan.

Om du vill sätta igång en ny runda av synkronisering oss instruktionerna i [Azure AD Connect-synkronisering: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Avgöra vilka autentiseringsmetoder som användarna kan använda

Det finns två faktorer som påverkar vilka autentiseringsmetoder är tillgängliga med en NPS-tillägg-distribution:

1. Lösenord krypteringsalgoritmen som används mellan RADIUS-klienten (VPN, Netscaler-server, eller andra) och NPS-servrar.
   - **PAP** har stöd för alla autentiseringsmetoder av Azure MFA i molnet: telefonsamtal, enkelriktad SMS, mobilapp och mobilapp-Verifieringskod.
   - **CHAPv2** och **EAP** stöd för telefonsamtal och mobilapp.
2. Inmatningsmetoder som klientprogrammet (VPN, Netscaler-server, eller andra) kan hantera. Till exempel har VPN-klienten något sätt att tillåta användaren att ange en Verifieringskod från en text- eller mobilappen?

När du distribuerar NPS-tillägget kan du använda dessa faktorer för att utvärdera vilka metoder är tillgängliga för användarna. Om RADIUS-klienten stöder PAP, men klienten UX saknar inmatningsfält för en Verifieringskod, sedan telefonsamtal och mobilappen är de två alternativ som stöds.

Du kan [inaktivera stöds inte autentiseringsmetoder](howto-mfa-mfasettings.md#verification-methods) i Azure.

### <a name="register-users-for-mfa"></a>Registrera användare för MFA

Innan du distribuerar och använder NPS-tillägget kan måste användare som krävs för att utföra tvåstegsverifiering vara registrerad för MFA. Fler omedelbart behöver testa tillägget när du distribuerar den, du minst ett test-konto som helt har registrerats för Multifaktorautentisering.

Använd de här stegen för att få ett testkonto igång:
1. Logga in på [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) med ett testkonto. 
2. Följ anvisningarna för att ställa in en verifieringsmetod.
3. Skapa en princip för villkorlig åtkomst eller [ändra användartillståndet](howto-mfa-userstates.md) att kräva tvåstegsverifiering för testkonto. 

Användarna måste också att följa dessa steg för att registrera innan de kan autentisera med NPS-tillägget.

## <a name="install-the-nps-extension"></a>Installera NPS-tillägget

> [!IMPORTANT]
> Installera NPS-tillägget på en annan server än den VPN-åtkomstpunkten.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Ladda ned och installera NPS-tillägget för Azure MFA

1. [Hämta NPS-tillägget](https://aka.ms/npsmfa) från Microsoft Download Center.
2. Kopiera den binära filen till nätverksprincipservern som du vill konfigurera.
3. Kör *setup.exe* och Följ installationsanvisningarna. Om det uppstår fel kontrollerar du att två bibliotek från avsnittet förutsättningar har installerats.

#### <a name="upgrade-the-nps-extension"></a>Uppgradera NPS-tillägget

När du uppgraderar en befintlig NPS-tillägget installerat kan för att undvika att en omstart av den underliggande servern gör du följande:

1. Avinstallera den befintliga versionen
1. Kör installationsprogrammet för nya
1. Starta om tjänsten Server (IAS)

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Installationsprogrammet skapar ett PowerShell-skript på den här platsen: `C:\Program Files\Microsoft\AzureMfa\Config` (där C:\ är din installationsenheten). Det här PowerShell-skriptet utför följande åtgärder varje gång det körs:

- Skapa ett självsignerat certifikat.
- Associera den offentliga nyckeln för certifikatet till tjänstens huvudnamn i Azure AD.
- Store certifikatet i lokala datorns certifikatarkiv.
- Bevilja åtkomst till certifikatets privata nyckel till användare i nätverket.
- Starta om NPS.

Om du inte vill använda ditt eget certifikat (i stället för de självsignerade certifikat som PowerShell-skriptet genererar), Kör PowerShell-skript för att slutföra installationen. Om du installerar tillägget på flera servrar, ha var och en sitt eget certifikat.

1. Kör Windows PowerShell som administratör.
2. Ändra kataloger.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Kör PowerShell-skript som skapas av installationsprogrammet.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Logga in på Azure AD som en administratör.
5. PowerShell-kommandotolkarna för din klient-ID. Använd det GUID som katalog-ID som du kopierade från Azure-portalen i kravavsnittet.
6. PowerShell visar ett meddelande när skriptet har slutförts.  

Upprepa dessa steg på ytterligare NPS-servrar som du vill konfigurera för belastningsutjämning.

> [!NOTE]
> Om du använder egna certifikat i stället för att certifikat med PowerShell-skriptet kan du se till att de justeras till NPS-namngivningskonventionen. Ämnesnamnet måste vara **CN =\<TenantID\>, OU = Microsoft NPS-tillägget**. 

## <a name="configure-your-nps-extension"></a>Konfigurera NPS-tillägget

Det här avsnittet innehåller designöverväganden och förslag för lyckade distributioner för NPS-tillägget.

### <a name="configuration-limitations"></a>Konfigurationsbegränsningar

- NPS-tillägget för Azure MFA innehåller inte verktyg för att migrera användare och inställningar från MFA-servern till molnet. Därför föreslår vi att du använder tillägget för nya distributioner i stället för befintlig distribution. Om du använder tillägget på en befintlig distribution, har dina användare att utföra proof-up igen för att fylla i sina MFA-information i molnet.  
- NPS-tillägget använder UPN från en lokal Active directory för att identifiera användaren vid Azure MFA för att utföra den sekundär autentisering. Tillägget kan konfigureras för att använda en annan identifierare som alternativa inloggnings-ID eller anpassad Active Directory-fält än UPN. Mer information finns i artikeln [avancerade konfigurationsalternativ för NPS-tillägget för multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Inte alla krypteringsprotokoll stöder alla verifieringsmetoder.
   - **PAP** har stöd för telefonsamtal, enkelriktad SMS, mobilapp och mobilapp-Verifieringskod
   - **CHAPv2** och **EAP** stöd för telefonsamtal och meddelanden via mobilapp

### <a name="control-radius-clients-that-require-mfa"></a>Kontrollen RADIUS-klienter som kräver MFA

När du aktiverar MFA för en RADIUS-klient som använder NPS-tillägget, måste alla autentiseringar för den här klienten genomför MFA. Om du vill aktivera MFA för vissa RADIUS-klienter kan du konfigurera två NPS-servrar och installera tillägget på endast en av dem. Konfigurera RADIUS-klienter som du vill kräva MFA för att skicka begäranden till NPS-server som konfigurerats med tillägget och andra RADIUS-klienter till NPS-servern inte har konfigurerats med tillägget.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Förbereda för användare som inte har registrerats för MFA

Om du har användare som inte har registrerats för MFA, kan du bestämma vad som händer när de försöker att autentisera. Använda registerinställningen *REQUIRE_USER_MATCH* i registersökvägen *HKLM\Software\Microsoft\AzureMFA* att styra hur funktionen. Den här inställningen har ett enda konfigurationsalternativ:

| Nyckel | Value | Standard |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | SANT/FALSKT | Inte har angetts (motsvarar SANT) |

Syftet med den här inställningen är att avgöra vad du ska göra när en användare inte har registrerats för MFA. När nyckeln finns inte, har inte angetts eller är satt till SANT, och användaren har registrerats inte sedan tillägget misslyckas MFA-kontrollen. När nyckeln är inställt på FALSKT och användaren inte har registrerats, fortsätter autentiseringen utan att behöva genomföra MFA. Om en användare är registrerad i MFA, måste de autentiseras med MFA även om REQUIRE_USER_MATCH är inställd på FALSE.

Du kan välja att skapa den här nyckeln och ange den till FALSE, medan användarna är och kan inte alla registreras för Azure MFA ännu. Eftersom inställningen nyckeln tillåter att användare som inte har registrerats för MFA för att logga in, bör du dock ta bort den här nyckeln innan du fortsätter till produktion.

## <a name="troubleshooting"></a>Felsökning

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hur bekräftar jag att klientcertifikatet är korrekt installerat?

Leta efter ett självsignerat certifikat som skapas av installationsprogrammet i certifikatarkiv och kontrollera att den privata nyckeln har behörigheterna för användaren **NÄTVERKSTJÄNST**. Certifikatet har ett ämnesnamn **CN \<tenantid\>, OU = Microsoft NPS-tillägget**

Självsignerat certifikat som genereras av den *AzureMfaNpsExtnConfigSetup.ps1* skript har också en giltigheten livslängd på två år. När du verifierat att certifikatet har installerats, bör du också kontrollera att certifikatet inte har gått ut.

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hur kan jag kontrollera att min klientcertifikatet är kopplade till min klient i Azure Active Directory?

Öppna PowerShell-Kommandotolken och kör följande kommandon:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Dessa kommandon skriva ut alla certifikat som kopplar din klient till din instans av NPS-tillägget i PowerShell-sessionen. Leta efter certifikatet genom att exportera dina klientcertifikatet som en ”Base64-kodad x.509 (.cer)” fil utan den privata nyckeln och jämför den med i listan från PowerShell.

Följande kommando skapar en fil med namnet ”npscertificate” på enhet ”c:” i formatet .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

När du har kört det här kommandot kan gå att enhet C, leta reda på filen och dubbelklicka på den. Gå till information och rulla ”tumavtryck”, jämföra tumavtrycket för certifikatet som installerades på den här servern. Tumavtryck för certifikatet måste matcha.

Giltigt-från- och -tills tidsstämplar som är i läsbara form, som kan användas för att filtrera bort uppenbara misfits om kommandot returnerar mer än ett certifikat.

-------------------------------------------------------------

### <a name="why-cant-i-sign-in"></a>Varför kan jag logga in?

Kontrollera att lösenordet inte har gått ut. NPS-tillägget stöder inte ändring av lösenord som en del av arbetsflödet för inloggning. Kontakta din organisations IT-personal för ytterligare hjälp.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Varför är Mina förfrågningar fungerar med ADAL-tokenfel?

Det här felet kan bero på något av flera skäl. Följ dessa steg för att felsöka:

1. Starta om NPS-servern.
2. Kontrollera att klientcertifikatet är korrekt installerat.
3. Kontrollera att certifikatet är associerat med din klientorganisation i Azure AD.
4. Kontrollera att https://login.microsoftonline.com/ kan nås från servern som kör tillägget.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Varför misslyckas autentiseringen med ett fel i HTTP-loggar som talar om att användaren inte kan hittas?

Kontrollera att AD Connect körs och att användaren finns i både Windows Active Directory och Azure Active Directory.

-------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Varför ser jag HTTP anslutningsfel i loggar med min autentiseringar misslyckas?

Kontrollera att https://adnotifications.windowsazure.com kan nås från servern som kör NPS-tillägget.

-------------------------------------------------------------

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Varför autentiseringen inte fungerar, trots ett giltigt certifikat vara närvarande?

Om din tidigare datorcertifikat har upphört att gälla och ett nytt certifikat har skapats, bör du ta bort utgångna certifikat. Med utgångna certifikat kan orsaka problem med NPS-tillägget startar.

Kontrollera det lokala datorkontot Certificate Store med hjälp av MMC för att kontrollera om du har ett giltigt certifikat och se till att certifikatet inte har passerats dess förfallodatum. Generera ett nyligen giltigt certifikat genom att köra stegen i avsnittet ”[Kör PowerShell-skript](#run-the-powershell-script)”

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Hantering av TLS/SSL-protokoll och chiffersviter

Vi rekommenderar att äldre och svagare krypteringssviter inaktiveras eller tas bort om inte krävs av din organisation. Information om hur du slutför den här aktiviteten finns i artikeln om hur du [hanterar SSL/TLS-protokoll och chiffersviter för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

## <a name="next-steps"></a>Nästa steg

- Konfigurera alternativa ID: N för inloggning eller Ställ in en undantagslista för IP-adresser som inte ska utföra tvåstegsverifiering i [avancerade konfigurationsalternativ för NPS-tillägget för Multifaktorautentisering](howto-mfa-nps-extension-advanced.md)

- Lär dig hur du integrerar [fjärrskrivbordsgateway](howto-mfa-nps-extension-rdg.md) och [VPN-servrar](howto-mfa-nps-extension-vpn.md) med NPS-tillägget

- [Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
