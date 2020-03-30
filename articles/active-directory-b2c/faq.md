---
title: Vanliga frågor och svar (Vanliga frågor) för Azure Active Directory B2C
description: Svar på vanliga frågor om Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264406"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Vanliga frågor och svar (Vanliga frågor och svar)

Den här sidan besvarar vanliga frågor om Azure Active Directory B2C (Azure AD B2C). Håll koll på uppdateringar.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Varför kan jag inte komma åt Azure AD B2C-tillägget i Azure-portalen?

Det finns två vanliga orsaker till varför Azure AD-tillägget inte fungerar för dig. Azure AD B2C kräver att din användarroll i katalogen är global administratör. Kontakta administratören om du tycker att du ska ha åtkomst. Om du har globala administratörsbehörighet kontrollerar du att du är i en Azure AD B2C-katalog och inte en Azure Active Directory-katalog. Du kan se instruktioner för [hur du skapar en Azure AD B2C-klient](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan jag använda Azure AD B2C-funktioner i min befintliga, medarbetarbaserade Azure AD-klient?

Azure AD och Azure AD B2C är separata produkterbjudanden och kan inte samexistera i samma klientorganisation. En Azure AD-klient representerar en organisation. En Azure AD B2C-klient representerar en samling identiteter som ska användas med förlitande part-program. Genom att lägga till **ny OpenID Connect-provider** under **Azure AD B2C > Identity Providers** eller med anpassade principer kan Azure AD B2C federera till Azure AD som tillåter autentisering av anställda i en organisation.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kan jag använda Azure AD B2C för att tillhandahålla social inloggning (Facebook och Google+) i Office 365?

Azure AD B2C kan inte användas för att autentisera användare för Microsoft Office 365. Azure AD är Microsofts lösning för att hantera anställdas åtkomst till SaaS-appar och den har funktioner som utformats för detta ändamål, till exempel licensiering och villkorlig åtkomst. Azure AD B2C tillhandahåller en plattform för hantering av identitet och åtkomst för att skapa webb- och mobilappar. När Azure AD B2C är konfigurerat för att federera till en Azure AD-klient hanterar Azure AD-klienten anställd åtkomst till program som är beroende av Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Vad är lokala konton i Azure AD B2C? Hur skiljer de sig från arbets- eller skolkonton i Azure AD?

I en Azure AD-klient inloggaden är användare som tillhör `<xyz>@<tenant domain>`klientens inloggning med en e-postadress i formuläret . Den `<tenant domain>` är en av de verifierade domänerna i klienten eller den ursprungliga `<...>.onmicrosoft.com` domänen. Den här typen av konto är ett arbets- eller skolkonto.

I en Azure AD B2C-klient vill de flesta appar att användaren ska joe@comcast.net bob@gmail.comlogga sarah@contoso.comin jim@live.commed en godtycklig e-postadress (till exempel , , eller ). Den här typen av konto är ett lokalt konto. Vi stöder också godtyckliga användarnamn som lokala konton (till exempel Joe, Bob, Sarah eller Jim). Du kan välja en av dessa två lokala kontotyper när du konfigurerar identitetsleverantörer för Azure AD B2C i Azure-portalen. I din Azure AD B2C-klient väljer du **Identitetsleverantörer,** väljer **Lokalt konto**och väljer sedan **Användarnamn**.

Användarkonton för program kan skapas via ett användarflöde för registrering, registrering eller inloggning användarflöde, Microsoft Graph API eller i Azure-portalen.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Vilka leverantörer av social identitet stöder ni nu? Vilka planerar du att stödja i framtiden?

Vi stöder för närvarande flera sociala identitetsleverantörer, inklusive Amazon, Facebook, GitHub (förhandsvisning), Google, LinkedIn, Microsoft-konto (MSA), QQ (förhandsvisning), Twitter, WeChat (förhandsvisning) och Weibo (förhandsvisning). Vi utvärderar att lägga till stöd för andra populära leverantörer av social identitet baserat på kundernas efterfrågan.

Azure AD B2C stöder också [anpassade principer](custom-policy-overview.md). Med anpassade principer kan du skapa en egen princip för alla identitetsleverantörer som stöder [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) eller SAML. Kom igång med anpassade principer genom att kolla in vårt [anpassade startpaket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)för policy.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan jag konfigurera scope för att samla in mer information om konsumenter från olika leverantörer av social identitet?

Nej. Standardomfattningar som används för vår uppsättning leverantörer av social identitet som stöds är:

* Facebook: e-post
* Google+: e-post
* Microsoft-konto: openid e-postprofil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Måste mitt program köras på Azure för att det ska fungera med Azure AD B2C?

Nej, du kan vara värd för ditt program var som helst (i molnet eller lokalt). Allt som behövs för att interagera med Azure AD B2C är möjligheten att skicka och ta emot HTTP-begäranden på allmänt tillgängliga slutpunkter.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Jag har flera Azure AD B2C-klienter. Hur hanterar jag dem på Azure-portalen?

Innan du öppnar "Azure AD B2C" på vänster meny i Azure-portalen måste du växla till den katalog som du vill hantera. Växla kataloger genom att klicka på din identitet längst upp till höger i Azure-portalen och välj sedan en katalog i listrutan som visas.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hur anpassar jag verifieringsmeddelanden (innehållet och fältet "Från:") som skickas av Azure AD B2C?

Du kan använda [företagets varumärkesfunktion](../active-directory/fundamentals/customize-branding.md) för att anpassa innehållet i verifieringsmeddelanden. Närmare bestämt kan dessa två delar av e-postmeddelandet anpassas:

* **Banner logotyp:** Visas längst ner till höger.
* **Bakgrundsfärg**: Visas högst upp.

    ![Skärmbild av ett anpassat verifieringsmeddelande](./media/faq/company-branded-verification-email.png)

E-postsignaturen innehåller Azure AD B2C-klientens namn som du angav när du först skapade Azure AD B2C-klienten. Du kan ändra namnet med hjälp av följande instruktioner:

1. Logga in på [Azure-portalen](https://portal.azure.com/) som global administratör.
1. Öppna **Azure Active Directory**-bladet.
1. Klicka på fliken **Egenskaper.**
1. Ändra fältet **Namn.**
1. Klicka på **Spara** längst upp på sidan.

För närvarande finns det inget sätt att ändra fältet "Från:" i e-postmeddelandet.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hur migrerar jag mina befintliga användarnamn, lösenord och profiler från min databas till Azure AD B2C?

Du kan använda Microsoft Graph API för att skriva migreringsverktyget. Mer information finns i [guiden Användarmigrering.](user-migration.md)

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Vilket lösenordsanvändarflöde används för lokala konton i Azure AD B2C?

Användarflödet för Azure AD B2C-lösenord för lokala konton baseras på principen för Azure AD. Azure AD B2C:s användarflöden för registrering, registrering eller inloggning och återställning av lösenord använder den "starka" lösenordsstyrkan och upphör inte att gälla för några lösenord. Mer information finns i [Lösenordsprinciper och begränsningar i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy).

Information om kontoutelåsningar och lösenord finns i [Hantera hot mot resurser och data i Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan jag använda Azure AD Connect för att migrera konsumentidentiteter som lagras i min lokala Active Directory till Azure AD B2C?

Nej, Azure AD Connect är inte utformat för att fungera med Azure AD B2C. Överväg att använda [Microsoft Graph API](manage-user-accounts-graph-api.md) för användarmigrering. Mer information finns i [guiden Användarmigrering.](user-migration.md)

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Kan min app öppna Azure AD B2C-sidor i en iFrame?

Nej, av säkerhetsskäl kan Azure AD B2C-sidor inte öppnas inom en iFrame. Vår tjänst kommunicerar med webbläsaren för att förbjuda iFrames. Säkerhetsgemenskapen i allmänhet och OAUTH2-specifikationen rekommenderar mot att använda iFrames för identitetsupplevelser på grund av risken för klickkapning.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Fungerar Azure AD B2C med CRM-system som Microsoft Dynamics?

Det finns även integrering med Microsoft Dynamics 365 Portal. Se [Konfigurera Dynamics 365 Portal för att använda Azure AD B2C för autentisering](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Fungerar Azure AD B2C med SharePoint lokalt 2016 eller tidigare?

Azure AD B2C är inte avsett för sharepoint-scenariot för extern partnerdelning. se [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) i stället.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Ska jag använda Azure AD B2C eller B2B för att hantera externa identiteter?

Läs [Jämför B2B-samarbete och B2C i Azure AD](../active-directory/b2b/compare-with-b2c.md) om du vill veta mer om hur du använder lämpliga funktioner på dina externa identitetsscenarier.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Vilka rapporterings- och granskningsfunktioner tillhandahåller Azure AD B2C? Är de samma som i Azure AD Premium?

Nej, Azure AD B2C stöder inte samma uppsättning rapporter som Azure AD Premium. Det finns dock många likheter:

* **Inloggningsrapporter** ger en post för varje inloggning med reducerad information.
* **Granskningsrapporter** omfattar både administratörsaktivitet och programaktivitet.
* **Användningsrapporter** inkluderar antalet användare, antal inloggningar och volymen mfa.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan jag lokalisera användargränssnittet för sidor som betjänas av Azure AD B2C? Vilka språk stöds?

Ja, se [språkanpassning](user-flow-language-customization.md). Vi tillhandahåller översättningar för 36 språk, och du kan åsidosätta alla strängar som passar dina behov.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Kan jag använda mina egna webbadresser på mina registrerings- och inloggningssidor som betjänas av Azure AD B2C? Kan jag till exempel ändra webbadressen från contoso.b2clogin.com till login.contoso.com?

Inte för tillfället. Den här funktionen finns i vår färdplan. Att verifiera din domän på fliken **Domäner** i Azure-portalen uppnår inte det här målet. Men med b2clogin.com erbjuder vi en [neutral toppdomän](b2clogin.md), och därmed det yttre utseendet kan genomföras utan omnämnande av Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hur tar jag bort min Azure AD B2C-klient?

Följ dessa steg för att ta bort din Azure AD B2C-klient.

Du kan använda den aktuella **programupplevelsen** eller vår nya enhetliga **appregistreringar (Preview)** upplevelse. [Läs mer om den nya upplevelsen](https://aka.ms/b2cappregintro)

#### <a name="applications"></a>[Program](#tab/applications/)

1. Logga in på [Azure-portalen](https://portal.azure.com/) som *prenumerationsadministratör*. Använd samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att registrera dig för Azure.
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Ta bort alla **användarflöden (principer)** i din Azure AD B2C-klient.
1. Ta bort alla **program som** du har registrerat i din Azure AD B2C-klient.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Under **Hantera** väljer du **Användare**.
1. Välj varje användare i tur och ordning (uteslut den *användare av prenumerationsadministratör som* du för närvarande är inloggad som). Välj **Ta bort** längst ned på sidan och välj **JA** när du uppmanas att göra det.
1. Under **Hantera**väljer du **Appregistreringar** (eller **Appregistreringar (Äldre).**
1. Välj **Visa alla program**
1. Välj programmet **b2c-extensions-app**, välj **Ta bort**och välj sedan **Ja** när du uppmanas att göra det.
1. Under **Hantera**väljer du **Användarinställningar**.
1. Om det finns, under **LinkedIn-kontoanslutningar,** väljer du **Nej**och väljer sedan **Spara**.
1. Under **Hantera**väljer du **Egenskaper**
1. Under **Åtkomsthantering för Azure-resurser** väljer du **Ja** och väljer sedan **Spara**.
1. Logga ut från Azure-portalen och logga sedan in igen för att uppdatera din åtkomst.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Välj Ta bort **katalog**på sidan **Översikt** . Följ instruktionerna på skärmen för att slutföra processen.

#### <a name="app-registrations-preview"></a>[Appregistreringar (förhandsversion)](#tab/app-reg-preview/)

1. Logga in på [Azure-portalen](https://portal.azure.com/) som *prenumerationsadministratör*. Använd samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att registrera dig för Azure.
1. Välj **katalog + prenumerationsfilter** på den övre menyn och välj sedan den katalog som innehåller din Azure AD B2C-klient.
1. Välj Azure AD **B2C**på den vänstra menyn . Du kan också välja **Alla tjänster** och sök efter och välj Azure **AD B2C**.
1. Ta bort alla **användarflöden (principer)** i din Azure AD B2C-klient.
1. Välj **Appregistreringar (förhandsgranskning)** och välj sedan fliken **Alla program.**
1. Ta bort alla program som du har registrerat.
1. Ta bort **b2c-extensions-appen**.
1. Under **Hantera** väljer du **Användare**.
1. Välj varje användare i tur och ordning (uteslut den *användare av prenumerationsadministratör som* du för närvarande är inloggad som). Välj **Ta bort** längst ned på sidan och välj **Ja** när du uppmanas att göra det.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Under **Hantera**väljer du **Användarinställningar**.
1. Om det finns, under **LinkedIn-kontoanslutningar,** väljer du **Nej**och väljer sedan **Spara**.
1. Under **Hantera**väljer du **Egenskaper**
1. Under **Åtkomsthantering för Azure-resurser** väljer du **Ja** och väljer sedan **Spara**.
1. Logga ut från Azure-portalen och logga sedan in igen för att uppdatera din åtkomst.
1. Välj **Azure Active Directory** på menyn till vänster.
1. Välj Ta bort **katalog**på sidan **Översikt** . Följ instruktionerna på skärmen för att slutföra processen.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kan jag skaffa Azure AD B2C som en del av Enterprise Mobility Suite?

Nej, Azure AD B2C är en Azure-tjänst som gäller för varje betalning och ingår inte i Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hur rapporterar jag problem med Azure AD B2C?

Se [Filsupportbegäranden för Azure Active Directory B2C](support-options.md).
