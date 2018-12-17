---
title: 'Azure AD Connect: Migrera från federation till direktautentisering för Azure AD | Microsoft Docs'
description: Information om hur du flyttar din hybrid identity-miljö från federation till direktautentisering.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5361c8940c8c7dba5338a3f5a0ed18910f7e45a0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410337"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Migrera från federation till direktautentisering för Azure AD
Följande dokument innehåller vägledning om hur du flyttar från AD FS till direktautentisering.

>[!NOTE]
>Ett nedladdningsbart en kopia av det här dokumentet är tillgänglig [här](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-pass-through-authentication"></a>Krav för direktautentisering
Följande krav måste anges innan du kan migrera.
### <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

Du bör ha minst ska kunna genomföra stegen för att migrera till direktautentisering [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Den här versionen innehåller betydande förändringar i hur inloggning konverteringen utförs och minskar den totala tid att migrera från Federation till autentisering i molnet från potentiellt timmar och minuter.

> [!IMPORTANT]
> Inaktuella dokumentation, verktyg och bloggar tyda på att användarkonvertering är ett obligatoriskt steg vid konvertering domäner från federerade till hanterade. **Konvertera användare** är krävs inte längre och Microsoft arbetar på att uppdatera dokumentation och verktyg för att återspegla detta.

Uppdatera Azure AD Connect till den senaste versionen gå igenom den här [uppdatera instruktioner](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Planera autentisering agenten antal och placering

Direktautentisering åstadkoms genom att distribuera lätt agenter på de Azure AD Connect-servern och på din lokala Windows-servrar. Installera agenter så nära som möjligt till dina Active Directory-domänkontrollanter som minskar svarstiderna.

För de flesta kunder två eller tre autentisering agenter är tillräckligt för hög tillgänglighet och kapacitet och en klient kan ha mer än 12 agenter som har registrerats. Första agenten installeras alltid på AAD Connect själva servern. Referera till den [information om nätverk trafik uppskattningar och prestanda hos](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations) agenten begränsningar och alternativ för agenten.

### <a name="plan-migration-method"></a>Planera migreringsmetod

Det finns två metoder för att migrera från federerad autentisering till PTA och sömlös enkel inloggning. Vilken metod du använder beror på hur AD FS ursprungligen konfigurerades.

- **Använda Azure AD Connect**. Om AD FS ursprungligen konfigurerades med hjälp av Azure AD Connect och ändringen direktautentisering *måste* utföras via Azure AD Connect-guiden.

När du använder Azure AD Connect, körs det cmdleten Set-MsolDomainAuthentication du automatiskt när du ändrar inloggningsmetod för användare och därför du har ingen kontroll över den unfederating alla verifierade federerade domäner i Azure AD-klienten.  
‎  
> [!NOTE]
> För närvarande kan du inte undvika icke federating alla domäner i din klientorganisation när du ändrar användaren logga in på direktautentisering när AAD Connect användes ursprungligen för att konfigurera AD FS för dig.  
‎
- **Använda Azure AD Connect med PowerShell**. Den här metoden kan endast användas när AD FS inte ursprungligen konfigurerades med Azure AD Connect. Du måste ändra metoden användare logga in via Azure AD Connect-guiden, men den grundläggande skillnaden är att den inte körs automatiskt cmdleten Set-MsolDomainAuthentication åt dig när den har ingen medvetenhet om AD FS-gruppen och därför har du fullständig kontroll över vilka domäner konverteras och i vilken ordning.

Utför steg i följande avsnitt för att förstå vilken metod du ska använda.

#### <a name="verify-current-user-sign-in-settings"></a>Kontrollera aktuell användare logga in inställningarna

Kontrollera dina aktuella användare logga in inställningar genom att logga in på Azure AD-portalen [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) med ett globalt administratörskonto. 

I den **användaren logga In** verifierar som **Federation** är **aktiverad** och att **sömlös enkel inloggning** och  **Direktautentisering** är **inaktiverad**. 

![Bild 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Kontrollera hur federation har konfigurerats

   1. Gå till din Azure AD Connect-servern och starta Azure AD Connect och välj sedan **konfigurera**.
   2. På den **ytterligare uppgifter** väljer **visa aktuell konfiguration** och välj sedan **nästa**.</br>
   ![Bild 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. I den **granska din lösning** skärmen, rulla ned till **Active Directory Federation Services (AD FS)**.</br>
   Om du ser att AD FS-konfigurationen är i det här avsnittet och sedan på ett säkert sätt kan du anta AD FS ursprungligen konfigurerades via Azure AD Connect och kan därför konvertering av din domänerna från federerad som hanteras kan också styras via Azure AD Connect  **Ändra användarinloggning** alternativ, den här processen beskrivs i avsnittet **alternativ 1: Konfigurerar direktautentisering med hjälp av Azure AD Connect**.  
‎
   4. Om du inte kan se Active Directory Federation Services som visas i de aktuella inställningarna så måste du manuellt konvertera domänerna från federerat till hanteras via PowerShell som beskrivs i avsnittet **alternativ 2 - växel från Federation till PTA med Azure AD Connect och PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentet aktuella federationsinställningar

Du hittar den aktuella inställningen genom att köra cmdleten Get-MsolDomainFederationSettings.

Kommandot är:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exempel:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


Validera alla inställningar som kanske har anpassats till dina Federation design- och dokumentation, mer specifikt **PreferredAuthenticationProtocol**, **SupportsMfa**, och  **PromptLoginBehavior**.

Mer information om vad inställningarna finns nedan.

[Active Directory Federation Services uppmana = parameterstöd för inloggning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Om SupportsMfa är för närvarande värdet ”true” sedan detta innebär att du använder en lokala MFA-lösning för att mata in en 2nd factor-utmaning i autentiseringsflödet för användaren. Detta fungerar inte längre för scenarier med Azure AD-autentisering och i stället måste du använda Azure MFA (molnbaserad) tjänst för att utföra samma funktion. Noggrant utvärdera dina krav på MFA innan du går vidare och se till att du förstår hur du utnyttjar Azure MFA och licensiering konsekvenserna registreringsprocessen för användaren innan du konverterar dina domäner.

#### <a name="backup-federation-settings"></a>Säkerhetskopiering federationsinställningar

Även om inga ändringar görs till andra förlitande part på AD FS-gruppen under den här processen, bör du kontrollera att du har en aktuell giltig säkerhetskopia av din AD FS-servergrupp som kan återställas. Du kan göra detta med hjälp av den kostnadsfria Microsoft [AD FS snabb återställa verktyget](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Det här verktyget kan användas för att säkerhetskopiera och återställa AD FS, antingen till en befintlig grupp eller en ny grupp.

Om du väljer att inte använda AD FS snabb återställa verktyget, bör sedan minst du exportera ”Microsoft Office 365 Identity-plattformen” förlitande parten och alla associerade anpassade anspråksregler som du har lagt till. Du kan göra detta via följande PowerShell-exempel

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Överväganden vid distribution och användning av AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Verifiera din nuvarande förbrukning i AD FS

Innan du konverterar federerade till hanterade bör bör du titta på hur du använder AD FS i dag för Azure AD/Office 365 och andra program (förtroenden för förlitande part). Mer specifikt bör du i följande tabell:

| Om| sedan |
|-|-|
| Du kommer att behålla AD FS för andra program.| Du kommer att använda både AD FS och Azure AD och kommer att behöva tänka på slutanvändarens upplevelse som ett resultat. Användare kan behöva autentisera två gånger i vissa fall kan en gång till Azure AD (där de får enkel inloggning och senare till andra program som Office 365) och igen för alla program som fortfarande kopplad till AD FS som en förlitande part. |
| AD FS är kraftigt anpassade och beroende av specifika anpassningsinställningarna i onload.js-filen inte kan dupliceras i Azure AD (exempel: du har ändrat inloggningen så att användarna bara ange något SamAccountName-format för sina användarnamn i motsats i ett UPN- eller har en kraftigt företagsanpassad inloggning)| Behöver du kontrollera att din aktuella anpassning kraven kan uppfyllas av Azure AD innan du fortsätter. Se avsnitten anpassning av AD FS och AD FS-anpassning för mer information och vägledning.|
| Du blockerar äldre autentiseringsklienter via AD FS.| Överväg att ersätta kontroller för att blockera äldre autentiseringsklienter som för närvarande finns på AD FS med en kombination av [villkorlig åtkomstkontroll för äldre autentisering](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) och [klientåtkomst för Exchange Online Regler för](http://aka.ms/EXOCAR). |
| Du behöver användare att utföra MFA mot den lokala MFA server-lösning när du autentiserar till AD FS.| Du kommer inte att kunna mata in en MFA-kontrollen via den lokala MFA-lösning i autentiseringsflödet för en hanterad domän, men du kan använda Azure MFA-tjänsten för att göra det framöver när domänen konverteras. Om användarna inte använder Azure MFA i dag, kommer det innebära en genomfört slutanvändarens registreringssteget som du måste förbereda för och kommunicera dina slutanvändare. |
| Du använder principer för åtkomstkontroll (AuthZ-regler) idag i AD FS för att styra åtkomsten till Office 365.| Överväg att ersätta dem med motsvarande Azure AD [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) och [åtkomstregler för Exchange Online-klienten](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Överväganden för vanliga AD FS-anpassningar

#### <a name="inside-corporate-network-claim"></a>Inifrån företagsnätverkanspråk

InsideCorporateNetwork anspråk utfärdas av AD FS om den autentiserande användaren är i företagsnätverket. Det här anspråket kan sedan skickas till Azure AD och används för att koppla förbi Multi-Factor authentication baserat på användarens nätverksplats. Se [tillförlitliga IP-adresser för federerade användare](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) för information om hur du avgör om du har aktiverat för närvarande i AD FS.

InsideCorporateNetwork anspråket visas inte längre när dina domäner konverteras till direktautentisering. [Namngivna platser i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) kan användas för att ersätta den här funktionen.

När med namnet platser har konfigurerats, alla principer för villkorlig åtkomst som konfigurerats för att inkludera eller exkludera nätverksplatserna som ”alla betrodda platser” eller ”MFA tillförlitliga IP-adresser” måste uppdateras för att återspegla de nyligen skapade med namnet platser.

Se [Active Directory villkorsstyrd åtkomstplatser](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) mer information om platsvillkoret för villkorlig åtkomst.

#### <a name="hybrid-azure-ad-joined-devices"></a>Enheter för hybrid Azure AD har anslutits

Koppla en enhet till Azure AD kan du skapa regler för villkorlig åtkomst som tillämpar enheter som uppfyller ditt åtkomst-standarder för säkerhet och efterlevnad och tillåter användare att logga in på en enhet med ett organisations arbets- eller skolkonto i stället för en personlig konto. Hybrid Azure AD-anslutna enheter kan du ansluta din AD-domänanslutna enheter till Azure AD. Din federerad miljö kan ha konfigurerats med den här funktionen.

Om du vill säkerställa att ansluta till Hybrid fortsätter att fungera för alla nya enheter som är anslutna till domänen när dina domäner har konverterats till direktautentisering, måste Azure AD Connect konfigureras för att synkronisera Active Directory-datorkonton för Windows 10-klienter Azure AD. För Windows 7 och Windows 8-datorkonton, Hybrid delta använder sömlös SSO för att registrera datorn i Azure AD och du behöver inte synkronisera dem som du gör med Windows 10-enheter. Du men måste distribuera en uppdaterad workplacejoin.exe-fil (via en MSI-fil) i dessa äldre klienter så att de kan registrera sig med sömlös enkel inloggning. [Ladda ned MSI-filen](https://www.microsoft.com/download/details.aspx?id=53554).

Mer information om det här kravet finns i [hur du konfigurerar hybrid Azure Active Directory-anslutna enheter](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>Anpassning

Din organisation kan ha [anpassade din AD FS-inloggningssidor](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) att visa information som är mer relevant i organisationen. I så, fall Överväg att göra liknande [anpassningar till inloggningssidan för Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Liknande anpassningar är tillgängliga, väntat vissa visuella ändringar. Du kanske vill inkludera ändringarna i din kommunikation till slutanvändare.

> [!NOTE]
> Företagsanpassning är tillgängligt endast om du har köpt Premium eller Basic-licens för Azure AD eller har en Office 365-licens.

## <a name="planning-for-smart-lockout"></a>Planera för smart kontoutelåsning

Azure AD smart kontoutelåsning skyddar mot brute force-lösenord attacker och förhindrar att den lokala Active Directory-kontot bli utelåsta när direktautentisering används och en grupp för kontoutelåsning har angetts i Active Directory. 

Mer information om [Smart kontoutelåsning funktion och hur du redigerar dess konfiguration](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="planning-deployment-and-support"></a>Planera distribution och support

### <a name="plan-the-maintenance-window"></a>Planera underhållet

Domän-konverteringsprocessen själva är relativt snabba, kan Azure AD fortfarande skicka vissa autentiseringsbegäranden till AD FS-servrarna under en period på upp till 4 timmar efter domän konverteringen har slutförts. Under den här fyra timmarsperioden och beroende på olika tjänsten på klientsidan cacheminnen dessa autentiseringar inte kan accepteras av Azure AD och användarna får ett felmeddelande som de ska kunna autentiseras mot AD FS fortfarande, men Azure AD accepterar inte längre en användarens utfärdade token som det federationsförtroendet har nu tagits bort.

> [!NOTE]
> Detta kommer endast inverkan användare komma åt tjänster via en webbläsare under det här inlägget konvertering fönstret förrän tjänsten på klientsidan cachen rensats. Äldre klienter (Exchange ActiveSync, Outlook 2010/2013) bör inte påverkas som Exchange Online från ett cacheminne med sina autentiseringsuppgifter för en viss tidsperiod som används för att autentisera igen användaren tyst utan att behöva gå tillbaka till AD FS. Autentiseringsuppgifter som lagras på enheten för dessa klienter används för att autentisera igen själva tyst när detta cachelagras är avmarkerad och användare får därför inte några frågor för lösenord på grund av domän-konverteringsprocessen. Däremot för Modern autentisering klienter (Office 2013/2016, IOS och Android-appar) dessa använder en giltig uppdatera Token för att hämta nya åtkomsttoken för fortsatt åtkomst till resurser i stället för att gå tillbaka till AD FS och därför är immun till alla frågor för lösenord som en resultat av domän-konvertering bearbeta och fortsätter att fungera utan övrig konfiguration krävs.
> [!IMPORTANT]
> Inte stänga av din AD FS-miljö eller ta bort Office 365 förtroende för förlitande part förrän du har kontrollerat att alla användare har autentiserar med hjälp av molnautentisering.

### <a name="plan-for-rollback"></a>Planera för återställning

Om ett större problem hittas och inte kan lösas snabbt, kanske du vill återställa lösningen tillbaka till Federation. Det är viktigt att planera vad du gör om distributionen inte går som planerat. Om konverteringen i domänen eller en användare misslyckas under distributionen eller behöver du återställer till federation, måste du förstå hur du minimera eventuella driftstopp och påverkan för dina användare.

#### <a name="rolling-back"></a>Återställa

Läs dokumentationen för Federation design och distribution för din viss distributionsinformation. Processen bör omfatta:

* Konvertera hanterade domäner till federerad med hjälp av Convert-MSOLDomainToFederated 

* Om det behövs kan du konfigurera ytterligare anspråk regler.

### <a name="plan-change-communications"></a>Planera ändra kommunikation

En viktig del av planeringen distribution och support är att säkerställa att dina slutanvändare är proaktivt dig informerad om ändringar och vad de kan brytas eller måste göra. 

När både direktautentisering och sömlös SSO distribueras, ändras inloggning för slutanvändaren när åtkomst till Office 365 och andra tillhörande resurser autentiseras via Azure AD. Användare utanför nätverket visas nu i Azure AD-inloggningssida, till skillnad från omdirigeras till sidan formulärbaserad presenteras av externa internetuppkopplade Web Application Proxy-servrar.

Det finns flera element att planera din kommunikationsstrategi för. Exempel på dessa är:

* Meddela användaren om kommande och utgivna funktioner via
  * E-post och andra interna kommunikationskanaler
  * Visuella objekt, till exempel affischer
  * Executive live eller andra kommunikation
* Bestämma vem som anpassar och som skickar meddelandet, och när.

## <a name="implementing-your-solution"></a>Implementera lösningen

Nu när du har planerat din lösning, är du redo att implementera den. Implementering innehåller följande komponenter:

   1. Förbereda för sömlös enkel inloggning på
   2. Om du ändrar inloggningsmetod direktautentisering och Aktivera sömlös SSO

## <a name="step-1--prepare-for-seamless-sso"></a>Steg 1 – Förbered för sömlös SSO

Du måste lägga till en Azure AD-URL till användarnas Zoninställningar för intranätet med hjälp av Grupprincip i Active Directory till dina enheter att använda sömlös enkel inloggning.

Som standard beräknas automatiskt rätt zonen Internet- eller intranätvärdar från en specifik URL i webbläsaren. Till exempel ”http://contoso/” mappar till zonen Intranät, medan ”http://intranet.contoso.com/” mappar till zonen Internet (eftersom URL: en innehåller en punkt). Webbläsare skickar inte Kerberos-biljetter till en slutpunkt i molnet, t.ex. Azure AD-URL, såvida inte du uttryckligen lägga till URL: en till webbläsarens intranätzonen.

Följ den [steg för att lansera](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) önskade ändringar till dina enheter.

> [!IMPORTANT]
> Den här ändringen ändra inte det sätt som användarna måste logga in till Azure AD. Det är dock viktigt den här konfigurationen tillämpas på alla enheter innan du fortsätter med de steg 3 Observera också att användare som loggar in på enheter som inte har fått den här konfigurationen behöver bara ange användarnamn och lösenord för att logga in på Azure AD.

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>Steg 2 – ändra inloggningsmetod till PTA och Aktivera sömlös enkel inloggning

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>Alternativ A: Konfigurera PTA med hjälp av Azure AD Connect

Använd den här metoden när AD FS har ursprungligen konfigurerats med Azure AD Connect. Du kan inte använda den här metoden om din AD FS inte ursprungligen konfigurerades med Azure AD Connect.

> [!IMPORTANT]
> Kom ihåg att genom att följa stegen nedan alla domäner kommer att konverteras från federerade till hanterade. Läs avsnittet planera Migreringsmetoden för mer information.[](#_Plan_Migration_Method)

Först måste du ändra inloggnings-metod:

   1. Öppna guiden på Azure AD Connect-servern.
   2. Välj **ändra användarinloggning i** och välj sedan **nästa**. 
   3. I den **Anslut till Azure AD** skärmen Ange användarnamn och lösenord för en Global administratör.
   4. Den **användarinloggning** skärmen, ändra alternativknappen från **Federation med AD FS** till **direktautentisering**väljer **aktivera enkel inloggning**  därefter **nästa**.
   5. Aktivera enkel inloggning på skärmen anger du autentiseringsuppgifter för administratörskontot för domänen och väljer nästa.  

   > [!NOTE]
   > Administratörsbehörighet för domänen krävs för att aktivera sömlös enkel inloggning som processen utför följande åtgärder som kräver dessa förhöjd behörighet. Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen och sedan tas bort när installationen har slutförts
   >
   > * Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory (AD).
   > * Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
   > * Dessutom skapas två Kerberos tjänsternas huvudnamn (SPN) för att representera två URL: er som används under Azure AD-inloggningen.
   > * Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen och sedan tas bort när installationen har slutförts

   6. I den **redo att konfigurera** skärmen, se till att **starta synkroniseringsprocessen när konfigurationen är klar** kryssrutan är markerad. Välj sedan **konfigurera**.</br>
   ![bild](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. Öppna den **Azure AD-portalen**väljer **Azure Active Directory**, och välj sedan **Azure AD Connect**.
   8. Kontrollera att som **Federation inaktiveras** medan **sömlös enkel inloggning på** och **Pass-omfattande autentisering** är **aktiverad**.</br>
   ![bild](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

Därefter måste du distribuera ytterligare autentiseringsmetoder. Öppna den **Azure-portalen**, bläddra till **Azure Active Directory, Azure AD Connect** och klicka på **direktautentisering**.

Från den **direktautentisering** klickar du på knappen ladda ned. Från den **hämta** Agent-skärmen, klicka på **acceptera villkoren och hämta**.

Hämtningen av ytterligare autentiseringsagenter påbörjas. Installera den sekundära autentiseringsagent på en domänansluten server. 

> [!NOTE]
> Första agenten installeras alltid på Azure AD Connect-servern som en del av konfigurationsändringar i avsnittet användare logga In i Azure AD Connect-verktyget. Inga ytterligare Autentiseringsagenter ska installeras på en separat server. Vi rekommenderar att ha mellan 2 – 3 ytterligare Autentiseringsagenter tillgängliga. 

Kör agentinstallationen för autentisering. Under installationen måste du ange autentiseringsuppgifter för en **Global administratör** konto.

![Bild 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![Bild 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

När autentisering-agenten har installerats kan gå du tillbaka till sidan Autentiseringsagenten för direktautentisering hälsotillstånd för att kontrollera status för ytterligare agenter.


Gå till belastningstester och nästa steg.

> [!IMPORTANT]
> Hoppa över avsnittet Alternativ B: Växla från Federation till PTA genom att använda Azure AD Connect och PowerShell som stegen i att avsnittet inte gäller.  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>Alternativ B - växlar du från federation till PTA med hjälp av Azure AD Connect och PowerShell

Använd det här alternativet om din federationsserver inte ursprungligen konfigurerades med hjälp av Azure AD Connect. Först måste du aktivera direktautentisering:

   1. Öppna guiden på Azure AD Connect-servern.
   2. Välj **ändra användarinloggning i** och välj sedan **nästa**.
   3. I den **Anslut till Azure AD** skärmen Ange användarnamn och lösenord för en Global administratör.
   4. På den **användarinloggning** skärmen, ändra alternativknappen från **inte konfigurerar** till **direktautentisering**väljer **aktivera enkel inloggning** därefter **nästa**.
   5. I **aktivera enkel inloggning** skärmen, ange autentiseringsuppgifter för administratörskontot för domänen och välj sedan **nästa**.

   > [!NOTE]
   > Administratörsbehörighet för domänen krävs för att aktivera sömlös enkel inloggning som processen utför följande åtgärder som kräver dessa förhöjd behörighet. Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen och sedan tas bort när installationen har slutförts.
   >
   > * Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory (AD).
   > * Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
   > * Dessutom skapas två Kerberos tjänsternas huvudnamn (SPN) för att representera två URL: er som används under Azure AD-inloggningen.

   6. I den **redo att konfigurera** skärmen, se till att **starta synkroniseringsprocessen när konfigurationen är klar** kryssrutan är markerad. Välj sedan **konfigurera**.</br>
   ‎![bild](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   När du väljer konfigurera genomförs följande steg:
   * Den första Autentiseringsagenten för direktautentisering är installerad
   * Direkt-funktionen är aktiverad
   * Sömlös enkel inloggning är aktiverat.  
   7. Kontrollera att **Federation** fortfarande **aktiverad** och **sömlös enkel inloggning** och **Pass-omfattande autentisering** är nu aktiverade.
   ![Bild 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. Välj **direktautentisering** och kontrollera att statusen är **Active**.</br>
   
   Om Autentiseringsagenten inte är aktiv, Följ [de här felsökningsstegen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) innan du fortsätter med domän konversationen processen i nästa steg. Du riskerar att orsaka ett avbrott för autentisering om du konverterar domäner innan du verifierar att PTA agenterna har installerats och att deras status visas som ”aktiv” i Azure-portalen.  
   9. Distribuera sedan ytterligare autentiseringsagenter. Öppna den **Azure-portalen**, bläddra till **Azure Active Directory**, **Azure AD Connect** och klicka på **direktautentisering**.
   10. Från den **direktautentisering** klickar du på den **hämta** knappen. Från den **ladda ned Agent** klickar du på på **acceptera villkoren och hämta**.
   
   Hämtningen av ytterligare autentiseringsagenter påbörjas. Installera den sekundära autentiseringsagent på en domänansluten server.

  > [!NOTE]
  > Första agenten installeras alltid på Azure AD Connect-servern som en del av konfigurationsändringar i avsnittet användare logga In i Azure AD Connect-verktyget. Inga ytterligare Autentiseringsagenter ska installeras på en separat server. Vi rekommenderar att ha mellan 2 – 3 ytterligare Autentiseringsagenter tillgängliga.
  
   11. Kör agentinstallationen för autentisering. Under installationen måste du ange autentiseringsuppgifter för en **Global administratör** konto.</br>
   ![Bild 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![Bild 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. När autentisering-agenten har installerats kan gå du tillbaka till sidan Autentiseringsagenten för direktautentisering hälsotillstånd för att kontrollera status för ytterligare agenter.

Nu är Federation fortfarande aktiverade och fungerar för dina domäner. Om du vill fortsätta med distributionen, måste varje domän som ska konverteras från federerade till hanterade så direktautentisering börjar autentisering begäranden för domänen.

Inte alla domäner måste de konverteras på samma gång, kanske du väljer att börja med en testdomän i din produktionsklient eller domänen med minsta möjliga antal användare.

Konverteringen utförs med hjälp av Azure AD PowerShell-modulen.

   1. Öppna **PowerShell** och logga in på Azure AD via en **Global administratör** konto.
   2. Om du vill konvertera den första domänen, kör du följande kommando:
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. Öppna den **Azure AD-portalen**väljer **Azure Active Directory**, och välj sedan **Azure AD Connect**.  
   4. När du har konverterat federerade domäner, kontrollerar du att som **Federation inaktiveras** medan **sömlös enkel inloggning** och **direktautentisering** är **Aktiverat**.</br>
   ![bild](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>Testning och nästa steg

### <a name="test-pass-through-authentication"></a>Test-direktautentisering 

När du din klient använder federation kan har användare omdirigerats från inloggningssidan för Azure AD till AD FS-miljön. Nu när klienten har konfigurerats för direktautentisering istället för federation användare kommer inte få omdirigeras till AD FS och i stället ska logga in direkt via Azure AD-inloggningssida.

Öppna Internet Explorer i InPrivate-läge för att undvika sömlös SSO loggar du in automatiskt och gå till inloggningssidan för Office 365 ([http://portal.office.com](http://portal.office.com/)). Skriv den **UPN** för dina användare och klicka på **nästa**. Se till att ange UPN för en hybrid-användare som har synkroniserats från din lokala Active Directory och som tidigare var federerad. Användaren ser skärmen för att ange sitt användarnamn och lösenord.

![Bild 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![Bild 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

När du skriver lösenordet, bör du hämta omdirigeras till Office 365-portalen.

![Bild 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>Testa sömlös enkel inloggning

   1. Logga in på en domän domänanslutna datorer som är anslutna till företagsnätverket. 
   2. Öppna **Internet Explorer** eller **Chrome** och gå till någon av följande webbadresser:   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (Ersätt Contoso med din domän).

      Användaren kort kommer att omdirigeras till inloggningssidan för Azure AD och ser meddelandet ”försöker att logga in” och bör inte ange ett användarnamn eller lösenord.</br>
   ![Bild 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. Sedan kan ska användaren få omdirigeras och har loggat in på åtkomstpanelen:

> [!NOTE]
> Sömlös enkel inloggning fungerar på Office 365-tjänster som stöder tips för domänen (till exempel myapps.microsoft.com/contoso.com). Office 365-portalen (portal.office.com) för närvarande stöder inte tips för domänen och därför det är normalt att användare måste ange sina UPN. När ett UPN är angivna, sömlös enkel inloggning på kan hämta Kerberos-biljetten för användarens räkning och logga dem i utan att ange ett lösenord.
> [!TIP]
> Överväg att distribuera [Hybrid för Azure AD Join i Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) för en förbättrad enkel inloggning.

### <a name="removal-of-the-relying-party-trust"></a>Borttagning av förlitande part

När du har verifierat att alla användare och klienter har autentiseras via Azure AD, kan det ses säkert att ta bort Office 365 förtroende för förlitande part.

Om AD FS inte används för andra syften (andra förtroende för förlitande part har konfigurerats), är det säkert att inaktivera AD FS nu.

### <a name="rollback"></a>Återställning

Om ett större problem hittas och inte kan lösas snabbt, kanske du vill återställa lösningen tillbaka till Federation.

Läs dokumentationen för Federation design och distribution för din viss distributionsinformation. Processen bör omfatta:

* Konvertera hanterade domäner till federerad med hjälp av Convert-MSOLDomainToFederated
* Om det behövs kan du konfigurera ytterligare anspråk regler.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>Aktivera synkronisering av userPrincipalName uppdateringar

Historiskt sett uppdateringar till attributet UserPrincipalName med synkroniseringstjänsten från den lokala har blockerats, om inte båda dessa villkor är uppfyllda:

* Användaren är hanterad (icke-federerade).
* Användaren har inte tilldelats en licens.

Mer information om hur du kontrollerar eller aktivera den här funktionen finns i följande artikel:

[Synkronisera userPrincipalName uppdateringar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Förnya sömlös SSO Kerberos-krypteringsnyckel

Det är viktigt att ofta förnyar Kerberos krypteringsnyckel för AZUREADSSOACC datorkontot (som representerar Azure AD) skapas i din lokala AD-skog. Vi rekommenderar starkt att du förnyar Kerberos-krypteringsnyckel minst var 30 dagar så att den överensstämmer med hur medlemmar i Active Directory-domänen för att skicka ändringar av lösenord. Eftersom ingen tillhörande enhet ansluten till AZUREADSSOACC datorkontoobjektet måste uppdateringen utföras manuellt.

Följ dessa steg på den lokala servern där du kör Azure AD Connect för att initiera förlängningen av Kerberos-krypteringsnyckel.

[Hur kan jag växla Kerberos krypteringsnyckel för datorkontot AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="monitoring-and-logging"></a>Övervakning och loggning

Servrar som kör den Autentiseringsagenter bör övervakas för att upprätthålla tillgängligheten för lösningen. Förutom allmän räknare för serverprestanda exponera den Autentiseringsagenter prestandaobjekt som kan användas för att förstå autentisering statistik och fel.

Autentiseringsagenter logga åtgärder till Windows-händelseloggarna under ”program och tjänsten Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin”.

Felsökningsloggar kan aktiveras om det behövs.

Visa mer information om [övervakning och loggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Nästa steg

- [Designbegrepp för Azure AD Connect](plan-connect-design-concepts.md)
- [Välj rätt-autentisering](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologier som stöds](plan-connect-design-concepts.md)
