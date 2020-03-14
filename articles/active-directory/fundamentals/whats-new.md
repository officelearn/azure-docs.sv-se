---
title: Nyheter Viktig information – Azure Active Directory | Microsoft Docs
description: Lär dig vad är nytt med Azure Active Directory, som föråldrade funktioner för de senaste versionsanteckningarna, kända problem, felkorrigeringar och kommande ändringar.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262105"
---
# <a name="whats-new-in-azure-active-directory"></a>Vad är nytt i Azure Active Directory?

>Få ett meddelande om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in denna URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` i ![RSS-feed reader-ikonen](./media/whats-new/feed-icon-16x16.png) feed reader.

Azure AD får förbättringar med jämna mellanrum. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion
- Planer för ändringar

Den här sidan uppdateras varje månad, så gå tillbaka till den regelbundet. Om du letar efter objekt som är äldre än sex månader kan du hitta dem i [arkivet för nyheter i Azure Active Directory](whats-new-archive.md).

---

## <a name="february-2020"></a>Februari 2020
 
### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identifiera säkra Poäng – MFA Improvement åtgärd uppdateringar

**Typ:** Planera för ändring  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
För att avspegla behovet av företag för att säkerställa den högsta säkerheten vid användning av principer som fungerar med deras verksamhet, tar Microsoft Secures score bort tre förbättrings åtgärder som är centrerade kring Multi-Factor Authentication (MFA) och lägger till två.

Följande förbättrings åtgärder kommer att tas bort:

- Registrera alla användare för MFA
- Kräv MFA för alla användare
- Kräv MFA för privilegierade Azure AD-roller

Följande förbättrings åtgärder kommer att läggas till:

- Se till att alla användare kan slutföra MFA för säker åtkomst
- Kräv MFA för administrativa roller

Dessa nya förbättringar kräver att du registrerar användare eller administratörer för MFA i din katalog och hur du skapar rätt uppsättning principer som passar organisationens behov. Huvud målet är att ha flexibilitet och samtidigt se till att alla användare och administratörer kan autentisera med flera faktorer eller riskfyllda identitets verifierings meddelanden. Detta kan vara en form av att ställa in säkerhets inställningar som gör att Microsoft kan avgöra när användare ska kunna använda MFA eller ha flera principer som tillämpar besluts fattandet i definitions området. Som en del av dessa uppdateringar av förbättrings åtgärden kommer principer för bas linje skydd inte längre att ingå i beräknings beräkningar. [Läs mer om vad som kommer i Microsofts säkra Poäng](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).
 
---

### <a name="azure-ad-domain-services-sku-selection"></a>Val av Azure AD Domain Services SKU

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services
 
Vi har hört feedback som Azure AD Domain Services kunder vill ha mer flexibilitet när de väljer prestanda nivåer för sina instanser. Från och med den 1 februari 2020 bytte vi från en dynamisk modell (där Azure AD fastställer prestanda-och pris nivån baserat på antal objekt) till en egen markerings modell. Nu kan kunderna välja en prestanda nivå som matchar deras miljö. Den här ändringen gör det också möjligt för oss att aktivera nya scenarier som resurs skogar och Premium funktioner som dagliga säkerhets kopieringar. Antalet objekt är nu obegränsat för alla SKU: er, men vi fortsätter att erbjuda förslag på antal objekt för varje nivå.

**Ingen omedelbar kund åtgärd krävs.** För befintliga kunder fastställer den dynamiska nivån som användes den 1 februari 2020 den nya standard nivån. Det finns inga priser eller prestanda som påverkar resultatet av den här ändringen. Azure AD DS-kunder kommer att behöva utvärdera prestanda krav när deras katalog storlek och arbets belastnings egenskaper ändras. Växling mellan tjänst nivåer fortsätter att vara en åtgärd utan avbrott och vi kommer inte längre att automatiskt flytta kunder till nya nivåer baserat på katalogens tillväxt. Dessutom kommer det inte att finnas några prisökningar och den nya prissättningen kommer att justeras mot vår aktuella fakturerings modell. Mer information finns i dokumentationen för [Azure AD DS SKU](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) och [sidan Azure AD Domain Services prissättning](https://azure.microsoft.com/pricing/details/active-directory-ds/).


---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – februari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I februari 2020 har vi lagt till dessa 31 nya appar med stöd för federation i app-galleriet: 

[IamIP patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 SSO för Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda e-mail Security Service](https://ess.barracudanetworks.com/sso/azure), [ABA repor ting](https://myaba.co.uk/client-access/signin/auth/msad), [i händelse av kris-online-portalen](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Biodlings Azure AD data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [korn](https://www.kornferry.com/solutions/kf-digital/kf-assess)-, Verkada [kommando](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial) [, Syxsense,](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [EAB navigera](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (begränsad utgåva)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [biljett hanteraren](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [mall Väljaren för team](https://links.officeatwork.com/templatechooser-download-teams), [bin](https://www.beesy.me/index.php/site/login), [hälso support system](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex vdr](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink för lärare och skolor](https://www.thinglink.com/), [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), InvitePeople [,](https://invitepeople.com/login) [utskrift – artikel galax](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – februari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud av gener](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-stöd för FIDO2 säkerhets nycklar i hybrid miljöer

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Vi presenterar den offentliga för hands versionen av Azure AD-stöd för FIDO2-säkerhetsnycklar i hybrid miljöer. Användare kan nu använda FIDO2-säkerhetsnycklar för att logga in på sina hybrid Azure AD-anslutna Windows 10-enheter och få sömlös inloggning till sina lokala och molnbaserade resurser. Stöd för Hybrid miljöer har varit den mest efterfrågade funktionen från våra kunder med lösen ords skydd eftersom vi ursprungligen startade den offentliga för hands versionen för FIDO2-stöd i Azure AD-anslutna enheter. Autentisering utan lösen ord med avancerade tekniker som biometrik och offentlig/privat nyckel kryptering ger bekvämlighet och enkel användning medan de är säkra. Med den här offentliga för hands versionen kan du nu använda modern autentisering som FIDO2-säkerhetsnycklar för att komma åt traditionella Active Directory-resurser. Mer information finns [på SSO till lokala resurser](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Kom igång genom att gå till [Aktivera FIDO2-säkerhetsnycklar för din klient](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) för stegvisa anvisningar. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Den nya mitt konto upplevelsen är nu allmänt tillgänglig 

**Typ:** Ändrad funktion  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Slut användar upplevelser
 
Mitt konto, det enda steget för att hantera slut användar kontots behov, är nu allmänt tillgängligt! Slutanvändare kan komma åt den här nya webbplatsen via URL eller i rubriken för den nya Mina appar-upplevelsen. Lär dig mer om alla självbetjänings funktioner som den nya upplevelsen erbjuder i [min konto Portal översikt](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Min konto webbplats-URL uppdateras till myaccount.microsoft.com

**Typ:** Ändrad funktion  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Slut användar upplevelser
 
Den nya slut användar upplevelsen för mitt konto kommer att uppdatera URL: en till https://myaccount.microsoft.com nästa månad. Hitta mer information om upplevelsen och alla självbetjänings funktioner som det erbjuder för slutanvändare i [mitt konto Portal hjälp](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Januari 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Den nya portalen för Mina appar är nu allmänt tillgänglig

**Typ:** Planera för ändring  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Slut användar upplevelser
 
Uppgradera din organisation till den nya My Apps-portalen som nu är allmänt tillgänglig! Mer information om den nya portalen och samlingar finns i [skapa samlingar på mina apps-portalen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Arbets ytor i Azure AD har bytt namn till samlingar

**Typ:** Ändrad funktion  
**Tjänste kategori:** Mina appar   
**Produkt kapacitet:** Slut användar upplevelser
 
Arbets ytor, filter administratörerna kan konfigurera för att organisera sina användares appar, kommer nu att kallas samlingar. Mer information om hur du konfigurerar dem finns i [skapa samlingar på mina apps-portalen](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefonin loggning och inloggning med anpassad princip (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C
 
Med telefonnummer för registrering och inloggning kan utvecklare och företag låta sina kunder registrera sig och logga in med ett eng ång slö sen ord som skickas till användarens telefonnummer via SMS. Den här funktionen gör det också möjligt för kunden att ändra sina telefonnummer om de förlorar åtkomsten till sin telefon. Med hjälp av anpassade principer kan du använda telefonin loggning och inloggning för att kommunicera med sitt varumärke genom sidan anpassning. Ta reda på hur du [ställer in telefonin loggning och inloggning med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nya etablerings anslutningar i Azure AD Application Gallery – januari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler privat åtkomst](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – januari 2020

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part
 
I januari 2020 har vi lagt till dessa 33 nya appar med stöd för federation i app-galleriet: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [snabbt Edge-moln](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Cloud backup för Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LEAVEBOT](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), TripActions, [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN – Azure AD SSO Gateway för Oracle E-Business Suite-EBS, och JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [värdbaserad MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Företag](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [TalentSoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB för Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient utföra Manager-programvara](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [samgranskning](https://portal.coreview.com/), [Squelch Cloud Office365-anslutning](https://laxmi.squelch.io/login), PingFlow- [autentisering](https://app-staging.pingview.io/), [PrinterLogic](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)SaaS, [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [, Akari Virtuell assistent](https://akari.io/akari-virtual-assistant/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Två nya identifieringar av identitets skydd

**Typ:** Ny funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet
 
Vi har lagt till två nya inloggnings typer som är länkade till identitets skydd: misstänkta regler för att ändra Inkorgen och omöjlig resa. De här offline-identifieringarna upptäcks av Microsoft Cloud App Security (MCAS) och påverkar användaren och inloggnings risken i identitets skyddet. Mer information om dessa identifieringar finns i våra [typer av inloggnings risker](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Bryta ändring: URI-fragment kommer inte att transporteras via omdirigeringen för inloggning

**Typ:** Ändrad funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering
 
Från och med den 8 februari 2020 lägger tjänsten till ett tomt fragment i begäran när en begäran skickas till login.microsoftonline.com för att logga in en användare.  Detta förhindrar en klass av omdirigerings attacker genom att se till att webbläsaren rensar alla befintliga fragment i begäran. Inget program bör ha ett beroende på detta beteende. Mer information finns i avsnittet om att [dela upp ändringar](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) i dokumentationen för Microsoft Identity Platform.

---

## <a name="december-2019"></a>December 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrera SAP SuccessFactors-etablering i Azure AD och on-premises AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** App-etablering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Nu kan du integrera SAP-SuccessFactors som en auktoritativ identitets källa i Azure AD. Den här integreringen hjälper dig att automatisera livs cykeln för slut punkt till slut punkt, inklusive användning av HR-baserade händelser, t. ex. nya anställningar eller uppsägningar, för att styra etableringen av Azure AD-konton.

Mer information om hur du konfigurerar SAP SuccessFactors inkommande etablering i Azure AD finns i själv studie kursen [Konfigurera SAP-SuccessFactors automatisk etablering](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Stöd för anpassade e-postmeddelanden i Azure AD B2C (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Nu kan du använda Azure AD B2C för att skapa anpassade e-postmeddelanden när användarna registrerar sig för att använda dina appar. Genom att använda DisplayControls (för närvarande i för hands version) och en e-postprovider från tredje part (till exempel, [SendGrid](https://sendgrid.com/), [Spark post](https://sparkpost.com/)eller en anpassad REST API), kan du använda din egen e-postmall, **från** adress och ämnes text, samt stöd för lokalisering och anpassad eng ång slö sen ord.

Mer information finns i [anpassad e-postverifiering i Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Utbyte av bas linje principer med säkerhets inställningar

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Identitets säkerhet och skydd

Som en del av en säker modell som är säker för autentisering tar vi bort befintliga principer för bas linje skydd från alla klienter. Den här borttagningen är avsedd för slut för ande i slutet av februari. Ersättningen för de här bas linje skydds principerna är säkerhets inställningar. Om du har använt principer för bas linje skydd måste du planera att flytta till den nya säkerhets standard principen eller villkorlig åtkomst. Om du inte har använt de här principerna finns det ingen åtgärd att vidta.

Mer information om de nya säkerhets standarderna finns i [Vad är säkerhets inställningar?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Mer information om principer för villkorlig åtkomst finns i [vanliga principer för villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>November 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för SameSite-attributet och Chrome 80

**Typ:** Planera för ändring  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Som en del av en säker standard modell för cookies, ändrar Chrome 80-webbläsaren hur den behandlar cookies utan attributet `SameSite`. Alla cookies som inte anger `SameSite`-attributet behandlas som om det var inställt på `SameSite=Lax`, vilket leder till att Chrome blockerar vissa scenarier mellan domänens cookie-delning som din app kan vara beroende av. Om du vill behålla det äldre Chrome-beteendet kan du använda `SameSite=None`-attributet och lägga till ytterligare ett `Secure`-attribut, så att cookies från flera platser bara kan nås via HTTPS-anslutningar. Chrome har schemalagts för att slutföra den här ändringen senast den 4 februari 2020.

Vi rekommenderar att alla utvecklare testar sina appar med hjälp av den här vägledningen:

- Ange standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ange standardvärdet för attributet **SameSite** till **none**.

- Lägg till ytterligare ett `SameSite`-attribut för **säker**.

Mer information finns i [kommande SameSite cookie-ändringar i ASP.net och ASP.net Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) och [potentiella avbrott till kund webbplatser och Microsofts produkter och tjänster i Chrome version 79 och senare](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Ny snabb korrigering för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Typ:** Fastsatt  
**Tjänste kategori:** Microsoft Identity Manager  
**Produkt kapacitet:** Hantering av identitets livs cykel

Det finns ett samlat snabb korrigerings paket (build 4.6.34.0) för Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Det här samlade paketet löser problem och lägger till förbättringar som beskrivs i avsnittet "problem som åtgärd ATS och förbättringar som lagts till i den här uppdateringen".

Mer information och hämta snabb korrigerings paketet finns [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Samlad uppdatering är tillgänglig](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Ny rapport för AD FS app-aktivitet som hjälper till att migrera appar till Azure AD (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Använd aktivitets rapporten ny Active Directory Federation Services (AD FS) (AD FS) i Azure Portal för att identifiera vilka av dina appar som kan migreras till Azure AD. Rapporten visar alla AD FS appar för kompatibilitet med Azure AD, söker efter eventuella problem och ger vägledning om att förbereda enskilda appar för migrering.

Mer information finns i [använda rapporten AD FS program aktivitet för att migrera program till Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nytt arbets flöde för användare som begär administratörs medgivande (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** Access Control

Det nya administratörs godkännande arbets flödet ger administratörer ett sätt att bevilja åtkomst till appar som kräver administratörs godkännande. Om en användare försöker få åtkomst till en app, men inte kan ge samtycke, kan de nu skicka en begäran om administratörs godkännande. Begäran skickas via e-post och placeras i en kö som är tillgänglig från Azure Portal, till alla administratörer som har angetts som granskare. När en granskare har åtgärd ATS för en väntande begäran, meddelas de begär ande användarna om åtgärden.

Mer information finns i [Konfigurera arbets flödet för administratörs medgivande (för hands version)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nya Azure AD App registrerar konfigurations upplevelsen för token för hantering av valfria anspråk (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Utvecklings miljö

Bladet ny **Azure AD App registreringar för token-konfiguration** på Azure Portal visar nu Apps-utvecklare en dynamisk lista med valfria anspråk för sina appar. Den här nya upplevelsen hjälper till att effektivisera Azure AD-App-migreringar och minimera valfria felkonfigurationer för anspråk.

Mer information finns i [tillhandahålla valfria anspråk till din Azure AD-App](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nytt arbets flöde med två stegs godkännande i hantering av Azure AD-rättigheter (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av rättigheter

Vi har lanserat ett nytt arbets flöde med två steg som gör att du kan kräva att två god kännare godkänner en användares begäran till ett Access-paket. Du kan till exempel ställa in det så att den begär ande användarens chef först måste godkänna, och sedan kan du också kräva att en resurs ägare godkänner detta. Om en av god kännarna inte godkänner beviljas inte åtkomst.

Mer information finns i [Inställningar för ändring av begäran och godkännande för ett Access-paket i hantering av Azure AD-rättigheter](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdateringar av sidan Mina appar tillsammans med nya arbets ytor (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** integration från tredje part

Nu kan du anpassa hur din organisations användare visar och får åtkomst till den uppdaterade Mina appar-upplevelsen. Den här nya upplevelsen innehåller även funktionen nya arbets ytor, vilket gör det enklare för användarna att hitta och organisera appar.

Mer information om den nya upplevelsen för Mina appar och hur du skapar arbets ytor finns i [skapa arbets ytor på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google social ID-stöd för Azure AD B2B-samarbete (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Business  
**Produkt kapacitet:** Användarautentisering

Nytt stöd för användning av Google social ID (Gmail-konton) i Azure AD hjälper till att förenkla samarbetet för dina användare och partner. Du behöver inte längre använda dina partner för att skapa och hantera ett nytt Microsoft-särskilt konto. Microsoft Teams har nu fullständigt stöd för Google-användare på alla klienter och över de vanliga och klient-relaterade autentiserings slut punkterna.

Mer information finns i [lägga till Google som en identitets leverantör för B2B-gäst användare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile-support för villkorlig åtkomst och enkel inloggning (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Villkorlig åtkomst  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Azure AD för Microsoft Edge på iOS och Android har nu stöd för enkel inloggning och villkorlig åtkomst i Azure AD:

- **Enkel inloggning för Microsoft Edge (SSO):** Enkel inloggning är nu tillgängligt över interna klienter (till exempel Microsoft Outlook och Microsoft Edge) för alla Azure AD-anslutna appar.

- **Villkorlig åtkomst för Microsoft Edge:** Via programbaserade villkorliga åtkomst principer måste användarna använda Microsoft Intune-skyddade webbläsare, till exempel Microsoft Edge.

Mer information om villkorlig åtkomst och enkel inloggning med Microsoft Edge finns i [Microsoft Edge Mobile support för villkorlig åtkomst och enkel inloggning nu allmänt tillgängliga](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogg inlägg. Mer information om hur du konfigurerar dina klient program med hjälp av [app-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) eller [enhets-baserad villkorlig åtkomst](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)finns i [Hantera webb åtkomst med hjälp av en Microsoft Intune-princip-skyddad webbläsare](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Hantering av Azure AD-rättigheter (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Hantering av rättigheter

Hantering av Azure AD-berättigande är en ny funktion för identitets styrning, som hjälper organisationer att hantera identitets-och åtkomst livs cykeln i stor skala. Den här nya funktionen hjälper till att automatisera arbets flöden för åtkomstbegäran, åtkomst tilldelningar, recensioner och förfallo datum för grupper, appar och SharePoint Online-webbplatser.

Med hantering av Azure AD-behörighet kan du effektivare hantera åtkomst både för anställda och även för användare utanför organisationen som behöver åtkomst till dessa resurser.

Mer information finns i [Vad är Azure AD-hantering av rättigheter?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatisera användar konto etablering för de här nyligen SaaS apparna som stöds

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part  

Nu kan du automatisera att skapa, uppdatera och ta bort användar konton för dessa nyligen integrerade appar:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace program vara](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [prioritets mat ris](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Mer information om hur du bättre skyddar din organisation med hjälp av automatiserad användar konto etablering finns i [Automatisera användar etablering för SaaS-program med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – november 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I november 2019 har vi lagt till dessa 21 nya appar med stöd för federation i app-galleriet:

[Flygbord](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [blå åtkomst för medlemmar (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal enkel inloggning (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [, Motus,](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial)MyAryaka [,](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial)BlueMail [, Beedle](https://loginself1.bluemail.me/), Visma [, OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), foko [, Qmarkets](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)- [idé & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [](https://teams-web.beedle.co/#/) [](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) , [JISC student registrering](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Nytt och förbättrat program Galleri för Azure AD

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har uppdaterat Azure AD-programgalleriet för att göra det enklare för dig att hitta förintegrerade appar som stöder etablering, OpenID Connect och SAML på din Azure Active Directory klient.

Mer information finns i [lägga till ett program i Azure Active Directory-klienten](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Utökad längd gräns för program Rolls definition från 120 till 240 tecken

**Typ:** Ändrad funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har hört kunder om att längd gränsen för definition svärdet för app-roll i vissa appar och tjänster är för kort med 120 tecken. Som svar har vi ökat den maximala längden för roll värdes definitionen till 240 tecken.

Mer information om hur du använder programspecifika roll definitioner finns i [lägga till app-roller i programmet och ta emot dem i token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Oktober 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Utfasning av identityRiskEvent-API: et för Azure AD Identity Protection risk identifieringar  

**Typ:** Planera för ändring  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Azure AD Premium P2-prenumeranter kan nu utföra komplexa frågor på Azure AD Identity Protections risk identifierings data genom att använda det nya riskDetection-API: et för Microsoft Graph. Den befintliga [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API Beta-versionen slutar att returnera data cirka **10 januari 2020**. Om din organisation använder identityRiskEvent-API: t bör du övergå till det nya riskDetection-API: et.

Mer information om det nya riskDetection-API: et finns i [referens dokumentationen för riskhantering](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Stöd för programproxy för attributet SameSite och Chrome 80

**Typ:** Planera för ändring  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Ett par veckor före versionen av Chrome 80-webbläsaren, planerar vi att uppdatera hur programproxy-cookies behandlar attributet **SameSite** . I och med lanseringen av Chrome 80 behandlas alla cookies som inte anger attributet **SameSite** som om de har angetts till `SameSite=Lax`.

För att undvika potentiellt negativa konsekvenser på grund av den här ändringen uppdaterar vi programproxyns åtkomst och sessionscookies genom att:

- Ställer in standardvärdet för inställningen **Använd säker cookie** till **Ja**.

- Ställer in standardvärdet för attributet **SameSite** till **none**.

    >[!NOTE]
    > Cookies för åtkomst till programproxyn har alltid skickats exklusivt över säkra kanaler. Dessa ändringar gäller endast för sessionscookies.

Mer information om cookies-inställningarna för programproxyn finns i [cookie-inställningar för att komma åt lokala program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Appregistreringar (bakåtkompatibelt) och konvergerad program hantering från program registrerings portalen (apps.dev.microsoft.com) kommer inte längre vara tillgänglig

**Typ:** Planera för ändring  
**Tjänste kategori:** EJ TILLÄMPLIGT  
**Produkt kapacitet:** Utvecklings miljö

I nära framtid kommer användare med Azure AD-konton inte längre att kunna registrera och hantera konvergerade program med hjälp av program registrerings portalen (apps.dev.microsoft.com) eller registrera och hantera program i Appregistreringar (bakåtkompatibelt) upplevelse i Azure Portal.

Mer information om den nya Appregistreringar upplevelsen finns i [Appregistreringar i Azure Portal tränings guide](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Användare behöver inte längre registrera sig på nytt vid migrering från användarspecifika MFA till villkorlig åtkomst-baserad MFA

**Typ:** Fastsatt  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Vi har åtgärdat ett känt problem, vilket innebär att när användare var tvungen att omregistrera om de har inaktiverats för Multi-Factor Authentication per användare (MFA) och sedan aktive ras för MFA via en princip för villkorlig åtkomst.

Om du vill kräva att användarna registrerar om kan du välja alternativet **Omregistrera MFA** från användarens autentiseringsmetoder i Azure AD-portalen. Mer information om hur du migrerar användare från per användare MFA till villkorlig åtkomst-baserad MFA finns i [konvertera användare från per användare MFA till villkorlig åtkomst baserat MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nya funktioner för att transformera och skicka anspråk i SAML-token

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** DEFINITION

Vi har lagt till ytterligare funktioner som hjälper dig att anpassa och skicka anspråk i SAML-token. De här nya funktionerna är:

- Ytterligare omvandlings funktioner för anspråk, vilket hjälper dig att ändra värdet som du skickar i anspråket.

- Möjlighet att tillämpa flera transformationer på ett enda anspråk.

- Möjlighet att ange anspråks källan, baserat på användar typen och gruppen som användaren tillhör.

Detaljerad information om de här nya funktionerna, inklusive hur du använder dem, finns i [Anpassa anspråk som utfärdats i SAML-token för företags program](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Sidan nya inloggnings tillägg för slutanvändare i Azure AD

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Övervaka & rapportering

Vi har lagt till en ny **inloggnings** sida (https://mysignins.microsoft.com) för att låta din organisations användare visa sin senaste inloggnings historik för att kontrol lera om det finns någon ovanlig aktivitet. På den nya sidan kan användarna se:

- Om någon försöker gissa sitt lösen ord.

- Om en angripare har loggat in på kontot och från vilken plats.

- Vilka appar angriparen försökte få åtkomst till.

Mer information finns i avsnittet [användare kan nu kontrol lera sin inloggnings historik för ovanlig aktivitets](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) blogg.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrering av Azure AD Domain Services (Azure AD DS) från klassisk till Azure Resource Manager virtuella nätverk

**Typ:** Ny funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Till våra kunder som har fastnat i klassiska virtuella nätverk – vi har bra nyheter! Du kan nu utföra en eng ång slö flytt från ett klassiskt virtuellt nätverk till ett befintligt virtuellt Resource Manager-nätverk. När du har flyttat till det virtuella Resource Manager-nätverket kan du dra nytta av de ytterligare och uppgraderade funktionerna, till exempel detaljerade lösen ords principer, e-postaviseringar och gransknings loggar.

Mer information finns i [förhands granskning – migrera Azure AD Domain Services från den klassiska virtuella nätverks modellen till Resource Manager](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Uppdateringar av layouten Azure AD B2C sid kontrakt

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Vi har introducerat några nya ändringar i version 1.2.0 av sid kontraktet för Azure AD B2C. I den här uppdaterade versionen kan du nu styra inläsnings ordningen för dina element, vilket också kan hjälpa till att stoppa flimmer som inträffar när format mal len (CSS) läses in.

En fullständig lista över de ändringar som gjorts i sidan kontrakt finns i [versions ändrings loggen](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Uppdatera till sidan Mina appar tillsammans med nya arbets ytor (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Mina appar  
**Produkt kapacitet:** Access Control

Nu kan du anpassa hur din organisations användare visar och kommer åt den anpassade appen Mina appar, inklusive att använda funktionen nya arbets ytor för att göra det lättare för dem att hitta appar. Funktionen nya arbets ytor fungerar som ett filter för de appar som organisationens användare redan har åtkomst till.

Mer information om hur du utvärderar nya funktioner för Mina appar och skapar arbets ytor finns i [skapa arbets ytor på portalen Mina appar (för hands version)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Stöd för den månatliga aktiva användarbaserade fakturerings modellen (allmän tillgänglighet)

**Typ:** Ny funktion  
**Tjänste kategori:** B2C – konsument identitets hantering  
**Produkt kapacitet:** B2B/B2C

Azure AD B2C stöder nu MAU-fakturering (Monthly Active Users). MAU-fakturering baseras på antalet unika användare med autentiserings aktivitet under en kalender månad. Befintliga kunder kan när som helst byta till den nya fakturerings metoden.

Från den 1 november 2019 debiteras alla nya kunder automatiskt med den här metoden. Den här fakturerings metoden fördelar kunder genom kostnads förmåner och möjligheten att planera framåt.

Mer information finns i [Uppgradera till månatliga aktiva användares fakturerings modell](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – oktober 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I oktober 2019 har vi lagt till dessa 35 nya appar med stöd för federation i app-galleriet:

[I händelse av kris – mobil](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno resa](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster, dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial) [, HunchBuzz](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial) [, Freshworks](https://login.hunchbuzz.com/integrations/azure/process) [, eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial) [, ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [Netskope](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), Bindtuning, [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)HireVue, HireVue [, USOnly](https://bindtuning.com/login), HireVue, WittyParrot, [,](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial),,, [HireVue Coordinate – EU](https://www.hirevue.com/),,,, [,](https://www.hirevue.com/)- [koordinat – USA](https://www.hirevue.com/), [ Kunskaps Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Maile!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [team](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), EAB [navigera implementering](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [speaking e-mail for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [talad e-post för Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care navigerings system](https://ihealthnav.com/account/signin), [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Meny alternativet konsol IDE rad säkerhet i Azure AD-portalen

**Typ:** Ändrad funktion  
**Tjänste kategori:** Identitets skydd  
**Produkt kapacitet:** & Skydd för identitets säkerhet

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

**Typ:** Ändrad funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Hantering av identitets livs cykel

Utgångs principen för Office 365-grupper har förbättrats för att automatiskt förnya grupper som används aktivt av dess medlemmar. Grupper förnyas automatiskt baserat på användar aktivitet i alla Office 365-appar, inklusive Outlook, SharePoint och Teams.

Den här förbättringen hjälper till att minska antalet aviseringar för din grupp och hjälper till att se till att aktiva grupper fortfarande är tillgängliga. Om du redan har en aktiv utgångs princip för dina Office 365-grupper behöver du inte göra något för att aktivera den här nya funktionen.

Mer information finns i [Konfigurera förfallo principen för Office 365-grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Uppdaterad Azure AD Domain Services (Azure AD DS)-skapande

**Typ:** Ändrad funktion  
**Tjänste kategori:** Azure AD Domain Services  
**Produkt kapacitet:** Azure AD Domain Services

Vi har uppdaterat Azure AD Domain Services (Azure AD DS) för att ta med en ny och förbättrad skapande upplevelse, som hjälper dig att skapa en hanterad domän i tre klick! Dessutom kan du nu ladda upp och Distribuera Azure AD DS från en mall.

Mer information finns i [Självstudier: skapa och konfigurera en Azure Active Directory Domain Services instans](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>September 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Plan för ändring: utfasning av Power BI innehålls paket

**Typ:** Planera för ändring  
**Tjänste kategori:** Uppgiftslämn  
**Produkt kapacitet:** Övervaka & rapportering

Från och med den 1 oktober 2019 börjar Power BI att ta bort alla innehålls paket, inklusive Azure AD Power BI-innehålls paketet. Som ett alternativ till detta innehålls paket kan du använda Azure AD-arbetsböcker för att få insikter om dina Azure AD-relaterade tjänster. Ytterligare arbets böcker kommer, inklusive arbets böcker om principer för villkorlig åtkomst i endast rapport läge, app-baserade insikter med mera.

Mer information om arbets böckerna finns i [så här använder du Azure Monitor-arbetsböcker för Azure Active Directory-rapporter](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Mer information om utfasningen av innehålls paket finns i blogg inlägget om att [presentera Power BI Template Apps allmänt tillgänglighet](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) .

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Min profil byter namn och integrerar med sidan Microsoft Office konto

**Typ:** Planera för ändring  
**Tjänste kategori:** Min profil/konto  
**Produkt kapacitet:** Samarbete

Från och med oktober blir min profil upplevelse mitt konto. Som en del av ändringen kommer **min profil** att ändras till **mitt konto**för närvarande. Den uppdaterade upplevelsen av namn ändringen och vissa design förbättringar erbjuder ytterligare integrering med Microsoft Office konto sidan. Mer specifikt kommer du att kunna komma åt Office-installationer och-prenumerationer från sidan **översikts konto** , tillsammans med Office-relaterade kontakt inställningar på sidan **Sekretess** .

Mer information om min profil (för hands version) finns i [Översikt över min profil (för hands version)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Mass hantering av grupper och medlemmar med CSV-filer i Azure AD-portalen (offentlig för hands version)

**Typ:** Ny funktion  
**Tjänste kategori:** Grupp hantering  
**Produkt kapacitet:** Samarbete

Vi är glada över att kunna meddela den offentliga för hands versionen av hanterings upplevelser för flera grupper i Azure AD-portalen. Nu kan du använda en CSV-fil och Azure AD-portalen för att hantera grupper och medlems listor, inklusive:

- Lägga till eller ta bort medlemmar från en grupp.

- Hämtar listan över grupper från katalogen.

- Hämtar listan över grupp medlemmar för en speciell grupp.

Mer information finns i avsnittet [Lägg till medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [Mass borttagnings medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), lista över grupp [medlemmar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)och [grupper med hämtnings grupper](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)för grupp.

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Dynamiskt medgivande stöds nu via en ny administratörs tillstånds slut punkt

**Typ:** Ny funktion  
**Tjänste kategori:** Autentiseringar (inloggningar)  
**Produkt kapacitet:** Användarautentisering

Vi har skapat en ny administratörs medgivande slut punkt som stöder dynamiskt medgivande, vilket är användbart för appar som vill använda den dynamiska godkännande modellen på Microsoft Identity Platform.

Mer information om hur du använder den nya slut punkten finns i [using the admin medgivande Endpoint](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nya federerade appar som är tillgängliga i Azure AD App Galleri – september 2019

**Typ:** Ny funktion  
**Tjänste kategori:** Företags program  
**Produkt kapacitet:** integration från tredje part

I september 2019 har vi lagt till dessa 29 nya appar med stöd för federation i app-galleriet:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO-åtkomst för Ethidex Compliance Office™ – enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iserver Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur rese-och utgifts](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial)hantering, [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [Arc-anläggningar](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus-team](https://stratus.emea.luware.cloud/login), [breda idéer](https://wideideas.online/wideideas/), [Prisma-moln](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT](https://clients.jdlt.co.uk/login)- [, RENRAKU,](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [sealpath Brings säker webbläsare](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [, Penneo](https://app.penneo.com/), [Hiretual](https://app.testhtm.com/settings/email-integration), [Cintoo-moln](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [värd arvet online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai team build](https://ms-contacts.coo-kai.jp/), [SonarQube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [Discovery-förmåner SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/) [, iTask](https://itask.yipinapp.com/)

Mer information om apparna finns i [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial). Mer information om hur du visar ditt program i Azure AD App-galleriet finns i [lista ditt program i Azure Active Directory program galleriet](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Ny Azure AD global läsar roll

**Typ:** Ny funktion  
**Tjänste kategori:** RBAC  
**Produkt kapacitet:** Access Control

Från den 24 september 2019 kommer vi att börja lansera en ny Azure Active Directory (AD) roll som kallas global läsare. Den här distributionen startar med produktions-och globala Cloud-kunder (GCC), som slutförs i hela världen i oktober.

Global läsar roll är den skrivskyddade motsvarigheten till global administratör. Användare med den här rollen kan läsa inställningar och administrativ information mellan Microsoft 365 tjänster, men kan inte vidta hanterings åtgärder. Vi har skapat rollen global läsare som hjälper till att minska antalet globala administratörer i din organisation. Eftersom globala administratörs konton är kraftfulla och utsatta för angrepp rekommenderar vi att du har färre än fem globala administratörer. Vi rekommenderar att du använder rollen global läsare för planering, granskningar och undersökningar. Vi rekommenderar också att du använder rollen global läsare i kombination med andra begränsade administratörs roller, t. ex. Exchange-administratör, för att få jobbet gjort utan att den globala administratörs rollen krävs.

Rollen global läsare fungerar med de nya Microsoft 365 administrations centret, administrations Center för team, team administrations Center, Security Center, Compliance Center, Azure AD administrations Center och administrations Center för enhets hantering.

>[!NOTE]
> I början av den offentliga för hands versionen fungerar inte den globala läsar rollen med: SharePoint, Privileged Access Management, Customer Lockbox, känslighets etiketter, team-livscykel, team rapportering & samtals analys, team hantering av IP-telefonen och team-appen Katalogen. Alla dessa tjänster är avsedda att fungera med rollen i framtiden.

Mer information finns i [Administratörs roll behörigheter i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Få åtkomst till en lokal rapport Server från din Power BI Mobile-app med hjälp av Azure Active Directory-programproxy

**Typ:** Ny funktion  
**Tjänste kategori:** App-proxy  
**Produkt kapacitet:** Access Control

Med den nya integreringen mellan Power BI mobilapp och Azure AD-programproxy kan du på ett säkert sätt logga in på den Power BI mobilappen och visa vilken som helst av organisationens rapporter som finns på den lokala Power BI-rapportserver.

Information om Power BI Mobile-appen, inklusive var du hämtar appen, finns på [Power BI webbplats](https://powerbi.microsoft.com/mobile/). Mer information om hur du konfigurerar Power BI mobilapp med Azure AD-programproxy finns i [aktivera fjärråtkomst till Power BI Mobile med azure AD-programproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Det finns en ny version av AzureADPreview PowerShell-modulen

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Katalogen

Nya cmdletar har lagts till i AzureADPreview-modulen för att hjälpa till att definiera och tilldela anpassade roller i Azure AD, inklusive:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Ny version av Azure AD Connect

**Typ:** Ändrad funktion  
**Tjänste kategori:** Andra  
**Produkt kapacitet:** Katalogen

Vi har släppt en uppdaterad version av Azure AD Connect för kunder med automatisk uppgradering. Den här nya versionen innehåller flera nya funktioner, förbättringar och fel korrigeringar. Mer information om den här nya versionen finns [Azure AD Connect: versions historik](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication-Server (MFA), version 8.0.2 är nu tillgänglig

**Typ:** Fastsatt  
**Tjänste kategori:** MFA  
**Produkt kapacitet:** & Skydd för identitets säkerhet

Om du är en befintlig kund, som aktiverade MFA Server tidigare än den 1 juli 2019, kan du nu ladda ned den senaste versionen av MFA Server (version 8.0.2). I den här nya versionen:

- Ett problem har åtgärd ATS så när Azure AD Sync ändrar en användare från inaktive rad till aktive rad skickas ett e-postmeddelande till användaren.

- Ett problem har åtgärd ATS så att kunderna kan uppgraderas och fortsätta att använda funktionen taggar.

- Las till Kosovo (+ 383) landskod.

- En eng ång slö MultiFactorAuthSvc-loggning har lagts till i filen. log.

- Bättre prestanda för webbtjänst-SDK.

- Andra mindre buggar.

Från och med den 1 juli 2019 erbjuder Microsoft även MFA Server för nya distributioner. Nya kunder som behöver Multi-Factor Authentication bör använda molnbaserade Azure-Multi-Factor Authentication. Mer information finns i [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
