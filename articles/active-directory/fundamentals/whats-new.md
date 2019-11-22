---
title: Nyheter Viktig information – Azure Active Directory | Microsoft Docs
description: Lär dig vad är nytt med Azure Active Directory, som föråldrade funktioner för de senaste versionsanteckningarna, kända problem, felkorrigeringar och kommande ändringar.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6c6e680de6253f5e822ba282df2e2397093d003
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271003"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Få ett meddelande om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in denna URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` i ![RSS-feed reader-ikonen](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure AD får förbättringar med jämna mellanrum. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i [arkivet för nyheter i Azure Active Directory](whats-new-archive.md).

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Utfasning av identityRiskEvent-API: et för Azure AD Identity Protection risk identifieringar  

**Typ:** ändringsplan  
**Tjänstekategori:** Identity Protection  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Azure AD Premium P2-prenumeranter kan nu utföra komplexa frågor på Azure AD Identity Protections risk identifierings data genom att använda det nya riskDetection-API: et för Microsoft Graph. Den befintliga [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API Beta-versionen slutar att returnera data cirka **10 januari 2020**. Om din organisation använder identityRiskEvent-API: t bör du övergå till det nya riskDetection-API: et.

Mer information om det nya riskDetection-API: et finns i [referens dokumentationen för riskhantering](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för programproxy för attributet SameSite och Chrome 80

**Typ:** ändringsplan  
**Tjänstekategori:** App Proxy  
**Produkt-funktionen:** Access Control

Ett par veckor före versionen av Chrome 80-webbläsaren, planerar vi att uppdatera hur programproxy-cookies behandlar attributet **SameSite** . I och med lanseringen av Chrome 80 behandlas alla cookies som inte anger attributet **SameSite** som om de har angetts till `SameSite=Lax`.

För att undvika potentiellt negativa konsekvenser på grund av den här ändringen uppdaterar vi programproxyns åtkomst och sessionscookies genom att:

- Ställer in standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ställer in standardvärdet för attributet **SameSite** till **none**.

    >[!NOTE]
    > Cookies för åtkomst till programproxyn har alltid skickats exklusivt över säkra kanaler. Dessa ändringar gäller endast för sessionscookies.

Mer information om cookies-inställningarna för programproxyn finns i [cookie-inställningar för att komma åt lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Appregistreringar (bakåtkompatibelt) och konvergerad program hantering från program registrerings portalen (apps.dev.microsoft.com) kommer inte längre vara tillgänglig

**Typ:** ändringsplan  
**Tjänstekategori:** saknas  
**Produkt-funktionen:** Utvecklarupplevelse

I nära framtid kommer användare med Azure AD-konton inte längre att kunna registrera och hantera konvergerade program med hjälp av program registrerings portalen (apps.dev.microsoft.com) eller registrera och hantera program i Appregistreringar (bakåtkompatibelt) upplevelse i Azure Portal.

Mer information om den nya Appregistreringar upplevelsen finns i [Appregistreringar i Azure Portal tränings guide](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Användare behöver inte längre registrera sig på nytt vid migrering från användarspecifika MFA till villkorlig åtkomst-baserad MFA

**Typ:** fast  
**Tjänste kategori:** MFA  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Vi har åtgärdat ett känt problem, vilket innebär att när användare var tvungen att omregistrera om de har inaktiverats för Multi-Factor Authentication per användare (MFA) och sedan aktive ras för MFA via en princip för villkorlig åtkomst.

Om du vill kräva att användarna registrerar om kan du välja alternativet **Omregistrera MFA** från användarens autentiseringsmetoder i Azure AD-portalen. Mer information om hur du migrerar användare från per användare MFA till villkorlig åtkomst-baserad MFA finns i [konvertera användare från per användare MFA till villkorlig åtkomst baserat MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nya funktioner för att transformera och skicka anspråk i SAML-token

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** SSO

Vi har lagt till ytterligare funktioner som hjälper dig att anpassa och skicka anspråk i SAML-token. De här nya funktionerna är:

- Ytterligare omvandlings funktioner för anspråk, vilket hjälper dig att ändra värdet som du skickar i anspråket.

- Möjlighet att tillämpa flera transformationer på ett enda anspråk.

- Möjlighet att ange anspråks källan, baserat på användar typen och gruppen som användaren tillhör.

Detaljerad information om de här nya funktionerna, inklusive hur du använder dem, finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Sidan nya inloggnings tillägg för slutanvändare i Azure AD

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** övervakning och rapportering

Vi har lagt till en ny **inloggnings** sida (https://mysignins.microsoft.com) för att låta din organisations användare visa sin senaste inloggnings historik för att kontrol lera om det finns någon ovanlig aktivitet. På den nya sidan kan användarna se:

- Om någon försöker gissa sitt lösen ord.

- Om en angripare har loggat in på kontot och från vilken plats.

- Vilka appar angriparen försökte få åtkomst till.

Mer information finns i avsnittet [användare kan nu kontrol lera sin inloggnings historik för ovanlig aktivitets](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) blogg.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrering av Azure AD Domain Services (Azure AD DS) från klassisk till Azure Resource Manager virtuella nätverk

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD Domain Services  
**Produkt-funktionen:** Azure AD Domain Services

Till våra kunder som har fastnat i klassiska virtuella nätverk – vi har bra nyheter! Du kan nu utföra en eng ång slö flytt från ett klassiskt virtuellt nätverk till ett befintligt virtuellt Resource Manager-nätverk. När du har flyttat till det virtuella Resource Manager-nätverket kan du dra nytta av de ytterligare och uppgraderade funktionerna, till exempel detaljerade lösen ords principer, e-postaviseringar och gransknings loggar.

Mer information finns i [förhands granskning – migrera Azure AD Domain Services från den klassiska virtuella nätverks modellen till Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Uppdateringar av layouten Azure AD B2C sid kontrakt

**Typ:** ny funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkt-funktionen:** B2B/B2C

Vi har introducerat några nya ändringar i version 1.2.0 av sid kontraktet för Azure AD B2C. I den här uppdaterade versionen kan du nu styra inläsnings ordningen för dina element, vilket också kan hjälpa till att stoppa flimmer som inträffar när format mal len (CSS) läses in.

En fullständig lista över de ändringar som gjorts i sidan kontrakt finns i [versions ändrings loggen](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdatera till sidan Mina appar tillsammans med nya arbets ytor (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** Mina appar  
**Produkt-funktionen:** Access Control

Nu kan du anpassa hur din organisations användare visar och kommer åt den anpassade appen Mina appar, inklusive att använda funktionen nya arbets ytor för att göra det lättare för dem att hitta appar. Funktionen nya arbets ytor fungerar som ett filter för de appar som organisationens användare redan har åtkomst till.

Mer information om hur du utvärderar nya funktioner för Mina appar och skapar arbets ytor finns i [skapa arbets ytor på portalen Mina appar (för hands version)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Stöd för den månatliga aktiva användarbaserade fakturerings modellen (allmän tillgänglighet)

**Typ:** ny funktion  
**Tjänstekategori:** B2C – Konsumentidentitetshantering  
**Produkt-funktionen:** B2B/B2C

Azure AD B2C stöder nu MAU-fakturering (Monthly Active Users). MAU-fakturering baseras på antalet unika användare med autentiserings aktivitet under en kalender månad. Befintliga kunder kan när som helst byta till den nya fakturerings metoden.

Från den 1 november 2019 debiteras alla nya kunder automatiskt med den här metoden. Den här fakturerings metoden fördelar kunder genom kostnads förmåner och möjligheten att planera framåt.

Mer information finns i [Uppgradera till månatliga aktiva användares fakturerings modell](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Meny alternativet konsol IDE rad säkerhet i Azure AD-portalen

**Typ:** ändrade funktionen  
**Tjänstekategori:** Identity Protection  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Nu kan du komma åt alla tillgängliga Azure AD-säkerhetsfunktioner från det nya **säkerhets** meny alternativet och från **sök** fältet i Azure Portal. Dessutom innehåller den nya **säkerhets** landnings sidan, som kallas **säkerhets komma igång**, länkar till vår offentliga dokumentation, säkerhets vägledning och distributions guider.

Den nya **säkerhets** menyn innehåller:

- Villkorlig åtkomst
- Identity Protection
- Security Center
- Identifiera säkra Poäng för identitet
- Autentiseringsmetoder
- MFA
- Risk rapporter – riskfyllda användare, riskfyllda inloggningar, risk identifiering
- Med mera...

Mer information finns i säkerhet för att [komma igång](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Utgångs princip för Office 365-grupper har förbättrats med autoförnyelse

**Typ:** ändrade funktionen  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** Identitetslivcykelhantering

Utgångs principen för Office 365-grupper har förbättrats för att automatiskt förnya grupper som används aktivt av dess medlemmar. Grupper förnyas automatiskt baserat på användar aktivitet i alla Office 365-appar, inklusive Outlook, SharePoint och Teams.

Den här förbättringen hjälper till att minska antalet aviseringar för din grupp och hjälper till att se till att aktiva grupper fortfarande är tillgängliga. Om du redan har en aktiv utgångs princip för dina Office 365-grupper behöver du inte göra något för att aktivera den här nya funktionen.

Mer information finns i [Konfigurera förfallo principen för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Uppdaterad Azure AD Domain Services (Azure AD DS)-skapande

**Typ:** ändrade funktionen  
**Tjänstekategori:** Azure AD Domain Services  
**Produkt-funktionen:** Azure AD Domain Services

Vi har uppdaterat Azure AD Domain Services (Azure AD DS) för att ta med en ny och förbättrad skapande upplevelse, som hjälper dig att skapa en hanterad domän i tre klick! Dessutom kan du nu ladda upp och Distribuera Azure AD DS från en mall.

Mer information finns i [Självstudier: skapa och konfigurera en Azure Active Directory Domain Services instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plan för ändring: utfasning av Power BI innehålls paket

**Typ:** ändringsplan  
**Tjänstekategori:** rapportering  
**Produkt-funktionen:** övervakning och rapportering

Från och med den 1 oktober 2019 börjar Power BI att ta bort alla innehålls paket, inklusive Azure AD Power BI-innehålls paketet. Som ett alternativ till detta innehålls paket kan du använda Azure AD-arbetsböcker för att få insikter om dina Azure AD-relaterade tjänster. Ytterligare arbets böcker kommer, inklusive arbets böcker om principer för villkorlig åtkomst i endast rapport läge, app-baserade insikter med mera.

Mer information om arbets böckerna finns i [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Mer information om utfasningen av innehålls paket finns i blogg inlägget om att [presentera Power BI Template Apps allmänt tillgänglighet](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Min profil byter namn och integrerar med sidan Microsoft Office konto

**Typ:** ändringsplan  
**Tjänste kategori:** Min profil/konto  
**Produkt-funktionen:** samarbete

Från och med oktober blir min profil upplevelse mitt konto. Som en del av ändringen kommer **min profil** att ändras till **mitt konto**för närvarande. Den uppdaterade upplevelsen av namn ändringen och vissa design förbättringar erbjuder ytterligare integrering med Microsoft Office konto sidan. Mer specifikt kommer du att kunna komma åt Office-installationer och-prenumerationer från sidan **översikts konto** , tillsammans med Office-relaterade kontakt inställningar på sidan **Sekretess** .

Mer information om min profil (för hands version) finns i [Översikt över min profil (för hands version)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Mass hantering av grupper och medlemmar med CSV-filer i Azure AD-portalen (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** samarbete

Vi är glada över att kunna meddela den offentliga för hands versionen av hanterings upplevelser för flera grupper i Azure AD-portalen. Nu kan du använda en CSV-fil och Azure AD-portalen för att hantera grupper och medlems listor, inklusive:

- Lägga till eller ta bort medlemmar från en grupp.

- Hämtar listan över grupper från katalogen.

- Hämtar listan över grupp medlemmar för en speciell grupp.

Mer information finns i avsnittet [Lägg till medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Mass borttagnings medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), lista över grupp [medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)och [grupper med hämtnings grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)för grupp.

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamiskt medgivande stöds nu via en ny administratörs tillstånds slut punkt

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** användarautentisering

Vi har skapat en ny administratörs medgivande slut punkt som stöder dynamiskt medgivande, vilket är användbart för appar som vill använda den dynamiska godkännande modellen på Microsoft Identity Platform.

Mer information om hur du använder den nya slut punkten finns i [using the admin medgivande Endpoint](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-azure-ad-global-reader-role"></a>Ny Azure AD global läsar roll

**Typ:** ny funktion  
**Tjänste kategori:** RBAC  
**Produkt-funktionen:** Access Control

Från den 24 september 2019 kommer vi att börja lansera en ny Azure Active Directory (AD) roll som kallas global läsare. Den här distributionen startar med produktions-och globala Cloud-kunder (GCC), som slutförs i hela världen i oktober.

Global läsar roll är den skrivskyddade motsvarigheten till global administratör. Användare med den här rollen kan läsa inställningar och administrativ information mellan Microsoft 365 tjänster, men kan inte vidta hanterings åtgärder. Vi har skapat rollen global läsare som hjälper till att minska antalet globala administratörer i din organisation. Eftersom globala administratörs konton är kraftfulla och utsatta för angrepp rekommenderar vi att du har färre än fem globala administratörer. Vi rekommenderar att du använder rollen global läsare för planering, granskningar och undersökningar. Vi rekommenderar också att du använder rollen global läsare i kombination med andra begränsade administratörs roller, t. ex. Exchange-administratör, för att få jobbet gjort utan att den globala administratörs rollen krävs.

Rollen global läsare fungerar med de nya Microsoft 365 administrations centret, administrations Center för team, team administrations Center, Security Center, Compliance Center, Azure AD administrations Center och administrations Center för enhets hantering.

>[!NOTE]
> I början av den offentliga för hands versionen fungerar inte den globala läsar rollen med: SharePoint, Privileged Access Management, Customer Lockbox, känslighets etiketter, team-livscykel, team rapportering & samtals analys, team hantering av IP-telefonen och team-appen Katalogen. Alla dessa tjänster är avsedda att fungera med rollen i framtiden.

Mer information finns i [Administratörs roll behörigheter i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Få åtkomst till en lokal rapport Server från din Power BI Mobile-app med hjälp av Azure Active Directory-programproxy

**Typ:** ny funktion  
**Tjänstekategori:** App Proxy  
**Produkt-funktionen:** Access Control

Med den nya integreringen mellan Power BI mobilapp och Azure AD-programproxy kan du på ett säkert sätt logga in på den Power BI mobilappen och visa vilken som helst av organisationens rapporter som finns på den lokala Power BI-rapportserver.

Information om Power BI Mobile-appen, inklusive var du hämtar appen, finns på [Power BI webbplats](https://powerbi.microsoft.com/mobile/). Mer information om hur du konfigurerar Power BI mobilapp med Azure AD-programproxy finns i [aktivera fjärråtkomst till Power BI Mobile med azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Det finns en ny version av AzureADPreview PowerShell-modulen

**Typ:** ändrade funktionen  
**Tjänstekategori:** andra  
**Produkt-funktionen:** Directory

Nya cmdletar har lagts till i AzureADPreview-modulen för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Ny version av Azure AD Connect

**Typ:** ändrade funktionen  
**Tjänstekategori:** andra  
**Produkt-funktionen:** Directory

Vi har släppt en uppdaterad version av Azure AD Connect för kunder med automatisk uppgradering. Den här nya versionen innehåller flera nya funktioner, förbättringar och fel korrigeringar. Mer information om den här nya versionen finns [Azure AD Connect: versions historik](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication-Server (MFA), version 8.0.2 är nu tillgänglig

**Typ:** fast  
**Tjänste kategori:** MFA  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Om du är en befintlig kund, som aktiverade MFA Server tidigare än den 1 juli 2019, kan du nu ladda ned den senaste versionen av MFA Server (version 8.0.2). I den här nya versionen:

- Ett problem har åtgärd ATS så när Azure AD Sync ändrar en användare från inaktive rad till aktive rad skickas ett e-postmeddelande till användaren.

- Ett problem har åtgärd ATS så att kunderna kan uppgraderas och fortsätta att använda funktionen taggar.

- Las till Kosovo (+ 383) landskod.

- En eng ång slö MultiFactorAuthSvc-loggning har lagts till i filen. log.

- Bättre prestanda för webbtjänst-SDK.

- Andra mindre buggar.

Från och med den 1 juli 2019 erbjuder Microsoft även MFA Server för nya distributioner. Nya kunder som behöver Multi-Factor Authentication bör använda molnbaserade Azure-Multi-Factor Authentication. Mer information finns i [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>Augusti 2019

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Förbättrad sökning, filtrering och sortering för grupper är tillgängligt i Azure AD-portalen (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** samarbete

Vi är glada över att kunna tillkännage den offentliga för hands versionen av de förbättrade grupper som är relaterade till Azure AD-portalen. Dessa förbättringar hjälper dig att hantera grupper och medlems listor bättre genom att tillhandahålla:

- Avancerade Sök funktioner, till exempel under Strängs sökning i grupp listor.
- Avancerade alternativ för filtrering och sortering i listor över medlemmar och ägare.
- Nya Sök funktioner för medlems-och ägar listor.
- Mer exakta grupp antal för stora grupper.

Mer information finns i [hantera grupper i Azure Portal](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Nya anpassade roller är tillgängliga för registrerings hantering av appar (offentlig för hands version)

**Typ:** ny funktion  
**Tjänste kategori:** RBAC  
**Produkt-funktionen:** Access Control

Anpassade roller (tillgängliga med en Azure AD P1-eller P2-prenumeration) kan nu hjälpa dig att ge dig detaljerad åtkomst, genom att låta dig skapa roll definitioner med särskilda behörigheter och sedan tilldela rollerna till särskilda resurser. För närvarande kan du skapa anpassade roller genom att använda behörigheter för att hantera app-registreringar och sedan tilldela rollen till en speciell app. Mer information om anpassade roller finns i [anpassade administratörs roller i Azure Active Directory (för hands version)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Om du behöver ytterligare behörigheter eller resurser som stöds, som du för närvarande inte ser, kan du skicka feedback till vår [Azure feedback-webbplats](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) så lägger vi till din begäran till vår uppdatering av väg kartan.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Nya etablerings loggar kan hjälpa dig att övervaka och felsöka din app etablerings distribution (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** Appetablering  
**Produkt-funktionen:** Identitetslivcykelhantering

Nya etablerings loggar är tillgängliga som hjälper dig att övervaka och felsöka distribution av användar-och grupp etablering. Dessa nya loggfiler innehåller information om:

- Vilka grupper har skapats i [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)
- Vilka roller importerades från [Amazon Web Services (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Vilka medarbetare som inte har importer ATS från [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

Mer information finns i [etablerings rapporter i Azure Active Directory portal (för hands version)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Nya säkerhets rapporter för alla Azure AD-administratörer (allmän tillgänglighet)

**Typ:** ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Som standard kommer alla Azure AD-administratörer snart att kunna komma åt moderna säkerhets rapporter i Azure AD. Fram till slutet av september kommer du att kunna använda banderollen högst upp i moderna säkerhets rapporter för att återgå till gamla rapporter.

Moderna säkerhets rapporter ger ytterligare funktioner från de äldre versionerna, inklusive:

- Avancerad filtrering och sortering
- Mass åtgärder, t. ex. problem med att ignorera användar risk
- Bekräftelse av komprometterade eller säkrade entiteter
- Risk tillstånd, täcker: vid risk, avstängd, åtgärdad och bekräftat komprometterad
- Nya riskfyllda identifieringar (tillgängliga för Azure AD Premium prenumeranter)

Mer information finns i [riskfyllda användare](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)och [risk identifieringar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>Användare som tilldelats hanterad identitet är tillgänglig för Virtual Machines och Virtual Machine Scale Sets (allmän tillgänglighet)

**Typ:** ny funktion  
**Tjänste kategori:** Hanterade identiteter för Azure-resurser  
**Produkt-funktionen:** Utvecklarupplevelse

Användare som tilldelats hanterade identiteter är nu allmänt tillgängliga för Virtual Machines och Virtual Machine Scale Sets. Som en del av detta kan Azure Skapa en identitet i Azure AD-klienten som är betrodd av den prenumeration som används och kan tilldelas till en eller flera Azure Service-instanser. Mer information om användarspecifika hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](https://aka.ms/azuremanagedidentity).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>Användare kan återställa sina lösen ord med hjälp av en mobilapp eller maskinvaru-token (allmän tillgänglighet)

**Typ:** ändrade funktionen  
**Tjänstekategori:** självbetjäningsportalen för återställning av lösenord  
**Produkt-funktionen:** användarautentisering

Användare som har registrerat en mobilapp med din organisation kan nu återställa sina egna lösen ord genom att godkänna ett meddelande från appen Microsoft Authenticator eller genom att ange en kod från deras mobilapp eller maskinvaru-token.

Mer information finns i [så här fungerar det: lösen ords återställning](https://aka.ms/authappsspr)via självbetjäning i Azure AD. Mer information om användar upplevelsen finns i [återställa ditt eget arbets-eller skol lösen ord översikt](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignorerar det delade cacheminnet MSAL.NET för on-of-scenarier

**Typ:** fast  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** användarautentisering

Från och med Azure AD Authentication Library (ADAL.NET) version 5.0.0 – för hands version måste Apps-utvecklare [serialisera en cache per konto för webbappar och webb-API: er](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). I annat fall kan vissa scenarier som använder [sig av Flow](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), tillsammans med vissa speciella användnings fall av `UserAssertion`, resultera i en behörighets höjning. För att undvika den här säkerhets risken ignorerar ADAL.NET nu det delade cacheminnet för Microsoft Authentication Library för dotNET (MSAL.NET) för scenarier med olika förutsättningar.

Mer information om det här problemet finns i [Azure Active Directory behörighets bibliotekets utökning av behörighets sårbarhet](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – augusti 2019

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** 3 part-integrering

I augusti 2019 har vi lagt till dessa 26 nya appar med stöd för federation i app-galleriet:

[Civic Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareports Inativ-portal (Europa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Tax](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy-närvaro](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritets mat ris](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync av Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [leverera. media™ portal](https://portal.deliver.media), [Frontline utbildning](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD Anslut](https://www.stashcat.com), [blinkar](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [titta efter färger](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [nät](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [EAB navigera i strategiska vård](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Det finns nya versioner av AzureAD PowerShell-och AzureADPreview PowerShell-modulerna

**Typ:** ändrade funktionen  
**Tjänstekategori:** andra  
**Produkt-funktionen:** Directory

Nya uppdateringar av AzureAD-och AzureAD Preview PowerShell-modulerna är tillgängliga:

- En ny `-Filter`-parameter har lagts till i `Get-AzureADDirectoryRole`-parametern i AzureAD-modulen. Med den här parametern kan du filtrera efter katalog roller som returneras av cmdleten.
- Nya cmdletar har lagts till i AzureADPreview-modulen för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Förbättringar av användar gränssnittet för dynamisk grupp regel verktyg i Azure Portal

**Typ:** ändrade funktionen  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** samarbete

Vi har gjort några förbättringar av användar gränssnittet för dynamisk grupp regel verktyget, som är tillgängliga i Azure Portal, för att hjälpa dig att enkelt konfigurera en ny regel eller ändra befintliga regler. Med den här design förbättringen kan du skapa regler med upp till fem uttryck i stället för bara en. Vi har också uppdaterat enhets egenskaps listan för att ta bort föråldrade enhets egenskaper.

Mer information finns i [Hantera dynamiska medlemskaps regler](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Ny Microsoft Graph app-behörighet som är tillgänglig för användning med åtkomst granskningar

**Typ:** ändrade funktionen  
**Tjänstekategori:** Åtkomstgranskningar  
**Produkt kapacitet:** Identitets styrning

Vi har introducerat en ny Microsoft Graph app-behörighet `AccessReview.ReadWrite.Membership`, som gör det möjligt för appar att automatiskt skapa och hämta åtkomst granskningar för grupp medlemskap och app-tilldelningar. Den här behörigheten kan användas av schemalagda jobb eller som en del av din automatisering, utan att det krävs någon inloggad användar kontext.

Mer information finns i exempel på [hur du skapar åtkomst granskningar för Azure AD med hjälp av Microsoft Graph app-behörigheter med PowerShell-bloggen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD-aktivitets loggar är nu tillgängliga för myndighets moln instanser i Azure Monitor

**Typ:** ändrade funktionen  
**Tjänstekategori:** rapportering  
**Produkt-funktionen:** övervakning och rapportering

Vi är glada över att kunna meddela att Azure AD-aktivitets loggar nu är tillgängliga för myndighets moln instanser i Azure Monitor. Nu kan du skicka Azure AD-loggar till ditt lagrings konto eller till en Event Hub för att integrera med dina SIEM-verktyg, t. ex. [SumoLogic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)och [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight). 

Mer information om hur du konfigurerar Azure Monitor finns [i Azure AD-aktivitets loggar i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Uppdatera dina användare till den nya, förbättrade säkerhets informations upplevelsen

**Typ:** ändrade funktionen  
**Tjänste kategori:**  Autentiseringar (inloggningar)   
**Produkt-funktionen:** användarautentisering

Den 25 september 2019 kommer vi att stänga av den gamla, icke-förbättrade säkerhets informationen för att registrera och hantera användar säkerhets information och bara aktivera den nya, [förbättrade versionen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271). Det innebär att användarna inte längre kommer att kunna använda den gamla upplevelsen.

Mer information om förbättrad säkerhets information finns i vår [Administratörs dokumentation](https://aka.ms/securityinfodocs) och i vår [användar dokumentation](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Om du vill aktivera den här nya upplevelsen måste du:

1. Logga in på Azure Portal som global administratör eller användar administratör.

2. Gå till **Azure Active Directory > användar inställningar > hantera inställningar för för hands versions funktionerna i åtkomst panelen**.

3. I **användarna kan använda för hands versions funktioner för att registrera och hantera säkerhets information – förbättrat** utrymme, Välj **vald**och sedan välja en grupp med användare eller Välj **alla** för att aktivera funktionen för alla användare i klienten.

4. I-**användarna kan använda för hands versions funktioner för registrering och hantering av** **säkerhet**.

5. Spara inställningarna.

    När du har sparat inställningarna kommer du inte längre att ha åtkomst till den gamla säkerhets informationen.

>[!Important]
>Om du inte slutför de här stegen före den 25 september 2019 aktive ras din Azure Active Directory klient automatiskt för den förbättrade upplevelsen. Om du har frågor kan du kontakta oss på registrationpreview@microsoft.com.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>Autentiseringsbegäranden med POST-inloggningar blir mer strikt verifierad

**Typ:** ändrade funktionen  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt kapacitet:** Standardisering

Från och med den 2 september 2019 kommer autentiseringsbegäranden som använder POST-metoden att verifieras striktare mot HTTP-standarder. Mer specifikt kommer blank steg och dubbla citat tecken (") inte längre att tas bort från begär ande formulär värden. De här ändringarna förväntas inte konvertera några befintliga klienter och hjälper till att kontrol lera att förfrågningar som skickas till Azure AD hanteras på ett tillförlitligt sätt varje gång.

Mer information finns i meddelanden om [ändringar i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Juli 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planera för ändring: programproxy-tjänstens uppdatering som endast stöder TLS 1,2

**Typ:** ändringsplan  
**Tjänstekategori:** App Proxy  
**Produkt-funktionen:** Access Control

För att hjälpa dig med vår starkaste kryptering kommer vi att börja begränsa åtkomsten till Application Proxy-tjänsten till endast TLS 1,2-protokoll. Den här begränsningen kommer inlednings vis att distribueras till kunder som redan använder TLS 1,2-protokoll, så att du inte ser effekten. Fullständig utfasning av TLS 1,0 och TLS 1,1-protokollen kommer att slutföras den 31 augusti 2019. Kunder som fortfarande använder TLS 1,0 och TLS 1,1 får ett avancerat meddelande om att förbereda inför den här ändringen.

För att upprätthålla anslutningen till Application Proxy-tjänsten i hela den här ändringen rekommenderar vi att du kontrollerar att kombinationerna av klient-och webb läsar servern har uppdaterats för att använda TLS 1,2. Vi rekommenderar också att du tar med alla klient system som används av dina anställda för att få åtkomst till appar som publicerats via tjänsten Application Proxy.

Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Plan för ändring: design uppdateringar kommer för program galleriet

**Typ:** ändringsplan  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** SSO

Nya användar gränssnitts ändringar kommer till design av bladet **Lägg till från galleriet** i bladet **Lägg till ett program** . Med de här ändringarna kan du enkelt hitta dina appar som stöder automatisk etablering, OpenID Connect, Security Assertion Markup Language (SAML) och inloggning med lösen ord (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Plan för ändring: borttagning av MFA-serverns IP-adress från Office 365-IP-adressen

**Typ:** ändringsplan  
**Tjänste kategori:** MFA  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Vi tar bort MFA-serverns IP-adress från [Office 365 IP-adress och URL-webbtjänst](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Om du för närvarande använder dessa sidor för att uppdatera brand Väggs inställningarna måste du se till att du även inkluderar listan över IP-adresser som beskrivs i avsnittet **krav för Azure Multi-Factor Authentication-Server brand vägg** i [komma igång med Azure Multi-Factor Authentication-server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) -artikeln.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Endast app-token kräver att klient programmet finns i resurs klienten

**Typ:** fast  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** användarautentisering

Den 26 juli 2019 ändrade vi hur vi tillhandahåller app-only-token via [tilldelningen av klientens autentiseringsuppgifter](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Tidigare kunde appar Hämta token för att anropa andra appar, oavsett om klient programmet fanns i klienten. Vi har uppdaterat det här beteendet så att resurser med en enda klient, ibland kallade webb-API: er, bara kan anropas av klient program som finns i resurs klienten.

Om din app inte finns i resurs klienten får du ett fel meddelande om att `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` åtgärda problemet genom att för att åtgärda det här problemet, måste du skapa klient program tjänstens huvud namn i klienten, antingen med hjälp av [Administratörs medgivande slut punkten](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) eller [via PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell) vilket säkerställer att klienten har gett appen behörighet att köras i klienten.

Mer information finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Det befintliga godkännandet mellan klienten och API: et fortsätter inte att krävas. Apparna bör fortfarande utföra sina egna verifierings kontroller.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nytt lösen ords lös inloggning till Azure AD med hjälp av FIDO2-säkerhetsnycklar

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** användarautentisering

Azure AD-kunder kan nu ange principer för att hantera FIDO2 säkerhets nycklar för deras organisations användare och grupper. Slutanvändare kan även registrera sina säkerhets nycklar med hjälp av nycklarna för att logga in på sina Microsoft-konton på webbplatser på FIDO enheter, samt logga in på sina Azure AD-anslutna Windows 10-enheter.

Mer information finns i [Aktivera lösen ords lös inloggning för Azure AD (för hands version)](/azure/active-directory/authentication/concept-authentication-passwordless) för administratörs information och [Konfigurera säkerhets information för att använda en säkerhets nyckel (för hands version)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) för information om slutanvändare.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – juli 2019

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** 3 part-integrering

I juli 2019 har vi lagt till dessa 18 nya appar med stöd för federation i app-galleriet:

[Ungerboeck program vara](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [starkt mönster Omnichannel kontakt Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [smarta Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [LOOOP](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO-åtkomst för Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [djupet](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstrakt](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [procent](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [FlipSnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial),, [PharmID](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial) [WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog-artefakt](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** övervakning och rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federerad katalog](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Ny Azure AD Domain Services service tag för nätverks säkerhets grupp

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD Domain Services  
**Produkt-funktionen:** Azure AD Domain Services

Om du är trött på att hantera långa listor över IP-adresser och intervall kan du använda den nya **AzureActiveDirectoryDomainServices** Network Service tag i din Azure-nätverks säkerhets grupp för att skydda inkommande trafik till din Azure AD Domain Services virtuella nätverks under nät.

Mer information om den här nya service tag-koden finns i [nätverks säkerhets grupper för Azure AD Domain Services](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD Domain Services  
**Produkt-funktionen:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nya autentiseringsmetoder användning & insikter (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** självbetjäningsportalen för återställning av lösenord  
**Produkt-funktionen:** övervakning och rapportering

Nya autentiseringsmetoder som används & Insights-rapporter kan hjälpa dig att förstå hur funktioner som Azure Multi-Factor Authentication och återställning av lösen ord för självbetjäning registreras och används i din organisation, inklusive antalet registrerade användare för varje funktion, hur ofta lösen ords återställning via självbetjäning används för att återställa lösen ord och med vilken metod som återställningen sker.

Mer information finns i [användning av autentiseringsmetoder & insikter (för hands version)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Nya säkerhets rapporter är tillgängliga för alla Azure AD-administratörer (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Alla Azure AD-administratörer kan nu välja banderollen överst i befintliga säkerhets rapporter, till exempel användare som har **flaggats för risk** rapport, för att börja använda den nya säkerhets upplevelsen som visas i rapporterna **riskfyllda användare** och **riskfyllda inloggningar** . Med tiden kommer alla säkerhets rapporter att flyttas från äldre versioner till de nya versionerna, med de nya rapporterna som ger dig följande ytterligare funktioner:

- Avancerad filtrering och sortering

- Mass åtgärder, t. ex. problem med att ignorera användar risk

- Bekräftelse av komprometterade eller säkrade entiteter

- Risk tillstånd, täcker: vid risk, avstängd, åtgärdad och bekräftat komprometterad

Mer information finns i rapporten om [riskfyllda användare](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) och [riskfyllda inloggningar](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nya säkerhets granskningar för Azure AD Domain Services (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** Azure AD Domain Services  
**Produkt-funktionen:** Azure AD Domain Services

Vi är glada över att kunna meddela att Azure AD Domain Service-säkerhetsgranskningen är offentlig för hands version. Med säkerhets granskning får du viktiga insikter om dina Authentication Services genom att strömma säkerhets gransknings händelser till riktade resurser, inklusive Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub med Azure AD Domain Service Portal.

Mer information finns i [Aktivera säkerhets granskningar för Azure AD Domain Services (för hands version)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Ny B2B direkt Federation med SAML/WS-utfodras (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** B2B  
**Produkt-funktionen:** B2B/B2C

Direkt federationen hjälper till att göra det enklare för dig att arbeta med partner vars IT-hanterade identitets lösning inte är Azure AD, genom att arbeta med identitets system som stöder SAML-eller WS-utfodras-standarder. När du har konfigurerat en direkt Federations relation med en partner, kan alla nya gäst användare som du bjuder in från domänen samar beta med dig som använder sitt befintliga organisations konto, vilket gör användar upplevelsen mer sömlös.

Mer information finns i [direkt Federation med AD FS och tredje parts leverantörer för gäst användare (för hands version)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** övervakning och rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federerad katalog](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ny kontroll för dubbletter av grupp namn i Azure AD-portalen

**Typ:** ny funktion  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** samarbete

Nu när du skapar eller uppdaterar ett grupp namn från Azure AD-portalen, kontrollerar vi om du duplicerar ett befintligt grupp namn i din resurs. Om vi bestämmer att namnet redan används av en annan grupp, blir du ombedd att ändra ditt namn.

Mer information finns i [hantera grupper i Azure AD-portalen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD stöder nu statiska frågeparametrar i svar (omdirigerings-URI: er)

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** användarautentisering

Azure AD-appar kan nu registrera och använda URI: er för svar (omdirigering) med statiska frågeparametrar (till exempel `https://contoso.com/oauth2?idp=microsoft`) för OAuth 2,0-begäranden. Den statiska Frågeparametern omfattas av sträng matchning för svars-URI: er, precis som andra delar av svars-URI: n. Om det inte finns någon registrerad sträng som matchar URL-kodad omdirigerings-URI, avvisas begäran. Om svars-URI: n hittas används hela strängen för att omdirigera användaren, inklusive parametern för den statiska frågan.

Dynamiska svars-URI: er är fortfarande förbjudna eftersom de utgör en säkerhets risk och inte kan användas för att bevara tillståndsinformation i en autentiseringsbegäran. För det här ändamålet använder du parametern `state`.

För närvarande blockerar appens registrerings skärmar för Azure Portal fortfarande frågeparametrar. Du kan dock redigera appens manifest manuellt för att lägga till och testa frågeparametrar i din app. Mer information finns i [Vad är nytt för autentisering?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Aktivitets loggar (MS Graph API: er) för Azure AD är nu tillgängliga via PowerShell-cmdletar

**Typ:** ny funktion  
**Tjänstekategori:** rapportering  
**Produkt-funktionen:** övervakning och rapportering

Vi är glada över att kunna meddela att Azure AD aktivitets loggar (gransknings-och inloggnings rapporter) nu är tillgängliga via Azure AD PowerShell-modulen. Tidigare kunde du skapa egna skript med MS Graph API-slutpunkter och nu har vi utökat den möjligheten till PowerShell-cmdletar.

Mer information om hur du använder dessa cmdlets finns i [Azure AD PowerShell-cmdlets för rapportering](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Uppdaterade filter kontroller för gransknings-och inloggnings loggar i Azure AD

**Typ:** ändrade funktionen  
**Tjänstekategori:** rapportering  
**Produkt-funktionen:** övervakning och rapportering

Vi har uppdaterat rapporterna för gransknings-och inloggnings loggar så att du nu kan använda olika filter utan att behöva lägga till dem som kolumner i rapport skärmarna. Dessutom kan du bestämma hur många filter som ska visas på skärmen. De här uppdateringarna fungerar tillsammans för att göra dina rapporter lättare att läsa och mer begränsade till dina behov.

Mer information om de här uppdateringarna finns i [filtrera gransknings loggar](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) och [filtrera inloggnings aktiviteter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Juni 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>New riskDetections-API för Microsoft Graph (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Vi är glada över att kunna presentera den nya riskDetections-API: n för Microsoft Graph nu i offentlig för hands version. Du kan använda den här nya API: n för att visa en lista över organisationens identitet skydd – relaterad användare och inloggnings risk identifieringar. Du kan också använda det här API: et för att effektivt fråga dina risk identifieringar, inklusive information om identifierings typ, status, nivå och mycket annat.

Mer information finns i [referens dokumentationen för riskhanterings-API](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – juni 2019

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** 3 part-integrering

I juni 2019 har vi lagt till dessa 22 nya appar med stöd för federation i app-galleriet:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN-klient](https://portal.azure.com/), [kostnadin](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Hjälp](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)verktyg för hjälp, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz i bilen Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure-konsolen](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-autentisering](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager för Oracle Inköps handel, Oracle Access Manager för Oracle E-Business Suite, Oracle-IDCS för E-Business Suite, Oracle-IDCS för, Oracle IDCS för JD Edwards

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** övervakning och rapportering

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Förhindra](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Mottagare](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visa real tids förloppet för Azure AD Provisioning-tjänsten

**Typ:** ändrade funktionen  
**Tjänstekategori:** Appetablering  
**Produkt-funktionen:** Identitetslivcykelhantering

Vi har uppdaterat Azure ADs etablerings miljö för att inkludera en ny förlopps indikator som visar hur långt du befinner dig i användar etablerings processen. Den här uppdaterade upplevelsen ger också information om antalet användare som etablerats under den aktuella cykeln, samt hur många användare som har etablerats till dagens datum.

Mer information finns i [kontrol lera status för användar etablering](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Företags anpassning visas nu på skärmen Logga ut och fel

**Typ:** ändrade funktionen  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** användarautentisering

Vi har uppdaterat Azure AD så att ditt företags varumärke nu visas på skärmen Logga ut och fel och på inloggnings sidan. Du behöver inte göra något för att aktivera den här funktionen, Azure AD använder bara de till gångar som du redan har konfigurerat i **företags anpassnings** delen av Azure Portal.

Mer information om hur du konfigurerar din företags anpassning finns i [lägga till anpassning till din organisations Azure Active Directory sidor](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure Multi-Factor Authentication-servern (MFA) är inte längre tillgänglig för nya distributioner

**Typ:** inaktuell  
**Tjänste kategori:** MFA  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication i organisationen måste nu använda molnbaserad Azure-Multi-Factor Authentication. Kunder som aktiverade MFA Server tidigare än 1 juli ser ingen ändring. Du kommer fortfarande att kunna ladda ned den senaste versionen, Hämta framtida uppdateringar och generera autentiseringsuppgifter för aktivering.

Mer information finns i [komma igång med Azure Multi-Factor Authentication-Server](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Mer information om molnbaserad Azure-Multi-Factor Authentication finns i [Planera en molnbaserad Azure-Multi-Factor Authentication distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maj 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Tjänst förändring: framtida stöd för endast TLS 1,2-protokoll i Application Proxy-tjänsten

**Typ:** ändringsplan  
**Tjänstekategori:** App Proxy  
**Produkt-funktionen:** Access Control

För att tillhandahålla bästa möjliga kryptering för våra kunder begränsar vi åtkomsten till endast TLS 1,2-protokoll på Application Proxy-tjänsten. Den här ändringen distribueras gradvis till kunder som redan använder TLS 1,2-protokoll, så du bör inte se några ändringar.

Utfasningen av TLS 1,0 och TLS 1,1 sker den 31 augusti 2019, men vi ger ytterligare Avancerat meddelande så att du har tid att förbereda dig för den här ändringen. För att förbereda för den här ändringen kontrollerar du att kombinationen av klient-och webb läsar Server, inklusive alla klienter som användarna använder för att komma åt appar som publicerats via programproxy, har uppdaterats för att använda TLS 1,2-protokollet för att upprätthålla anslutningen till programmet Proxy service. Mer information finns i [lägga till ett lokalt program för fjärråtkomst via Application Proxy i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Använd rapporten användning och insikter för att visa dina app-relaterade inloggnings data

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** övervakning och rapportering

Nu kan du använda rapporten användning och insikter som finns i avsnittet **företags program** i Azure Portal för att få en programinriktad vy över dina inloggnings data, inklusive information om:

- Mest använda appar för din organisation

- Appar med de mest misslyckade inloggnings programmen

- Vanligaste inloggnings fel för varje app

Mer information om den här funktionen finns i [användnings-och insikts rapport i Azure Active Directory Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatisera din användar etablering för molnappar med Azure AD

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** övervakning och rapportering

Följ de här nya självstudierna för att använda Azure AD Provisioning-tjänsten för att automatisera skapande, borttagning och uppdatering av användar konton för följande molnbaserade appar:

- [Samstämm](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Du kan också följa den här nya [guiden Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), som innehåller information om hur du etablerar grupp objekt.

Mer information om hur du bättre skyddar din organisation genom att tillhandahålla automatiserade användar konton finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Identitets säkra poäng är nu tillgängligt i Azure AD (allmän tillgänglighet)

**Typ:** ny funktion  
**Tjänstekategori:** saknas  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Nu kan du övervaka och förbättra din position för identiteter genom att använda funktionen identitet säkra poäng i Azure AD. I funktionen för identitetens säkra poäng används en enda instrument panel för att hjälpa dig:

- Objektivt mäta din position för identiteter baserat på en poäng på mellan 1 och 223.

- Planera för förbättringar av din identitets säkerhet

- Granska lyckade säkerhets förbättringar

Mer information om funktionen för identitets säkerhets Poäng finns [i vad är identitetens säkra poäng i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Ny Appregistreringar upplevelse är nu tillgänglig (allmän tillgänglighet)

**Typ:** ny funktion  
**Tjänstekategori:** autentiseringar (inloggningar)  
**Produkt-funktionen:** Utvecklarupplevelse

Den nya [Appregistreringar](https://aka.ms/appregistrations) upplevelsen är nu allmänt tillgänglig. Den här nya upplevelsen innehåller alla viktiga funktioner som du är van vid från Azure Portal och program registrerings portalen och som ökar genom att:

- **Bättre hantering av appar.** I stället för att se dina appar över olika portaler kan du nu se alla dina appar på en plats.

- **Förenklad registrering av appar.** Från den förbättrade navigerings upplevelsen av förbättringar-behörigheten är det nu enklare att registrera och hantera dina appar.

- **Mer detaljerad information.** Du hittar mer information om din app, inklusive snabb starts guider med mera.

Mer information finns i [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/) och [Appregistreringar Experience är nu allmänt tillgänglig!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogg meddelande.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nya funktioner som är tillgängliga i API för riskfyllda användare för identitets skydd

**Typ:** ny funktion  
**Tjänstekategori:** Identity Protection  
**Produkt-funktionen:** Identitetssäkerhet och skydd

Vi är glada över att kunna meddela att du nu kan använda API: erna för riskfyllda användare för att hämta användares risk historik, ignorera riskfyllda användare och bekräfta användare som komprometterade. Den här ändringen hjälper dig att effektivt uppdatera risk statusen för dina användare och förstå deras risk historik.

Mer information finns i [referens dokumentationen för riskfyllda användare](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Gallery – maj 2019

**Typ:** ny funktion  
**Tjänstekategori:** Företagsappar  
**Produkt-funktionen:** 3 part-integrering

I maj 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [riktiga länkar](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [displayer](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales-engagerande](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [system](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [META4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Arbets plats](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobolt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API-moln](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [näring](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Mer information om apparna som finns i [SaaS-programintegration med Azure Active Directory](https://aka.ms/appstutorial). Läs mer om att lista ditt program i Azure AD-appgalleri [lista ditt program i Azure Active Directory-programgalleriet](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Förbättrade grupper för att skapa och hantera funktioner i Azure AD-portalen

**Typ:** ny funktion  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** samarbete

Vi har gjort förbättringar i de grupprelaterade upplevelserna i Azure AD-portalen. Dessa förbättringar gör det möjligt för administratörer att hantera grupper listor, medlems listor och ge ytterligare skapande alternativ.

Förbättringarna innefattar:

- Grundläggande filtrering efter medlemskaps typ och grupptyp.

- Tillägg av nya kolumner, t. ex. källa och e-postadress.

- Möjlighet att välja flera grupper, medlemmar och ägar listor för enkel borttagning.

- Möjlighet att välja en e-postadress och lägga till ägare när gruppen skapas.

Mer information finns i [skapa en grundläggande grupp och lägga till medlemmar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Konfigurera en namngivnings princip för Office 365-grupper i Azure AD portal (allmän tillgänglighet)

**Typ:** ändrade funktionen  
**Tjänstekategori:** grupphantering  
**Produkt-funktionen:** samarbete

Administratörer kan nu konfigurera en namngivnings princip för Office 365-grupper med hjälp av Azure AD-portalen. Den här ändringen hjälper till att upprätthålla konsekventa namngivnings konventioner för Office 365-grupper som skapats eller redigerats av användare i din organisation.

Du kan konfigurera namngivnings principen för Office 365-grupper på två olika sätt:

- Definiera prefix eller suffix som läggs till automatiskt i ett grupp namn.

- Ladda upp en anpassad uppsättning blockerade ord för din organisation, vilket inte är tillåtet i grupp namn (till exempel "VD, löner, HR").

Mer information finns i [framtvinga en namngivnings princip för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-slutpunkter är nu tillgängliga för Azure AD-aktivitets loggar (allmän tillgänglighet)

**Typ:** ändrade funktionen  
**Tjänstekategori:** rapportering  
**Produkt-funktionen:** övervakning och rapportering

Vi är glada över att kunna meddela allmän tillgänglighet för stöd för Microsoft Graph API-slutpunkter för Azure AD-aktivitets loggar. I den här versionen kan du nu använda version 1,0 av både gransknings loggarna i Azure AD och inloggnings loggarna.

Mer information finns i [Översikt över Azure AD audit log API](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Administratörer kan nu använda villkorlig åtkomst för den kombinerade registrerings processen (offentlig för hands version)

**Typ:** ny funktion  
**Tjänstekategori:** villkorlig åtkomst  
**Produkt-funktionen:** Identitetssäkerhet och skydd  

Administratörer kan nu skapa principer för villkorlig åtkomst för användning av den kombinerade registrerings sidan. Detta omfattar att tillämpa principer för att tillåta registrering om:

- Användare finns i ett betrott nätverk.

- Användare är en låg inloggnings risk.

- Användare finns på en hanterad enhet.

- Användarna godkänner organisationens användnings villkor (TOU).

Om du vill ha mer information om villkorlig åtkomst och återställning av lösen ord kan du se [blogg inlägget för den villkorliga åtkomsten i blogg inlägget för registrering i Azure AD kombinerat MFA och lösen ords återställning](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Mer information om principer för villkorlig åtkomst för den kombinerade registrerings processen finns i [principer för villkorlig åtkomst för kombinerad registrering](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Mer information om funktionen användnings villkor för Azure AD finns i Azure Active Directory användnings [villkor](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
