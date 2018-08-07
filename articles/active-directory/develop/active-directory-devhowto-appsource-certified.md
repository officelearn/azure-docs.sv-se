---
title: Hämta certifierad för Azure Active Directory AppSource | Microsoft Docs
description: Information om hur du får ditt program AppSource-certifierad för Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/03/2017
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 8b23d99b838449681f83ff2e88bd96ee90502404
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578865"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Så här hämtar du AppSource-certifierat för Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) är ett mål för företagsanvändare att identifiera, testa och hantera line-of-business SaaS-program (fristående SaaS och tillägg till befintliga Microsoft SaaS-produkter).

Om du vill visa en fristående SaaS-program på AppSource, måste programmet godkänna enkel inloggning från arbetskonton från alla företag eller organisation som har Azure Active Directory. Logga in igen måste använda den [OpenID Connect](v1-protocols-openid-connect-code.md) eller [OAuth 2.0](v1-protocols-oauth-code.md) protokoll. SAML-integrering är inte godkänd för AppSource-certifiering.

## <a name="guides-and-code-samples"></a>Guider och kodexempel
Om du vill lära dig om hur du integrerar ditt program med Azure Active Directory med öppna ID connect, följa våra guider och kodexempel i den [Utvecklarhandbok för Azure Active Directory](azure-ad-developers-guide.md#get-started "Kom igång med Azure AD för utvecklare").

## <a name="multi-tenant-applications"></a>Program för flera innehavare

Ett program som stöder inloggningar från användare från alla företag eller din organisation som har Azure Active Directory utan en separat instans, konfiguration eller distribution kallas en *program med flera innehavare*. AppSource rekommenderar att implementera program med flera innehavare för att aktivera den *klickar du på* kostnadsfria utvärderingsversionen.

För att aktivera flera innehavare för ditt program:
- Ange `Multi-Tenanted` egenskap `Yes` på din programregistrering informationen i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (som standard konfigureras program som har skapats i Azure Portal som *enda klient*)
- Uppdatera koden för att skicka begäranden till den '`common`' slutpunkt (uppdatera slutpunkten från *https://login.microsoftonline.com/{yourtenant}* till *https://login.microsoftonline.com/common*)
- För vissa plattformar, t.ex. ASP.NET, måste du också uppdatera koden för att godkänna flera certifikatutfärdare

Läs mer om flera innehavare: [så här loggar du in alla Azure Active Directory (AD)-användare med programmönstret för flera innehavare](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Enda klient program
Program som bara godkänna inloggningar från användare av en definierad Azure Active Directory-instans kallas *enda klient programmet*. Externa användare (inklusive arbete eller skola konton från andra organisationer eller personligt konto) kan logga in i en enda klient program när du lägger till varje användare som *gästkontot* till Azure Active Directory-instans som den programmet har registrerats. Du kan lägga till användare som gästkonton till en Azure Active Directory via den [ *Azure AD B2B-samarbete* ](../b2b/what-is-b2b.md) - och det kan göras [programmässigt](../b2b/code-samples.md). När du lägger till en användare som gästkontot till en Azure Active Directory, skickas ett e-postinbjudan till den användare som har att tacka ja till inbjudan genom att klicka på länken i e-postinbjudan. Inbjudningar som skickas till en ytterligare användare i en organisationen som bjuder in som också är medlem i partnerorganisationen behöver inte acceptera inbjudan för att logga in.

Enda klient program kan du aktivera den *kontakta mig* upplevelse, men om du vill aktivera enkelklickning / kostnadsfria utvärderingen som AppSource rekommenderar aktivera flera innehavare för ditt program i stället.


## <a name="appsource-trial-experiences"></a>AppSource utvärderingsversion upplevelser

### <a name="free-trial-customer-led-trial-experience"></a>Kostnadsfri utvärderingsversion (kund-ledda utvärderingsversionen) 
Den *ledda av kunden utvärderingsversion* är den upplevelse som AppSource rekommenderar som den erbjuder ett enda musklick åtkomst till programmet. Under en illustration av hur den här upplevelsen ser ut:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Användare hittar ditt program i AppSource Web Site</li><li>Väljer alternativet ”kostnadsfri utvärdering”</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource omdirigerar användaren till en URL i din webbplats</li><li>Din webbplats startar den <i>single-sign-on</i> automatiskt (på sidhämtning)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Användaren omdirigeras till Microsoft på inloggningssidan</li><li>Användaren anger autentiseringsuppgifter och logga in</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Användaren lämnar samtycke för programmet</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Inloggningen är klar och användaren omdirigeras tillbaka till din webbplats</li><li>Användaren börjar den kostnadsfria utvärderingsversionen</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontakta mig (partnerledd utvärderingsversion experience)
Den *partner utvärderingsversionen* kan användas när en manuell eller en långsiktig åtgärd som ska hända att etablera användaren / företaget: till exempel programmet behöver för att etablera virtuella datorer, databasinstanser eller åtgärder som tar lång tid att slutföra. I det här fallet när användaren väljer den *begär utvärderingsversion* knappen och fyller i ett formulär, skickar AppSource användarens kontaktinformation. Vid mottagning av den här informationen kan du sedan etablera miljön och skicka instruktionerna för användaren på hur du kommer åt utvärderingen:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Användare kan hitta ditt program AppSource-webbplats</li><li>Kontakta mig alternativet automatiskt</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Fyller i ett formulär med kontaktinformation</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Du får information om användare</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Konfigurationsmiljö</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Kontakta användaren med utvärderingsversion information</td>
        </tr>
        </table><br/><br/>
        <ul><li>Du får användarens information och installationsprogrammet utvärderingsversion instans</li><li>Du skickar du hyperlänken för att komma åt ditt program till användaren</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Användaren har åtkomst till ditt program och slutföra processen single-sign-on</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Användaren lämnar samtycke för programmet</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Inloggningen är klar och användaren omdirigeras tillbaka till din webbplats</li><li>Användaren börjar den kostnadsfria utvärderingsversionen</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mer information
Läs mer om AppSource utvärderingen [videon](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar program som stöder Azure Active Directory-inloggningar finns i [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Information om hur du lista dina SaaS-program i AppSource, gå finns i [AppSource partnerinformation](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Support
Azure Active Directory-integrering, använder vi [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) till communityn för att tillhandahålla stöd för. 

Vi rekommenderar starkt att du först ställa dina frågor i Stack Overflow och bläddra bland befintliga problem om du vill se om någon har bett din fråga innan. Se till att dina frågor eller kommentarer är märkta med [ `[azure-active-directory]` och `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: azure-ad-developers-guide.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: azure-ad-developers-guide.md#get-started


<!--Image references-->