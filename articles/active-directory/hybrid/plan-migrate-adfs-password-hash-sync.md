---
title: 'Azure AD Connect: Migrera från federation till synkronisering av lösenordshash för Azure AD | Microsoft Docs'
description: Information om hur du flyttar din hybrid identity-miljö från federation till synkronisering av lösenordshash.
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
ms.openlocfilehash: cad368cb968b94d1327cc99ed4dfa6df0aedd2cd
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555106"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Migrera från federation till synkronisering av lösenordshash för Azure AD
Följande dokument innehåller råd om hur du flyttar från AD FS till synkronisering av lösenordshash.

>[!NOTE]
>Ett nedladdningsbart en kopia av det här dokumentet är tillgänglig [här](https://aka.ms/ADFSTOPHSDPDownload).


## <a name="prerequisites-for-the-migration"></a>Förutsättningar för migrering 
Följande krav måste anges innan du kan migrera.
### <a name="update-azure-ad-connect"></a>Uppdatera Azure AD Connect

Du bör ha minst ska kunna genomföra stegen för att migrera till direktautentisering [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Den här versionen innehåller betydande förändringar i hur inloggning konverteringen utförs och minskar den totala tid att migrera från Federation till autentisering i molnet från potentiellt timmar och minuter.

> [!IMPORTANT]
> Inaktuella dokumentation, verktyg och bloggar tyda på att användarkonvertering är ett obligatoriskt steg vid konvertering domäner från federerade till hanterade. Observera att konvertering av användare krävs inte längre och Microsoft arbetar på att uppdatera dokumentation och verktyg för att återspegla detta.

Uppdatera Azure AD Connect till den senaste versionen gå igenom den här [uppdatera instruktioner](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Lösenord hash-synkronisering krävs behörigheter

Azure AD Connect kan konfigureras med standardinställningar eller Anpassad Installation. Om du har använt alternativet Anpassad Installation den [nödvändiga behörigheter](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) för synkronisering av Lösenordshash inte kan vara på plats.

Azure AD Connect AD DS-tjänsten konto behöver följande behörigheter för att kunna synkronisera lösenordshash.

* Replikera katalogändringar

* Replikera katalogen ändras alla

Nu är ett bra tillfälle att verifiera de behörigheter som används i alla domäner i skogen.

### <a name="plan-migration-method"></a>Planera migreringsmetod

Det finns två metoder för att migrera från federerad autentisering till synkronisering av Lösenordshash och sömlös enkel inloggning. Vilken metod du använder beror på hur AD FS ursprungligen konfigurerades. 



- **Alternativ A: Använda Azure AD Connect**. Om AD FS ursprungligen konfigurerades med Azure AD Connect, måste ändringen Hash-synkronisering av lösenord som användaren loggar in metod utföras via Azure AD Connect-guiden.   
När du använder Azure AD Connect, körs det cmdleten Set-MsolDomainAuthentication du automatiskt när du ändrar inloggningsmetod för användare och därför du har ingen kontroll över den unfederating alla verifierade federerade domäner i Azure AD-klienten.

> [!NOTE]
> Du kan inte undvika unfederating alla domäner i din klientorganisation när du ändrar den användaren logga in till synkronisering av Lösenordshash när AAD Connect användes ursprungligen för att konfigurera AD FS för dig just nu.  



- **Alternativ B: Använda Azure AD Connect med PowerShell**. Den här metoden kan endast användas när AD FS inte ursprungligen konfigurerades med Azure AD Connect. Du måste ändra metoden användare logga in via Azure AD Connect-guiden, men den grundläggande skillnaden är att den inte körs automatiskt cmdleten Set-MsolDomainAuthentication åt dig när den har ingen medvetenhet om AD FS-gruppen och därför har du fullständig kontroll över vilka domäner konverteras och i vilken ordning.

Utför steg i följande avsnitt för att förstå vilken metod du ska använda.

#### <a name="verify-current-user-sign-in-settings"></a>Kontrollera aktuell användare logga in inställningarna

Kontrollera dina aktuella användare logga in inställningar genom att logga in på Azure AD-portalen [ https://aad.portal.azure.com ](https://aad.portal.azure.com/) med ett globalt administratörskonto.

Kontrollera att Federation är aktiverat och att sömlös enkel inloggning och direktautentisering är inaktiverat i avsnittet användare logga In. Kontrollera också det aktuella tillståndet för synkronisering av lösenord som ska visas som inaktiverad, såvida inte detta tidigare har slagits på.

![Bild 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Verifiera konfigurationen av Azure AD Connect

   1. Gå till din Azure AD Connect-servern och starta Azure AD Connect, och välj sedan konfigurera. 
   2. Välj Visa aktuell konfiguration på skärmen ytterligare uppgifter och därefter på Nästa.</br>
   ![Bild 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. Ta del av status för synkronisering av lösenord på skärmen granska din lösning.</br> 

   Om synkronisering av Lösenordshash är för närvarande inaktiverad, måste du följa stegen i den här guiden för att aktivera den. Synkronisering av Lösenordshash är för närvarande inställd på aktiverad, kan du på ett säkert sätt hoppa över avsnittet [steg 1 – Aktivera synkronisering av Lösenordshash](#step-1--enable-password-hash-synchronization) i den här guiden.
   4. Rulla ned till Active Directory Federation Services (AD FS) i fönstret granska din lösning.</br>
 
   Om du ser att AD FS-konfigurationen är i det här avsnittet och sedan på ett säkert sätt kan du anta AD FS ursprungligen konfigurerades via Azure AD Connect och kan därför konvertering av din domänerna från federerad som hanteras kan också styras via Azure AD Connect ”ändra användarinloggning -i ”alternativet, den här processen beskrivs i avsnittet **alternativ A - växeln från Federation till synkronisering av Lösenordshash med hjälp av Azure AD Connect**.
   5. Om du inte kan se Active Directory Federation Services som visas i de aktuella inställningarna så måste du manuellt konvertera domänerna från federerat till hanteras via PowerShell som beskrivs i avsnittet **alternativ B - växlar du från federering till Synkronisering av Lösenordshash med hjälp av Azure AD Connect och PowerShell**.

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
Validera alla inställningar som kanske har anpassats till dina Federation design- och dokumentation, mer specifikt PreferredAuthenticationProtocol SupportsMfa och PromptLoginBehavior.

Mer information om vad inställningarna finns nedan.

[Active Directory Federation Services uppmana = parameterstöd för inloggning](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Om SupportsMfa är för närvarande värdet ”true” sedan detta innebär att du använder en lokala MFA-lösning för att mata in en 2nd factor-utmaning i autentiseringsflödet för användaren. Detta fungerar inte längre för scenarier med Azure AD-autentisering och i stället måste du använda Azure MFA (molnbaserad) tjänst för att utföra samma funktion. Noggrant utvärdera dina krav på MFA innan du går vidare och se till att du förstår hur du utnyttjar Azure MFA och licensiering konsekvenserna registreringsprocessen för användaren innan du konverterar dina domäner. Vår Distributionsguide för Azure MFA som innehåller mer information finns på [ https://aka.ms/deploymentplans ](https://aka.ms/deploymentplans).

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
| AD FS är kraftigt anpassade och beroende av specifika anpassningsinställningarna i onload.js-filen inte kan dupliceras i Azure AD (exempel: du har ändrat inloggningen så att användarna bara ange något SamAccountName-format för sina användarnamn i motsats i ett UPN- eller har en kraftigt Företagsanpassad inloggningen går)| Behöver du kontrollera att din aktuella anpassning kraven kan uppfyllas av Azure AD innan du fortsätter. Se avsnitten anpassning av AD FS och AD FS-anpassning för mer information och vägledning.|
| Du blockerar äldre autentiseringsklienter via AD FS.| Överväg att ersätta kontroller för att blockera äldre autentiseringsklienter som för närvarande finns på AD FS med en kombination av [villkorlig åtkomstkontroll för äldre autentisering](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) och [klientåtkomst för Exchange Online Regler för](http://aka.ms/EXOCAR).|
| Du behöver användare att utföra MFA mot den lokala MFA server-lösning när du autentiserar till AD FS.| Du kommer inte att kunna mata in en MFA-kontrollen via den lokala MFA-lösning i autentiseringsflödet för en hanterad domän, men du kan använda Azure MFA-tjänsten för att göra det framöver när domänen konverteras. Om användarna inte använder Azure MFA i dag, kommer det innebära en gång slutanvändarens registreringssteget som du måste förbereda för och kommunicera dina slutanvändare.|
| Du använder principer för åtkomstkontroll (AuthZ-regler) idag i AD FS för att styra åtkomsten till Office 365.| Överväg att ersätta dem med motsvarande Azure AD [principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) och [åtkomstregler för Exchange Online-klienten](http://aka.ms/EXOCAR).|

### <a name="considerations-for-common-ad-fs-customizations"></a>Överväganden för vanliga AD FS-anpassningar

#### <a name="inside-corporate-network-claim"></a>Inifrån företagsnätverkanspråk

InsideCorporateNetwork anspråk utfärdas av AD FS om den autentiserande användaren är i företagsnätverket. Det här anspråket kan sedan skickas till Azure AD och används för att koppla förbi Multi-Factor authentication baserat på användarens nätverksplats. Se [tillförlitliga IP-adresser för federerade användare](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) för information om hur du avgör om du har aktiverat för närvarande i AD FS.

InsideCorporateNetwork anspråket visas inte längre när dina domäner konverteras till synkronisering av Lösenordshash. [Namngivna platser i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) kan användas för att ersätta den här funktionen.

När med namnet platser har konfigurerats, alla principer för villkorlig åtkomst som konfigurerats för att inkludera eller exkludera nätverksplatserna som ”alla betrodda platser” eller ”MFA tillförlitliga IP-adresser” måste uppdateras för att återspegla de nyligen skapade med namnet platser.

Se [Active Directory villkorsstyrd åtkomstplatser](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations) mer information om platsvillkoret för villkorlig åtkomst.

#### <a name="hybrid-azure-ad-joined-devices"></a>Enheter för hybrid Azure AD har anslutits

Koppla en enhet till Azure AD kan du skapa regler för villkorlig åtkomst som tillämpar enheter som uppfyller ditt åtkomst-standarder för säkerhet och efterlevnad och tillåter användare att logga in på en enhet med ett organisations arbets- eller skolkonto i stället för en personlig konto. Hybrid Azure AD-anslutna enheter kan du ansluta din AD-domänanslutna enheter till Azure AD. Din federerad miljö kan ha konfigurerats med den här funktionen.

Om du vill säkerställa att ansluta till Hybrid fortsätter att fungera för alla nya enheter som är anslutna till domänen när dina domäner har konverterats till synkronisering av Lösenordshash, måste Azure AD Connect konfigureras för att synkronisera Active Directory-datorkonton för Windows 10-klienter Azure AD. För Windows 7 och Windows 8-datorkonton, Hybrid delta använder sömlös SSO för att registrera datorn i Azure AD och du behöver inte synkronisera dem som du gör med Windows 10-enheter. Du men måste distribuera en uppdaterad workplacejoin.exe-fil (via en MSI-fil) i dessa äldre klienter så att de kan registrera sig med sömlös enkel inloggning. [Ladda ned MSI-filen](https://www.microsoft.com/download/details.aspx?id=53554). 

Mer information finns i [hur du konfigurerar hybrid Azure Active Directory-anslutna enheter](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Anpassning

Din organisation kan ha [anpassade din AD FS-inloggningssidor](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) att visa information som är mer relevant i organisationen. I så, fall Överväg att göra liknande [anpassningar till inloggningssidan för Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Liknande anpassningar är tillgängliga, väntat vissa visuella ändringar. Du kanske vill inkludera ändringarna i din kommunikation till slutanvändare.

> [!NOTE]
> Företagsanpassning är tillgängligt endast om du har köpt Premium eller Basic-licens för Azure AD eller har en Office 365-licens.

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

När både Lösenordshashsynkronisering och sömlös SSO distribueras, ändras inloggning för slutanvändaren när åtkomst till Office 365 och andra tillhörande resurser autentiseras via Azure AD. Användare utanför nätverket visas nu i Azure AD-inloggningen sidan, till skillnad från omdirigeras till sidan formulärbaserad presenteras av externa internetuppkopplade Web Application Proxy-servrar.

Det finns flera element att planera din kommunikationsstrategi för. Exempel på dessa är:

* Meddela användaren om kommande och utgivna funktioner via
  * E-post och andra interna kommunikationskanaler
  * Visuella objekt, till exempel affischer
  * Executive live eller andra kommunikation
* Bestämma vem som anpassar och som skickar meddelandet, och när.

## <a name="implementing-your-solution"></a>Implementera lösningen

Nu när du har planerat din lösning, är du redo att implementera den. Implementering innehåller följande komponenter:

1. Aktivera synkronisering av lösenordshash

2. Förbereda för sömlös enkel inloggning på

3. Om du ändrar inloggningsmetod till synkronisering av Lösenordshash och Aktivera sömlös SSO

### <a name="step-1--enable-password-hash-synchronization"></a>Steg 1 – Aktivera synkronisering av lösenordshash

Det första steget för att implementera den här lösningen är att aktivera synkronisering av Lösenordshash på Azure AD Connect-guiden. Synkronisering av Lösenordshash är en valfri funktion som kan aktiveras i miljöer med Federation utan någon inverkan på autentiseringsflödet. I det här fallet Azure AD Connect startar synkronisering av lösenords-hash utan att påverka användare logga in med federation.

Därför rekommenderar vi den här åtgärden som en förberedande aktivitet bra innan du ändrar din domäner inloggningsmetod. Så får du gott om tid att validera synkronisering av Lösenordshash fungerar korrekt.

Så här aktiverar synkronisering av Lösenordshash:

   1. Öppna guiden på Azure AD Connect-servern och välj Konfigurera.
   2. Välj Anpassa synkroniseringsalternativ och sedan klicka på Nästa.
   3. Ange användarnamnet och lösenordet för en Global administratör i Anslut till Azure AD.
   4. Klicka på Nästa i Connect skärmen kataloger.
   5. Klicka på Nästa på skärmen domän och Organisationsenhet filtrering.
   6. Välj synkronisering av lösenord i skärmen för valfria funktioner och väljer nästa.
   ![Bild 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Välj därefter på alla återstående skärmar och konfigurera på den sista skärmen.
   8. Azure AD Connect startar synkronisering av lösenords-hash vid nästa synkronisering.

När synkronisering av Lösenordshash har aktiverats, hashar lösenordet för alla användare i Azure AD Connect synkronisering omfång kommer rehashed och skrivs till Azure AD. Beroende på antalet användare, kan den här åtgärden ta från några minuter till flera timmar.

För planering, bör du uppskattar att cirka 20 000 användare kan bearbetas i timmen.

Om du vill validera synkronisering av Lösenordshash fungerar korrekt, Använd felsökning-åtgärd på Azure AD Connect-guiden.

   1. Öppna en ny Windows PowerShell-session på din Azure AD Connect-server med alternativet Kör som administratör.
   2. Kör Set-ExecutionPolicy RemoteSigned eller Set-ExecutionPolicy obegränsad.
   3. Starta Azure AD Connect-guiden.
   4. Gå till sidan ytterligare aktiviteter, avancerade och klicka på Nästa.
   5. På sidan om felsökning, klickar du på Starta till startmenyn felsökning i PowerShell.
   6. Välj Felsök synkronisering av lösenordshash på huvudmenyn.
   7. I sub-menyn väljer du lösenord hashsynkronisering inte fungerar alls.

Om du har hittat problem kan du använda informationen på [i den här artikeln](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) att felsöka.

### <a name="step-2--prepare-for-seamless-sso"></a>Steg 2 – förbereda för sömlös SSO

Du måste lägga till en Azure AD-URL till användarnas Zoninställningar för intranätet med hjälp av Grupprincip i Active Directory till dina enheter att använda sömlös enkel inloggning.

Som standard beräknas automatiskt rätt zonen Internet- eller intranätvärdar från en specifik URL i webbläsaren. Till exempel ”http://contoso/” mappar till zonen Intranät, medan ”http://intranet.contoso.com/” mappar till zonen Internet (eftersom URL: en innehåller en punkt). Webbläsare skickar inte Kerberos-biljetter till en slutpunkt i molnet, t.ex. Azure AD-URL, såvida inte du uttryckligen lägga till URL: en till webbläsarens intranätzonen.

Följ den [steg för att lansera](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) önskade ändringar till dina enheter.

> [!IMPORTANT]
> Den här ändringen ändra inte det sätt som användarna måste logga in till Azure AD. Det är dock viktigt den här konfigurationen tillämpas på alla enheter innan du fortsätter med steg3. Observera också att användare som loggar in på enheter som inte har fått den här konfigurationen behöver bara ange användarnamn och lösenord för att logga in på Azure AD.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>Steg 3 – ändra inloggningsmetod till PHS och Aktivera sömlös enkel inloggning

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>Alternativ A - växlar du från federation till PHS med hjälp av Azure AD Connect

Använd den här metoden när AD FS har ursprungligen konfigurerats med Azure AD Connect. Du kan inte använda den här metoden om din AD FS inte ursprungligen konfigurerades med Azure AD Connect. Första **ändra användare logga in metod**

   1. Öppna guiden på Azure AD Connect-servern.
   2. Välj Ändra användare logga in och sedan klicka på Nästa.
   ![Bild 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. I den **Anslut till Azure AD** skärmen Ange användarnamn och lösenord för en **Global administratör**.
   4. I den **användarinloggning** skärmen, ändra alternativknappen från Federation med AD FS för att skicka Hash-synkronisering och se till att markera kryssrutan konvertera inte användarkonton som detta är en föråldrad steg och tas bort från framtida versioner Anslut med AAD. Också välja Aktivera enkel inloggning och välj sedan **nästa**.
   ![Bild 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Från och med Azure AD Connect version 1.1.880.0, aktiveras sömlös enkel inloggning för kryssrutan som standard.
   
   > [!IMPORTANT]
   > Du kan ignorera varningar som anger den användarkonvertering och fullständig synkronisering av lösenordshash är obligatoriska steg för att konvertera från federation till autentisering i molnet. Observera att dessa steg krävs inte längre, framtida versioner av Azure AD Connect har inte ett alternativ för att konvertera användare. Om du fortfarande ser dessa varningar, kontrollera att du har den senaste versionen av Azure AD Connect och som du använder den senaste versionen av den här guiden. Mer information finns i den [uppdatering Azure AD Connect-avsnittet](#_Update_Azure_AD).
   
   5. Aktivera enkel inloggning på skärmen anger du autentiseringsuppgifter för administratörskontot för domänen och väljer nästa.
   ![Bild 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > Administratörsbehörighet för domänen krävs för att aktivera sömlös enkel inloggning som processen utför följande åtgärder som kräver dessa förhöjd behörighet. Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen och sedan tas bort när installationen har slutförts
   >  * Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory (AD).
   >  * Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
   >  * Dessutom skapas två Kerberos tjänsternas huvudnamn (SPN) för att representera två URL: er som används under Azure AD-inloggningen.
   >  * Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen och sedan tas bort när installationen har slutförts
   
   6. Redo att konfigurera skärmen, se till att ”starta synkroniseringsprocessen när konfigurationen är klar” är markerad. Välj sedan konfigurera.
   ![Bild 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > I det här läget kommer federerade domäner att ändras till hanterad autentisering vilket kan nu använda synkronisering av Lösenordshash som metod för autentisering.
       
   7. Öppna Azure AD-portalen, väljer Azure Active Directory och välj sedan Azure AD Connect.
   8. Kontrollera att Federation inaktiveras när sömlös enkel inloggning och synkronisering av lösenord är aktiverade.  
  ![Bild 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Gå till [testning och nästa steg](#testing-and-next-steps).
   
   > [!IMPORTANT]
   > Hoppa över avsnittet Alternativ B – växla från Federation till Lösenordshashsynkronisering med Azure AD Connect och PowerShell som stegen i avsnittet gäller inte.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>Alternativ B - växlar du från federation till PHS med hjälp av Azure AD Connect och PowerShell

Använd det här alternativet om din federationsserver inte ursprungligen konfigurerades med hjälp av Azure AD Connect.

Som en del av den här processen kan du Aktivera sömlös enkel inloggning och växla dina domäner från federerade till hanterade.

   1. Öppna guiden på Azure AD Connect-servern.
   2. Välj Ändra användare logga in och sedan klicka på Nästa. 
   3. Ange användarnamnet och lösenordet för en Global administratör i Anslut till Azure AD.
   4. På skärmen användarinloggning, ändra alternativknappen från inte konfigurerar till synkronisering av Lösenordshash, aktivera Markera enkel inloggning och sedan väljer nästa.
   
   Före ändringen: ![Bild 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Efter ändringen:  
   ![Bild 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Från och med Azure AD Connect version 1.1.880.0, aktiveras sömlös enkel inloggning för kryssrutan som standard.
   
   5. Aktivera enkel inloggning på skärmen anger du autentiseringsuppgifter för administratörskontot för domänen och väljer nästa.
   
   > [!NOTE]
   > Administratörsbehörighet för domänen krävs för att aktivera sömlös enkel inloggning som processen utför följande åtgärder som kräver dessa förhöjd behörighet. Autentiseringsuppgifter som domänadministratör lagras inte i Azure AD Connect eller i Azure AD. De används endast för att aktivera funktionen och sedan tas bort när installationen har slutförts.
   > * Ett datorkonto med namnet AZUREADSSOACC (som representerar Azure AD) skapas i din lokala Active Directory (AD).
   > * Krypteringsnyckel för det datorkonto Kerberos delas på ett säkert sätt med Azure AD.
   > * Dessutom skapas två Kerberos tjänsternas huvudnamn (SPN) för att representera två URL: er som används under Azure AD-inloggningen.
   
   6. Redo att konfigurera skärmen, se till att ”starta synkroniseringsprocessen när konfigurationen är klar” är markerad. Välj sedan konfigurera.
   ![Bild 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   När väljer konfigurerar konfigureras sömlös enkel inloggning enligt förhandsversioner steg. Lösenord för synkronisering av Lösenordshash konfiguration ändras inte eftersom den har aktiverats tidigare.
   
   > [!IMPORTANT]
   > Inga ändringar görs i hur användarna loggar in i det här läget.  
   
   7. Kontrollera att Federation fortsätter att vara aktiverad och nu sömlös enkel inloggning är aktiverad på Azure AD-portalen.
   ![Bild 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konvertera domäner från federerad som hanteras

Nu är Federation fortfarande aktiverade och fungerar för dina domäner. Om du vill fortsätta med distributionen, måste varje domän som ska konverteras från federerade till hanterade att tvinga användarautentisering via synkronisering av Lösenordshash.

> [!IMPORTANT]
> Inte alla domäner måste de konverteras på samma gång, kanske du väljer att börja med en testdomän i din produktionsklient eller domänen med minsta möjliga antal användare.

Konverteringen utförs med hjälp av Azure AD PowerShell-modulen.

   1. Öppna PowerShell och logga in på Azure AD med ett globalt administratörskonto.  
   2. Om du vill konvertera den första domänen, kör du följande kommando:  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. Öppna Azure AD-portalen, väljer Azure Active Directory och välj sedan Azure AD Connect.
   4. Kontrollera att domänen har konverterats till hanterade genom att köra följande kommando:
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Testning och nästa steg

### <a name="test-authentication-with-phs"></a>Testa autentisering med PHS

När du din klient använder federation kan har användare omdirigerats från inloggningssidan för Azure AD till AD FS-miljön. Nu när klienten har konfigurerats för synkronisering av Lösenordshash istället för federation användare kommer inte få omdirigeras till AD FS och i stället ska logga in direkt via Azure AD-inloggningssida.

Öppna Internet Explorer i InPrivate-läge för att undvika sömlös SSO loggar du in automatiskt och gå till inloggningssidan för Office 365 ([http://portal.office.com](http://portal.office.com/)). Ange ditt användar-UPN och klicka på Nästa. Se till att ange UPN för en hybrid-användare som har synkroniserats från din lokala Active Directory och som tidigare var federerad. Användaren ser skärmen för att ange sitt användarnamn och lösenord.

![Bild 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Bild 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

När du skriver lösenordet, bör du hämta omdirigeras till Office 365-portalen.

![Bild 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Testa sömlös enkel inloggning

Logga in på en domänansluten dator som är ansluten till företagsnätverket. Öppna Internet Explorer och gå till någon av följande webbadresser:  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (Ersätt Contoso med din domän).

Användaren kort kommer att omdirigeras till inloggningssidan för Azure AD och ser meddelandet ”försöker att logga in” och bör inte ange ett användarnamn eller lösenord.

![Bild 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

Sedan kan ska användaren få omdirigeras och har loggat in på åtkomstpanelen:

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

### <a name="troubleshooting"></a>Felsökning

Supportteamet bör förstå hur du kan felsöka eventuella problem med autentisering som kan uppstå under eller efter ändringen från federation som hanteras. Använd följande Felsökningsdokumentation för att supportteamet bekanta dig med vanliga åtgärder för felsökning och lämpliga åtgärder som kan hjälpa till att isolera och lösa problemet.

[Felsöka Azure Active Directory synkronisering av Lösenordshash](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Felsöka Azure Active Directory sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Förnya sömlös SSO Kerberos-dekryptering

Det är viktigt att ofta förnyar Kerberos krypteringsnyckel för AZUREADSSOACC datorkontot (som representerar Azure AD) skapas i din lokala AD-skog. Vi rekommenderar starkt att du förnyar Kerberos-krypteringsnyckel minst var 30 dagar så att den överensstämmer med hur medlemmar i Active Directory-domänen för att skicka ändringar av lösenord. Eftersom det är inte associerad måste enhet ansluten till AZUREADSSOACC datorkontoobjektet i förlängningen utföras manuellt.

Följ dessa steg på den lokala servern där du kör Azure AD Connect för att starta förnyelsen av Kerberos-krypteringsnyckel.

[Hur kan jag växla Kerberos krypteringsnyckel för datorkontot AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>Nästa steg

- [Designbegrepp för Azure AD Connect](plan-connect-design-concepts.md)
- [Välj rätt-autentisering](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologier som stöds](plan-connect-design-concepts.md)
