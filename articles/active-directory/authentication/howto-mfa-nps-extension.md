---
title: Tillhandahålla Azure MFA-funktioner med NPS - Azure Active Directory
description: Lägga till molnbaserade tvåstegsverifieringsfunktioner i din befintliga autentiseringsinfrastruktur
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c8606f0b7ab47d624ec66c8cda539e571cec6ce
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393056"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication

NPS-tillägget (Network Policy Server) för Azure MFA lägger till molnbaserade MFA-funktioner i autentiseringsinfrastrukturen med hjälp av dina befintliga servrar. Med NPS-tillägget kan du lägga till telefonsamtal, sms eller telefonappverifiering i ditt befintliga autentiseringsflöde utan att behöva installera, konfigurera och underhålla nya servrar. 

Det här tillägget skapades för organisationer som vill skydda VPN-anslutningar utan att distribuera Azure MFA Server. NPS-tillägget fungerar som ett kort mellan RADIUS och molnbaserade Azure MFA för att tillhandahålla en andra autentiseringsfaktor för federerade eller synkroniserade användare.

När NPS-tillägget för Azure MFA ska kunna användas innehåller autentiseringsflödet följande komponenter: 

1. **NAS/VPN Server** tar emot begäranden från VPN-klienter och konverterar dem till RADIUS-begäranden till NPS-servrar. 
2. **NPS Server** ansluter till Active Directory för att utföra den primära autentiseringen för RADIUS-begäranden och skickar begäran efter lyckade resultat till alla installerade tillägg.  
3. **NPS-tillägg** utlöser en begäran till Azure MFA för sekundär autentisering. När tillägget tar emot svaret, och om MFA-utmaningen lyckas, slutförs autentiseringsbegäran genom att tillhandahålla NPS-servern med säkerhetstoken som innehåller ett MFA-anspråk, utfärdat av Azure STS.  
4. **Azure MFA** kommunicerar med Azure Active Directory för att hämta användarens information och utför den sekundära autentiseringen med en verifieringsmetod som konfigurerats för användaren.

Följande diagram illustrerar det här flödet för autentiseringsbegäran på hög nivå: 

