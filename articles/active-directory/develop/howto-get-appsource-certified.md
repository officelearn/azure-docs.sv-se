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
ms.date: 08/21/2018
ms.author: celested
ms.reviewer: andret
ms.custom: aaddev
ms.openlocfilehash: 603da0add9efba4e68088bf0eb6ab919ec9988e2
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955502"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Så här hämtar du AppSource-certifierat för Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) är ett mål för företagsanvändare att identifiera, testa och hantera line-of-business SaaS-program (fristående SaaS och tillägg till befintliga Microsoft SaaS-produkter).

Om du vill visa en fristående SaaS-program på AppSource, måste programmet godkänna enkel inloggning från arbetskonton från alla företag eller organisation som har Azure Active Directory (AD Azure). Logga in igen måste använda den [OpenID Connect](v1-protocols-openid-connect-code.md) eller [OAuth 2.0](v1-protocols-oauth-code.md) protokoll. SAML-integrering är inte godkänd för AppSource-certifiering.

## <a name="guides-and-code-samples"></a>Guider och kodexempel

Om du vill lära dig om hur du integrerar ditt program med Azure AD med öppna ID connect, följa våra guider och kodexempel i den [Utvecklarhandbok för Azure Active Directory](v1-overview.md#get-started "Kom igång med Azure AD för utvecklare").

## <a name="multi-tenant-applications"></a>Program för flera innehavare

En *program med flera innehavare* är ett program som accepterar inloggningar från användare från alla företag eller din organisation som har Azure AD utan att kräva en separat instans, konfiguration och distribution. AppSource rekommenderar att implementera program med flera innehavare för att aktivera den *klickar du på* kostnadsfria utvärderingsversionen.

Följ dessa steg om du vill aktivera flera innehavare för ditt program:
1. Ange `Multi-Tenanted` egenskap `Yes` på din programregistrering informationen i den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Som standard konfigureras program som har skapats i Azure-portalen som  *[enda klient](#single-tenant-applications)*.
1. Uppdatera koden för att skicka begäranden till den `common` slutpunkt. Gör detta genom att uppdatera slutpunkten från `https://login.microsoftonline.com/{yourtenant}` till `https://login.microsoftonline.com/common*`.
1. För vissa plattformar som ASP .NET, måste du också att uppdatera koden för att godkänna flera utfärdare.

Läs mer om flera innehavare, [så här loggar du in alla Azure Active Directory (Azure AD)-användare med programmönstret för flera innehavare](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Enda klient program

En *enda klient programmet* är ett program som accepterar endast inloggningar från användare av en definierad Azure AD-instans. Externa användare (inklusive arbets- eller skolkonton från andra organisationer eller personliga konton) kan logga in på en enda klient programmet när du lägger till varje användare som ett gästkonto i Azure AD-instans att programmet har registrerats. 

Du kan lägga till användare som gästkonton till Azure AD via den [Azure AD B2B-samarbete](../b2b/what-is-b2b.md) och du kan göra detta [programmässigt](../../active-directory-b2c/code-samples.md). När du använder B2B, kan användare skapa en självbetjäningsportal som inte kräver en inbjudan att logga in. Mer information finns i [självbetjäningsportalen för Azure AD B2B-samarbete registrering](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

Enda klient program kan du aktivera den *kontakta mig* upplevelse, men om du vill aktivera den enda Klicka/kostnadsfria utvärderingsversionen som AppSource rekommenderar aktivera flera innehavare för ditt program i stället.

## <a name="appsource-trial-experiences"></a>AppSource utvärderingsversion upplevelser

### <a name="free-trial-customer-led-trial-experience"></a>Kostnadsfri utvärderingsversion (kund-ledda utvärderingsversionen) 

Utvärderingsversionen av ledda av kunden är den upplevelse som AppSource rekommenderar som den erbjuder ett enda musklick åtkomst till programmet. Under en illustration av hur den här upplevelsen ser ut:<br/><br/>

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

Du kan använda utvärderingsversionen för partner när en manuell eller en långsiktig åtgärd som ska hända att etablera användare/företaget – till exempel ditt program behöver etablera virtuella datorer, databasinstanser eller åtgärder som tar lång tid att slutföra. I det här fallet när användaren väljer den **begär utvärderingsversion** knappen och fyller i ett formulär, skickar AppSource användarens kontaktinformation. När du får den här informationen kan du sedan etablera miljön och skicka instruktionerna för användaren på hur du kommer åt utvärderingen:<br/><br/>

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

- Mer information om hur du skapar program som stöder Azure AD-inloggningar finns i [autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Information om hur du lista dina SaaS-program i AppSource, gå finns i [AppSource partnerinformation](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Få support

Azure AD-integrering, använder vi [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) till communityn för att tillhandahålla stöd för. 

Vi rekommenderar starkt att du först ställa dina frågor i Stack Overflow och bläddra bland befintliga problem om du vill se om någon har bett din fråga innan. Se till att dina frågor eller kommentarer är märkta med [ `[azure-active-directory]` och `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och forma vårt innehåll.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started


<!--Image references-->
