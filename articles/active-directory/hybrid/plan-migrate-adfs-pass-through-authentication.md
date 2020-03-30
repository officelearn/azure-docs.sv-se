---
title: 'Azure AD Connect: Migrera från federation till PTA för Azure AD'
description: Den här artikeln innehåller information om hur du flyttar din hybrididentitetsmiljö från federation till direktautentisering.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13a5fc216abc890c19ce3a2d75335431fe2a6799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528650"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrera från federation till direktautentisering för Azure Active Directory

I den här artikeln beskrivs hur du flyttar dina organisationsdomäner från AD FS (Active Directory Federation Services) till direktautentisering.

> [!NOTE]
> För att ändra autentiseringsmetoden krävs planering, testning och eventuellt driftstopp. [Stegvis distribution](how-to-connect-staged-rollout.md) är ett alternativt sätt att testa och gradvis migrera från federation till molnautentisering med hjälp av direktautentisering.

## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Förutsättningar för att migrera till direktautentisering

Följande förutsättningar krävs för att migrera från ad fs till att använda direktautentisering.

### <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

För att kunna slutföra de steg som krävs för att migrera till direktautentisering måste du ha [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 eller en senare version. I Azure AD Connect 1.1.819.0 ändras inloggningskonverteringen avsevärt. Den totala tiden för att migrera från AD FS till molnautentisering i den här versionen minskas från potentiellt timmar till minuter.

> [!IMPORTANT]
> Du kan läsa i inaktuell dokumentation, verktyg och bloggar som användarkonvertering krävs när du konverterar domäner från federerad identitet till hanterad identitet. Det behövs inte längre *konvertering av användare.* Microsoft arbetar med att uppdatera dokumentation och verktyg för att återspegla den här ändringen.

Om du vill uppdatera Azure AD Connect slutför du stegen i [Azure AD Connect: Uppgradera till den senaste versionen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Planera nummer och placering av autentiseringsagenter

Direktautentisering kräver distribution av lätta agenter på Azure AD Connect-servern och på din lokala dator som kör Windows Server. Om du vill minska svarstiden installerar du agenterna så nära Active Directory-domänkontrollanterna som möjligt.

För de flesta kunder är två eller tre autentiseringsagenter tillräckliga för att ge hög tillgänglighet och önskad kapacitet. En klient kan ha högst 12 agenter registrerade. Den första agenten installeras alltid på själva Azure AD Connect-servern. Mer information om agentbegränsningar och agentdistributionsalternativ finns i [Azure AD-direktautentisering: Aktuella begränsningar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations).

### <a name="plan-the-migration-method"></a>Planera migreringsmetoden

Du kan välja mellan två metoder för att migrera från federerad identitetshantering till direktautentisering och sömlös enkel inloggning (SSO). Vilken metod du använder beror på hur AD FS-instansen ursprungligen konfigurerades.

* **Azure AD Connect**. Om du ursprungligen konfigurerade AD FS med Hjälp av Azure AD Connect *måste* du ändra till direktautentisering med hjälp av Azure AD Connect-guiden.

   Azure AD Connect kör automatiskt **cmdlet set-MsolDomainAuthentication** när du ändrar användarens inloggningsmetod. Azure AD Connect tar automatiskt bort alla verifierade federerade domäner i din Azure AD-klientorganisation.

   > [!NOTE]
   > Om du ursprungligen använde Azure AD Connect för att konfigurera AD FS kan du inte undvika att ta bort alla domäner i din klientorganisation när du ändrar användarloggningen för att skicka genom autentisering.
‎
* **Azure AD Connect med PowerShell**. Du kan bara använda den här metoden om du inte ursprungligen konfigurerade AD FS med hjälp av Azure AD Connect. För det här alternativet måste du fortfarande ändra inloggningsmetoden för användare via Azure AD Connect-guiden. Kärnskillnaden med det här alternativet är att guiden inte automatiskt kör cmdleten **Set-MsolDomainAuthentication.** Med det här alternativet har du full kontroll över vilka domäner som konverteras och i vilken ordning.

För att förstå vilken metod du ska använda, utför du stegen i följande avsnitt.

#### <a name="verify-current-user-sign-in-settings"></a>Verifiera aktuella inställningar för användarloggning

1. Logga in på [Azure AD-portalen](https://aad.portal.azure.com/) med hjälp av ett globalt administratörskonto.
2. Kontrollera följande inställningar i avsnittet **Användares inloggning:**
   * **Federationen** är inställd **på Aktiverad**.
   * **Sömlös enkel inloggning** är **inaktiverad**.
   * **Direktautentisering** är inställt **på Inaktiverad**.

   ![Skärmbild av inställningarna i inloggningsavsnittet för Azure AD Connect-användare](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Kontrollera hur federationen har konfigurerats

1. Öppna Azure AD Connect på Din Azure AD Connect-server. Välj **Konfigurera**.
2. På sidan **Ytterligare uppgifter** väljer du Visa **aktuell konfiguration**och väljer sedan **Nästa**.<br />
 
   ![Skärmbild av alternativet Visa aktuell konfiguration på sidan Ytterligare uppgifter](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Bläddra till **AD FS (Active Directory Federation Services) under** **Ytterligare uppgifter > Hantera federationen.**<br />

   * Om AD FS-konfigurationen visas i det här avsnittet kan du lugnt anta att AD FS ursprungligen konfigurerades med hjälp av Azure AD Connect. Du kan konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av alternativet Azure AD Connect **Change-användare.** Mer information om processen finns i avsnittet **Alternativ A: Konfigurera direktautentisering med hjälp av Azure AD Connect**.
   * Om AD FS inte finns med i de aktuella inställningarna måste du manuellt konvertera dina domäner från federerad identitet till hanterad identitet med hjälp av PowerShell. Mer information om den här processen finns i avsnittet **Alternativ B: Växla från federation till direktautentisering med hjälp av Azure AD Connect och PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentströmfederationsinställningar

Om du vill hitta dina aktuella federationsinställningar kör du cmdleten **Get-MsolDomainFederationSettings:**

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exempel:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Kontrollera alla inställningar som kan ha anpassats för dokumentationen för federationens design och distribution. Specifikt, leta efter anpassningar i **PreferredAuthenticationProtocol**, **SupportsMfa**och **PromptLoginBehavior**.

Mer information finns i dessa artiklar:

* [STÖD för AD FS prompt=inloggningsparameter](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Om **SupportsMfa** är inställt på **True**använder du en lokal multifaktorautentiseringslösning för att injicera en andra faktorutmaning i användarautentiseringsflödet. Den här inställningen fungerar inte längre för Azure AD-autentiseringsscenarier. 
>
> Använd i stället molnbaserad tjänst för Azure Multi-Factor Authentication för att utföra samma funktion. Utvärdera noggrant dina multifaktorautentiseringskrav innan du fortsätter. Innan du konverterar dina domäner ska du se till att du förstår hur du använder Azure Multi-Factor Authentication, licenskonsekvenserna och registreringsprocessen för användare.

#### <a name="back-up-federation-settings"></a>Säkerhetskopiera federationsinställningar

Även om inga ändringar görs på andra beroende parter i AD FS-servergruppen under de processer som beskrivs i den här artikeln, rekommenderar vi att du har en aktuell giltig säkerhetskopia av DIN AD FS-servergrupp som du kan återställa från. Du kan skapa en aktuell giltig säkerhetskopia med hjälp av det kostnadsfria [Microsoft AD FS Rapid Restore Tool](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Du kan använda verktyget för att säkerhetskopiera AD FS och återställa en befintlig servergrupp eller skapa en ny servergrupp.

Om du väljer att inte använda AD FS Rapid Restore Tool bör du åtminstone exportera Microsoft Office 365 Identity Platform som förlitar sig på partförtroende och alla tillhörande anpassade anspråksregler som du har lagt till. Du kan exportera den förlitande partens förtroende och tillhörande anspråksregler med hjälp av följande PowerShell-exempel:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Spridningsöverväganden och användning av AD FS

I det här avsnittet beskrivs distributionsöverväganden och information om hur du använder AD FS.

### <a name="current-ad-fs-use"></a>Aktuell AD FS-användning

Innan du konverterar från federerad identitet till hanterad identitet bör du titta närmare på hur du för närvarande använder AD FS för Azure AD, Office 365 och andra program (förlitande part-förtroenden). Tänk närmare på de scenarier som beskrivs i följande tabell:

| Om | Sedan |
|-|-|
| Du planerar att fortsätta använda AD FS med andra program (förutom Azure AD och Office 365). | När du har konverterat dina domäner använder du både AD FS och Azure AD. Tänk på användarupplevelsen. I vissa fall kan användare behöva autentisera två gånger: en gång till Azure AD (där en användare får SSO-åtkomst till andra program, till exempel Office 365) och igen för alla program som fortfarande är bundna till AD FS som en förlitande part förtroende. |
| AD FS-instansen är starkt anpassad och förlitar sig på specifika anpassningsinställningar i filen onload.js (till exempel om du har ändrat inloggningsupplevelsen så att användarna bara använder ett **SamAccountName-format** för sitt användarnamn i stället för ett UPN (User Principal Name) eller om din organisation har markerat inloggningsupplevelsen med hög grad). Filen onload.js kan inte dupliceras i Azure AD. | Innan du fortsätter måste du kontrollera att Azure AD kan uppfylla dina aktuella anpassningskrav. Mer information och vägledning finns i avsnitten om AD FS-varumärkes- och AD FS-anpassning.|
| Du använder AD FS för att blockera tidigare versioner av autentiseringsklienter.| Överväg att ersätta AD FS-kontroller som blockerar tidigare versioner av autentiseringsklienter med hjälp av en kombination av [kontroller för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) och Exchange [Online-klientåtkomstregler](https://aka.ms/EXOCAR). |
| Du kräver att användare utför multifaktorautentisering mot en lokal multifaktorautentiseringsserverlösning när användare autentiserar till AD FS.| I en hanterad identitetsdomän kan du inte injicera en multifaktorautentiseringsutmaning via den lokala multifaktorautentiseringslösningen i autentiseringsflödet. Du kan dock använda azure multifaktorautentiseringstjänsten för multifaktorautentisering när domänen har konverterats.<br /><br /> Om användarna för närvarande inte använder Azure Multi-Factor Authentication krävs ett engångssteg för användarregistrering. Du måste förbereda och meddela den planerade registreringen till användarna. |
| Du använder för närvarande åtkomstkontrollprinciper (AuthZ-regler) i AD FS för att styra åtkomsten till Office 365.| Överväg att ersätta principerna med motsvarande Azure [AD-principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) och [Exchange Online-klientåtkomstregler](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Vanliga AD FS-anpassningar

I det här avsnittet beskrivs vanliga AD FS-anpassningar.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork fordran

AD FS utfärdar **InsideCorporateNetwork-anspråket** om användaren som autentiserar finns i företagsnätverket. Det här anspråket kan sedan skickas vidare till Azure AD. Anspråket används för att kringgå multifaktorautentisering baserat på användarens nätverksplats. Mer information om hur du tar reda på om den här funktionen för närvarande är tillgänglig i AD FS finns [i Betrodda INTERNET-adresser för federerade användare](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

**InsideCorporateNetwork-anspråket** är inte tillgängligt när dina domäner har konverterats till direktautentisering. Du kan använda [namngivna platser i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) för att ersätta den här funktionen.

När du har konfigurerat namngivna platser måste du uppdatera alla principer för villkorlig åtkomst som har konfigurerats för att inkludera eller utesluta nätverket **Alla betrodda platser** eller **MFA-betrodda IPs-värden** för att återspegla de nya namngivna platserna.

Mer information om **platsvillkoret** i villkorlig åtkomst finns i [Active Directory Conditional Access-platser](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

När du ansluter en enhet till Azure AD kan du skapa regler för villkorlig åtkomst som tillämpar att enheter uppfyller dina åtkomststandarder för säkerhet och efterlevnad. Användare kan också logga in på en enhet med hjälp av ett organisationsverk eller skolkonto i stället för ett personligt konto. När du använder hybrid-Azure AD-anslutna enheter kan du ansluta till dina Active Directory-domänanslutna enheter till Azure AD. Din federerade miljö kan ha konfigurerats för att använda den här funktionen.

För att säkerställa att hybridkoppling fortsätter att fungera för alla enheter som är anslutna till domänen efter att dina domäner har konverterats till direktautentisering, för Windows 10-klienter, måste du använda Azure AD Connect för att synkronisera Active Directory-datorkonton till Azure AD.

För datorkonton i Windows 8 och Windows 7 använder hybridkoppling sömlös SSO för att registrera datorn i Azure AD. Du behöver inte synkronisera Windows 8- och Windows 7-datorkonton som du gör för Windows 10-enheter. Du måste dock distribuera en uppdaterad workplacejoin.exe-fil (via en MSI-fil) till Windows 8- och Windows 7-klienter så att de kan registrera sig med hjälp av sömlös SSO. [Hämta MSI-filen](https://www.microsoft.com/download/details.aspx?id=53554).

Mer information finns i [Konfigurera hybrid Azure AD-anslutna enheter](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Anpassning

Om din organisation [har anpassat dina AD FS-inloggningssidor](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) för att visa information som är mer relevant för organisationen kan du överväga att göra liknande [anpassningar till inloggningssidan för Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Även om liknande anpassningar är tillgängliga, bör vissa visuella ändringar på inloggningssidor förväntas efter konverteringen. Du kanske vill ge information om förväntade ändringar i din kommunikation till användare.

> [!NOTE]
> Organisationsprofil är endast tillgängligt om du köper Premium- eller Basic-licensen för Azure Active Directory eller om du har en Office 365-licens.

## <a name="plan-for-smart-lockout"></a>Planera för smart lockout

Azure AD smart lockout skyddar mot brute-force lösenordsattacker. Smart utelåsning förhindrar att ett lokalt Active Directory-konto låses ute när direktautentisering används och en grupprincip för kontoutelåsning anges i Active Directory.

Mer information finns i [Azure Active Directory smart lockout](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="plan-deployment-and-support"></a>Planera distribution och support

Slutför de uppgifter som beskrivs i det här avsnittet för att hjälpa dig att planera för distribution och support.

### <a name="plan-the-maintenance-window"></a>Planera underhållsfönstret

Även om domänkonverteringsprocessen är relativt snabb kan Azure AD fortsätta att skicka vissa autentiseringsbegäranden till DINA AD FS-servrar i upp till fyra timmar efter att domänkonverteringen är klar. Under det här fyratimmarsfönstret, och beroende på olika tjänstsidans cacheminnen, kanske Azure AD inte accepterar dessa autentiseringar. Användare kan få ett felmeddelande. Användaren kan fortfarande autentisera mot AD FS, men Azure AD accepterar inte längre användarens utfärdade token eftersom federationsförtroendet nu har tagits bort.

Endast användare som kommer åt tjänsterna via en webbläsare under det här fönstret efter konverteringen innan servicesidans cache rensas påverkas. Äldre klienter (Exchange ActiveSync, Outlook 2010/2013) förväntas inte påverkas eftersom Exchange Online behåller en cache med sina autentiseringsuppgifter under en viss tidsperiod. Cacheminnet används för att tyst återauktorisera användaren. Användaren behöver inte återgå till AD FS. Autentiseringsuppgifter som lagras på enheten för dessa klienter används för att tyst återauktorisera sig när cachelagrade rensas. Användare förväntas inte få några lösenordsprodor som ett resultat av domänkonverteringsprocessen.

Moderna autentiseringsklienter (Office 2016 och Office 2013-, iOS- och Android-appar) använder en giltig uppdateringstoken för att hämta nya åtkomsttoken för fortsatt åtkomst till resurser i stället för att återgå till AD FS. Dessa klienter är immuna mot alla lösenordsuppmaningar som följer av domänkonverteringsprocessen. Klienterna fortsätter att fungera utan ytterligare konfiguration.

> [!IMPORTANT]
> Stäng inte av AD FS-miljön eller ta bort den förtroende som stöds av Den Office 365-förlitande parten tills du har verifierat att alla användare kan autentisera med hjälp av molnautentisering.

### <a name="plan-for-rollback"></a>Planera för återställning

Om du stöter på ett stort problem som du inte kan lösa snabbt kan du välja att återställa lösningen till federationen. Det är viktigt att planera vad du ska göra om distributionen inte distribueras som avsett. Om konverteringen av domänen eller användarna misslyckas under distributionen, eller om du behöver återställa till federationen, måste du förstå hur du kan minska eventuella avbrott och minska effekten på användarna.

#### <a name="to-roll-back"></a>Så här återställer du

Om du vill planera för återställning finns i dokumentationen för federationens design och distribution för din specifika distributionsinformation. Processen bör omfatta följande uppgifter:

* Konvertera hanterade domäner till federerade domäner med hjälp av **cmdleten Konvertera-MSOLDomainToFederated.**
* Om det behövs kan du konfigurera ytterligare anspråksregler.

### <a name="plan-communications"></a>Planera kommunikation

En viktig del av planeringen av distribution och support är att se till att användarna får proaktiv information om kommande ändringar. Användare bör veta i förväg vad de kan uppleva och vad som krävs av dem.

När både direktautentisering och sömlös SSO har distribuerats har användaren inloggningsupplevelse för åtkomst till Office 365 och andra resurser som autentiseras via Azure AD-ändringar. Användare som befinner sig utanför nätverket ser bara inloggningssidan för Azure AD. Dessa användare omdirigeras inte till den formulärbaserade sida som presenteras av proxyservrar för externa program.

Inkludera följande element i din kommunikationsstrategi:

* Meddela användarna om kommande och utgivna funktioner med hjälp av:
   * E-post och andra interna kommunikationskanaler.
   * Visuella objekt, till exempel affischer.
   * Executive, live eller annan kommunikation.
* Bestäm vem som ska anpassa kommunikationen och vem som ska skicka kommunikationen och när.

## <a name="implement-your-solution"></a>Implementera din lösning

Du planerade din lösning. Nu kan du nu genomföra det. Genomförandet omfattar följande komponenter:

* Förbereder sömlös SSO.
* Ändra inloggningsmetoden för att gå igenom autentisering och aktivera sömlös SSO.

### <a name="step-1-prepare-for-seamless-sso"></a>Steg 1: Förbered dig för sömlös SSO

För att dina enheter ska kunna använda sömlös SSO måste du lägga till en Azure AD-URL i användarnas intranätzonsinställningar med hjälp av en gruppprincip i Active Directory.

Som standard beräknar webbläsare automatiskt rätt zon, antingen internet eller intranät, från en URL. **Http:\/\/contoso/** mappar till intranätzonen och **http:\/\/intranet.contoso.com** kartor till internetzonen (eftersom webbadressen innehåller en punkt). Webbläsare skickar Kerberos-biljetter till en molnslutpunkt, till exempel Azure AD-URL:en, endast om du uttryckligen lägger till URL:en i webbläsarens intranätzon.

Gör stegen för att [distribuera](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) de nödvändiga ändringarna på dina enheter.

> [!IMPORTANT]
> Att göra den här ändringen ändrar inte hur användarna loggar in på Azure AD. Det är dock viktigt att du använder den här konfigurationen på alla dina enheter innan du fortsätter. Användare som loggar in på enheter som inte har fått den här konfigurationen behöver helt enkelt ange ett användarnamn och lösenord för att logga in på Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Steg 2: Ändra inloggningsmetoden för att vidarebefordra autentisering och aktivera sömlös SSO

Du har två alternativ för att ändra inloggningsmetoden för vidareströmningsautentisering och aktivera sömlös SSO.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Alternativ A: Konfigurera direktautentisering med hjälp av Azure AD Connect

Använd den här metoden om du först konfigurerade AD FS-miljön med hjälp av Azure AD Connect. Du kan inte använda den här metoden om du *inte* ursprungligen konfigurerade AD FS-miljön med hjälp av Azure AD Connect.

> [!IMPORTANT]
> När du har slutfört följande steg konverteras alla domäner från federerad identitet till hanterad identitet. Mer information finns [i Planera migreringsmetoden](#plan-the-migration-method).

Ändra först inloggningsmetoden:

1. Öppna Azure AD Connect-guiden på Azure AD Connect-servern.
2. Välj **Ändra användarloggning**och välj sedan **Nästa**. 
3. På sidan **Anslut till Azure AD** anger du användarnamn och lösenord för ett globalt administratörskonto.
4. På sidan **Använda inloggning** väljer du knappen **Direktautentisering,** väljer **Aktivera enkel inloggning**och väljer sedan **Nästa**.
5. På sidan **Aktivera enkel inloggning** anger du autentiseringsuppgifterna för ett domänadministratörskonto och väljer sedan **Nästa**.

   > [!NOTE]
   > Autentiseringsuppgifter för domänadministratörskonto krävs för att aktivera sömlös SSO. Processen slutför följande åtgärder, som kräver dessa förhöjda behörigheter. Autentiseringsuppgifterna för domänadministratörskontot lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifterna för domänadministratörskontot används bara för att aktivera funktionen. Autentiseringsuppgifterna ignoreras när processen har slutförts.
   >
   > 1. Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory-instans.
   > 2. Datorkontots Kerberos-dekrypteringsnyckel delas säkert med Azure AD.
   > 3. Två Kerberos-tjänsthuvudnamn (SPN) skapas för att representera två webbadresser som används under Azure AD-inloggning.

6. Kontrollera att kryssrutan **Starta synkroniseringsprocessen när konfigurationen är klar när konfigurationen är klar** är markerad på sidan Klar att **konfigurera.** Välj sedan **Konfigurera**.<br />

   ![Skärmbild av sidan Klar att konfigurera](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. I Azure AD-portalen väljer du **Azure Active Directory**och väljer sedan Azure AD **Connect**.
8. Verifiera dessa inställningar:
   * **Federationen** är inställd **på Inaktiverad**.
   * **Sömlös enkel inloggning** är inställd på **Aktiverad**.
   * **Direktautentisering** är inställd **på Aktiverad**.<br />

   ![Skärmbild som visar inställningarna i avsnittet Användares inloggning](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Nästa. distribuera ytterligare autentiseringsmetoder:

1. Gå till **Azure Active Directory** > **Azure AD Connect**i Azure Portal och välj sedan **Direktautentisering**.
2. Välj knappen **Hämta** på sidan **Direktautentisering.**
3. På sidan **Hämta agent** väljer du Acceptera villkor **och hämta**.

   Ytterligare autentiseringsagenter börjar hämtas. Installera den sekundära autentiseringsagenten på en domänansluten server. 

   > [!NOTE]
   > Den första agenten installeras alltid på själva Azure AD Connect-servern som en del av konfigurationsändringarna som gjorts i avsnittet **Användarloggning i** Azure AD Connect-verktyget. Installera ytterligare autentiseringsagenter på en separat server. Vi rekommenderar att du har två eller tre ytterligare autentiseringsagenter tillgängliga. 

4. Kör installationen av autentiseringsagenten. Under installationen måste du ange autentiseringsuppgifterna för ett globalt administratörskonto.

   ![Skärmbild som visar knappen Installera på sidan Microsoft Azure AD Connect Authentication Agent Package](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Skärmbild som visar inloggningssidan](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. När autentiseringsagenten är installerad kan du gå tillbaka till hälsosidan för direktautentiseringsagenten för att kontrollera status för de ytterligare agenterna.

Hoppa till [Testning och nästa steg](#testing-and-next-steps).

> [!IMPORTANT]
> Hoppa över avsnittet **Alternativ B: Växla från federation till direktautentisering med hjälp av Azure AD Connect och PowerShell**. Stegen i det avsnittet gäller inte om du väljer Alternativ A för att ändra inloggningsmetoden för vidarekopplingsautentisering och aktivera sömlös SSO. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Alternativ B: Växla från federation till direktautentisering med hjälp av Azure AD Connect och PowerShell

Använd det här alternativet om du inte först konfigurerade federerade domäner med hjälp av Azure AD Connect.

Aktivera först direktautentisering:

1. Öppna Azure AD Connect-guiden på Azure AD Connect Server.
2. Välj **Ändra användarloggning**och välj sedan **Nästa**.
3. På sidan **Anslut till Azure AD** anger du användarnamn och lösenord för ett globalt administratörskonto.
4. På sidan **Använda inloggning** väljer du knappen **Direktautentisering.** Välj **Aktivera enkel inloggning**och välj sedan **Nästa**.
5. På sidan **Aktivera enkel inloggning** anger du autentiseringsuppgifterna för ett domänadministratörskonto och väljer sedan **Nästa**.

   > [!NOTE]
   > Autentiseringsuppgifter för domänadministratörskonto krävs för att aktivera sömlös SSO. Processen slutför följande åtgärder, som kräver dessa förhöjda behörigheter. Autentiseringsuppgifterna för domänadministratörskontot lagras inte i Azure AD Connect eller i Azure AD. Autentiseringsuppgifterna för domänadministratörskontot används bara för att aktivera funktionen. Autentiseringsuppgifterna ignoreras när processen har slutförts.
   > 
   > 1. Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory-instans.
   > 2. Datorkontots Kerberos-dekrypteringsnyckel delas säkert med Azure AD.
   > 3. Två Kerberos-tjänsthuvudnamn (SPN) skapas för att representera två webbadresser som används under Azure AD-inloggning.

6. Kontrollera att kryssrutan **Starta synkroniseringsprocessen när konfigurationen är klar när konfigurationen är klar** är markerad på sidan Klar att **konfigurera.** Välj sedan **Konfigurera**.<br />

   ![Skärmbild som visar sidan Klar att konfigurera och knappen Konfigurera](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Följande steg inträffar när du väljer **Konfigurera:**

   1. Den första direktautentiseringsagenten installeras.
   2. Direktfunktionen är aktiverad.
   3. Sömlös SSO är aktiverat.

7. Verifiera dessa inställningar:
   * **Federationen** är inställd **på Aktiverad**.
   * **Sömlös enkel inloggning** är inställd på **Aktiverad**.
   * **Direktautentisering** är inställd **på Aktiverad**.
   
   ![Skärmbild som visar inställningarna i avsnittet Användares inloggning](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Välj **Direktautentisering** och kontrollera att statusen är **Aktiv**.<br />
   
   Om autentiseringsagenten inte är aktiv slutför du några [felsökningssteg](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) innan du fortsätter med domänkonverteringsprocessen i nästa steg. Du riskerar att orsaka ett autentiseringsavbrott om du konverterar dina domäner innan du verifierar att dina direktautentiseringsagenter har installerats och att deras status **är aktiv** i Azure-portalen.

Distribuera sedan ytterligare autentiseringsagenter:

1. Gå till **Azure Active Directory** > **Azure AD Connect**i Azure Portal och välj sedan **Direktautentisering**.
2. Välj knappen **Hämta** på sidan **Direktautentisering.** 
3. På sidan **Hämta agent** väljer du Acceptera villkor **och hämta**.
 
   Autentiseringsagenten börjar hämtas. Installera den sekundära autentiseringsagenten på en domänansluten server.

   > [!NOTE]
   > Den första agenten installeras alltid på själva Azure AD Connect-servern som en del av konfigurationsändringarna som gjorts i avsnittet **Användarloggning i** Azure AD Connect-verktyget. Installera ytterligare autentiseringsagenter på en separat server. Vi rekommenderar att du har två eller tre ytterligare autentiseringsagenter tillgängliga.
 
4. Kör installationen av autentiseringsagenten. Under installationen måste du ange autentiseringsuppgifterna för ett globalt administratörskonto.<br />

   ![Skärmbild som visar knappen Installera på sidan Microsoft Azure AD Connect Authentication Agent Package](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Skärmbild som visar inloggningssidan](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. När autentiseringsagenten har installerats kan du återgå till hälsosidan för direktautentiseringsagenten för att kontrollera status för de ytterligare agenterna.

Nu är federerad autentisering fortfarande aktiv och fungerar för dina domäner. Om du vill fortsätta med distributionen måste du konvertera varje domän från federerad identitet till hanterad identitet så att direktautentisering börjar betjäna autentiseringsbegäranden för domänen.

Du behöver inte konvertera alla domäner samtidigt. Du kan välja att börja med en testdomän på din produktionsklient eller börja med domänen som har det lägsta antalet användare.

Slutför konverteringen med hjälp av Azure AD PowerShell-modulen:

1. Logga in på Azure AD i PowerShell med hjälp av ett globalt administratörskonto.
2. Om du vill konvertera den första domänen kör du följande kommando:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. I Azure AD-portalen väljer du **Azure Active Directory** > **Azure AD Connect**.
4. När du har konverterat alla federerade domäner kontrollerar du följande inställningar:
   * **Federationen** är inställd **på Inaktiverad**.
   * **Sömlös enkel inloggning** är inställd på **Aktiverad**.
   * **Direktautentisering** är inställd **på Aktiverad**.<br />

   ![Skärmbild som visar inställningarna i avsnittet Användares inloggning](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testning och nästa steg

Slutför följande uppgifter för att verifiera direktautentisering och slutföra konverteringsprocessen.

### <a name="test-pass-through-authentication"></a>Testgenomströmningsautentisering 

När din klient använde federerad identitet omdirigerades användare från inloggningssidan för Azure AD till din AD FS-miljö. Nu när klienten är konfigurerad för att använda direktautentisering i stället för federerad autentisering omdirigeras inte användare till AD FS. I stället loggar användarna in direkt på inloggningssidan för Azure AD.

Så här testar du genomströmningsautentisering:

1. Öppna Internet Explorer i InPrivate-läge så att sömlös SSO inte loggar in dig automatiskt.
2. Gå till inloggningssidan för Office 365 ([https://portal.office.com](https://portal.office.com/)).
3. Ange ett användar-UPN och välj sedan **Nästa**. Se till att du anger UPN för en hybridanvändare som synkroniserades från din lokala Active Directory-instans och som tidigare använde federerad autentisering. En sida där du anger användarnamn och lösenord visas:

   ![Skärmbild som visar inloggningssidan där du anger ett användarnamn](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Skärmbild som visar inloggningssidan där du anger ett lösenord](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. När du har angett lösenordet och valt **Logga in**omdirigeras du till Office 365-portalen.

   ![Skärmbild som visar Office 365-portalen](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Testa sömlös SSO

Så här testar du sömlös SSO:

1. Logga in på en domänansluten dator som är ansluten till företagsnätverket.
2. I Internet Explorer eller Chrome går du till någon av följande webbadresser (ersätt "contoso" med din domän):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Användaren omdirigeras kort till inloggningssidan för Azure AD, som visar meddelandet "Försöker logga in dig". Användaren uppmanas inte att ange ett användarnamn eller lösenord.<br />

   ![Skärmbild som visar inloggningssidan och meddelandet för Azure AD](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. Användaren omdirigeras och har loggat in på åtkomstpanelen:

   > [!NOTE]
   > Sömlös SSO fungerar på Office 365-tjänster som stöder domäntips (till exempel myapps.microsoft.com/contoso.com). Office 365-portalen (portal.office.com) stöder för närvarande inte domäntips. Användare måste ange ett UPN. När en UPN har angetts hämtar sömlös SSO Kerberos-biljetten för användarens räkning. Användaren är inloggad utan att ange ett lösenord.

   > [!TIP]
   > Överväg att distribuera [Azure AD hybrid-anslutning på Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) för en förbättrad SSO-upplevelse.

### <a name="remove-the-relying-party-trust"></a>Ta bort förtroende för den förlitande parten

När du har bekräftat att alla användare och klienter har autentiserats via Azure AD är det säkert att ta bort förtroendet för den förlitande parten i Office 365.

Om du inte använder AD FS för andra ändamål (det vill säga för andra förlitande part truster), är det säkert att inaktivera AD FS just nu.

### <a name="rollback"></a>Rollback

Om du upptäcker ett stort problem och inte kan lösa det snabbt kan du välja att återställa lösningen till federationen.

Mer information om federationsdesign och distribution finns i dokumentationen för federationsdesign och distribution. Processen bör omfatta dessa uppgifter:

* Konvertera hanterade domäner till federerad autentisering med hjälp av cmdleten **Konvertera-MSOLDomainToFederated.**
* Om det behövs konfigurerar du ytterligare anspråksregler.

### <a name="sync-userprincipalname-updates"></a>Synkronisera uppdateringar för userPrincipalName

Historiskt sett blockeras uppdateringar av attributet **UserPrincipalName,** som använder synkroniseringstjänsten från den lokala miljön, om inte båda dessa villkor är sanna:

* Användaren finns i en hanterad (icke-federerad) identitetsdomän.
* Användaren har inte tilldelats någon licens.

Mer information om hur du verifierar eller aktiverar den här funktionen finns i [Synkronisera uppdateringar av UserPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rulla över den sömlösa SSO Kerberos dekrypteringsnyckeln

Det är viktigt att ofta rulla över Kerberos-dekrypteringsnyckeln för AZUREADSSOACC-datorkontot (som representerar Azure AD). AZUREADSSOACC-datorkontot skapas i din lokala Active Directory-skog. Vi rekommenderar starkt att du rullar över Kerberos dekrypteringsnyckeln minst var 30:e dag för att anpassa sig till hur Active Directory-domänmedlemmar skickar lösenordsändringar. Det finns ingen associerad enhet ansluten till AZUREADSSOACC-datorkontoobjektet, så du måste utföra överrullningen manuellt.

Initiera överrullningen av den sömlösa SSO Kerberos-dekrypteringsnyckeln på den lokala servern som kör Azure AD Connect.

Mer information finns i [Hur rullar jag över Kerberos-dekrypteringsnyckeln för AZUREADSSOACC-datorkontot?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)

## <a name="monitoring-and-logging"></a>Övervakning och loggning

Övervaka de servrar som kör autentiseringsagenterna för att upprätthålla lösningens tillgänglighet. Förutom allmänna serverprestandaräknare exponerar autentiseringsagenterna prestandaobjekt som kan hjälpa dig att förstå autentiseringsstatistik och -fel.

Autentiseringsagenter loggar åtgärder till Windows-händelseloggar som finns under Program- och tjänstloggar\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Du kan också aktivera loggning för felsökning.

Mer information finns i [Felsöka Azure Active Directory direktautentisering](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [azure AD Connect-designkoncept](plan-connect-design-concepts.md).
* Välj [rätt autentisering](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).
* Läs mer om [topologier som stöds](plan-connect-design-concepts.md).
