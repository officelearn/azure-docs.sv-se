---
title: "Senaste nytt Viktig information för Azure Active Directory | Microsoft Docs"
description: "Läs mer om nyheterna i Azure Active Directory (AD Azure) inklusive senaste viktig information, kända problem, felkorrigeringar, föråldrade funktioner och kommande ändringar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f1538e1c26cfe658c7f42ccdd57d8bf5aca0b1fb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?




> Hålla dig uppdaterad med vad är nytt i Azure Active Directory genom att prenumerera på detta [feed](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) i din favorit RSS-feed läsare.



Vi förbättrar Azure Active Directory med jämna mellanrum. Om du vill aktivera dig att hålla dig uppdaterad med den senaste utvecklingen finns i det här avsnittet information om:

-   De senaste versionerna 
-   Kända problem 
-   Felkorrigeringar 
-   Föråldrade funktioner 
-   Planer för ändringar 

Du besöker sidan regelbundet vi uppdateras varje månad.

## <a name="november-2017"></a>November 2017
 
### <a name="retiring-acs"></a>Ta bort ACS



**Typ:** ändringsplan  
**Tjänstekategori:** ACS  
**Produkten kapaciteten:** åtkomst till tjänsten 


Microsoft Azure Active Directory-åtkomstkontroll (även kallat åtkomstkontrolltjänsten eller ACS) ska tas bort i sen 2018.  Ytterligare information, inklusive en detaljerad schema & övergripande migrering vägledning ges i nästa några veckor. Under tiden kan lämna kommentarer på denna sida med frågor om ACS och medlem i vårt team kommer för att svara.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsaråtkomst till den hantera webbläsaren i Intune 


**Typ:** ändringsplan  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd




Med det här problemet kommer du att kunna begränsa webbläsaråtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av Intune Managed Browser som en godkänd app. 

Den här ändringen kan du konfigurera följande villkor för program-baserad villkorlig åtkomst:

**-Klientprogram:** webbläsare

**Vad är effekten av ändringen?**

Idag, blockeras åtkomst när du använder det här villkoret. När förhandsgranskning av det här beteendet är tillgänglig, kommer all åtkomst kräver användning av den hanterade webbläsaren. 

Titta och mer på de kommande bloggar och viktig information för den här funktionen. 

