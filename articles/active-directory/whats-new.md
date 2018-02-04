---
title: "Nyheter Viktig information för Azure Active Directory | Microsoft Docs"
description: "Läs mer om nyheterna i Azure Active Directory (Azure AD), till exempel senaste viktig information, kända problem, felkorrigeringar, föråldrade funktioner och kommande ändringar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 34826332db2e63d442d47ada74fcbad4cad600ae
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?




> Håll dig uppdaterad med vad är nytt i Azure Active Directory (AD Azure) genom att prenumerera på den [ ![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [feed](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us).



Azure AD tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen innehåller i den här artikeln information om:

-   De senaste versionerna
-   Kända problem
-   Felkorrigeringar
-   Föråldrade funktioner
-   Planer för ändringar

Den här sidan uppdateras varje månad, så kontrollera igen den regelbundet.


## <a name="january-2018"></a>Januari 2018
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Nya federerade appar tillgängliga i Azure AD App-galleriet 

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkten kapaciteten:** 3 part-integrering
 

Följande nya appar med stöd för identitetsfederation lades till i appgalleriet i januari 2018:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust sekretess hanteringsprogramvara](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk federerad Directory](https://go.microsoft.com/fwlink/?linkid=864699) och [återgivning NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

En fullständig översikt över alla tillgängliga kurser finns [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>Inloggning med identifierad ytterligare risk

**Typ:** ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkten kapaciteten:** identitet säkerhet och skydd
 

Den information som du får en identifierad risk händelse är bundet till din Azure AD-prenumeration. Med Azure AD Premium P2-edition får du den mest detaljerade informationen om alla underliggande identifieringar.

Med Azure AD Premium P1-versionen visas identifieringar som inte omfattas av din licens som händelsen risk logga in med ytterligare risk upptäcktes.

Mer information finns i avsnittet om [Azure Active Directory-riskhändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Dölj Office 365-program från slutanvändarens åtkomst paneler

**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning
 

Du kan nu bättre hantera hur Office 365-program visas på användarnas åtkomst paneler via en ny Användarinställning. Det här alternativet är användbart för att minska mängden appar i en användares åtkomst panelerna om du vill visa bara Office-appar i Office-portalen. Inställningen finns i den **användarinställningar** och etiketteras **användare kan bara visa Office 365-appar i Office 365-portalen**.
 

Mer information finns i [dölja ett program från användarens upplevelse i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Sömlös logga in på appar som har aktiverats för lösenord SSO direkt från appens URL 

**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning
 

Webbläsartillägget för Mina appar finns nu tillgänglig via ett enkelt verktyg som ger dig den Mina appar enkel inloggning på kapaciteten som en genväg i webbläsaren. När du installerar användarens ikonen waffle i webbläsaren ger som dem snabb åtkomst till appar. Användare kan nu dra nytta av:

- Möjligheten att direkt logga in på lösenord SSO-baserade appar från appens inloggningssidan
- Starta alla appar som använder funktionen Snabbsökning
- Genvägar till nyligen använda appar från tillägget
- Tillägget är tillgängliga för gräns, Chrome och Firefox.
 
Mer information finns i [Mina appar säker inloggning tillägget](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction#my-apps-secure-sign-in-extension).

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administration upplevelse i klassiska Azure-portalen har tagits bort

**Typ:** föråldrad   
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** Directory
 

Från och med 8 januari 2018, Azure AD-administration upplevelse i den klassiska Azure-portalen har tagits bort. Detta ägde rum tillsammans med tillbakadragningen av den klassiska Azure-portalen sig själv. Framöver, bör du använda den [administrationscentret för Azure AD](https://aad.portal.azure.com) för din portal-baserad administration av Azure AD.
 
---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure AD-administration upplevelse i klassiska Azure-portalen har tagits bort

**Typ:** föråldrad  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** Directory
 

Från och med den 8 januari 2018 har webbportalen PhoneFactor tagits bort. Den här portalen användes för administration av MFA-server, men de här funktionerna har flyttats till Azure portal på portal.azure.com. 

MFA-konfigurationen finns på: **Azure Active Directory \> MFA-Server**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Föråldrad Azure AD-rapporter


**Typ:** föråldrad  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** Identitetshantering livscykel  


Med den allmänna tillgängligheten för den nya Azure Active Directory-administrationskonsolen och nya API: erna finns nu tillgängligt för både aktivitet och säkerhet kan rapporten API: er under ”/ rapporter” slutpunkt har tagits bort från slutet av den 31 December 2017.


**Vad är tillgängliga?**

Som en del av övergången till en ny Administratörskonsol, har vi gjort 2 nya API: er tillgängliga för hämtning av Azure AD-aktivitetsloggar. Ny uppsättning API: er ger bättre filtrera och sortera funktioner förutom att tillhandahålla bättre gransknings- och logga in aktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporter kan nu nås via identitetsskydd riskhändelser API i Microsoft Graph.

Mer information finns i:

- [Kom igång med Azure Active Directory reporting API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Kom igång med Azure Active Directory Identity Protection och Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)


---


## <a name="december-2017"></a>December 2017
 

### <a name="terms-of-use-in-the-access-panel"></a>Villkor för användning i åtkomstpanelen

**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning/kompatibilitet
 
Du kan nu gå till åtkomstpanelen och visa användningsvillkoren som du tidigare har accepterat.

Följ de här stegen:

1. Gå till den [MyApps portal](https://myapps.microsoft.com), och logga in.

2. Välj ditt namn i det övre högra hörnet och välj sedan **profil** från listan. 

3. På din **profil**väljer **granska användningsvillkoren**. 

4. Nu kan du granska användningsvillkoren accepteras. 

Mer information finns i [Azure AD-användningsvillkor Använd funktion (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>Nya Azure AD-inloggningsupplevelsen

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** användarautentisering
 
Azure AD och identitetssystem användargränssnitt för Microsoft-konto har designats om så att de har ett konsekvent utseende. Dessutom kan sidan för Azure AD samlar in användarnamnet först, följt av autentiseringsuppgifter på en annan skärm.

Mer information finns i [den nya Azure AD inloggningen är nu tillgänglig som förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/).
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Färre inloggning prompter: en ny ”jag vill förbli inloggad” upplevelse för Azure AD-inloggning

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** användarautentisering
 
Den **Håll mig inloggad** kryssrutan på sidan för Azure AD har ersatts med en ny fråga som visas när du kunna autentisera. 

Om du svarar **Ja** till den här uppmaningen tjänsten ger dig en beständig uppdateringstoken. Det här beteendet är samma som om du har valt den **Håll mig inloggad** kryssrutan i gamla upplevelsen. För federerade klienter visar den här uppmaningen när du autentisera med den federerade tjänsten.

Mer information finns i [färre inloggning prompter: ”Jag vill förbli inloggad” avanmäla för Azure AD är en förhandsversion](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/). 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Lägg till konfiguration för att kräva användningsvillkor som ska expanderas innan accepterar

**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning
 
Ett alternativ för administratörer kräver användarna att expandera användningsvillkoren innan du godkänner villkoren.

Välj antingen **på** eller **av** användare måste expandera användningsvillkoren. Den **på** inställningen kräver att användarna ska visa användningsvillkoren innan du godkänner dem.

Mer information finns i [Azure AD-användningsvillkor Använd funktion (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/active-directory-tou).
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>Begränsade aktivering för berättigade rolltilldelningar

**Typ:** ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Du kan använda begränsade aktivering för att aktivera berättigade Azure-resurs rolltilldelningar med mindre självständigt än de ursprungliga tilldelningen standardvärdena. Ett exempel är om du tilldelade till ägare av en prenumeration i din klient. Du kan aktivera ägarrollen för upp till fem resurser som ingår i prenumerationen (till exempel resursgrupper och virtuella datorer) med begränsade aktivering. Ange omfång aktiveringen kan minska risken för körning oönskade ändringar av kritiska Azure-resurser.

Mer information finns i [vad är Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure).
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Nya federerade appar i appgalleriet för Azure AD

**Typ:** ny funktion  
**Tjänstekategori:** företagsappar  
**Produkten kapaciteten:** integration från tredje part
 
Följande nya appar med stöd för identitetsfederation lades till i appgalleriet i December 2017:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe upplevelse Manager [EFI digitala StoreFront](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [bild fungerar](https://go.microsoft.com/fwlink/?linkid=863517), [MOBI](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure AD-integrering](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO för bambu resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO för Bitbucket resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR Zenegy Azure AD-integrering.

En fullständig översikt över alla tillgängliga kurser finns [SaaS-programintegrering med Azure Active Directory](https://aka.ms/appstutorial).

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Godkännandearbetsflöden för Azure AD directory roller

**Typ:** ändrade funktionen  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Arbetsflöde för godkännande för Azure AD directory roller är allmänt tillgänglig.

Med Godkännandearbetsflöde kan privilegierade rollen administratörer kräva kvalificerade rollmedlemmar till begäran rollaktivering innan de kan använda den privilegierade rollen. Flera användare och grupper kan vara delegerade godkännande ansvarsområden. Berättigad rollmedlemmar få meddelanden när godkännande har slutförts och deras roll är aktiv.

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>Direkt-autentisering: Skype för företag-support

**Typ:** ändrade funktionen  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkten kapaciteten:** användarautentisering


Direkt autentiseringen nu stöder användarinloggningar till Skype för företag klientprogram som stöder modern autentisering, vilket innefattar online och hybridtopologier. 

Mer information finns i [Skype för företag-topologier som stöds med modern autentisering](https://technet.microsoft.com/library/mt803262.aspx).
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Uppdateringar till Azure AD Privileged Identity Management för Azure RBAC (förhandsgranskning)

**Typ:** ändrade funktionen  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management
 
Med förhandsversion uppdatering av Azure AD Privileged Identity Management (PIM) för rollbaserad åtkomstkontroll (RBAC) kan du nu:

* Använd Just Enough Administration.
* Kräv godkännande för att aktivera resursroller för.
* Schemalägga en framtida aktivering av en roll som kräver godkännande för både Azure AD och Azure RBAC-roller.

 
Mer information finns i [Privileged Identity Management för Azure-resurser (förhandsgranskning)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

 
---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control service pensionering



**Typ:** ändringsplan  
**Tjänstekategori:** Access Control service  
**Produkten kapaciteten:** Access Control service 


 Azure Active Directory-åtkomstkontroll (även kallat Access Control service) ska tas bort i sen 2018. Mer information som innehåller en detaljerad schema och övergripande migrering vägledning ges i nästa några veckor. Du kan lämna kommentarer på den här sidan om du har frågor om tjänsten åtkomstkontroll och en gruppmedlem besvara dem.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Begränsa webbläsaråtkomst till Intune Managed Browser 


**Typ:** ändringsplan  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd




Du kan begränsa webbläsaråtkomst till Office 365 och andra Azure AD-anslutna molnappar med hjälp av Intune Managed Browser som en godkänd app. 

Nu kan du konfigurera följande villkor för program-baserad villkorlig åtkomst:

**-Klientprogram:** webbläsare

**Vad är effekten av ändringen?**

Idag, blockeras åtkomst när du använder det här villkoret. När förhandsgranskningen är tillgänglig, kommer all åtkomst kräver användning av den hanterade webbläsaren. 

Leta efter den här funktionen och mer information finns i kommande bloggar och viktig information. 

Mer information finns i [villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD app-baserad villkorlig åtkomst

 
**Typ:** ändringsplan  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd




Följande appar är tänkta att läggas till i listan över [godkända klientprogram](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Mer information finns i:

- [Godkända klienten app-krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>Användningsvillkor för stöd för flera språk



**Typ:** ny funktion    
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning/kompatibilitet





Administratörer kan nu skapa nya villkor för användning som innehåller flera PDF-dokument. Du kan tagga dessa PDF-dokument med motsvarande språk. PDF-filen visas för användarna med motsvarande språk utifrån deras preferenser. Om det finns ingen matchning, visas standardspråk.


---
 

### <a name="real-time-password-writeback-client-status"></a>Realtid lösenord tillbakaskrivning klientstatus



**Typ:** ny funktion  
**Tjänstekategori:** Självbetjäning för lösenordsåterställning  
**Produkten kapaciteten:** användarautentisering


 

Du kan nu granska statusen för din lokala lösenord tillbakaskrivning. Det här alternativet är tillgängligt i den **lokalt integration** avsnitt i den [lösenordsåterställning](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) sidan. 

Om det finns problem med anslutningen till din lokala tillbakaskrivning klient, visas ett felmeddelande som ger dig med:

- Information om varför du inte kan ansluta till din lokala tillbakaskrivning av klienten.
- En länk till dokumentationen som hjälper dig att lösa problemet. 


Mer information finns i [lokalt integration](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD app-baserad villkorlig åtkomst 



 
**Typ:** ny funktion  
**Tjänstekategori:** Azure AD  
**Produkten kapaciteten:** identitet säkerhet och skydd





Du kan nu begränsa åtkomst till Office 365 och andra Azure AD-anslutna molnappar till [godkända klientappar](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) som stöder Intune app protection-principer med hjälp av [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam). Intune app protection-principer används för att konfigurera och skydda företagets data på dessa program.

Genom att kombinera [app-baserade](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) med [enhetsbaserad](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) principer för villkorlig åtkomst, har du möjlighet att skydda data för personliga och företagets enheter.

Följande villkor och kontroller är nu tillgängliga för användning med app-baserad villkorlig åtkomst:

**Villkor för plattform som stöds**

- iOS
- Android

**Klienten appar villkor**

- Mobila appar och skrivbordsklienter

**Åtkomstkontroll**

- Kräv godkänd klientapp


Mer information finns i [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam).

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Hantera Azure AD-enheter i Azure-portalen



**Typ:** ny funktion  
**Tjänstekategori:** enhetsregistrering och hantering  
**Produkten kapaciteten:** identitet säkerhet och skydd

 



Nu hittar du alla dina enheter som är anslutna till Azure AD och enhetsrelaterade aktiviteter i ett och samma ställe. Det finns en ny administration-miljö för att hantera identiteter för enheten och inställningar i Azure-portalen. I den här versionen kan du:

- Visa alla enheter som är tillgängliga för villkorlig åtkomst i Azure AD.
- Visa egenskaper, bland annat din hybrid Azure AD-anslutna enheter.
- Hitta BitLocker-nycklar för dina Azure AD-anslutna enheter, hantera din enhet med Intune och mycket mer.
- Hantera Azure AD-enhetsrelaterade inställningar.

Mer information finns i [hantera enheter med hjälp av Azure portal](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Stöd för macOS som en enhetsplattform för villkorlig åtkomst i Azure AD 



**Typ:** ny funktion    
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd 
 

Du kan nu inkludera (eller exkludera) macOS som en enhet plattform villkor i din Azure AD-principen för villkorlig åtkomst. Med tillägg av macOS till enhetsplattformar som stöds kan du:

- **Registrera och hantera macOS enheter med hjälp av Intune.** Precis som andra plattformar som iOS och Android, ett företagets portalprogram är tillgängliga för macOS att enhetlig registreringar. Du kan använda nya företagsportal-appen för macOS för att registrera en enhet med Intune och registrera den med Azure AD.
- **Kontrollera macOS enheter följa efterlevnadsprinciper för din organisation som definierats i Intune.** I Intune på Azure-portalen kan kan du nu ställa in policyer för efterlevnad för macOS enheter. 
- **Begränsa åtkomsten till program i Azure AD för att endast kompatibla macOS enheter.** Skapa principer med villkorlig åtkomst har macOS som ett separat enhet plattform. Du kan nu skapa macOS-specifika villkorliga åtkomstprinciper för det aktuella programmet i Azure.

Mer information finns i:

- [Skapa en enhetsefterlevnadsprincip för macOS enheter med Intune](https://aka.ms/macoscompliancepolicy)
- [Villkorlig åtkomst i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Network Policy Servertillägg för Azure Multi-Factor Authentication 


**Typ:** ny funktion    
**Tjänstekategori:** multifaktorautentisering  
**Produkten kapaciteten:** användarautentisering




NPS-tillägget för Azure Multi-Factor Authentication lägger till molnbaserade multifaktorautentisering funktioner till din infrastruktur för autentisering med hjälp av din befintliga servrar. Med filnamnstillägget Network Policy Server du kan lägga till telefonsamtal, textmeddelande eller app telefonverifiering till ditt befintliga autentiseringsflödet. Du behöver inte installera, konfigurera och underhålla nya servrar. 

Det här tillägget har skapats för organisationer som vill skydda virtuella privata nätverksanslutningar utan att distribuera Azure Multi-Factor Authentication-servern. Nätverksprincipservern tillägg fungerar som ett kort mellan RADIUS och molnbaserad Azure Multi-Factor Authentication för att tillhandahålla en andra faktor-autentisering för federerad eller synkroniserade användare.


Mer information finns i [integrera din befintliga infrastruktur för Network Policy Server med Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Återställ eller ta bort borttagna användare


**Typ:** ny funktion    
**Tjänstekategori:** användarhantering  
**Produkten kapaciteten:** Directory 



I administrationscentret för Azure AD kan du nu:

- Återställa en borttagen användare. 
- Ta bort en användare permanent.


**Prova det:**

1. Välj i administrationscentret för Azure AD [alla användare](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) i den **hantera** avsnitt. 

2. Från den **visa** väljer **bort användare nyligen**. 

3. Välj en eller flera nyligen borttagna användare och sedan återställa dem eller ta bort dem permanent.

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nya godkända klientappar för Azure AD app-baserad villkorlig åtkomst

 
**Typ:** ändrade funktionen  
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd


Följande appar har lagts till i listan över [godkända klientprogram](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement):

- Microsoft Planner
- Azure Information Protection 


Mer information finns i:

- [Godkända klienten app-krav](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Använd ”eller” mellan kontroller i en princip för villkorlig åtkomst 


**Typ:** ändrade funktionen    
**Tjänstekategori:** villkorlig åtkomst  
**Produkten kapaciteten:** identitet säkerhet och skydd

 
Nu kan du använda ”eller” (kräver en av de markerade kontrollerna) för villkorlig åtkomstkontroller. Du kan använda den här funktionen när du skapar principer med ”eller” mellan åtkomstkontroller. Du kan till exempel använda den här funktionen för att skapa en princip som kräver att användaren måste logga in med hjälp av Multi-Factor authentication ”eller” på en kompatibel enhet.

Mer information finns i [kontrollerna i Azure AD villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

 
---

### <a name="aggregation-of-real-time-risk-events"></a>Sammanställning av realtidsskyddet riskhändelser


**Typ:** ändrade funktionen    
**Tjänstekategori:** identitetsskydd  
**Produkten kapaciteten:** identitet säkerhet och skydd


I Azure AD Identity Protection samman alla realtid riskhändelser som kommer från samma IP-adress på en viss dag nu för varje typ av händelse risk. Den här ändringen begränsar mängden riskhändelser framgår användarsäkerhet utan ändringar.

Den underliggande realtid identifieringen fungerar varje gång användaren loggar in. Om du har en säkerhetsprincip för inloggning risk konfigurera Multi-Factor authentication eller blockera åtkomst, utlöses den fortfarande under varje riskfyllda inloggning.

 
---
 




## <a name="october-2017"></a>Oktober 2017


### <a name="deprecate-azure-ad-reports"></a>Föråldrad Azure AD-rapporter


**Typ:** ändringsplan  
**Tjänstekategori:** rapportering  
**Produkten kapaciteten:** Identitetshantering livscykel  



Azure-portalen ger dig:

- En ny Azure AD-administrationskonsolen.
- Nya API: er för aktiviteten och säkerhet.
 
På grund av de nya funktionerna som rapporten API: er under/Reports slutpunkten har avslutats på 10 December 2017. 

---

### <a name="automatic-sign-in-field-detection"></a>Fältet för automatisk inloggning identifiering


**Typ:** fast   
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  



Azure AD stöder automatisk inloggning fältet identifiering för program som återger en HTML-Användarfält namn och lösenord. De här stegen finns dokumenterade i [så att automatiskt samla in inloggning fält för ett program](https://docs.microsoft.com/azure/active-directory/application-config-sso-problem-configure-password-sso-non-gallery#how-to-manually-capture-sign-in-fields-for-an-application). Du hittar den här funktionen genom att lägga till en *icke-galleriet* på den **företagsprogram** sidan i den [Azure-portalen](http://aad.portal.azure.com). Du kan också konfigurera den **enkel inloggning** läge för den här nya programmet till **lösenordsbaserade enkel inloggning**, ange en URL och spara sedan sidan.
 
Den här funktionen har tillfälligt inaktiverats på grund av ett problem med tjänsten. Problemet har lösts och fältet för automatisk inloggning identifieringen är tillgänglig igen.

---

### <a name="new-multi-factor-authentication-features"></a>Nya funktioner för multifaktorautentisering


**Typ:** ny funktion  
**Tjänstekategori:** multifaktorautentisering  
**Produkten kapaciteten:** identitet säkerhet och skydd  



Multifaktorautentisering (MFA) är en viktig del av att skydda din organisation. Om du vill göra mer anpassningsbar autentiseringsuppgifter och erfarenhet smidigare följande funktioner har lagts till: 

- Multi-Factor utmaning resultat integreras direkt i Azure AD-inloggning rapporten som innehåller Programmeringsåtkomst till MFA resultat.
- MFA-konfigurationen är mer djupt integrerad med Azure AD-konfiguration upplevelse i Azure-portalen.

Med den här offentliga förhandsversionen är en integrerad del av kärnor Azure AD-konfiguration av MFA hantering och rapportering. Du kan nu hantera MFA management portal-funktionerna i Azure AD-upplevelse.

Mer information finns i [referens för rapportering av MFA i Azure portal](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 


---

### <a name="terms-of-use"></a>Användningsvillkor



**Typ:** ny funktion  
**Tjänstekategori:** villkor för användning  
**Produkten kapaciteten:** styrning  



Du kan använda Azure AD användningsvillkoren presentera information, till exempel relevanta FRISKRIVNINGAR för juridiska eller efterlevnadskrav till användare.

Du kan använda Azure AD-villkor för användning i följande scenarier:

- Allmänna villkor för användning av alla användare i din organisation
- Särskilda villkor för användning baserat på en användares attribut (till exempel läkare kontra sjuksköterskor) eller inrikes kontra internationella anställda, utförs av dynamiska grupper
- Särskilda villkor för användning av åtkomst till Hög inverkan branschspecifika appar, t.ex. Salesforce

Mer information finns i [användningsvillkoren för Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-tou).


---

### <a name="enhancements-to-privileged-identity-management"></a>Förbättringar av Privileged Identity Management


**Typ:** ny funktion  
**Tjänstekategori:** Privileged Identity Management  
**Produkten kapaciteten:** Privileged Identity Management  


Du kan hantera, styr och övervaka åtkomst till Azure-resurser (förhandsgranskning) inom organisationen med Azure AD Privileged Identity Management:

- Prenumerationer
- Resursgrupper
- Virtuella datorer 

Alla resurser i Azure-portalen som använder funktionen Azure RBAC kan dra nytta av säkerhets- och hanteringsfunktioner för livscykel som Azure AD Privileged Identity Management har att erbjuda.

Mer information finns i [Privileged Identity Management för Azure-resurser](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).


---

### <a name="access-reviews"></a>Åtkomstgranskningar


**Typ:** ny funktion  
**Tjänstekategori:** åtkomst till granskningar  
**Produkten kapaciteten:** styrning  



Organisationer kan använda åtkomst granskningar (förhandsversion) för att effektivt hantera gruppmedlemskap och åtkomst till företagsprogram: 

- Du kan certifiera om gästanvändares åtkomst med åtkomstgranskningar av deras åtkomst till program och medlemskap i grupper. Granskare bestämma effektivt om så att gästerna fortsatt åtkomst baserat på de insikter som tillhandahålls av access granskningar.
- Du kan certifiera om medarbetares åtkomst till program och gruppmedlemskap med åtkomstgranskningar.

Du kan samla in åtkomstgranskningskontroller i program som är relevanta för din organisation för att spåra granskningar för efterlevnad eller riskkänsliga program.

Mer information finns i [åtkomst till Azure AD granskar](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Dölj tredjepartsprogram från Mina appar och Office 365 app programstart



**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkten kapaciteten:** enkel inloggning  



Nu hantera appar som visas på användarnas portaler via en ny **dölja app** egenskapen. Du kan dölja appar för att i fall där appen paneler visas för backend-tjänster eller dubbla paneler och oreda användarnas app startprogram. Att växlingsknappen är i den **egenskaper** avsnitt av appen från tredje part och etiketteras **synligt för användaren?** Du kan även dölja en app programmässigt med hjälp av PowerShell. 

Mer information finns i [dölja tredjepartsprogram från användarens upplevelse i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Vad är tillgängliga?**

 Som en del av övergången till en ny Administratörskonsol två nya API: er för att hämta Azure AD-aktivitet loggar är tillgängliga. Ny uppsättning API: er ger bättre filtrera och sortera funktioner förutom att tillhandahålla bättre gransknings- och logga in aktiviteter. Data som tidigare var tillgängliga via säkerhetsrapporter nu kan nås via Identity Protection Risk händelser API i Microsoft Graph.


## <a name="september-2017"></a>September 2017

### <a name="hotfix-for-identity-manager"></a>Snabbkorrigering för Identity Manager


**Typ:** ändrade funktionen  
**Tjänstekategori:** Identity Manager  
**Produkten kapaciteten:** Identitetshantering livscykel  



En sammanslagning snabbkorrigeringen (build 4.4.1642.0) är tillgängliga från och med 25 September 2017 för Identity Manager 2016 Service Pack 1. Det här paketet:

- Löser problem och lägger till förbättringar.
- Är en kumulativ uppdatering som ersätter alla Identity Manager 2016 Service Pack 1 uppdateringar upp till build 4.4.1459.0 för Identity Manager 2016. 
- Du måste ha Identity Manager 2016 skapa 4.4.1302.0. 

Mer information finns i [samlade paketet (build 4.4.1642.0) är tillgänglig för Identity Manager 2016 Service Pack 1](https://support.microsoft.com/help/4021562). 

---
