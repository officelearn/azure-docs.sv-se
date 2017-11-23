---
title: "Använda befintliga NPS-servrar för att ge funktioner för Azure MFA | Microsoft Docs"
description: "NPS-tillägget för Azure Multi-Factor Authentication är en enkel lösning för att lägga till molnbaserade tvåstegsverifiering vericiation funktioner i din befintliga infrastruktur för autentisering."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: ebd6109fdae00da9e6dc1fc456573327d521e7e9
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrera din befintliga infrastruktur för NPS med Azure Multi-Factor Authentication

Server (NPS)-tillägget för Azure MFA lägger till funktioner för molnbaserade MFA till din infrastruktur för autentisering med hjälp av din befintliga servrar. Med filnamnstillägget NPS du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering din befintliga autentiseringsflödet utan att behöva installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda VPN-anslutningar utan att distribuera Azure MFA-Server. NPS-tillägget fungerar som ett kort mellan RADIUS och molnbaserad Azure MFA för att tillhandahålla en andra faktor-autentisering för federerad eller synkroniserade användare.

När du använder NPS-tillägget för Azure MFA innehåller autentiseringsflödet följande komponenter: 

1. **NAS-VPN-Server** tar emot förfrågningar från VPN-klienter och konverterar dem till RADIUS-förfrågningar till NPS-servrar. 
2. **NPS-servern** ansluter till Active Directory för att utföra den primära autentiseringen för RADIUS-begäranden och, vid lyckades, skickar begäran till alla installerade tillägg.  
3. **NPS-tillägget** utlöser en begäran om att Azure MFA för den sekundära autentiseringen. När tillägget tar emot svaret och om MFA-kontrollen lyckas, den är klar autentiseringsbegäran genom att tillhandahålla NPS-servern med säkerhetstoken som innehåller ett MFA anspråk kan utfärdas av Azure STS.  
4. **Azure MFA** kommunicerar med Azure Active Directory för att hämta information om användarens och utför sekundära autentiseringen med hjälp av en verifieringsmetod som konfigurerats för användaren.

Följande diagram illustrerar det här övergripande autentiseringsflödet för begäran: 

