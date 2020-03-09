---
title: Tillhandahåll Azure MFA-funktioner med NPS-Azure Active Directory
description: Lägg till molnbaserade tvåstegsverifiering i din befintliga infrastruktur för autentisering
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
ms.openlocfilehash: 43f355f22774477466d2965cef02adcc4ec4f497
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378111"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication

Nätverks princip Server (NPS)-tillägget för Azure MFA lägger till molnbaserade MFA-funktioner till din infrastruktur för autentisering med hjälp av befintliga servrar. Med NPS-tillägget kan du lägga till telefonsamtal, textmeddelande eller telefon programs verifiering till ditt befintliga autentiseringspaket utan att behöva installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda VPN-anslutningar utan att distribuera Azure MFA-servern. NPS-tillägget fungerar som ett kort mellan RADIUS och molnbaserad Azure MFA för att tillhandahålla en andra faktor för autentisering för federerade eller synkroniserade användare.

När du använder NPS-tillägget för Azure MFA innehåller autentiserings flödet följande komponenter: 

1. **NAS/VPN-servern** tar emot begär Anden från VPN-klienter och konverterar dem till RADIUS-begäranden till NPS-servrar. 
2. **NPS-servern** ansluter till Active Directory för att utföra den primära autentiseringen för RADIUS-begäranden och skickar begäran till eventuella installerade tillägg vid lyckad.  
3. **NPS-tillägget** utlöser en begäran till Azure MFA för den sekundära autentiseringen. När tillägget får svaret, och om MFA-utmaningen lyckas, slutförs autentiseringsbegäran genom att tillhandahålla NPS-servern med säkerhetstoken som innehåller ett MFA-anspråk som utfärdats av Azure STS.  
4. **Azure MFA** kommunicerar med Azure Active Directory för att hämta användarens information och utför den sekundära autentiseringen med hjälp av en verifieringsmetod som kon figurer ATS för användaren.

Följande diagram illustrerar det här flödet för begäran om autentisering på hög nivå: 

