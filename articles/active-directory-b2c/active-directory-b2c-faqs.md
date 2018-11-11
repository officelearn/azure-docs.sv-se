---
title: Vanliga frågor om Azure Active Directory B2C | Microsoft Docs
description: Vanliga frågor (och svar FAQ) om Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8cec3d3695eaff8719757dd022951b969de99f6a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51010651"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Vanliga frågor (och svar FAQ) 
Den här sidan får du svar på vanliga frågor om Azure Active Directory (Azure AD) B2C. Kontrollera tillbaka för uppdateringar.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Varför kan jag inte använda Azure AD B2C-tillägget i Azure-portalen?
Det finns två vanliga orsaker till varför Azure AD-tillägget inte fungerar för dig.  Azure AD B2C kräver din användarroll i katalogen är global administratör.  Kontakta administratören om du tror att du ska ha åtkomst.  Om du har behörigheter som global administratör kan du kontrollera att du är i en Azure AD B2C-katalog och inte en Azure Active Directory-katalog.  Du kan se anvisningar för [skapar en Azure AD B2C-klient](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan jag använda Azure AD B2C-funktioner i min befintliga, anställd-baserad Azure AD-klient?
Azure AD och Azure AD B2C är separata produkterbjudanden och kan inte finnas i samma klient.  En Azure AD-klient representerar en organisation.  En Azure AD B2C-klient representerar en samling med identiteter som ska användas med förlitande parters program.  Azure AD B2C kan federera Azure AD att tillåta autentisering av anställda i en organisation med anpassade principer (i allmänt tillgänglig förhandsversion).

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kan jag använda Azure AD B2C för att tillhandahålla social inloggning (Facebook och Google +) i Office 365?
Azure AD B2C kan inte användas för att autentisera användare för Microsoft Office 365.  Azure AD är Microsofts lösning för hantering av personalens åtkomst till SaaS-appar och har funktioner som utformats för detta ändamål, till exempel licensierings- och villkorlig åtkomst.  Azure AD B2C tillhandahåller en plattform för identitets- och för att skapa webbprogram och mobilappar.  När Azure AD B2C är konfigurerad för att federera till en Azure AD-klient, hanterar Azure AD-klienten Medarbetaråtkomst till program som förlitar sig på Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Vad är lokala konton i Azure AD B2C? Hur skiljer de från arbets-eller skolkonton i Azure AD?
I en Azure AD-klient, användare som tillhör klientorganisationen logga in med en e-postadress i formatet `<xyz>@<tenant domain>`.  Den `<tenant domain>` är en av de verifierade domänerna i klienten eller första `<...>.onmicrosoft.com` domän. Den här typen av konto är ett arbets- eller skolkonto konto.

I en Azure AD B2C-klient, de flesta appar vill att användaren ska logga in med valfri godtycklig e-postadress (till exempel joe@comcast.net, bob@gmail.com, sarah@contoso.com, eller jim@live.com). Den här typen av konto är ett lokalt konto.  Vi stöder också godtyckliga användarnamn som lokala konton (till exempel Johan, bob, sarah eller jim). Du kan välja något av dessa två typer av lokala konton när du konfigurerar identitetsprovidrar för Azure AD B2C i Azure-portalen. I din Azure AD B2C-klient klickar du på **identitetsprovidrar** och välj sedan **användarnamn** under lokala konton. 

Användarkonton för program måste alltid skapas via en registreringsprincip registrering eller inloggning – i principen eller genom att använda Azure AD Graph API. Användarkonton som skapats i Azure-portalen används bara för att administrera klienten.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Vilka leverantörer via sociala du stöder nu? Vilka som kommer att stödja i framtiden?
Vi stöder för närvarande Facebook, Google +, LinkedIn, Amazon, Twitter (förhandsversion), WeChat (förhandsversion), Weibo (förhandsversion) och QT (förhandsversion). Vi lägger till stöd för andra populära sociala identitetsleverantörer baserat på kundernas behov.

Azure AD B2C har också lagt till stöd för [anpassade principer](active-directory-b2c-overview-custom.md).  Dessa [anpassade principer](active-directory-b2c-overview-custom.md) att en utvecklare att skapa sina egna princip som med alla identitetsprovider som stöder [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) eller SAML. 

Kom igång med anpassade principer genom att läsa vår [anpassad princip startpaket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan jag konfigurera omfång för att samla in mer information om användare från olika leverantörer via sociala?
Nej. Standard-omfattningar som används för vår uppsättning sociala identitetsleverantörer som stöds är:

* Facebook: e-post
* Google +: e-post
* Microsoft-konto: openid e-postprofil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Har mitt program att köras på Azure för att den fungerar med Azure AD B2C?
Nej, kan du vara värd för ditt program var som helst (i molnet eller lokalt). Allt som behövs för att interagera med Azure AD B2C är möjligheten att skicka och ta emot HTTP-förfrågningar på offentligt tillgängliga slutpunkter.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Jag har flera Azure AD B2C-klienter. Hur kan jag hantera dem på Azure portal?
Innan du öppnar ”Azure AD-B2C” i menyn till vänster på Azure-portalen, måste du växla till den katalog som du vill hantera.  Växla kataloger genom att klicka på din identitet uppe till höger på Azure-portalen och välj sedan en katalog i nedrullningsbara menyn som visas.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hur anpassar jag verifiering e-postmeddelanden (innehållet och ”från”: fält) skickas av Azure AD B2C?
Du kan använda den [funktionen för företagsanpassning](../active-directory/fundamentals/customize-branding.md) att anpassa innehållet i e-postmeddelanden för verifiering. Mer specifikt kan du anpassa dessa två element för e-postmeddelandet:

* **Banderoll logotyp**: visas längst ned till höger.
* **Bakgrundsfärg**: visas högst upp.

    ![Skärmbild av ett anpassat bekräftelsemeddelande](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

E-signaturen innehåller Azure AD B2C-klientens namn som du angav när du först skapade Azure AD B2C-klient. Du kan ändra namnet med de här anvisningarna:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som Global administratör.
1. Öppna den **Azure Active Directory** bladet.
1. Klicka på den **egenskaper** fliken.
1. Ändra den **namn** fält.
1. Klicka på **Spara** längst upp på sidan.

Det finns för närvarande inget sätt att ändra den ”från”: i e-postmeddelandet.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hur kan jag migrera min befintliga användarnamn, lösenord och profiler från min databas till Azure AD B2C?
Du kan använda Azure AD Graph API för att skriva din Migreringsverktyg. Se den [användaren Migreringsguide](active-directory-b2c-user-migration.md) mer information.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Vilka lösenordsprincip används för lokala konton i Azure AD B2C?
Lösenordsprinciper för lokala konton för Azure AD B2C bygger på principen för Azure AD. Azure AD B2C användarens registrering, registrering eller inloggning och lösenord och Återställ principer använder ”starka” lösenordssäkerhet och löper ut eventuella lösenord. Läs den [Azure AD-lösenordsprincip](https://msdn.microsoft.com/library/azure/jj943764.aspx) för mer information. Information om kontolåsningar och lösenord finns i [hanterar hot mot resurser och data i Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan jag använda Azure AD Connect för att migrera konsumentidentiteter som lagras på min lokala Active Directory till Azure AD B2C?
Nej, Azure AD Connect är inte avsedd att fungera med Azure AD B2C. Överväg att använda den [Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) för användarmigrering.  Se den [användaren Migreringsguide](active-directory-b2c-user-migration.md) mer information.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Öppnar min app in Azure AD B2C-sidor i en iFrame?
Nej, kan Azure AD B2C sidor inte öppnas i en iFrame av säkerhetsskäl.  Vår tjänst kommunicerar med webbläsaren om du vill förbjuda iFrames.  Säkerhets-communityn i allmän och en OAUTH2-specifikationen rekommenderar mot att använda iFrames för identitetsupplevelser på grund av risken för Klicka fästpunkter.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Fungerar Azure AD B2C med till exempel Microsoft Dynamics CRM-system?
Integrering med Microsoft Dynamics 365-portalen är tillgänglig.  Se [konfigurerar Dynamics 365-portalen att använda Azure AD B2C för autentisering](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Har Azure AD B2C fungerar med lokal SharePoint 2016 eller tidigare?
Azure AD B2C är inte avsedd för SharePoint extern delning av partner scenariot; Se [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) i stället.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Bör jag använda Azure AD B2C eller B2B för att hantera externa identiteter?
Den här artikeln om [externa identiteter](../active-directory/active-directory-b2b-compare-external-identities.md) vill veta mer om att tillämpa lämpliga funktioner på dina externa identity-scenarier.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Vilka rapporterings- och granskning funktioner Azure AD B2C tillhandahåller? Är de samma som Azure AD Premium?
Nej, stöder inte Azure AD B2C med samma uppsättning rapporter som Azure AD Premium. Men det finns många gemensamma utformning gör:

* **Logga in rapporter** ger en post för varje inloggning med minskad information.
* **Granskningsrapporter** är både administrativa aktiviteter samt programaktivitet. 
* **Användningsrapporter** är antalet användare, antal inloggningar och mängden MFA. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan jag översätter Användargränssnittet för sidor som hanteras av Azure AD B2C? Vilka språk stöds?
Visst!  Läs mer om [språkanpassning](active-directory-b2c-reference-language-customization.md), vilket är i offentlig förhandsversion.  Vi ger översättningar för 36 språk och du kan åsidosätta valfri sträng för att passa dina behov.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Kan jag använda min egen URL: er i min registrering och inloggning sidor som hanteras av Azure AD B2C? Exempelvis kan jag ändra URL: en från login.microsoftonline.com till login.contoso.com?
För närvarande inte. Den här funktionen finns i vår översikt. Verifiera din domän i den **domäner** fliken i Azure-portalen inte uppnå det här målet.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hur tar jag bort min Azure AD B2C-klient?
Följ dessa steg för att ta bort din Azure AD B2C-klient:

1. Ta bort alla principer i din Azure AD B2C-klient.
1. Nu logga in på den [Azure-portalen](https://portal.azure.com/) som administratör för prenumerationen. (Använd samma arbets- eller skolkonto eller samma Microsoft-konto som användes för att registrera dig för Azure.)
1. Växla till Azure AD B2C-klient som du vill ta bort.
2. Gå till Active Directory-menyn till vänster.
3. Välj **Användare och grupper**.
4. Markera varje användare i sin tur (Uteslut du för närvarande är inloggad som administratör för prenumerationen användaren). Klicka på **ta bort** längst ned på sidan och klicka på **Ja** när du tillfrågas.
5. Klicka på den **appregistreringar**.
6. Välj program, kallat **b2c-extensions-app**. Klicka på **ta bort** och klicka på **Ja** när du tillfrågas.
7. Välj **Översikt**.
8. Klicka på **ta bort katalogen**. Följ anvisningarna på skärmen för att slutföra processen.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Får jag Azure AD B2C som en del av Enterprise Mobility Suite?
Nej, Azure AD B2C är en betala per användning Azure-tjänsten och är inte en del av Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hur gör jag för att rapportera problem med Azure AD B2C?
Se [filen supportbegäranden för Azure Active Directory B2C](active-directory-b2c-support.md).