![Flödesdiagram för autentisering](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planera distributionen

NPS-tillägget hanterar automatiskt redundans, så du behöver en särskild konfiguration.

Du kan skapa så många Azure MFA-aktiverade NPS-servrar som du behöver. Om du installerar flera servrar, bör du använda ett certifikat för skillnaden för var och en av dem. När du skapar ett certifikat för varje server innebär att du kan uppdatera varje cert individuellt och oroa dig inte om driftstopp på alla servrar.

VPN-servrar vidarebefordra autentiseringsbegäranden, så att de behöver känna till de nya Azure MFA-aktiverade NPS-servrarna.

## <a name="prerequisites"></a>Krav

NPS-tillägget är avsedda att fungera med den befintliga infrastrukturen. Kontrollera att du har följande krav innan du börjar.

### <a name="licenses"></a>Licenser

NPS-tillägget för Azure MFA är tillgängligt för kunder med [licenser för Azure Multi-Factor Authentication](multi-factor-authentication.md) (ingår i Azure AD Premium, EMS eller en fristående MFA-licens). Förbrukningsbaserad licenser för Azure MFA som per användare eller per autentisering licenser är inte kompatibla med NPS-tillägget. 

### <a name="software"></a>Programvara

Windows Server 2008 R2 SP1 eller senare.

### <a name="libraries"></a>Bibliotek

Dessa bibliotek installeras automatiskt med tillägget.

-   [Visual C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory-modulen för Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Den Microsoft Azure Active Directory-modulen för Windows PowerShell installeras, om det inte redan finns, via ett konfigurationsskript som du körs som en del av installationen. Det finns inget behov av att installera den här modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory"></a>Azure Active Directory

Alla som använder tillägget NPS måste synkroniseras till Azure Active Directory med Azure AD Connect och måste registreras för MFA.

När du installerar tillägget behöver du autentiseringsuppgifter för katalog-ID och administratör för Azure AD-klienten. Du kan hitta din katalog-ID i den [Azure-portalen](https://portal.azure.com). Logga in som administratör, Välj den **Azure Active Directory** ikon på vänster och välj sedan **egenskaper**. Kopiera GUID i den **katalog-ID** och spara den. Du kan använda detta GUID som klient-ID när du installerar NPS-tillägget.

![Hitta din katalog-ID under Azure Active Directory-egenskaper](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>Förbered din miljö

Innan du installerar NPS-tillägget som du vill förbereda miljön för att hantera autentiseringstrafiken.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Aktivera NPS-rollen på en domänansluten server

NPS-servern ansluter till Azure Active Directory och autentiseras av MFA-begäranden. Välj en server för den här rollen. Vi rekommenderar att du väljer en server som inte hanterar förfrågningar från andra tjänster, eftersom tillägget NPS genererar fel för alla begäranden som inte är RADIUS. NPS-servern måste ställas in som den primära och sekundära autentiseringsservern för din miljö. Det går inte att proxy RADIUS-begäranden till en annan server.

1. Öppna på servern, den **guiden Lägg till roller och funktioner** Serverhanteraren Quickstart-menyn.
2. Välj **rollbaserad eller funktionsbaserad installation** för din installation.
3. Välj den **nätverksprincip och åtkomsttjänster** -serverrollen. Ett fönster kan visas för att meddela dig om nödvändiga funktioner för att köra den här rollen.
4. Vill du fortsätta med guiden förrän bekräftelsesidan. Välj **installera**.

Nu när du har en server som är avsedd för NPS kan konfigurera du också den här servern för att hantera inkommande RADIUS-förfrågningar från VPN-lösning.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Konfigurera din VPN-lösning för att kommunicera med NPS-servern

Stegen för att konfigurera din princip för RADIUS-autentisering varierar beroende på vilken VPN-lösning som du använder. Konfigurera principen så att den pekar till RADIUS-NPS-servern.

### <a name="sync-domain-users-to-the-cloud"></a>Synkronisera användare till molnet

Det här steget kan redan vara klar på din klient, men det är bra att kontrollera att Azure AD Connect har synkroniserat dina databaser nyligen.

1. Logga in på [Azure Portal](https://portal.azure.com) som administratör.
2. Välj **Azure Active Directory** > **Azure AD Connect**
3. Kontrollera att din synkroniseringsstatus **aktiverad** och att dina senaste synkronisering har mindre än en timme sedan.

Om du startar en ny runda av synkronisering oss instruktionerna i [Azure AD Connect-synkronisering: Schemaläggaren](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Bestämma vilka autentiseringsmetoder som användarna kan använda

Det finns två faktorer som påverkar vilka autentiseringsmetoder som är tillgängliga med en NPS-tillägg-distribution:

1. Lösenord krypteringsalgoritmen som används mellan RADIUS-klienten (VPN, Netscaler server eller andra) och NPS-servrar.
   - **PAP** stöder alla autentiseringsmetoder i Azure MFA i molnet: telefonsamtal, enkelriktade SMS, mobilappavisering och Verifieringskod för mobila appar.
   - **CHAPv2** och **EAP** stöd för telefonsamtal och mobilappavisering.
2. Inkommande metoder som klientprogram (VPN, Netscaler server eller andra) kan hantera. Till exempel har vissa innebär att tillåta användaren att ange verifieringskoden från text- eller mobilapp i VPN-klienten?

När du distribuerar NPS-tillägget kan du använda dessa faktorer för att utvärdera vilka metoder som är tillgängliga för användarna. Om RADIUS-klienten stöder PAP, men klienten UX saknar inmatningsfält för en Verifieringskod, sedan telefonsamtal och mobilappavisering är de två alternativ som stöds.

Du kan [inaktivera stöds inte autentiseringsmetoder](multi-factor-authentication-whats-next.md#selectable-verification-methods) i Azure.

### <a name="enable-users-for-mfa"></a>Aktivera användare för MFA

Innan du distribuerar fullständig NPS-tillägget som du behöver aktivera MFA för användare som du vill utföra tvåstegsverifiering. Mer direkt om du vill testa tillägget när du distribuerar det, behöver du minst ett testkonto som är fullständigt registrerad för Multifaktorautentisering.

Följ dessa steg för att hämta ett testkonto igång:
1. Logga in på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) med ett testkonto. 
2. Följ anvisningarna för att ställa in en verifieringsmetod.
3. Skapa en princip för villkorlig åtkomst eller [ändra användartillståndet](multi-factor-authentication-get-started-user-states.md) kräver tvåstegsverifiering för test-kontot. 

Användarna måste också att följa dessa steg för att registrera innan de kan autentisera med NPS-tillägget.

## <a name="install-the-nps-extension"></a>Installera NPS-tillägg

> [!IMPORTANT]
> Installera NPS-tillägg på en annan server än åtkomstpunkt VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Hämta och installera NPS-tillägget för Azure MFA

1.  [Hämta tillägget NPS](https://aka.ms/npsmfa) från Microsoft Download Center.
2.  Kopiera den binära filen till nätverksprincipservern som du vill konfigurera.
3.  Kör *setup.exe* och Följ installationsanvisningarna. Om det uppstår fel kontrollerar du att två bibliotek från avsnittet förutsättningar har installerats.

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet

Installationsprogrammet skapar en PowerShell-skript på den här platsen: `C:\Program Files\Microsoft\AzureMfa\Config` (där C:\ är installationsenheten). Det här PowerShell-skriptet utförs följande åtgärder:

-   Skapa ett självsignerat certifikat.
-   Koppla den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD.
-   Lagra certifikatet i lokala datorns certifikatarkiv.
-   Bevilja åtkomst till certifikatets privata nyckel till användare i nätverket.
-   Starta om NPS.

Om du inte vill använda dina egna certifikat (i stället för de självsignerade certifikat som genereras av PowerShell-skriptet), Kör PowerShell-skript för att slutföra installationen. Om du installerar tillägget på flera servrar, ha var och en sina egna certifikat.

1. Kör Windows PowerShell som administratör.
2. Ändra kataloger.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Kör PowerShell-skript som skapas av installationsprogrammet.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Logga in på Azure AD som administratör.
5. PowerShell frågar efter klient-ID. Använd katalog-ID-GUID som du kopierade från Azure-portalen i avsnittet förutsättningar.
6. PowerShell visar ett meddelande när skriptet har slutförts.  

Upprepa dessa steg på ytterligare NPS-servrar som du vill konfigurera för belastningsutjämning.

>[!NOTE]
>Om du använder egna certifikat i stället för att certifikat med PowerShell-skript, kontrollera att justera NPS namngivningskonventionen. Ämnesnamn måste vara **CN =\<TenantID\>, OU = Microsoft NPS tillägget**. 

## <a name="configure-your-nps-extension"></a>Konfigurera NPS-tillägget

Det här avsnittet innehåller överväganden vid utformning och förslag för lyckad distribution för NPS-tillägget.

### <a name="configuration-limitations"></a>Begränsningar för konfiguration

- NPS-tillägget för Azure MFA innehåller inte verktyg för att migrera användare och inställningar från MFA-Server till molnet. Därför föreslår vi använder tillägget för nya distributioner, i stället för befintlig distribution. Om du använder tillägget på en befintlig distribution kan måste användarna utföra bevis upp igen för att fylla i information om MFA i molnet.  
- NPS-tillägget använder UPN från lokala Active directory för att identifiera användaren vid Azure MFA för att utföra den sekundära auktor Tillägget kan konfigureras för att använda en annan identifierare som alternativt inloggnings-ID eller anpassade Active Directory-fältet än UPN. Se [avancerade konfigurationsalternativ för NPS-tillägg för multi-Factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) för mer information.
- Inte alla krypteringsprotokoll stöder alla verifieringsmetoderna.
   - **PAP** stöder telefonsamtal, enkelriktade SMS, mobilappavisering och Verifieringskod för mobila appar
   - **CHAPv2** och **EAP** stöd för telefonsamtal och mobilappavisering

### <a name="control-radius-clients-that-require-mfa"></a>Kontrollen RADIUS-klienter som kräver MFA

När du aktiverar MFA för en RADIUS-klient med hjälp av NPS-tillägget, måste alla autentiseringar för den här klienten gör en MFA. Om du vill aktivera MFA för vissa RADIUS-klienter, men inte andra kan du konfigurera två NPS-servrar och installera tillägget på endast en av dem. Konfigurera RADIUS-klienter som du vill kräva MFA för att skicka begäranden till NPS-server som konfigurerats med tillägget och andra RADIUS-klienter till NPS-servern inte har konfigurerats med tillägget.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Förbereda för användare som inte är registrerade för MFA

Om du har användare som inte är registrerade för MFA, kan du bestämma vad som händer när de försöker autentisera. Använda registerinställningen *REQUIRE_USER_MATCH* i registersökvägen *HKLM\Software\Microsoft\AzureMFA* att styra hur funktionen. Den här inställningen har ett enda konfigurationsalternativ:

| Nyckel | Värde | Standard |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | SANT/FALSKT | Inte har angetts (motsvarar TRUE) |

Syftet med den här inställningen är att avgöra vad du gör om en användare inte har registrerats för MFA. När nyckeln finns inte, har inte angetts eller är inställd på TRUE, och användaren har inte registrerats sedan tillägget misslyckas MFA-kontrollen. När nyckeln är inställd på FALSE och användaren inte är registrerad, fortsätter autentiseringen utan att utföra MFA. Om en användare har registrerats i MFA, måste de autentisera med MFA även om REQUIRE_USER_MATCH är inställd på FALSE.

Du kan välja att skapa den här nyckeln och ange den till FALSE, medan användarna är onboarding och kan inte registreras för Azure MFA ännu. Eftersom inställningen nyckeln tillåter att användare som inte är registrerade för MFA för att logga in, bör du dock ta bort den här nyckeln innan du fortsätter till produktionen.

## <a name="troubleshooting"></a>Felsökning

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hur kan jag bekräfta att klienten certifikat installeras som förväntat?

Leta efter ett självsignerat certifikat som skapas av installationsprogrammet i certifikatarkivet och kontrollera att den privata nyckeln har behörigheterna för användaren **NÄTVERKSTJÄNSTEN**. Certifikatet har ett ämnesnamn **CN \<tenantid\>, OU = Microsoft NPS-tillägg**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hur kan jag bekräfta att min klient cert är kopplad till min klient i Azure Active Directory?

Öppna PowerShell-Kommandotolken och kör följande kommandon:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Dessa kommandon ut alla certifikat som kopplar din klient till din instans av NPS-tillägget i PowerShell-sessionen. Leta efter certifikatet genom att exportera certifikatet klienten som en ”Base64-kodat X.509(.cer)” fil utan den privata nyckeln och jämför den med listan från PowerShell.

Giltigt-från- och -tills tidsstämplar som är läsbart format, som kan användas för att filtrera ut uppenbara misfits om kommandot returnerar mer än ett certifikat.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Varför är min begäranden inte med ADAL fel?

Det här felet kan bero på ett av flera skäl. Följ dessa steg för att felsöka:

1. Starta om NPS-servern.
2. Kontrollera att certifikat som klienten är installerad som förväntat.
3. Kontrollera att certifikatet är associerad med din klient på Azure AD.
4. Kontrollera att https://login.microsoftonline.com/ är tillgänglig från den server som kör tillägget.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Varför misslyckas autentiseringen med ett fel i HTTP-loggar som anger att användaren inte kan hittas?

Kontrollera att AD Connect är igång och att användaren finns i både Windows Active Directory och Azure Active Directory.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Varför ser HTTP connect-fel i loggarna med min autentiseringar misslyckas?

Kontrollera att https://adnotifications.windowsazure.com kan nås från servern som kör NPS-tillägget.


## <a name="next-steps"></a>Nästa steg

- Konfigurera alternativa ID för inloggning eller ställa in en undantagslista för IP-adresser som inte får utföra tvåstegsverifiering i [avancerade konfigurationsalternativ för NPS-tillägg för Multifaktorautentisering](nps-extension-advanced-configuration.md)

- Lär dig hur du integrerar [fjärrskrivbordsgateway](nps-extension-remote-desktop-gateway.md) och [VPN-servrar](nps-extension-vpn.md) med hjälp av NPS-tillägget

- [Åtgärda felmeddelanden från NPS-tillägget för Azure Multi-Factor Authentication](multi-factor-authentication-nps-errors.md)