Mer information finns i [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD app-baserad villkorlig åtkomst

 
**Typ:** ändringsplan  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd




Följande appar är tänkta att läggas till i listan över [godkända klientprogram](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Mer information finns i:

- [Godkända klienten app-krav](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Villkoren för användning stöd för flera språk



**Typ:** ny funktion    
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning/kompatibilitet





Administratörer kan nu skapa nya villkor för användning (TOU) som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med motsvarande språk. Användare som finns i omfattningen visas PDF-filen med motsvarande språk utifrån deras preferenser. Om det finns ingen matchning, visas standardspråk.


---
 

### <a name="realtime-password-writeback-client-status"></a>Realtid lösenord tillbakaskrivning klientstatus



**Typ:** ny funktion  
**Tjänstekategori:** SSPR  
**Produkten kapaciteten:** användarautentisering


 

Nu kan du granska status för lokala lösenord tillbakaskrivning av klienten. Det här alternativet är tillgängligt i den **lokalt integration** avsnitt i den  **[lösenordsåterställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**  sidan. 

Om det finns problem med anslutningen till din lokala tillbakaskrivning klient, visas ett felmeddelande som ger dig med:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivning av klient 
- En länk till dokumentationen som hjälper dig att lösa problemet. 


Mer information finns i [lokalt integration](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD app-baserad villkorlig åtkomst 



 
**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** identitet säkerhet och skydd





Du kan nu begränsa åtkomst till Office 365 och andra Azure AD-anslutna molnappar till [godkända klientappar](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) som stöd för Intune App Protection principer med hjälp av [Azure AD app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md). Intune app protection-principer används för att konfigurera och skydda företagets data på dessa program.

Genom att kombinera [app-baserade](active-directory-conditional-access-mam.md) med [enhetsbaserad](active-directory-conditional-access-policy-connected-applications.md) principer för villkorlig åtkomst, har du möjlighet att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med app-baserad villkorlig åtkomst:

**Villkor för plattform som stöds**

- iOS
- Android

**Klienten appar villkor**

- Mobila appar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkända-klientappen


Mer information finns i [Azure Active Directory app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md).

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure-portalen



**Typ:** ny funktion  
**Tjänstekategori:** Enhetsregistrering och hantering  
**Produkten kapaciteten:** identitet säkerhet och skydd

 



Nu kan du visa alla dina enheter som är anslutna till Azure AD och enhetsrelaterade aktiviteter i ett och samma ställe. Det finns en ny administration-miljö för att hantera identiteter för enheten och inställningar i Azure-portalen. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD

- Visa egenskaper, inklusive din Azure AD-hybridlösning anslutna enheter

- Hitta BitLocker-nycklar för dina Azure AD-anslutna enheter, hantera din enhet med Intune och mycket mer.

- Hantera Azure AD-enheten-relaterade inställningar


Mer information finns i [hantera enheter med hjälp av Azure portal](device-management-azure-portal.md).



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som enhetsplattform för villkorlig åtkomst i Azure AD 



**Typ:** ny funktion    
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd 
 

Du kan nu inkludera (eller exkludera) macOS som enhet plattform villkor i din Azure AD-principen för villkorlig åtkomst. Med tillägg av macOS till enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS enheter med Intune** -precis som andra plattformar som iOS och Android, ett företagets portalprogram finns för macOS sköta enhetlig registreringar. Ny företagsportalapp för macOS kan du registrera en enhet med Intune och registrera den med Azure AD.
 
- **Se till att macOS enheter följa efterlevnadsprinciper för din organisation som definierats i Intune** -gå till Intune i Azure portal, du kan nu konfigurera principer för efterlevnad för macOS enheter. 
  
- **Begränsa åtkomsten till program i Azure AD för att endast kompatibla macOS enheter** -redigering av villkorlig åtkomst princip har macOS som ett separat enhet plattform. Det här alternativet kan du redigera macOS specifika villkorliga åtkomstprinciper för målprogrammet i Azure.

Mer information finns i:

- [Skapa en enhetsefterlevnadsprincip för macOS enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>NPS-tillägget för Azure MFA 


**Typ:** ny funktion    
**Tjänstekategori:** MFA  
**Produkten kapaciteten:** användarautentisering




Server (NPS)-tillägget för Azure MFA lägger till funktioner för molnbaserade MFA till din infrastruktur för autentisering med hjälp av din befintliga servrar. Med filnamnstillägget NPS du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering din befintliga autentiseringsflödet utan att behöva installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda VPN-anslutningar utan att distribuera Azure MFA-Server. NPS-tillägget fungerar som ett kort mellan RADIUS och molnbaserad Azure MFA för att tillhandahålla en andra faktor-autentisering för federerad eller synkroniserade användare.


Mer information finns i [integrera din befintliga infrastruktur för NPS med Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare


**Typ:** ny funktion    
**Tjänstekategori:** användarhantering  
**Produkten kapaciteten:** Directory 



I administrationscentret för Azure AD kan du nu:

- Återställ en borttagen användare 
- Permanent ta bort en användare 


**Prova det:**

1. Välj i administrationscentret för Azure AD [ **alla användare** ](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) i den **hantera** avsnitt. 

2. Från den **visa** väljer **bort användare nyligen**. 

4. Välj en eller flera nyligen borttagna användare och sedan återställa dem eller ta bort dem permanent.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD app-baserad villkorlig åtkomst

 
**Typ:** ändrade funktionen  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd


Följande appar har lagts till i listan över [godkända klientprogram](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Microsoft Azure Information Protection 


Mer information finns i:

- [Godkända klienten app-krav](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>Möjligheten att 'eller' mellan kontroller i en princip för villkorlig åtkomst 


**Typ:** ändrade funktionen    
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd

 
Möjligheten att 'Eller' (kräver en av de markerade kontrollerna) kontroller för villkorlig åtkomst har släppts. Den här funktionen kan du skapa principer med en **eller** mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att en användare att logga in med multifaktorautentisering **eller** på en kompatibel enhet.

Mer information finns i [kontrollerna i Azure Active Directory för villkorlig åtkomst](active-directory-conditional-access-controls.md).

 
---

### <a name="aggregation-of-realtime-risk-events"></a>Sammanställning av riskhändelser i realtid


**Typ:** ändrade funktionen    
**Tjänstekategori:** identitetsskydd  
**Produkten kapaciteten:** identitet säkerhet och skydd


Om du vill förbättra upplevelsen administration i Azure AD Identity Protection samman realtid riskhändelser som har sitt ursprung på samma IP-adress på en viss dag nu för varje typ av händelse risk. Den här ändringen begränsar mängden riskhändelser visas utan att användarens säkerhets förändras.

Den underliggande realtid identifieringen fungerar varje gång en användare loggar in. Om du har en inloggning risk princip säkerhetsinställningar till MFA eller blockera åtkomst, utlöses den fortfarande under varje riskfyllda inloggning.

 
---
 




## <a name="october-2017"></a>Oktober 2017


### <a name="deprecating-azure-ad-reports"></a>Sluta Azure AD-rapporter


**Typ:** ändringsplan  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** Identitetshantering livscykel  



Azure-portalen ger dig:

- En ny Azure Active Directory-Administrationskonsol 
- Nya API: er för aktiviteten och säkerhet
 
På grund av de nya funktionerna i rapporten API: er under den **/rapporter** slutpunkten ska tas bort på 10 December 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Fältet för automatisk inloggning identifiering


**Typ:** fast   
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  



Azure Active Directory har stöd för automatisk inloggning fältet identifiering för program som renderas ett HTML-fält för användarnamn och lösenord.  De här stegen finns dokumenterade i [så att automatiskt samla in inloggning fält för ett program](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Du hittar den här funktionen genom att lägga till en *icke-galleriet* på den **företagsprogram** sidan i den [Azure-portalen](http://aad.portal.azure.com). Du kan också konfigurera den **enkel inloggning** läge för den här nya programmet till **lösenordsbaserade enkel inloggning**, ange en URL och sedan spara sidan.
 
Den här funktionen har tillfälligt inaktiverats för en viss tidsperiod på grund av problem med en tjänst. Problemet har lösts och fältet för automatisk inloggning identifieringen är tillgänglig igen.

---

### <a name="new-mfa-features"></a>Nya MFA-funktioner


**Typ:** ny funktion  
**Tjänstekategori:** MFA  
**Produkten kapaciteten:** identitet säkerhet och skydd  



Multifaktorautentisering (MFA) är en viktig del av att skydda din organisation. Följande funktioner har lagts till för att göra mer anpassningsbar autentiseringsuppgifter och erfarenhet smidigare: 

- Integrering av Multi-Factor challenge resultat direkt i Azure AD-inloggning rapporten, inklusive Programmeringsåtkomst till MFA-resultat

- Djupare integrering av MFA-konfigurationen till Azure AD-konfiguration upplevelse i Azure-portalen

Med den här offentliga förhandsversionen är en integrerad del av kärnor Azure AD-konfiguration av MFA hantering och rapportering. Aggregering av information om båda funktionerna kan du hantera MFA management portal-funktionerna i Azure AD-upplevelse.

Mer information finns i [referens för multifaktorautentisering rapportering i Azure-portalen](active-directory-reporting-activity-sign-ins-mfa.md) 


---

### <a name="introducing-terms-of-use"></a>Introduktion till villkor för användning



**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning  



Azure AD-villkor för användning av ger en enkel metod för att presentera information till slutanvändare. Detta säkerställer att användarna ser relevanta FRISKRIVNINGAR för juridiska eller efterlevnadskrav.

Du kan använda Azure AD-villkor för användning i följande scenarier:

- Allmänna villkor för användningsvillkor för alla användare i din organisation. 

- Särskilda villkor för användning baserat på användarattribut (t.ex. läkare vs sjuksköterskor eller nationella vs internationella anställda, utförs av dynamiska grupper). 

- Särskilda villkor för användning av åtkomst till affärskritiska påverkan appar, t.ex. Salesforce.

Mer information finns i [Azure Active Directory användningsvillkoren](active-directory-tou.md).


---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av privileged identity Management


**Typ:** ny funktion  
**Tjänstekategori:** PIM  
**Produkten kapaciteten:** Privileged Identity Management  


Med Azure Active Directory Privileged Identity Management (PIM), kan du nu hantera, kontrollera och övervaka åtkomst till resurser i Azure (förhandsversion) inom organisationen:

- Prenumerationer
- Resursgrupper
- Virtuella datorer. 

Alla resurser i Azure-portalen som utnyttjar Azure rollbaserad åtkomstkontroll (RBAC)-funktioner kan dra nytta av alla säkerhets- och hanteringsfunktioner för livscykel Azure AD PIM har att erbjuda.

Mer information finns i [PIM för Azure-resurser](privileged-identity-management/azure-pim-resource-rbac.md).


---

### <a name="introducing-access-reviews"></a>Introduktion till access granskar


**Typ:** ny funktion  
**Tjänstekategori:** åtkomst till granskningar  
**Produkten kapaciteten:** styrning  



Åtkomst granskningar (förhandsgranskning) gör att organisationer att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan recertify gästen användaråtkomst med hjälp av åtkomst granskning av deras åtkomst till program och medlemskap i grupper. De insikter som tillhandahålls av access granskningar aktivera granskare effektivt besluta om gäster ska ha fortsatt åtkomst.

- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst till Azure AD granskar](active-directory-azure-ad-controls-access-reviews-overview.md).


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>Dölja tredjepartsprogram från Mina appar och Office 365-starta



**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  



Du kan nu hantera appar som visas på din användare portaler via en ny bättre **dölja app** egenskapen. Dölja appar hjälper med fall där appen paneler visas för backend-tjänster eller dubbla paneler och hamna fyller hela användarens app startprogram. Att växlingsknappen finns i egenskapsavsnittet för appen från tredje part och etiketteras **synligt för användaren?** Du kan även dölja en app programmässigt med hjälp av PowerShell. 

Mer information finns i [dölja tredjepartsprogram från användarens upplevelse i Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**Vad är tillgängliga?**

 Som en del av övergången till en ny Administratörskonsol är 2 nya API: er för att hämta Azure AD-aktivitetsloggar tillgängliga. Ny uppsättning API: er ger bättre filtrera och sortera funktioner förutom att tillhandahålla bättre gransknings- och logga in aktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporter kan nu nås via identitetsskydd riskhändelser API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-microsoft-identity-manager"></a>Snabbkorrigering för Microsoft Identity Manager


**Typ:** ändrade funktionen  
**Tjänstekategori:** Microsoft Identity Manager  
**Produkten kapaciteten:** Identitetshantering livscykel  



En samlad snabbkorrigering (build 4.4.1642.0) är tillgängliga från och med 25 September 2017 för Microsoft Identity Manager (MIM) 2016 2016 servicepack 1 (SP1). Det här paketet:

- Löser problem och lägger till förbättringar
- Är en kumulativ uppdatering som ersätter alla uppdateringar i MIM 2016 SP1 upp till build 4.4.1459.0 för Microsoft Identity Manager 2016. 
- Du måste ha **Microsoft Identity Manager 2016 skapa 4.4.1302.0.** 

Mer information finns i [samlade paketet (build 4.4.1642.0) är tillgängligt för Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
