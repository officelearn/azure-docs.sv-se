---
title: Artikel Index för programhantering i Azure Active Directory | Microsoft Azure
description: Lär dig hur du anpassar ett sista giltighetsdatum för federationscertifikat och förnya certifikat som snart upphör att gälla.
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
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: cd95a1f1e0631340fa9844fd31c3d8c0af1168dd
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347060"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Artikelindex för programhantering i Azure Active Directory
Den här sidan innehåller en fullständig lista över alla dokument som skrivits om de olika programrelaterade funktionerna i Azure Active Directory (AD Azure).

Det finns en kort introduktion till varje större funktionsområde, samt information om vilka artiklar för att läsa beroende på vilken information du söker.

## <a name="overview-articles"></a>Översikt Översiktsartiklar
Artiklarna nedan är bra startpunkter för de som vill bara ha en kort beskrivning av Azure AD application management-funktioner.

| Artikeln Guide |  |
|:---:| --- |
| En introduktion till hantering programproblem som löser Azure AD |[Hantera program med Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| En översikt över de olika funktionerna i Azure AD som härrör aktivering av enkel inloggning, definiera vem som har åtkomst till appar och hur användarna starta appar |[Programåtkomst och enkel inloggning i Azure Active Directory](manage-apps/what-is-single-sign-on.md) |
| En titt på de olika steg som ingår när de integrerar appar i din Azure AD |[Integrera Azure Active Directory med program](manage-apps/plan-an-application-integration.md)<br /><br />[Aktivera enkel inloggning för SaaS-appar](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Hantera åtkomst till appar](manage-apps/what-is-access-management.md) |
| En förklaring av hur appar representeras i Azure AD |[Hur och varför program läggs till Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Felsökningsartiklar
Det här avsnittet ger snabb åtkomst till relevanta felsökningsguider. Mer information om varje funktionsområde kan hittas på resten av den här sidan.

| Funktionsområde |  |
|:---:| --- |
| Federerad enkel inloggning |[Felsöka SAML-baserad enkel inloggning](develop/howto-v1-debug-saml-sso-issues.md) |
| Lösenordsbaserad enkel inloggning |[Felsöka Access Panel-tillägg för Internet Explorer](manage-apps/manage-access-panel-browser-extension.md) |
| Programproxy |[Approxy felsökningsguiden](manage-apps/application-proxy-troubleshoot.md) |
| Enkel inloggning mellan lokala AD och Azure AD |[Felsökning av synkronisering av Lösenordshash](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[Felsöka tillbakaskrivning av lösenord](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dynamiska gruppmedlemskap |[Felsöka dynamiska gruppmedlemskap](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Enkel inloggning (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federerad enkel inloggning: Logga in på många appar med hjälp av en identitet
Tillåter användare att komma åt en mängd olika appar och tjänster med endast en uppsättning autentiseringsuppgifter för enkel inloggning. Federation är en metod som du kan aktivera enkel inloggning. När användarna försöker logga in på federerade appar de kommer få omdirigeras till organisationens officiella inloggningssidan renderas av Azure Active Directory och sedan omdirigeras tillbaka till appen efter lyckad autentisering.

| Artikeln Guide |  |
|:---:| --- |
| En introduktion till federation och andra typer av inloggning |[Enkel inloggning med Azure AD](manage-apps/what-is-single-sign-on.md) |
| Tusentals SaaS-appar som redan är integrerade i Azure AD med förenklad konfiguration för enkel inloggning steg |[Komma igång med Azure AD-programgalleriet](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Fullständig lista över förintegrerade appar som har stöd för Federation](saas-apps/tutorial-list.md)<br /><br />[Lägg till din App i Azure AD-Appgalleri](develop/howto-app-gallery-listing.md) |
| Fler än 150 app självstudier om hur du konfigurerar enkel inloggning för appar som [Salesforce](saas-apps/salesforce-tutorial.md), [ServiceNow](saas-apps/servicenow-tutorial.md), [Google Apps](saas-apps/google-apps-tutorial.md), [Workday](saas-apps/workday-tutorial.md), och många fler |[Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](saas-apps/tutorial-list.md) |
| Hur du manuellt konfigurera och anpassa din konfiguration för enkel inloggning |[Hur du konfigurerar federerad enkel inloggning till appar som inte ingår i Azure Active Directory-Programgalleriet](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)<br /><br />[Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](active-directory-saml-claims-customization.md) |
| Felsökningsguide för federerade appar som använder SAML-protokoll |[Felsöka SAML-baserad enkel inloggning](develop/howto-v1-debug-saml-sso-issues.md) |
| Så här konfigurerar du appens certifikat upphör att gälla och förnya certifikat |[Hantera certifikat för federerad enkel inloggning i Azure Active Directory](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

Federerad enkel inloggning är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för ett obegränsat antal program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), kan du [använda grupper för att tilldela åtkomst till federerade program](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Lösenordsbaserad enkel inloggning: konto delning och enkel inloggning för icke-federerade appar
Om du vill aktivera enkel inloggning till program som inte stöder federation, erbjuder Azure AD funktioner för lösenord som kan på ett säkert sätt lagra lösenord till SaaS-appar och automatiskt registrera användare i dessa appar. Du kan enkelt distribuera autentiseringsuppgifter för nyligen skapade konton och dela team konton med flera personer. Användarna behöver inte nödvändigtvis har uppgifter de konton som de får åtkomst till.

| Artikeln Guide |  |
|:---:| --- |
| En introduktion till hur lösenordsbaserad SSO fungerar och en kort teknisk översikt |[Lösenordsbaserad enkel inloggning med Azure AD](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| En sammanfattning av de scenarier som rör konto delning och hur de här problemen blir lösta av Azure AD |[Dela konton med Azure AD](active-directory-sharing-accounts.md) |
| Ändra lösenordet för vissa appar automatiskt med jämna mellanrum |[Automatisk förnyelse av lösenord (förhandsversion)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Distribution och felsökning för Internet Explorer-versionen av hanteringstillägget för Azure AD-lösenord |[Så här distribuerar du Access Panel-tillägg för Internet Explorer med hjälp av en Grupprincip](manage-apps/deploy-access-panel-browser-extension.md)<br /><br />[Felsöka Access Panel-tillägg för Internet Explorer](manage-apps/manage-access-panel-browser-extension.md) |

Lösenordsbaserad enkel inloggning är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för ett obegränsat antal program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), kan du [använda grupper för att tilldela åtkomst till program](#managing-access-to-applications). Automatiserad förnyelse av lösenord är ett [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktionen.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>App Proxy: Åtkomst med enkel inloggning och fjärråtkomst till lokala program
Om du har program i ditt privata nätverk som ska användas av användare och enheter utanför nätverket, kan du använda Azure AD-programproxy för att aktivera säker åtkomst till dessa appar.

| Artikeln Guide |  |
|:---:| --- |
| Översikt över Azure AD Application Proxy och hur det fungerar |[Att tillhandahålla säker fjärråtkomst till lokala program](manage-apps/application-proxy.md) |
| Självstudier om hur du konfigurerar Application Proxy och hur du publicerar din första app |[Hur du konfigurerar Azure AD App Proxy](manage-apps/application-proxy-enable.md)<br /><br />[Tyst installation App Proxy-koppling](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Hur du publicerar program med hjälp av App Proxy](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[Hur du använder ett eget domännamn](manage-apps/application-proxy-configure-custom-domain.md) |
| Så här aktiverar du enkel inloggning och villkorlig åtkomst för appar som publiceras med App Proxy |[Enkel inloggning med programproxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Villkorlig åtkomst och Application Proxy](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Vägledning om hur du använder Application Proxy för följande scenarier |[Ge stöd för interna klientprogram](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Ge stöd för anspråksmedvetna program](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Ge stöd för program som publicerats på separata nätverk och platser](manage-apps/application-proxy-connector-groups.md) |
| Felsökningsguide för Application Proxy |[Approxy felsökningsguiden](manage-apps/application-proxy-troubleshoot.md) |

Programproxyn är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för ett obegränsat antal program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), kan du [använda grupper för att tilldela åtkomst till program](#managing-access-to-applications).

Du kanske också är intresserad av [Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), vilket gör att du kan migrera dina lokala program till Azure när du fortfarande uppfyller identitet behoven för dessa program.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Aktivera enkel inloggning mellan Azure AD och lokalt AD
Om organisationen upprätthåller en Windows Server Active Directory lokalt tillsammans med Azure Active Directory i molnet, och sedan vill du förmodligen att aktivera enkel inloggning mellan dessa två system. Azure AD Connect (verktyget som integrerar de här två system tillsammans) tillhandahåller flera alternativ för att konfigurera enkel inloggning: upprätta federation med AD FS eller en annan federationsleverantör eller aktivera Lösenordssynkronisering.

| Artikeln Guide |  |
|:---:| --- |
| En översikt över alternativ för enkel inloggning som erbjuds i Azure AD Connect, samt information om hur du hanterar hybridmiljöer |[Användaren loggar in alternativ i Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Allmänna riktlinjer för att hantera miljöer med både lokala Active Directory och Azure Active Directory |[Designöverväganden för Azure AD-Hybrid Identity](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md) |
| Hjälp med att använda Lösenordssynkronisering för att aktivera enkel inloggning |[Implementera Lösenordssynkronisering med Azure AD Connect](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Felsöka Lösenordssynkronisering](https://support.microsoft.com/kb/2855271) |
| Vägledning om hur du använder tillbakaskrivning av lösenord för att aktivera enkel inloggning |[Komma igång med lösenordshantering i Azure AD](authentication/quickstart-sspr.md)<br /><br />[Felsök tillbakaskrivning av lösenord](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Hjälp med att använda Identitetsproviders från tredje part för att aktivera enkel inloggning |[Lista över kompatibla från tredje part Identitetsprovidrar som kan användas för att aktivera enkel inloggning](https://aka.ms/ssoproviders) |
| Hur Windows 10-användare kan utnyttja fördelarna med enkel inloggning via Azure AD Join |[Utöka Molnkapaciteten till Windows Join 10-enheter via Azure Active Directory](active-directory-azureadjoin-overview.md) |

Azure AD Connect är tillgänglig för [alla utgåvor av Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure AD självbetjäningsportalen för lösenordsåterställning är tillgängligt för [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Tillbakaskrivning av lösenord till en lokal AD är en [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktionen.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Villkorlig åtkomst: Tillämpa ytterligare säkerhetskrav för appar med hög risk
När du konfigurerar enkel inloggning till dina appar och resurser kan skydda du sedan ytterligare känsliga program genom att tillämpa specifika krav på varje logga in på appen. Du kan exempelvis använda Azure AD för att begäran att all åtkomst till en viss app alltid kräva multifaktorautentisering, oavsett om appen innately stöder funktionen. Ett annat vanligt exempel på villkorlig åtkomst är att kräva att användare är ansluten till organisationens betrott nätverk för att komma åt ett särskilt känsliga program.

| Artikeln Guide |  |
|:---:| --- |
| En introduktion till funktioner för villkorlig åtkomst som erbjuds i Azure AD, Office 365 och Intune |[Hantera risker med villkorlig åtkomst](active-directory-conditional-access-azure-portal.md) |
| Hur du aktiverar villkorlig åtkomst för följande typer av resurser |[Villkorlig åtkomst för SaaS-appar](conditional-access/app-based-conditional-access.md)<br /><br />[Villkorlig åtkomst för Office 365-tjänster](active-directory-conditional-access-device-policies.md)<br /><br />[Villkorlig åtkomst för lokala program](active-directory-conditional-access-azure-portal.md)<br /><br />[Villkorlig åtkomst för lokala program publiceras via Azure AD App Proxy](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Hur du registrerar enheter med Azure Active Directory för att aktivera principer för enhetsbaserad villkorlig åtkomst |[Översikt över Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Så här aktiverar du automatisk Enhetsregistrering för domänanslutna Windows-enheter](active-directory-conditional-access-automatic-device-registration.md)<br />– [Steg för Windows 8.1-enheter](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Steg för Windows 7-enheter](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Hur du använder appen Microsoft Authenticator för tvåstegsverifiering | [Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

Villkorlig åtkomst är en [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktionen.

## <a name="apps--azure-ad"></a>Appar och Azure AD
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>Cloud Discovery: Hitta vilka SaaS-appar som används i din organisation
Cloud Discovery analyserar dina trafikloggar mot Microsoft Cloud App Securitys cloud app-katalogen för över 16 000 moln-appar som rangordnas och poängsätts baserat på fler än 70 riskfaktorer använder för att ge dig med pågående insyn i molnet, Shadow IT och risken Skuggkopia IT utgör i din organisation.

| Artikeln Guide |  |
|:---:| --- |
| En allmän översikt över hur det fungerar |[Konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatiskt etablera och avetablera användarkonton i SaaS-appar
Automatisera den genereringen, underhållet och borttagningen av användaridentiteter i SaaS-program, till exempel Dropbox, Salesforce, ServiceNow med mera. Matcha och synkronisera befintliga identiteter mellan Azure AD och din SaaS-appar och styra åtkomsten genom att automatiskt inaktivera konton när användarna lämnar organisationen.

| Artikeln Guide |  |
|:---:| --- |
| Lär dig mer om hur det fungerar och få svar på vanliga frågor |[Automatisera användaren etablering och avetablering för SaaS-appar](manage-apps/user-provisioning.md) |
| Konfigurera hur information mappas mellan Azure AD och din SaaS-app |[Anpassa attributmappningar](manage-apps/customize-application-attributes.md)<br><br>[Skriva uttryck för attributmappningar](manage-apps/functions-for-customizing-application-data.md) |
| Så här aktiverar du Automatisk etablering för alla appar som stöder SCIM-protokollet |[Konfigurera automatisk Användaretablering till någon SCIM-Enabled-App](manage-apps/use-scim-to-provision-users-and-groups.md) |
| Rapportera om och felsöka användaretablering |[Rapportering om automatisk användaretablering](manage-apps/check-status-user-account-provisioning.md)<br><br>[Felsöka användaretablering](active-directory-application-provisioning-content-map.md) |
| Begränsa vilka hämtar tilldelas en applikation baserat på deras attributvärden |[Omfångsfilter](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) |

Automatisk användaretablering är tillgänglig för alla utgåvor av Azure AD för upp till tio appar per användare. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) har stöd för ett obegränsat antal program. Om din organisation har [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) eller [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), kan du [använda grupper för att hantera vilka användare ha blivit etablerad](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Skapa program som integreras med Azure AD
Om din organisation utvecklar eller underhållet av line-of-business (LoB) appar, eller om du utvecklar en app med kunder som använder Azure Active Directory följande självstudier hjälper integrera du dina program med Azure AD.

| Artikeln Guide |  |
|:---:| --- |
| Vägledning för både IT-proffs och utvecklare av företagsprogram om integrering av appar med Azure AD |[IT Pro's Guide för att utveckla program för Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Utvecklarguide för Azure Active Directory](develop/azure-ad-developers-guide.md) |
| Hur programmet leverantörer kan lägga till sina appar till Azure AD-Appgalleri |[Lista ditt program i Azure Active Directory-Programgalleriet](develop/howto-app-gallery-listing.md) |
| Hantera åtkomst till utvecklade program med Azure Active Directory |[Så här aktiverar du Användartilldelning för utvecklade program](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Tilldela användare till din App](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Tilldela gruppen till din App](active-directory-applications-guiding-developers-assigning-groups.md) |

Om du utvecklar konsumentinriktade program kan du kanske vill använda [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) så att du inte behöver att utveckla dina egna identitetssystem för att hantera dina användare. [Läs mer](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Med hjälp av grupper och Självbetjäning för att hantera vem som har åtkomst till vilka appar
För att hjälpa dig att hantera vem som ska ha åtkomst till vilka resurser, kan du ange tilldelningar och behörigheter i stor skala med grupper i Azure Active Directory. IT kan välja att aktivera självbetjäning funktioner så att användare kan bara begära behörighet när de behöver den.

| Artikeln Guide |  |
|:---:| --- |
| En översikt över Azure AD access management-funktioner |[Introduktion till hantering av åtkomst till appar](manage-apps/what-is-access-management.md)<br /><br />[Hur Access Management arbetar i Azure AD](fundamentals/active-directory-manage-groups.md)<br /><br />[Hur du använder grupper för att hantera åtkomst till SaaS-program](users-groups-roles/groups-saasapps.md) |
| Aktivera självbetjäning hanteringen av appar och grupper |[Hantering av programåtkomst via självbetjäning](active-directory-self-service-application-access.md)<br /><br />[Självbetjäning, grupphantering](users-groups-roles/groups-self-service-management.md) |
| Anvisningar för att skapa grupper i Azure AD |[Så här skapar du säkerhetsgrupper](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[Hur du kan ange ägare för en grupp](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Hur du använder gruppen ”Alla användare”](active-directory-accessmanagement-dedicated-groups.md) |
| Använda dynamiska grupper för att automatiskt fylla i gruppmedlemskap med attributbaserad medlemskapsregler |[Dynamiskt gruppmedlemskap: Avancerade regler](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Felsöka dynamiska gruppmedlemskap](users-groups-roles/groups-troubleshooting.md) |

Gruppbaserad programåtkomsthantering är tillgänglig för [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) och [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Grupphantering via självbetjäning, hantering av programåtkomst via självbetjäning och dynamiska grupper är [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funktioner.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B-samarbete: Ge partneråtkomst till program
Om ditt företag har inlett ett samarbete med andra företag, är det troligt att du behöver hantera partneråtkomst till företagets program. Azure Active Directory B2B-samarbete ger ett enkelt och säkert sätt att dela dina appar med partner.

| Artikeln Guide |  |
|:---:| --- |
| En översikt över annan Azure AD-funktioner som kan hjälper dig att hantera externa användare som partners, kunder, osv. |[Jämför funktioner för att hantera externa identiteter i Azure AD](active-directory-b2b-compare-external-identities.md) |
| En introduktion till B2B-samarbete och hur du kommer igång |[Enkel, säker Cloud Partner-integrering med Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Azure Active Directory B2B-samarbete](active-directory-b2b-collaboration-overview.md) |
| En djupdykning i Azure AD B2B-samarbete och hur du använder den |[B2B-samarbete: Hur det fungerar](active-directory-b2b-how-it-works.md)<br /><br />[Aktuella begränsningar för Azure AD B2B-samarbete](active-directory-b2b-current-limitations.md)<br /><br />[Detaljerad genomgång av Azure AD B2B-samarbete](active-directory-b2b-detailed-walkthrough.md) |
| För referensartiklar med teknisk information om hur fungerar Azure AD B2B-samarbete |[CSV-filformat för att lägga till Partneranvändare](active-directory-b2b-references-csv-file-format.md)<br /><br />[Användarattribut som påverkas av Azure AD B2B-samarbete](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Tokenformat för användare för Partneranvändare](active-directory-b2b-references-external-user-token-format.md) |

B2B-samarbete är för närvarande tillgängligt för [alla utgåvor av Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Åtkomstpanelen: En portal för att komma åt appar och självbetjäningsfunktioner
Azure AD-åtkomstpanelen är där slutanvändare kan starta sina appar och komma åt de självbetjäningsfunktioner att de kan hantera sina appar och gruppmedlemskap. Förutom åtkomstpanelen, som andra alternativ för att komma åt SSO-aktiverade appar ingår i listan nedan.

| Artikeln Guide |  |
|:---:| --- |
| En jämförelse av de olika alternativ som är tillgängliga för att distribuera appar för enkel inloggning för användare |[Distribuera Azure AD-integrerade program till användare](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| En översikt över åtkomstpanelen och dess mobila motsvarande MyApps |[Introduktion till åtkomstpanelen och MyApps](user-help/active-directory-saas-access-panel-introduction.md)<br />– [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Hur du kommer åt Azure AD-appar från Office 365-webbplats |[Med hjälp av Office 365-Appstartaren](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Hur du kommer åt Azure AD-appar från den mobila appen Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />– [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Hur du kommer åt Azure AD-appar med djuplänkar för att initiera enkel inloggning |[Hämta inloggning Direktlänkar till dina appar](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Åtkomstpanelen är tillgänglig för [alla utgåvor av Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Rapporter: Enkelt granska ändringar i appen och övervaka inloggningar till appar
Azure Active Directory innehåller flera rapporter och aviseringar för att övervaka din organisation åtkomst till program. Du kan ta emot aviseringar om avvikande inloggningar till dina appar och du kan spåra när och varför en användares åtkomst till ett program har ändrats.

| Artikeln Guide |  |
|:---:| --- |
| En översikt över rapportfunktionerna i Azure Active Directory |[Komma igång med Azure AD-rapportering](active-directory-reporting-getting-started.md) |
| Så här övervakar du inloggningar och appanvändning användare |[Visa åtkomst- och användningsrapporter](active-directory-view-access-usage-reports.md) |
| Spåra ändringar i som kan komma åt ett visst program |[Azure Active Directory Granskningsrapporthändelser](active-directory-reporting-audit-events.md) |
| Exportera data för de här rapporterna till dina önskade verktyg med Reporting-API |[Komma igång med Azure AD Reporting-API](active-directory-reporting-api-getting-started.md) |

Att se vilka rapporter som ingår i olika utgåvor av Azure Active Directory, [Klicka här](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Se också
[Vad är Azure Active Directory?](fundamentals/active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
