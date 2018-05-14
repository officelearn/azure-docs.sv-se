---
title: Artikel Index för programhantering i Azure Active Directory | Microsoft Azure
description: Lär dig hur du anpassar ett sista giltighetsdatum för federationscertifikat och hur du förnyar certifikat som upphör snart att gälla.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 4f899e3f45174aef8274810e184cc374bcca2c50
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Artikelindex för programhantering i Azure Active Directory
Den här sidan innehåller en omfattande lista över alla dokument som skrivits om de olika program-relaterade funktionerna i Azure Active Directory (AD Azure).

Det finns en kort introduktion till varje större funktion, samt information om vilka artiklar för att läsa beroende på vilken information du söker efter.

## <a name="overview-articles"></a>Översikt över artiklar
Artiklarna nedan är bra startpunkter för dem som för en kort beskrivning av Azure AD funktioner för programhantering.

| Artikel Guide |  |
|:---:| --- |
| En introduktion till management programproblem som löser Azure AD |[Hantera program med Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| En översikt över de olika funktionerna i Azure AD som härrör aktivering av enkel inloggning, definiera vem som har åtkomst till appar och hur användare starta appar |[Programåtkomst och enkel inloggning i Azure Active Directory](active-directory-appssoaccess-whatis.md) |
| En titt på de olika stegen när integrera appar i din Azure AD |[Integrera Azure Active Directory med program](manage-apps/plan-an-application-integration.md)<br /><br />[Aktivera enkel inloggning till SaaS-appar](active-directory-enterprise-apps-manage-sso.md)<br /><br />[Hantera åtkomst till appar](active-directory-managing-access-to-apps.md) |
| En förklaring av hur appar representeras i Azure AD |[Hur och varför program läggs till Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Felsökning av artiklar
Det här avsnittet ger snabb åtkomst till relevanta riktlinjer för felsökning. Mer information om varje funktionsområde finns på resten av den här sidan.

| Funktionsområde |  |
|:---:| --- |
| Federerad enkel inloggning |[Felsökning av SAML-baserade enkel inloggning](active-directory-saml-debugging.md) |
| Lösenordsbaserade enkel inloggning |[Felsökning av Access panelen-tillägg för Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Programproxy |[Felsökningsguide för App-Proxy](active-directory-application-proxy-troubleshoot.md) |
| Enkel inloggning mellan lokala AD och Azure AD |[Felsöka Lösenordssynkronisering för Hash](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Felsöka tillbakaskrivning av lösenord](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dynamiska gruppmedlemskap |[Felsökning av dynamiska gruppmedlemskap](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Enkel inloggning (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federerad enkel inloggning: Logga in på många appar med hjälp av en identitet
Ger användare åtkomst till en mängd olika appar och tjänster med endast en uppsättning autentiseringsuppgifter för enkel inloggning. Federation är en metod som du kan aktivera enkel inloggning. När användarna försöker logga in till federerade appar de ska omdirigeras till organisationens officiella inloggningssidan återges av Azure Active Directory och omdirigeras sedan tillbaka till appen vid autentiseringen.

| Artikel Guide |  |
|:---:| --- |
| En introduktion till federation och andra typer av inloggning |[Enkel inloggning med Azure AD](active-directory-appssoaccess-whatis.md) |
| SaaS-appar som redan är integrerade med Azure AD med tusentals förenklad enkel inloggning konfigurationssteg |[Komma igång med Azure AD application gallery](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Fullständig lista över förintegrerade appar som har stöd för Federation](active-directory-saas-tutorial-list.md)<br /><br />[Lägga till din App i Appgalleriet för Azure AD](active-directory-app-gallery-listing.md) |
| Fler än 150 app självstudier om hur du konfigurerar enkel inloggning för appar som [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md), och mycket mer |[Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Hur du manuellt konfigurera och anpassa din konfiguration för enkel inloggning |[Hur att konfigurera federerad enkel inloggning för appar som inte ingår i Azure Active Directory-Programgalleriet](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](active-directory-saml-claims-customization.md) |
| Felsökningsguide för federerade appar som använder SAML-protokoll |[Felsökning av SAML-baserade enkel inloggning](active-directory-saml-debugging.md) |
| Hur du konfigurerar din app certifikatets förfallodatum och hur du förnyar certifikat |[Hantera certifikat för federerad enkel inloggning i Azure Active Directory](active-directory-sso-certs.md) |

Federerad enkel inloggning är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för obegränsade program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), sedan kan du [använda grupper för att tilldela åtkomst till federerade program](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Lösenordsbaserade enkel inloggning: kontot delning och enkel inloggning för icke-federerade appar
Om du vill aktivera enkel inloggning för program som inte stöder federation erbjuder Azure AD lösenord hanteringsfunktioner som kan lagras säkert lösenord till SaaS-appar och logga in användare automatiskt dessa appar. Du kan enkelt distribuera autentiseringsuppgifterna för nyligen skapade konton och dela team konton med flera personer. Användarna behöver inte känner till autentiseringsuppgifterna för de konton som de får åtkomst till.

| Artikel Guide |  |
|:---:| --- |
| En introduktion till hur lösenordsbaserade fungerar för SSO och en kortfattad teknisk översikt |[Lösenordsbaserade enkel inloggning med Azure AD](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| En sammanfattning av de scenarier som rör konto delning och hur de här problemen lösas av Azure AD |[Dela konton med Azure AD](active-directory-sharing-accounts.md) |
| Ändra lösenordet för vissa appar automatiskt med jämna mellanrum |[Automatisk förnyelse av lösenord (förhandsgranskning)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Distribution och felsökning för Internet Explorer-versionen av Azure AD-lösenord management-tillägget |[Hur du distribuerar Access panelen-tillägg för Internet Explorer med hjälp av Grupprincip](active-directory-saas-ie-group-policy.md)<br /><br />[Felsökning av Access panelen-tillägg för Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Lösenordsbaserade enkel inloggning är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för obegränsade program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), sedan kan du [använda grupper för att bevilja åtkomst till program](#managing-access-to-applications). Lösenord för automatisk förnyelse är en [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktion.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>App-Proxy: Åtkomst med enkel inloggning och fjärråtkomst till lokala program
Om du har program i ditt privata nätverk som ska användas av användare och enheter utanför nätverket, kan du använda Azure AD Application Proxy ger säker åtkomst till apparna.

| Artikel Guide |  |
|:---:| --- |
| Översikt över Azure AD Application Proxy och hur det fungerar |[Att tillhandahålla säker fjärråtkomst till lokala program](manage-apps/application-proxy.md) |
| Självstudier om hur du konfigurerar Application Proxy och hur du publicerar din första app |[Hur du konfigurerar Azure AD App-Proxy](manage-apps/application-proxy-enable.md)<br /><br />[Tyst installation App Proxy Connector](active-directory-application-proxy-silent-installation.md)<br /><br />[Hur du publicerar program med App-Proxy](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Hur du använder ett eget domännamn](manage-apps/application-proxy-configure-custom-domain.md) |
| Aktivera enkel inloggning och villkorlig åtkomst för appar som publiceras med App-Proxy |[Single-sign-on med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Villkorlig åtkomst och Application Proxy](application-proxy-enable-remote-access-sharepoint.md) |
| Information om hur du använder Application Proxy för följande scenarier |[Ge stöd för Native Client-program](active-directory-application-proxy-native-client.md)<br /><br />[Ge stöd för anspråksmedvetna program](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Ge stöd för program som publicerats i separata nätverk och platser](active-directory-application-proxy-connectors-azure-portal.md) |
| Felsökningsguide för Application Proxy |[Felsökningsguide för App-Proxy](active-directory-application-proxy-troubleshoot.md) |

Application Proxy är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för obegränsade program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), sedan kan du [använda grupper för att bevilja åtkomst till program](#managing-access-to-applications).

Du kan också vara intresserad av [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), där du kan migrera dina lokala program till Azure när fortfarande uppfyller identitet behov av dessa program.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Aktivera enkel inloggning mellan Azure AD och lokala AD
Om organisationen upprätthåller en Windows Server Active Directory lokalt tillsammans med Azure Active Directory i molnet, kommer du förmodligen vill aktivera enkel inloggning mellan dessa två system. Azure AD Connect (verktyg som tillsammans integrerar dessa två system) innehåller flera alternativ för att konfigurera enkel inloggning: upprätta federation med AD FS eller en annan provider för federation eller aktivera Lösenordssynkronisering.

| Artikel Guide |  |
|:---:| --- |
| En översikt över alternativ för enkel inloggning som erbjuds i Azure AD Connect, samt information om hur du hanterar hybridmiljöer |[Användaren loggar in alternativ i Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Allmänna riktlinjer för att hantera miljöer med både lokala Active Directory och Azure Active Directory |[Överväganden vid utformning av Azure AD-Hybrid](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md) |
| Hjälp med att använda Lösenordssynkronisering för att aktivera enkel inloggning |[Implementera Lösenordssynkronisering med Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Felsöka Lösenordssynkronisering](https://support.microsoft.com/en-us/kb/2855271) |
| Hjälp med att använda tillbakaskrivning av lösenord för att aktivera enkel inloggning |[Komma igång med lösenordshantering i Azure AD](authentication/quickstart-sspr.md)<br /><br />[Felsök tillbakaskrivning av lösenord](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Anvisningar om hur du använder identitetsleverantörer från tredje part för att aktivera enkel inloggning |[Lista över kompatibla utomstående leverantörer som kan användas för att aktivera enkel inloggning](https://aka.ms/ssoproviders) |
| Hur Windows 10-användare kan dra nytta av fördelarna med enkel inloggning via Azure AD Join |[Utöka Molnkapaciteten till Windows Join 10 enheter via Azure Active Directory](active-directory-azureadjoin-overview.md) |

Azure AD Connect är tillgängliga för [alla utgåvor av Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure AD Självbetjäning för återställning av lösenord är tillgängligt för [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Tillbakaskrivning av lösenord till lokal AD är en [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktion.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Villkorlig åtkomst: Tillämpa ytterligare säkerhetskrav för hög risk appar
När du ställer in enkel inloggning till dina appar och resurser som kan du sedan ytterligare skydda känsliga program genom att tillämpa specifika säkerhetskrav på varje logga in på appen. Du kan exempelvis använda Azure AD så att all åtkomst till en viss app alltid kräva multifaktorautentisering, oavsett om huruvida appen stöder funktionen innately begäran. Ett annat vanligt exempel av villkorlig åtkomst är att kräva att användare är ansluten till organisationens betrott nätverk för att komma åt ett särskilt känsliga program.

| Artikel Guide |  |
|:---:| --- |
| En introduktion till funktionerna som villkorlig åtkomst som erbjuds i Azure AD och Office 365 och Intune |[Hantera risker med villkorlig åtkomst](active-directory-conditional-access-azure-portal.md) |
| Hur du aktiverar villkorlig åtkomst för följande typer av resurser |[Villkorlig åtkomst för SaaS-appar](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Villkorlig åtkomst för Office 365-tjänster](active-directory-conditional-access-device-policies.md)<br /><br />[Villkorlig åtkomst för lokala program](active-directory-conditional-access-azure-portal.md)<br /><br />[Villkorlig åtkomst för lokala program som publicerats via Azure AD App-Proxy](application-proxy-enable-remote-access-sharepoint.md) |
| Hur du registrerar enheter med Azure Active Directory för att aktivera principer för enhetsbaserad villkorlig åtkomst |[Översikt över Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Så här aktiverar du automatisk Enhetsregistrering för domänanslutna Windowsenheter](active-directory-conditional-access-automatic-device-registration.md)<br />– [Steg för Windows 8.1-enheter](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Steg för Windows 7-enheter](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Hur du använder Microsoft Authenticator-appen för tvåstegsverifiering | [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

Villkorlig åtkomst är en [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktion.

## <a name="apps--azure-ad"></a>Appar och Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud App Discovery: Hitta vilka SaaS-appar som används i din organisation
Cloud App Discovery hjälper IT-avdelningar information om vilka SaaS-appar används i hela organisationen. Det kan Mät appanvändning och dess popularitet så att den kan fastställa vilka appar kommer mest nytta av som ansluts under IT-kontroll och integreras med Azure AD.

| Artikel Guide |  |
|:---:| --- |
| En allmän översikt över hur det fungerar |[Hitta ej sanktionerad molnprogram med Cloud App Discovery](manage-apps/cloud-app-discovery.md) |
| En mer grundlig genomgång i hur det fungerar med svar på frågor om sekretess |[Säkerhet och överväganden för sekretess](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Vanliga frågor och svar |[Vanliga frågor och svar för Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Självstudier för distribution av Cloud App Discovery |[Handboken för distribution](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Distributionsguiden för System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Installera på proxyservrar med anpassade portar](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Ändringsloggen för uppdateringar av Cloud App Discovery-agenten |[Ändringslogg](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery är en [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktion.

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatiskt etablera och ta bort etableringen av användarkonton i SaaS-appar
Automatisera skapande, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Matchar och synkronisera befintliga identiteter mellan Azure AD och din SaaS-appar och kontrollera åtkomsten genom att automatiskt inaktivera konton när användarna lämnar organisationen.

| Artikel Guide |  |
|:---:| --- |
| Läs mer om hur det fungerar och vanliga frågor och svar |[Automatisera användaren etablering och avetablering för SaaS-appar](active-directory-saas-app-provisioning.md) |
| Konfigurera hur information mappas mellan Azure AD och SaaS-appar |[Anpassa attributmappning](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Skriva uttryck för attributmappning](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Så här aktiverar du Automatisk etablering till någon app som har stöd för protokollet SCIM |[Konfigurera automatisk Användaretablering till någon SCIM-Enabled-App](active-directory-scim-provisioning.md) |
| Rapport om och felsökning av användaretablering |[Rapportering om automatisk användaretablering](active-directory-saas-provisioning-reporting.md)<br><br>[Felsökning av användaretablering](active-directory-application-provisioning-content-map.md) |
| Begränsa vem som hämtar tilldelas en applikation baserat på deras attributvärden |[Omfångsfilter](active-directory-saas-scoping-filters.md) |

Automatisk användaretablering är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för obegränsade program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), sedan kan du [använda grupper för att hantera vilka användare få etableras](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Skapa program som integreras med Azure AD
Om din organisation utvecklar eller underhålla line-of-business (LoB)-program, eller om du utvecklar en app med kunder som använder Azure Active Directory följande kurser hjälper integrerar du dina program med Azure AD.

| Artikel Guide |  |
|:---:| --- |
| Riktlinjer för både IT-proffs och programutvecklare på integrera appar med Azure AD |[IT Pro's Guide för utveckling av program för Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Utvecklarhandbok för Azure Active Directory](active-directory-developers-guide.md) |
| Hur programmet leverantörer kan lägga till sina appar i Appgalleriet för Azure AD |[Visar en lista över ditt program i Azure Active Directory-Programgalleriet](active-directory-app-gallery-listing.md) |
| Hantera åtkomst till utvecklade program med Azure Active Directory |[Så här aktiverar du Användartilldelning för utvecklade program](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Tilldela användare till din App](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Tilldela gruppen till din App](active-directory-applications-guiding-developers-assigning-groups.md) |

Om du utvecklar konsumentinriktade program du vill utnyttja [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) så att du inte behöver utveckla egna identitetssystem för att hantera dina användare. [Läs mer](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Med hjälp av grupper och självbetjäningsportalen för att hantera som har åtkomst till vilka appar
För att hjälpa dig att hantera som ska ha åtkomst till vilka resurser, kan du ange tilldelningar och behörigheter i stor skala med hjälp av grupper i Azure Active Directory. IT kan välja att aktivera självbetjäningsfunktioner så att användare kan bara begära tillstånd när det behövs.

| Artikel Guide |  |
|:---:| --- |
| En översikt över Azure AD access management-funktioner |[Introduktion till att hantera åtkomst till appar](active-directory-managing-access-to-apps.md)<br /><br />[Hur Access Management arbetar i Azure AD](active-directory-manage-groups.md)<br /><br />[Använda grupper för att hantera åtkomst till SaaS-program](active-directory-accessmanagement-group-saasapps.md) |
| Aktivera tjänsten hantering av appar och grupper |[Självbetjäning programhantering](active-directory-self-service-application-access.md)<br /><br />[Self-Service Group Management](active-directory-accessmanagement-self-service-group-management.md) |
| Instruktioner för att konfigurera dina grupper i Azure AD |[Så här skapar du säkerhetsgrupper](active-directory-groups-create-azure-portal.md)<br /><br />[Hur du kan ange ägare för en grupp](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Hur du använder gruppen ”Alla användare”](active-directory-accessmanagement-dedicated-groups.md) |
| Använda dynamiska grupper för att automatiskt fylla i gruppmedlemskap med attributbaserad medlemskapsregler |[Dynamiska gruppmedlemskap: Avancerade regler](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Felsökning av dynamiska gruppmedlemskap](active-directory-accessmanagement-troubleshooting.md) |

Hantering av grupp-baserade program är tillgängligt för [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Grupphantering via självbetjäning, självbetjäning programhantering och dynamiska grupper är [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktioner.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B-samarbete: Aktivera partneråtkomst till program
Om ditt företag samarbetar med andra företag, är det troligt att du behöver hantera partneråtkomst till företagets program. Azure Active Directory B2B-samarbete ger ett enkelt och säkert sätt att dela dina appar med partners.

| Artikel Guide |  |
|:---:| --- |
| En översikt över Azure AD för olika funktioner som kan hjälper dig att hantera externa användare som partners, kunder, osv. |[Jämförelse av funktioner för att hantera externa identiteter i Azure AD](active-directory-b2b-compare-external-identities.md) |
| En introduktion till B2B-samarbete och hur du kommer igång |[Enkel, säker, molnet Partner integrering med Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory B2B-samarbete](active-directory-b2b-collaboration-overview.md) |
| En mer grundlig genomgång i Azure AD B2B-samarbete och hur du använder det. |[B2B-samarbete: Så här fungerar det](active-directory-b2b-how-it-works.md)<br /><br />[Aktuella begränsningar i Azure AD B2B-samarbete](active-directory-b2b-current-limitations.md)<br /><br />[Detaljerad genomgång av Azure AD B2B-samarbete](active-directory-b2b-detailed-walkthrough.md) |
| För referensartiklar med teknisk information om hur fungerar Azure AD B2B-samarbete |[CSV-filformat för att lägga till Partneranvändarna](active-directory-b2b-references-csv-file-format.md)<br /><br />[Användarattribut som påverkas av Azure AD B2B-samarbete](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Tokenformat för användare för Partner användare](active-directory-b2b-references-external-user-token-format.md) |

B2B-samarbete är tillgänglig för [alla utgåvor av Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Åtkomstpanelen: En portal för att komma åt appar och självbetjäningsfunktioner
Azure AD-åtkomstpanelen är där slutanvändarna kan starta sina appar och åtkomst till de självbetjäningsfunktioner som tillåter dem att hantera sina appar och gruppmedlemskap. Utöver åtkomstpanelen inkluderas andra alternativ för att komma åt SSO-aktiverade appar i listan nedan.

| Artikel Guide |  |
|:---:| --- |
| En jämförelse av de olika alternativ som är tillgängliga för distribution av appar för enkel inloggning för användare |[Distribuera Azure AD-integrerade program till användare](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| En översikt över åtkomstpanelen och dess mobila motsvarande MyApps |[Introduktion till åtkomstpanelen och MyApps](active-directory-saas-access-panel-introduction.md)<br />– [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Hur du kommer åt Azure AD-appar från Office 365-webbplatsen |[Med hjälp av Office 365 App starta](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Hur du kommer åt Azure AD-appar från den mobila appen Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />– [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Hur du kommer åt Azure AD-appar med djuplänkar för att initiera enkel inloggning |[Hämta inloggning Direktlänkar till dina appar](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Åtkomstpanelen är tillgänglig för [alla utgåvor av Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Rapporter: Enkelt granska ändringar i appen och övervaka inloggningar till appar
Azure Active Directory innehåller flera rapporter och aviseringar för att övervaka din organisation åtkomst till program. Du kan ta emot aviseringar om avvikande inloggningar till dina appar och du kan följa när och varför en användare åtkomst till ett program har ändrats.

| Artikel Guide |  |
|:---:| --- |
| En översikt över rapportfunktionerna i Azure Active Directory |[Komma igång med Azure AD-rapportering](active-directory-reporting-getting-started.md) |
| Så här övervakar inloggningar och appanvändning användare |[Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md) |
| Spåra ändringar i som kan komma åt ett visst program |[Azure Active Directory-Granskningsrapporthändelser](active-directory-reporting-audit-events.md) |
| Exportera data för de här rapporterna till din önskade verktyg med Reporting-API |[Komma igång med Azure AD Reporting API](active-directory-reporting-api-getting-started.md) |

Att se vilka rapporter inkluderas i olika versioner av Azure Active Directory, [Klicka här](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Se också
[Vad är Azure Active Directory?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