![Flödesdiagram för autentisering](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planera distributionen

NPS-tillägget hanterar automatiskt redundans, så du behöver ingen särskild konfiguration.

Du kan skapa så många Azure MFA-aktiverade NPS-servrar som du behöver. Om du installerar flera servrar bör du använda ett differensklientcertifikat för var och en av dem. Att skapa ett certifikat för varje server innebär att du kan uppdatera varje certifikat individuellt och inte oroa dig för driftstopp på alla dina servrar.

VPN-servrar dirigerar autentiseringsbegäranden, så de måste vara medvetna om de nya Azure MFA-aktiverade NPS-servrarna.

## <a name="prerequisites"></a>Krav

NPS-tillägget är tänkt att fungera med din befintliga infrastruktur. Kontrollera att du har följande förutsättningar innan du börjar.

### <a name="licenses"></a>Licenser

NPS-tillägget för Azure MFA är tillgängligt för kunder med [licenser för Azure Multi-Factor Authentication](multi-factor-authentication.md) (ingår i Azure AD Premium, EMS eller en fristående MFA-licens). Förbrukningsbaserade licenser för Azure MFA, till exempel per användare eller per autentiseringslicenser, är inte kompatibla med NPS-tillägget. 

### <a name="software"></a>Programvara

Windows Server 2008 R2 SP1 eller senare.

### <a name="libraries"></a>Bibliotek

Dessa bibliotek installeras automatiskt med tillägget.

- [Visuella C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-modul för Windows PowerShell version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory Module för Windows PowerShell installeras, om den inte redan finns, via ett konfigurationsskript som du kör som en del av installationsprocessen. Det finns ingen anledning att installera den här modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory"></a>Azure Active Directory

Alla som använder NPS-tillägget måste synkroniseras till Azure Active Directory med Azure AD Connect och måste vara registrerade för MFA.

När du installerar tillägget behöver du katalog-ID och administratörsautentiseringsuppgifter för din Azure AD-klientorganisation. Du hittar ditt katalog-ID i [Azure-portalen](https://portal.azure.com). Logga in som administratör. Sök efter och välj **Azure Active Directory**och välj sedan **Egenskaper**. Kopiera GUID i rutan **Katalog-ID** och spara det. Du använder det här GUID som klient-ID när du installerar NPS-tillägget.

![Hitta ditt katalog-ID under Azure Active Directory-egenskaper](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Nätverkskrav

NPS-servern måste kunna kommunicera med följande webbadresser över portarna 80 och 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https:\//credentials.azure.com

Dessutom krävs anslutning till följande webbadresser för att slutföra [installationen av kortet med det medföljande PowerShell-skriptet](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Förbered din miljö

Innan du installerar NPS-tillägget vill du förbereda miljön för att hantera autentiseringstrafiken.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Aktivera NPS-rollen på en domänansluten server

NPS-servern ansluter till Azure Active Directory och autentiserar MFA-begäranden. Välj en server för den här rollen. Vi rekommenderar att du väljer en server som inte hanterar begäranden från andra tjänster, eftersom NPS-tillägget genererar fel för alla begäranden som inte är RADIUS. NPS-servern måste ställas in som den primära och sekundära autentiseringsservern för din miljö. Den kan inte proxy RADIUS-begäranden till en annan server.

1. Öppna guiden Lägg **till roller och funktioner** på snabbstartsmenyn Serverhanteraren på servern.
2. Välj **Rollbaserad eller funktionsbaserad installation** för din installationstyp.
3. Välj serverrollen **Nätverksprincip och Åtkomsttjänster.** Ett fönster kan dyka upp för att informera dig om nödvändiga funktioner för att köra den här rollen.
4. Fortsätt genom guiden tills bekräftelsesidan. Välj **Installera**.

Nu när du har en server som är avsedd för NPS bör du också konfigurera den här servern för att hantera inkommande RADIUS-begäranden från VPN-lösningen.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurera din VPN-lösning för att kommunicera med NPS-servern

Beroende på vilken VPN-lösning du använder varierar stegen för att konfigurera RADIUS-autentiseringsprincipen. Konfigurera den här principen så att den pekar på RADIUS NPS-servern.

### <a name="sync-domain-users-to-the-cloud"></a>Synkronisera domänanvändare till molnet

Det här steget kanske redan är klart för din klient, men det är bra att dubbelkolla att Azure AD Connect har synkroniserat dina databaser nyligen.

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory** > **Azure AD Connect**
3. Kontrollera att synkroniseringsstatusen är **aktiverad** och att den senaste synkroniseringen var mindre än en timme sedan.

Om du behöver starta en ny synkroniseringsrunda, oss instruktionerna i [Azure AD Connect sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Ta reda på vilka autentiseringsmetoder användarna kan använda

Det finns två faktorer som påverkar vilka autentiseringsmetoder som är tillgängliga med en NPS-tilläggsdistribution:

1. Lösenordskrypteringsalgoritmen som används mellan RADIUS-klienten (VPN, Netscaler-servern eller andra) och NPS-servrarna.
   - **PAP** stöder alla autentiseringsmetoder för Azure MFA i molnet: telefonsamtal, enkelriktad textmeddelande, mobilappanmälan, OATH-maskinvarutoken och verifieringskod för mobilappar.
   - **CHAPV2** och **EAP** stöder telefonsamtal och mobilapp anmälan.

      > [!NOTE]
      > När du distribuerar NPS-tillägget använder du dessa faktorer för att utvärdera vilka metoder som är tillgängliga för användarna. Om RADIUS-klienten stöder PAP, men klientens användargränssnitt inte har inmatningsfält för en verifieringskod, är telefonsamtal och mobilappmeddelanden de två alternativ som stöds.
      >
      > Om din VPN-klient UX stöder indatafält och du har konfigurerat Network Access Policy - autentiseringen kan lyckas, men ingen av DE RADIUS-attribut som konfigurerats i nätverksprincipen kommer inte att tillämpas på varken Network Access Device, som RRAS-servern eller VPN-klienten. Därför kan VPN-klienten ha mer åtkomst än önskat eller mindre till ingen åtkomst.
      >

2. De inmatningsmetoder som klientprogrammet (VPN, Netscaler-servern eller någon annan) kan hantera. Har VPN-klienten till exempel några sätt att tillåta användaren att skriva in en verifieringskod från en text- eller mobilapp?

Du kan [inaktivera autentiseringsmetoder som inte stöds](howto-mfa-mfasettings.md#verification-methods) i Azure.

### <a name="register-users-for-mfa"></a>Registrera användare för MFA

Innan du distribuerar och använder NPS-tillägget måste användare som måste utföra tvåstegsverifiering registreras för MFA. Mer omedelbart, för att testa tillägget när du distribuerar det, behöver du minst ett testkonto som är helt registrerat för multifaktorautentisering.

Så här hämtar du ett testkonto:

1. Logga in [https://aka.ms/mfasetup](https://aka.ms/mfasetup) med ett testkonto.
2. Ställ in en verifieringsmetod genom att följa anvisningarna.
3. [Skapa en princip för villkorlig åtkomst](howto-mfa-getstarted.md#create-conditional-access-policy) för att kräva multifaktorautentisering för testkontot.

## <a name="install-the-nps-extension"></a>Installera NPS-tillägget

> [!IMPORTANT]
> Installera NPS-tillägget på en annan server än VPN-åtkomstpunkten.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Hämta och installera NPS-tillägget för Azure MFA

1. [Hämta NPS-tillägget](https://aka.ms/npsmfa) från Microsoft Download Center.
2. Kopiera binärfilen till den nätverksprincipserver som du vill konfigurera.
3. Kör *setup.exe* och följ installationsanvisningarna. Om du stöter på fel dubbelkollar du att de två biblioteken från det nödvändiga avsnittet har installerats.

#### <a name="upgrade-the-nps-extension"></a>Uppgradera NPS-tillägget

När du uppgraderar en befintlig INSTALLATION av NPS-tillägg, för att undvika en omstart av den underliggande servern, utför du följande steg:

1. Avinstallera den befintliga versionen
1. Kör den nya installationsprogrammet
1. Starta om tjänsten Network Policy Server (IAS)

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Installationsprogrammet skapar ett PowerShell-skript på `C:\Program Files\Microsoft\AzureMfa\Config` den här platsen: (där C:\ är din installationsenhet). Det här PowerShell-skriptet utför följande åtgärder varje gång det körs:

- Skapa ett självsignerat certifikat.
- Associera den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD.
- Lagra certifikatet i den lokala maskincertbutiken.
- Bevilja åtkomst till certifikatets privata nyckel till Nätverksanvändare.
- Starta om NPS.

Om du inte vill använda dina egna certifikat (i stället för de självsignerade certifikat som PowerShell-skriptet genererar) kör du PowerShell-skriptet för att slutföra installationen. Om du installerar tillägget på flera servrar bör var och en ha ett eget certifikat.

1. Kör Windows PowerShell som administratör.
2. Byt kataloger.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Kör PowerShell-skriptet som skapats av installationsprogrammet.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Logga in på Azure AD som administratör.
5. PowerShell-uppmaningar till ditt klient-ID. Använd det guid för katalog-ID som du kopierade från Azure-portalen i avsnittet förutsättningar.
6. PowerShell visar ett lyckat meddelande när skriptet är klart.  

Upprepa dessa steg på alla ytterligare NPS-servrar som du vill ställa in för belastningsutjämning.

Om ditt tidigare datorcertifikat har upphört att gälla och ett nytt certifikat har genererats bör du ta bort alla utgångna certifikat. Om du har utgångna certifikat kan det orsaka problem med att NPS-tillägget startas.

> [!NOTE]
> Om du använder dina egna certifikat i stället för att generera certifikat med PowerShell-skriptet kontrollerar du att de anpassar sig till NPS-namngivningskonventionen. Ämnesnamnet måste vara **\<CN=\>TenantID ,OU=Microsoft NPS-tillägg**. 

### <a name="microsoft-azure-government-additional-steps"></a>Ytterligare steg i Microsoft Azure Government

För kunder som använder Azure Government-molnet krävs följande ytterligare konfigurationssteg på varje NPS-server:

1. Öppna **Registereditorn** på NPS-servern.
1. Navigera till `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Ange följande nyckelvärden:

    | Registernyckel       | Värde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Upprepa de två föregående stegen för att ange registernyckelvärden för varje NPS-server.
1. Starta om NPS-tjänsten för varje NPS-server.

    För minimal påverkan, ta varje NPS-server ur NLB-rotationen en i taget och vänta på att alla anslutningar ska tömmas.

### <a name="certificate-rollover"></a>Överrullning av certifikat

Med versionen 1.0.1.32 av NPS-tillägget stöds nu läsning av flera certifikat. Den här funktionen hjälper till att underlätta rullande certifikatuppdateringar innan de upphör att gälla. Om din organisation kör en tidigare version av NPS-tillägget bör du uppgradera till version 1.0.1.32 eller senare.

Certifikat som skapats av skriptet är giltiga i `AzureMfaNpsExtnConfigSetup.ps1` 2 år. IT-organisationer bör övervaka certifikat för utgångsdatum. Certifikat för NPS-tillägget placeras i certifikatarkivet Lokal dator under Personligt och utfärdas till klient-ID som anges i skriptet.

När ett certifikat närmar sig utgångsdatumet bör ett nytt certifikat skapas för att ersätta det.  Den här processen utförs `AzureMfaNpsExtnConfigSetup.ps1` genom att köra igen och behålla samma klient-ID när du uppmanas. Den här processen bör upprepas på varje NPS-server i din miljö.

## <a name="configure-your-nps-extension"></a>Konfigurera NPS-tillägget

Det här avsnittet innehåller designöverväganden och förslag på lyckade NPS-tilläggsdistributioner.

### <a name="configuration-limitations"></a>Begränsningar för konfiguration

- NPS-tillägget för Azure MFA innehåller inte verktyg för att migrera användare och inställningar från MFA Server till molnet. Därför föreslår vi att du använder tillägget för nya distributioner i stället för befintlig distribution. Om du använder tillägget på en befintlig distribution måste användarna utföra korrekturupp- -igen för att fylla i sina MFA-uppgifter i molnet.  
- NPS-tillägget använder UPN från den lokala Active-katalogen för att identifiera användaren på Azure MFA för att utföra sekundärautentisering. Tillägget kan konfigureras för att använda en annan identifierare som alternativt inloggnings-ID eller anpassat Active Directory-fält än UPN. Mer information finns i artikeln [Avancerade konfigurationsalternativ för NPS-tillägget för multifaktorautentisering](howto-mfa-nps-extension-advanced.md).
- Alla krypteringsprotokoll stöder inte alla verifieringsmetoder.
   - **PAP** stöder telefonsamtal, enkelriktad sms, mobilappsavisering och verifieringskod för mobilappar
   - **CHAPV2** och **EAP** stöder telefonsamtal och meddelanden om mobilappar

### <a name="control-radius-clients-that-require-mfa"></a>Kontrollera RADIUS-klienter som kräver MFA

När du har aktiverat MFA för en RADIUS-klient med NPS-tillägget krävs alla autentiseringar för den här klienten för att utföra MFA. Om du vill aktivera MFA för vissa RADIUS-klienter men inte andra kan du konfigurera två NPS-servrar och installera tillägget på bara en av dem. Konfigurera RADIUS-klienter som du vill kräva att MFA skickar begäranden till NPS-servern som konfigurerats med tillägget och andra RADIUS-klienter till NPS-servern som inte är konfigurerad med tillägget.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Förbered för användare som inte är registrerade för MFA

Om du har användare som inte är registrerade för MFA kan du bestämma vad som händer när de försöker autentisera. Använd registerinställningen *REQUIRE_USER_MATCH* i registersökvägen *HKLM\Software\Microsoft\AzureMFA* för att styra funktionsbeteendet. Den här inställningen har ett enda konfigurationsalternativ:

| Nyckel | Värde | Default |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | SANT/FALSKT | Inte inställd (motsvarande SANT) |

Syftet med den här inställningen är att avgöra vad som ska göra när en användare inte är registrerad för MFA. När nyckeln inte finns, inte är inställd eller är inställd på SANT och användaren inte är registrerad, misslyckas tillägget MFA-utmaningen. När nyckeln är inställd på FALSKT och användaren inte registreras fortsätter autentiseringen utan att utföra MFA. Om en användare är registrerad i MFA måste de autentisera med MFA även om REQUIRE_USER_MATCH är inställt på FALSKT.

Du kan välja att skapa den här nyckeln och ställa in den på FALSE medan användarna är ombord, och kanske inte alla är registrerade för Azure MFA ännu. Men eftersom inställningen av nyckeln tillåter användare som inte är registrerade för MFA att logga in, bör du ta bort den här nyckeln innan du går till produktion.

## <a name="troubleshooting"></a>Felsökning

### <a name="nps-extension-health-check-script"></a>Hälsokontrollskript för NPS-tillägg

Följande skript är tillgängligt för att utföra grundläggande hälsokontrollsteg vid felsökning av NPS-tillägget.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hur kontrollerar jag att klientcertifikatet är installerat som förväntat?

Leta efter det självsignerade certifikat som skapats av installationsprogrammet i cert-arkivet och kontrollera att den privata nyckeln har behörigheter som beviljats **användarens NETWORK SERVICE**. Certifikatet har ett ämnesnamn **för CN \<tenantid\>, OU = Microsoft NPS Extension**

Självsignerade certifikat som genereras av *AzureMfaNpsExtnConfigSetup.ps1-skriptet* har också en giltighetstid på två år. När du kontrollerar att certifikatet är installerat bör du också kontrollera att certifikatet inte har upphört att gälla.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hur kan jag verifiera att mitt klientcertifikat är associerat med min klient i Azure Active Directory?

Öppna Kommandotolken i PowerShell och kör följande kommandon:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Dessa kommandon skriver ut alla certifikat som associerar din klient med din instans av NPS-tillägget i PowerShell-sessionen. Leta efter ditt certifikat genom att exportera ditt klientcert som en "Base-64 kodade X.509(.cer)" fil utan den privata nyckeln och jämför den med listan från PowerShell.

Följande kommando kommer att skapa en fil med namnet "npscertificate" på din "C:" enhet i formatet .cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

När du har kört det här kommandot går du till C-enheten, letar reda på filen och dubbelklickar på den. Gå till detaljer och bläddra ner till "tumavtryck", jämför tumavtrycket för certifikatet som är installerat på servern med det här. Certifikatets tumavtryck ska stämma överens.

Valid-From och Valid-Until-tidsstämplar, som är i läsbar form, kan användas för att filtrera bort uppenbara felpassningar om kommandot returnerar mer än ett certifikat.

---

### <a name="why-cannot-i-sign-in"></a>Varför kan jag inte logga in?

Kontrollera att lösenordet inte har upphört att gälla. NPS-tillägget stöder inte att lösenord ändras som en del av inloggningsarbetsflödet. Kontakta organisationens IT-personal för ytterligare hjälp.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Varför misslyckas mina begäranden med ADAL-tokenfel?

Det här felet kan bero på en av flera orsaker. Så här hjälper du till att felsöka:

1. Starta om NPS-servern.
2. Kontrollera att klientcertifikatet är installerat som förväntat.
3. Kontrollera att certifikatet är associerat med din klient på Azure AD.
4. Kontrollera att `https://login.microsoftonline.com/` kan nås från servern som kör tillägget.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Varför misslyckas autentisering med ett fel i HTTP-loggar som anger att användaren inte hittas?

Kontrollera att AD Connect körs och att användaren finns i både Active Directory och Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Varför ser jag HTTP-anslutningsfel i loggar med alla mina autentiseringar misslyckas?

Kontrollera att https://adnotifications.windowsazure.com kan nås från servern som kör NPS-tillägget.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Varför fungerar inte autentiseringen, trots att ett giltigt certifikat finns?

Om ditt tidigare datorcertifikat har upphört att gälla och ett nytt certifikat har genererats bör du ta bort alla utgångna certifikat. Om du har utgångna certifikat kan det orsaka problem med att NPS-tillägget startas.

Kontrollera om du har ett giltigt certifikat genom att kontrollera det lokala datorkontots certifikatarkiv med MMC och se till att certifikatet inte har passerat utgångsdatumet. Om du vill generera ett nyligen giltigt certifikat kör du stegen under avsnittet "[Kör PowerShell-skriptet](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Hantering av TLS/SSL-protokoll och chiffersviter

Vi rekommenderar att äldre och svagare chiffersviter inaktiveras eller tas bort om det inte krävs av din organisation. Information om hur du slutför den här aktiviteten finns i artikeln om hur du [hanterar SSL/TLS-protokoll och chiffersviter för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Ytterligare felsökning

Ytterligare felsökningsvägledning och möjliga lösningar finns i artikeln [Lös felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt och konfiguration av nätverksprincipserver i Windows Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Konfigurera alternativa ID:n för inloggning eller konfigurera en undantagslista för IPs som inte ska utföra tvåstegsverifiering i [avancerade konfigurationsalternativ för NPS-tillägget för multifaktorautentisering](howto-mfa-nps-extension-advanced.md)

- Lär dig hur du integrerar [fjärrskrivbordsgateway-](howto-mfa-nps-extension-rdg.md) och [VPN-servrar](howto-mfa-nps-extension-vpn.md) med NPS-tillägget

- [Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
