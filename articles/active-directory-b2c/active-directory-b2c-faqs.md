---
title: "Vanliga frågor (FAQ) - Azure AD B2C | Microsoft Docs"
description: "Vanliga frågor och svar om Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 43c930c18c284a8e6c71174bcf905904001dcde2
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Vanliga frågor (FAQ) 
Den här sidan svar på vanliga frågor om Azure Active Directory (AD Azure) B2C. Hålla kontroll för uppdateringar.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kan jag använda Azure AD B2C-funktioner i Mina befintliga, medarbetare-baserad Azure AD-klient?
Azure AD och Azure AD B2C separat produkterbjudanden och kan inte finnas i samma klientorganisation.  En Azure AD-klient representerar en organisation.  En Azure AD B2C-klient representerar en samling med identiteter som ska användas med förlitande parters program.  Azure AD B2C kan federera till Azure AD som tillåter autentisering för anställda i en organisation med anpassade principer (i förhandsversion).

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Kan jag använda Azure AD B2C för att tillhandahålla inloggning via sociala medier (Facebook och Google +) i Office 365?
Azure AD B2C kan inte användas för att autentisera användare för Microsoft Office 365.  Azure AD är Microsofts lösning för hantering av medarbetarnas åtkomst till SaaS-appar och funktioner som är utformade för detta ändamål, till exempel licensiering och villkorlig åtkomst.  Azure AD B2C innehåller en identitets- och plattform för att skapa webb- och mobilprogram.  När Azure AD B2C är konfigurerad för att federera till en Azure AD-klient, hanterar Azure AD-klienten medarbetarnas åtkomst till program som förlitar sig på Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Vad är lokala konton i Azure AD B2C? Vad är de från arbets-eller skolkonton i Azure AD?
I en Azure AD-klient användare som tillhör innehavaren logga in med en e-postadress i formatet `<xyz>@<tenant domain>`.  Den `<tenant domain>` är en av de verifierade domänerna i innehavaren eller första `<...>.onmicrosoft.com` domän. Den här typen av konto är ett konto för arbetet eller skolan.

De flesta appar i Azure AD B2C-klient vill användaren att logga in med en godtycklig e-postadress (till exempel joe@comcast.net, bob@gmail.com, sarah@contoso.com, eller jim@live.com). Den här typen av konto är ett lokalt konto.  Vi stöder också godtycklig användarnamn som lokala konton (till exempel joe bob, Sara eller jim). Du kan välja något av dessa två typer av lokalt konto genom att konfigurera Azure AD B2C i Azure-portalen.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Vilka sociala identitetsleverantörer du stöder nu? Vilka som planerar att stödja i framtiden?
Vi stöder för närvarande Facebook, Google +, LinkedIn, Amazon, Twitter (förhandsgranskning), WeChat (förhandsgranskning), Weibo (förhandsversion) och QT (förhandsversion). Vi lägger till stöd för andra populära sociala identitetsleverantörer baserat på kundernas behov.

