---
title: Hur du hämtar AppSource certifierad för Azure Active Directory | Microsoft Docs
description: Information om hur du hämtar programmet AppSource certifierad för Azure Active Directory.
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
ms.openlocfilehash: 844c19803f57987f4435d013cd12aa3d1ef8aeb8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Hur du hämtar AppSource certifierade för Azure Active Directory
[Microsoft AppSource](https://appsource.microsoft.com/) är mål för användare i verksamheten att upptäcka, försök och hantera line-of-business SaaS-program (fristående SaaS och tillägg till befintliga Microsoft SaaS-produkter).

Om du vill visa en fristående SaaS-program på AppSource måste ditt program acceptera enkel inloggning från arbetskonton från alla företag eller organisation som har Azure Active Directory. Logga in processen måste använda den [OpenID Connect](./active-directory-protocols-openid-connect-code.md) eller [OAuth 2.0](./active-directory-protocols-oauth-code.md) protokoll. SAML-integration accepteras inte för AppSource certifiering.

## <a name="guides-and-code-samples"></a>Guider och kodexempel
Om du vill lära dig hur du integrerar ditt program med Azure Active Directory med öppna ID connect, Följ våra guider och kodexempel i den [Utvecklarhandbok för Azure Active Directory](./active-directory-developers-guide.md#get-started "Kom igång med Azure AD för utvecklare").

## <a name="multi-tenant-applications"></a>Program med flera klienter

Ett program som accepterar inloggningar från användare från företaget eller organisationen som har Azure Active Directory utan att kräva en separat instans, konfiguration och distribution kallas en *flera innehavare programmet*. AppSource rekommenderar att program implementera flera innehavare för att aktivera den *enkelklickning* kostnadsfria utvärderingsversionen.

För att aktivera flera innehavare för ditt program:
- Ange `Multi-Tenanted` egenskapen `Yes` för programregistrering av på den [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) (som standard konfigureras program som har skapats i Azure Portal som *stöd för en innehavare*)
- Uppdatera din kod för att skicka begäranden till den '`common`-slutpunkt (uppdatera slutpunkten från *https://login.microsoftonline.com/{yourtenant}* till *https://login.microsoftonline.com/common*)
- För vissa plattformar som ASP.NET, måste du också uppdatera din kod för att godkänna flera certifikatutfärdare

Läs mer om multitenans: [loggar in alla Azure Active Directory (AD)-användare med flera innehavare programmönster](./active-directory-devhowto-multi-tenant-overview.md).

### <a name="single-tenant-applications"></a>Stöd för en innehavare program
Program som accepterar endast inloggningar från användare av en definierad Azure Active Directory-instans kallas *stöd för en innehavare programmet*. Externa användare (inklusive arbetet eller skolan konton från andra organisationer eller personligt konto) kan logga in på en enskild klient programmet när du lägger till varje användare som *gästkontot* till Azure Active Directory-instansen att programmet har registrerats. Du kan lägga till användare som gästkonton till en Azure Active Directory via den [ *Azure AD B2B-samarbete* ](../b2b/what-is-b2b.md) - och det kan göras [genom att programmera](../b2b/code-samples.md). När du lägger till en användare som gästkontot ett Azure Active Directory, skickas ett e-postinbjudan till användare måste acceptera inbjudan genom att klicka på länken i e-postinbjudan. Inbjudningar som skickas till en ytterligare användare i organisationen bjuda in som även är medlem i resurspartnerns organisation behöver inte acceptera en inbjudan för att logga in.

Stöd för en innehavare program kan du aktivera den *kontakta mig* upplevelse, men om du vill aktivera den enkelklickning / kostnadsfria utvärderingsversionen som AppSource rekommenderar aktiverar multitenans för ditt program i stället.


## <a name="appsource-trial-experiences"></a>AppSource utvärderingsversion upplevelser

### <a name="free-trial-customer-led-trial-experience"></a>Kostnadsfri utvärderingsversion (kund-ledde utvärderingsversionen) 
Den *kunden ledde utvärderingsversion* är upplevelsen AppSource rekommenderar som den erbjuder ett enda musklick åtkomst till ditt program. Under en illustration av hur det här upplevelsen ser ut som:<br/><br/>

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%"/><ul><li>Användaren hittar programmet i AppSource webbplats</li><li>'Kostnadsfri utvärderingsversion' alternativet väljs</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" /><ul><li>AppSource dirigerar användaren till en URL i din webbplats</li><li>Din webbplats startar den <i>single-sign-on</i> automatiskt (på sidinläsning)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%"/><ul><li>Användaren omdirigeras till Microsoft-inloggningssida</li><li>Användaren anger autentiseringsuppgifter för inloggning</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%"/><ul><li>Användaren lämnar samtycke för ditt program</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Inloggningen har slutförts och användaren omdirigeras till webbplatsen</li><li>Användaren startar den kostnadsfria utvärderingsversionen</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontakta mig (Partner ledde utvärderingsversionen)
Den *partner utvärderingsversionen* kan användas när en manuell eller en långsiktig åtgärd som ska hända att etablera användaren / företaget: exempelvis programmet måste etablera virtuella datorer, databasinstanser eller åtgärder som tar lång tid att slutföra. I det här fallet när användaren väljer den *begära utvärderingsversion* knappen och fyllning i ett formulär skickar AppSource användarens kontaktinformation. Vid mottagning av den här informationen kan du sedan etablera miljön och skicka instruktionerna till användaren på hur du kommer åt utvärderingsversionen:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%"/><ul><li>Användaren hittar programmet i AppSource-webbplats</li><li>Kontakta mig alternativet automatiskt</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%"/><ul><li>Fyller i ett formulär med kontaktinformation</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%"/></td>
            <td>Du får användarinformation</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%"/></td>
            <td>Konfigurationsmiljö</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%"/></td>
            <td>Kontakta användaren med testversionen information</td>
        </tr>
        </table><br/><br/>
        <ul><li>Du får användaren information och utvärderingsversion instans av installationsprogrammet</li><li>Du skickar hyperlänken ska få åtkomst till ditt program till användaren</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%"/><ul><li>Användaren har åtkomst till programmet och slutföra processen enkel inloggning</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%"/><ul><li>Användaren lämnar samtycke för ditt program</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%"/><ul><li>Inloggningen har slutförts och användaren omdirigeras till webbplatsen</li><li>Användaren startar den kostnadsfria utvärderingsversionen</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mer information
Läs mer om AppSource utvärderingsversionen [den här videon](https://aka.ms/trialexperienceforwebapps). 
 
## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar program som stöder Azure Active Directory inloggningar finns [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) 

- Gå finns information om hur du listar SaaS-program i AppSource [AppSource partnerinformation](https://appsource.microsoft.com/partners)


## <a name="get-support"></a>Support
Vi använder för Azure Active Directory-integrering [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource) med gemenskapen att tillhandahålla stöd för. 

Vi rekommenderar starkt att du be dina frågor på Stack Overflow först och bläddra befintliga problem för att se om någon har frågat din fråga innan. Kontrollera att dina frågor eller kommentarer är märkta med [ `[azure-active-directory]` och `[appsource]` ](http://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

<!--Reference style links -->
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]: ./active-directory-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Howto-Multitenant-Overview]: ./active-directory-devhowto-multi-tenant-overview.md
[AAD-QuickStart-Web-Apps]: ./active-directory-developers-guide.md#get-started


<!--Image references-->