![Flödes schema för autentisering](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planera distributionen

NPS-tillägget hanterar automatiskt redundans, så du behöver inte en speciell konfiguration.

Du kan skapa så många Azure MFA-aktiverade NPS-servrar som du behöver. Om du installerar flera servrar bör du använda ett skillnads klient certifikat för var och en av dem. Att skapa ett certifikat för varje server innebär att du kan uppdatera varje certifikat individuellt och inte bekymra dig om nedtid på alla dina servrar.

VPN-servrar dirigerar autentiseringsbegäranden, så de måste vara medvetna om de nya Azure MFA-aktiverade NPS-servrarna.

## <a name="prerequisites"></a>Förutsättningar

NPS-tillägget är avsett att fungera med din befintliga infrastruktur. Kontrol lera att du har följande krav innan du börjar.

### <a name="licenses"></a>Licenser

NPS-tillägget för Azure MFA är tillgängligt för kunder med [licenser för azure Multi-Factor Authentication](multi-factor-authentication.md) (ingår i Azure AD Premium, EMS eller en fristående MFA-licens). Användnings licenser för Azure MFA, till exempel per användare eller per autentisering, är inte kompatibla med NPS-tillägget. 

### <a name="software"></a>Programvara

Windows Server 2008 R2 SP1 eller senare.

### <a name="libraries"></a>Bibliotek

Dessa bibliotek installeras automatiskt med tillägget.

- [Visual C++ Redistributable-paket för visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory-modul för Windows PowerShell version 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Microsoft Azure Active Directory-modul för Windows PowerShell installeras, om den inte redan finns, via ett konfigurations skript som du kör som en del av installations processen. Du behöver inte installera den här modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory"></a>Azure Active Directory

Alla som använder NPS-tillägget måste synkroniseras till Azure Active Directory att använda Azure AD Connect och måste vara registrerade för MFA.

När du installerar tillägget behöver du katalog-ID och admin-autentiseringsuppgifter för din Azure AD-klient. Du kan hitta ditt katalog-ID i [Azure Portal](https://portal.azure.com). Logga in som administratör. Sök efter och välj **Azure Active Directory**och välj sedan **Egenskaper**. Kopiera GUID i rutan **katalog-ID** och spara den. Du använder det här GUID som klient-ID när du installerar NPS-tillägget.

![Hitta ditt katalog-ID under Azure Active Directory egenskaper](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Nätverkskrav

NPS-servern måste kunna kommunicera med följande URL: er över portarna 80 och 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Dessutom krävs anslutning till följande URL: er för att slutföra [installationen av kortet med hjälp av det angivna PowerShell-skriptet](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Förbered din miljö

Innan du installerar NPS-tillägget vill du förbereda din miljö för att hantera autentiserings trafiken.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Aktivera NPS-rollen på en domänansluten Server

NPS-servern ansluter till Azure Active Directory och autentiserar MFA-begärandena. Välj en server för den här rollen. Vi rekommenderar att du väljer en server som inte hanterar förfrågningar från andra tjänster, eftersom NPS-tillägget genererar fel för förfrågningar som inte är RADIUS. NPS-servern måste vara konfigurerad som den primära och sekundära autentiseringsservern för din miljö. den kan inte proxy RADIUS-begäranden till en annan server.

1. På din server öppnar du **guiden Lägg till roller och funktioner** från snabb starts menyn för Serverhanteraren.
2. Välj **rollbaserad eller funktions baserad installation** av installations typen.
3. Välj Server rollen **nätverks policy och åtkomst tjänster** . Ett fönster kan visas för att informera dig om vilka funktioner som krävs för att köra den här rollen.
4. Fortsätt med guiden tills bekräftelse sidan. Välj **Installera**.

Nu när du har en server som är avsedd för NPS bör du även konfigurera den här servern så att den hanterar inkommande RADIUS-begäranden från VPN-lösningen.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurera VPN-lösningen för att kommunicera med NPS-servern

Stegen för att konfigurera din RADIUS-autentiseringsprincip varierar beroende på vilken VPN-lösning du använder. Konfigurera den här principen så att den pekar på RADIUS NPS-servern.

### <a name="sync-domain-users-to-the-cloud"></a>Synkronisera domän användare till molnet

Det här steget kanske redan har slutförts på din klient, men det är bäst att kontrol lera att Azure AD Connect har synkroniserat dina databaser nyligen.

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory** > **Azure AD Connect**
3. Kontrol lera att din synkroniseringsstatus är **aktive rad** och att din senaste synkronisering var mindre än en timme sedan.

Om du behöver starta en ny avrundning av synkroniseringen kan du använda anvisningarna i [Azure AD Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bestäm vilka autentiseringsmetoder som användarna kan använda

Det finns två faktorer som påverkar vilka autentiseringsmetoder som är tillgängliga med en distribution av NPS-tillägg:

1. Algoritmen för lösen ords kryptering som används mellan RADIUS-klienten (VPN, NetScaler Server eller andra) och NPS-servrarna.
   - **PAP** stöder alla autentiseringsmetoder i Azure MFA i molnet: telefonsamtal, envägs textmeddelande, meddelande från mobilapp, Oath-token och verifierings kod för mobilapp.
   - **CHAPv2** -och **EAP** -support för telefonsamtal och aviseringar för mobilapp.

      > [!NOTE]
      > När du distribuerar NPS-tillägget använder du dessa faktorer för att utvärdera vilka metoder som är tillgängliga för dina användare. Om din RADIUS-klient har stöd för PAP, men klientens UX saknar indatafält för en verifierings kod, är telefonsamtal och aviseringar för mobilapp de två alternativen som stöds.
      >
      > Om ditt gränssnitt för VPN-klienten har stöd för indatatyper och du har konfigurerat principen för nätverks åtkomst, kan autentiseringen lyckas, men inget av de RADIUS-attribut som kon figurer ATS i nätverks principen tillämpas på ingen nätverks åtkomst enhet, som RRAS-servern eller VPN-klienten. Det innebär att VPN-klienten kan ha mer åtkomst än vad som önskas eller är mindre för att få åtkomst.
      >

2. De ingångs metoder som klient programmet (VPN, NetScaler-servern eller andra) kan hantera. Exempelvis har VPN-klienten några sätt att tillåta användaren att ange en verifierings kod från en text-eller mobilapp?

Du kan [inaktivera autentiseringsmetoder som inte stöds](howto-mfa-mfasettings.md#verification-methods) i Azure.

### <a name="register-users-for-mfa"></a>Registrera användare för MFA

Innan du distribuerar och använder NPS-tillägget måste användare som krävs för att utföra tvåstegsverifiering registreras för MFA. Mer än en gång för att testa tillägget när du distribuerar det måste du ha minst ett test konto som är fullständigt registrerat för Multi-Factor Authentication.

Använd de här stegen för att få ett test konto startat:

1. Logga in på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) med ett test konto.
2. Följ anvisningarna för att ställa in en verifieringsmetod.
3. [Skapa en princip för villkorlig åtkomst](howto-mfa-getstarted.md#create-conditional-access-policy) för att kräva Multi-Factor Authentication för test kontot.

## <a name="install-the-nps-extension"></a>Installera NPS-tillägget

> [!IMPORTANT]
> Installera NPS-tillägget på en annan server än VPN-åtkomst punkten.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Hämta och installera NPS-tillägget för Azure MFA

1. [Ladda ned NPS-tillägget](https://aka.ms/npsmfa) från Microsoft Download Center.
2. Kopiera binärfilen till den nätverks princip server som du vill konfigurera.
3. Kör *Setup. exe* och följ installations anvisningarna. Om du stöter på fel, kontrol lera att de två biblioteken från avsnittet förutsättning har installerats.

#### <a name="upgrade-the-nps-extension"></a>Uppgradera NPS-tillägget

När du uppgraderar en befintlig installation av NPS-tillägg måste du utföra följande steg för att undvika att starta om den underliggande servern:

1. Avinstallera den befintliga versionen
1. Kör det nya installations programmet
1. Starta om tjänsten nätverks princip Server (IAS)

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Installations programmet skapar ett PowerShell-skript på den här platsen: `C:\Program Files\Microsoft\AzureMfa\Config` (där C:\ är installations enheten). Det här PowerShell-skriptet utför följande åtgärder varje gången det körs:

- Skapa ett självsignerat certifikat.
- Koppla certifikatets offentliga nyckel till tjänstens huvud namn i Azure AD.
- Lagra certifikatet i den lokala datorns certifikat arkiv.
- Bevilja åtkomst till certifikatets privata nyckel till nätverks användare.
- Starta om NPS.

Om du inte vill använda dina egna certifikat (i stället för de självsignerade certifikat som PowerShell-skriptet genererar) kör du PowerShell-skriptet för att slutföra installationen. Om du installerar tillägget på flera servrar bör var och en ha sitt eget certifikat.

1. Kör Windows PowerShell som administratör.
2. Ändra kataloger.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Kör PowerShell-skriptet som skapats av installations programmet.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Logga in på Azure AD som administratör.
5. PowerShell-prompt för klient-ID. Använd det katalog-ID-GUID som du kopierade från Azure Portal i avsnittet krav.
6. PowerShell visar ett meddelande när skriptet har slutförts.  

Upprepa de här stegen på eventuella ytterligare NPS-servrar som du vill konfigurera för belastnings utjämning.

Om ditt tidigare dator certifikat har upphört att gälla och ett nytt certifikat har skapats, bör du ta bort eventuella utgångna certifikat. Certifikat som har upphört att gälla kan orsaka problem med att NPS-tillägget startar.

> [!NOTE]
> Om du använder egna certifikat i stället för att skapa certifikat med PowerShell-skriptet ser du till att de överensstämmer med namngivnings konventionen för NPS. Ämnes namnet måste vara **CN =\<TenantID\>, OU = Microsoft NPS-tillägg**. 

### <a name="microsoft-azure-government-additional-steps"></a>Microsoft Azure Government ytterligare steg

För kunder som använder Azure Government Cloud krävs följande ytterligare konfigurations steg på varje NPS-server:

1. Öppna **Registereditorn** på NPS-servern.
1. Navigera till `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`. Ange följande nyckel värden:

    | Register nyckel       | Värde |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Upprepa föregående två steg för att ange register nyckel värden för varje NPS-server.
1. Starta om NPS-tjänsten för varje NPS-server.

    För minimal påverkan tar du bort varje NPS-server från NLB-rotationen en i taget och väntar på att alla anslutningar ska tömmas.

### <a name="certificate-rollover"></a>Certifikat förnyelse

Med version 1.0.1.32 av NPS-tillägget stöds nu läsning av flera certifikat. Den här funktionen hjälper till att under lätta rullandea certifikat uppdateringar innan de upphör att gälla. Om din organisation kör en tidigare version av NPS-tillägget bör du uppgradera till version 1.0.1.32 eller högre.

Certifikat som skapats av `AzureMfaNpsExtnConfigSetup.ps1` skriptet är giltiga i två år. IT-organisationer bör övervaka certifikat för förfallo datum. Certifikat för NPS-tillägget placeras i den lokala datorns certifikat Arkiv under personligt och utfärdas till klient-ID: t som angetts för skriptet.

När ett certifikat närmar sig förfallo datumet ska ett nytt certifikat skapas för att ersätta det.  Den här processen åstadkommer du genom att köra `AzureMfaNpsExtnConfigSetup.ps1` igen och behålla samma klient-ID när du uppmanas till det. Den här processen bör upprepas på varje NPS-server i din miljö.

## <a name="configure-your-nps-extension"></a>Konfigurera ditt NPS-tillägg

Det här avsnittet innehåller design överväganden och förslag för lyckade distributioner av NPS-tillägg.

### <a name="configuration-limitations"></a>Konfigurations begränsningar

- NPS-tillägget för Azure MFA innehåller inte verktyg för att migrera användare och inställningar från MFA server till molnet. Därför rekommenderar vi att du använder tillägget för nya distributioner i stället för en befintlig distribution. Om du använder tillägget för en befintlig distribution måste användarna göra ett korrektur och fylla i sina MFA-uppgifter i molnet.  
- NPS-tillägget använder UPN från den lokala Active Directory för att identifiera användaren på Azure MFA för att utföra den sekundära autentiseringen. Tillägget kan konfigureras för att använda en annan identifierare, t. ex. alternativt inloggnings-ID eller anpassade Active Directory fält än UPN. Mer information finns i artikeln [avancerade konfigurations alternativ för NPS-tillägget för Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Alla krypterings protokoll stöder inte alla verifierings metoder.
   - **PAP** stöder telefonsamtal, envägs textmeddelande, mobilapp och verifierings kod för mobilapp
   - **CHAPv2** -och **EAP** -support för telefonsamtal och aviseringar för mobilapp

### <a name="control-radius-clients-that-require-mfa"></a>Kontrol lera RADIUS-klienter som kräver MFA

När du aktiverar MFA för en RADIUS-klient med hjälp av NPS-tillägget, krävs alla autentiseringar för den här klienten för att utföra MFA. Om du vill aktivera MFA för vissa RADIUS-klienter, men inte andra, kan du konfigurera två NPS-servrar och installera tillägget på bara en av dem. Konfigurera RADIUS-klienter som du vill kräva MFA för att skicka begär anden till NPS-servern som kon figurer ATS med tillägget, och andra RADIUS-klienter till NPS-servern som inte har kon figurer ATS med tillägget.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Förbereda för användare som inte har registrerats för MFA

Om du har användare som inte är registrerade för MFA kan du bestämma vad som händer när de försöker autentisera sig. Använd register inställningen *REQUIRE_USER_MATCH* i register Sök vägen *HKLM\Software\Microsoft\AzureMFA* för att styra funktions sättet. Den här inställningen har ett enda konfigurations alternativ:

| Nyckel | Värde | Default |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Inte angivet (motsvarar sant) |

Syftet med den här inställningen är att fastställa vad som ska göras när en användare inte är registrerad för MFA. Om nyckeln inte finns, eller har angetts till TRUE, och användaren inte är registrerad, Miss lyckas tillägget MFA-utmaningen. När nyckeln har angetts till FALSe och användaren inte är registrerad, fortsätter autentiseringen utan att utföra MFA. Om en användare har registrerats i MFA måste de autentiseras med MFA även om REQUIRE_USER_MATCH har angetts till FALSe.

Du kan välja att skapa den här nyckeln och ange den som falsk när dina användare registrerar sig, och de kanske inte alltid registreras för Azure MFA ännu. Men eftersom om du anger nyckeln tillåter användare som inte har registrerats för MFA för att logga in, bör du ta bort den här nyckeln innan du kommer till produktionen.

## <a name="troubleshooting"></a>Felsökning

### <a name="nps-extension-health-check-script"></a>Skript för hälso kontroll av NPS-tillägg

Följande skript är tillgängligt i TechNet-galleriet för att utföra grundläggande hälso kontrolls steg när du felsöker NPS-tillägget.

[MFA_NPS_Troubleshooter. ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hur gör jag för att verifiera att klient certifikatet är installerat som det ska?

Leta efter det självsignerade certifikatet som skapats av installations programmet i certifikat arkivet och kontrol lera att den privata nyckeln har behörighet för användar **nätverks tjänsten**. Certifikatet har ett ämnes namn för **CN \<tenantid\>, OU = Microsoft NPS-tillägg**

Självsignerade certifikat som genereras av *AzureMfaNpsExtnConfigSetup. ps1* -skriptet har också en giltighets tid på två år. När du verifierar att certifikatet har installerats bör du också kontrol lera att certifikatet inte har upphört att gälla.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hur kan jag kontrol lera att mitt klient certifikat är kopplat till min klient organisation i Azure Active Directory?

Öppna PowerShell-Kommandotolken och kör följande kommandon:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Dessa kommandon skriver ut alla certifikat som associerar din klient organisation med din instans av NPS-tillägget i din PowerShell-session. Sök efter ditt certifikat genom att exportera klient certifikatet som en "Base-64-kodad X. 509 (. cer)"-fil utan den privata nyckeln och jämför den med listan från PowerShell.

Följande kommando skapar en fil med namnet "npscertificate" på enheten "C:" i formatet. cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

När du har kört det här kommandot går du till C-enheten, letar upp filen och dubbelklickar på den. Gå till information och rulla ned till "tumavtryck", jämför tumavtrycket för det certifikat som är installerat på servern. Certifikatets tumavtrycken bör överensstämma.

Giltig – från och giltig – tills tidsstämplar, som är i läslig form, kan användas för att filtrera ut uppenbara felaktigheter om kommandot returnerar mer än ett certifikat.

---

### <a name="why-cannot-i-sign-in"></a>Varför kan jag inte logga in?

Kontrol lera att ditt lösen ord inte har gått ut. NPS-tillägget har inte stöd för att ändra lösen ord som en del av inloggnings arbets flödet. Kontakta din organisations IT-personal om du behöver ytterligare hjälp.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Varför fungerar inte mina begär Anden med ADAL-token-fel?

Det här felet kan bero på en av flera olika orsaker. Använd de här stegen för att felsöka:

1. Starta om NPS-servern.
2. Kontrol lera att klient certifikatet är installerat som förväntat.
3. Kontrol lera att certifikatet är kopplat till din klient organisation i Azure AD.
4. Kontrollera att https://login.microsoftonline.com/ kan nås från servern som kör tillägget.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Varför Miss känner autentiseringen med ett fel i HTTP-loggar som anger att användaren inte hittas?

Kontrol lera att AD Connect körs och att användaren finns i både Windows Active Directory och Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Varför visas HTTP Connect-fel i loggar med alla mina autentiseringar som inte fungerar?

Kontrollera att https://adnotifications.windowsazure.com kan nås från servern som kör NPS-tillägget.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Varför fungerar inte autentisering, trots att ett giltigt certifikat finns?

Om ditt tidigare dator certifikat har upphört att gälla och ett nytt certifikat har skapats, bör du ta bort eventuella utgångna certifikat. Certifikat som har upphört att gälla kan orsaka problem med att NPS-tillägget startar.

Kontrol lera att du har ett giltigt certifikat genom att kontrol lera det lokala dator kontots certifikat Arkiv med hjälp av MMC och se till att certifikatet inte har passerat förfallo datumet. Om du vill generera ett nytt giltigt certifikat kör du stegen i avsnittet[kör PowerShell-skriptet](#run-the-powershell-script)

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Hantering av TLS/SSL-protokoll och chiffersviter

Vi rekommenderar att äldre och svagare chiffersviter inaktive ras eller tas bort om de inte krävs av din organisation. Information om hur du slutför den här aktiviteten finns i artikeln om hur du [hanterar SSL/TLS-protokoll och chiffersviter för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Ytterligare fel sökning

Ytterligare fel söknings vägledning och möjliga lösningar finns i artikeln [åtgärda fel meddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Nästa steg

- Konfigurera alternativa ID: n för inloggning eller konfigurera en undantags lista för IP-adresser som inte utför tvåstegsverifiering i [avancerade konfigurations alternativ för NPS-tillägget för Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Lär dig att integrera [Fjärrskrivbordsgateway](howto-mfa-nps-extension-rdg.md) och [VPN-servrar](howto-mfa-nps-extension-vpn.md) med hjälp av NPS-tillägget

- [Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