Azure AD B2C också har lagt till stöd för [anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Dessa [anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom) att en utvecklare att skapa sina egna princip som med alla identitetsleverantör som stöder [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) eller SAML. 

Kom igång med anpassade principer genom att checka ut våra [anpassad princip startpaket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kan jag konfigurera scope för att samla in mer information om användare från olika sociala identitetsleverantörer?
Nej, men den här funktionen finns på vår översikt. Standard-omfattningar som används för våra uppsättning sociala identitetsleverantörer som stöds är:

* Facebook: e-post
* Google +: e-post
* Microsoft-konto: openid e-postprofil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Har mitt program att köras i Azure för den fungerar med Azure AD B2C?
Nej, kan du värd för ditt program var som helst (i molnet eller lokalt). Allt som behövs för att interagera med Azure AD B2C är möjligheten att skicka och ta emot HTTP-förfrågningar på offentligt tillgänglig slutpunkter.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Jag har flera innehavare av Azure AD B2C. Hur kan jag hantera dem på Azure portal?
Innan du öppnar 'Azure AD B2C ”i vänster-menyn i Azure portal, måste du växla till den katalog som du vill hantera.  Växla kataloger genom att klicka på din identitet i övre högra hörnet i Azure-portalen och sedan välja en katalog i listrutan som visas.  En stegvis med bilder, finns i [navigera till Azure AD B2C inställningar](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hur anpassa bekräftelsemeddelanden (innehållet och ”från”: fältet) skickas av Azure AD B2C?
Du kan använda den [funktionen för företagsanpassning](../active-directory/customize-branding.md) att anpassa innehållet i e-post för verifiering. Mer specifikt kan du anpassa dessa två element för e-postmeddelandet:

* **Banderoll logotypen**: visas längst ned till höger.
* **Bakgrundsfärg**: visas högst upp.

    ![Skärmbild av en anpassad e-postmeddelandet](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

E-signaturen innehåller B2C-klientens namn som du angav när du först skapade B2C-klient. Du kan ändra namnet använder dessa instruktioner:

1. Logga in på den [Azure-portalen](https://portal.azure.com/) som administratör för prenumerationen.
1. Öppna den **Azure Active Directory** bladet.
1. Klicka på den **egenskaper** fliken.
1. Ändra den **namn** fältet.
1. Klicka på **spara** överst på sidan.

Det finns för närvarande inget sätt att ändra den ”från”: i e-postmeddelandet. Rösta på [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) du är intresserad av att anpassa brödtexten i e-postmeddelandet.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hur kan jag migrera Mina befintliga användarnamn, lösenord och profiler från databasen till Azure AD B2C?
Du kan använda Azure AD Graph API för att skriva Migreringsverktyget för. Finns det [användaren Migreringsguide](active-directory-b2c-user-migration.md) mer information.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Vilka lösenordsprincip används för lokala konton i Azure AD B2C?
Azure AD B2C lösenordsprincip för lokala konton baseras på principen för Azure AD. Azure AD B2C s registrering, registrering eller inloggning och lösenord och Återställ principer använder ”starka” lösenordssäkerhet och inte ut eventuella lösenord. Läs den [Azure AD-lösenordsprincip](https://msdn.microsoft.com/library/azure/jj943764.aspx) för mer information.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kan jag använda Azure AD Connect för att migrera konsumenten identiteter som lagras på min lokala Active Directory till Azure AD B2C?
Nej, Azure AD Connect är inte avsedd att fungera med Azure AD B2C. Överväg att använda den [Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) för användarmigrering.  Finns det [användaren Migreringsguide](active-directory-b2c-user-migration.md) mer information.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Öppnar min app in Azure AD B2C sidor i en iFrame?
Nej, av säkerhetsskäl, Azure AD B2C-sidor kan inte öppnas en iFrame.  Vår tjänst kommunicerar med webbläsare för att förhindra iFrames.  Säkerhets-community och OAUTH2-specifikationen rekommenderar mot med iFrames för identitetsupplevelser på grund av risken för Klicka fästpunkter.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Fungerar Azure AD B2C med till exempel Microsoft Dynamics CRM-system?
Integrering med Microsoft Dynamics 365-portalen är tillgänglig.  Se [konfigurerar Dynamics 365-portalen att använda Azure AD B2C för autentisering](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Har Azure AD B2C fungerar med SharePoint lokalt 2016 eller tidigare?
Azure AD B2C är inte avsedd för SharePoint extern delning av partner scenariot; Se [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) i stället.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Bör jag använda Azure AD B2C eller B2B för att hantera externa identiteter?
Den här artikeln om [externa identiteter](../active-directory/active-directory-b2b-compare-external-identities.md) att lära dig mer om att tillämpa lämpliga funktioner till externa identity-scenarier.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Vilka rapportering och granskning funktioner Azure AD B2C ger? Är de samma som i Azure AD Premium?
Nej, stöder inte Azure AD B2C samma uppsättning rapporter som Azure AD Premium. Det finns emellertid många gemensamma utformning gör:

* **Logga in rapporter** är bara tillgängliga i Azure-portalen (Azure Active Directory > aktivitet > inloggningar) och är inte tillgängliga via Graph API. De ger en post för varje inloggning med nedsatt information.
* **Granska rapporter** är bara tillgängliga i Azure-portalen (Azure Active Directory > aktivitet > granskningsloggar) och är inte tillgängliga via Graph API. De omfattar både administratörsaktivitet samt programaktivitet. 
* **Användningsrapporter** är bara tillgängliga via den [användning Reporting API](active-directory-b2c-reference-usage-reporting-api.md) och är inte tillgängliga via Azure portal. De är antalet användare, antal inloggningar och volym för Multifaktorautentisering. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kan jag lokalisera Användargränssnittet för sidor som hanteras av Azure AD B2C? Vilka språk som stöds?
Visst!  Läs mer om [språk anpassning](active-directory-b2c-reference-language-customization.md), som är tillgänglig som förhandsversion.  Vi ger översättningar för 36 språk och du kan åsidosätta valfri sträng så att de passar dina behov.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Kan jag använda min egen URL: er på sidorna registrering och inloggning som hanteras av Azure AD B2C? Till exempel ändrar URL: en från login.microsoftonline.com till login.contoso.com?
För närvarande inte. Den här funktionen finns på vår översikt. Verifiera din domän i den **domäner** fliken på den klassiska Azure-portalen inte utför det här målet.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hur tar jag bort min Azure AD B2C-klient?
Följ dessa steg för att ta bort din Azure AD B2C-klient:

1. Följ dessa steg för att [navigera till Azure AD B2C inställningar](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) på Azure-portalen.
1. Navigera till den **program**, **identitetsleverantörer**, och **alla principer** och ta bort alla poster i var och en av dem.
1. Nu logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/) som administratör för prenumerationen. (Använda samma arbets- eller skolkonto eller samma Microsoft-konto som du använde för att registrera dig för Azure.)
1. Gå till Active Directory-tillägget till vänster och klicka på din B2C-klient.
1. Klicka på den **användare** fliken.
1. Markera varje användare i sin tur (exkludera du för närvarande är inloggad som administratör för prenumeration användaren). Klicka på **ta bort** längst ned på sidan och klicka på **Ja** när du tillfrågas.
1. Klicka på den **program** fliken.
1. Välj **program som företaget äger** i den **visa** listrutan och klicka på bock.
1. Ett program kallas **b2c-tillägg-app**. Klicka på **ta bort** längst ned på sidan och klicka på **Ja** när du tillfrågas.
1. Navigera till Active Directory-tillägget igen och välj din B2C-klient.
1. Klicka på **ta bort** längst ned på sidan. Följ instruktionerna på skärmen för att slutföra processen.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kan jag få Azure AD B2C som en del av Enterprise Mobility Suite?
Nej, Azure AD B2C är en betala per användning i Azure-tjänsten och är inte en del av Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hur rapporterar problem med Azure AD B2C?
Se [filbegäranden stöd för Azure Active Directory B2C](active-directory-b2c-support.md).
