---
title: Så här skaffar du AppSource-certifierad för Azure Active Directory| Microsoft-dokument
description: Information om hur du får ditt program AppSource certifierat för Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 3ad4efa3b8126a9b9c6557822f61e3bfff3fe120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154890"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Så här skaffar du AppSource-certifierad för Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) är en destination för företagsanvändare att upptäcka, prova och hantera branschspecifika SaaS-program (fristående SaaS och tillägg till befintliga Microsoft SaaS-produkter).

Om du vill lista ett fristående SaaS-program på AppSource måste ditt program acceptera enkel inloggning från arbetskonton från alla företag eller organisationer som har Azure Active Directory (Azure AD). Inloggningsprocessen måste använda [OpenID Connect-](v1-protocols-openid-connect-code.md) eller [OAuth 2.0-protokollen.](v1-protocols-oauth-code.md) SAML-integrering accepteras inte för AppSource-certifiering.

## <a name="guides-and-code-samples"></a>Guider och kodexempel

Om du vill lära dig mer om hur du integrerar ditt program med Azure AD med Open ID connect följer du våra guider och kodexempel i [Azure Active Directory-utvecklarguiden](v1-overview.md#get-started "Komma igång med Azure AD för utvecklare").

## <a name="multi-tenant-applications"></a>Program för flera innehavare

Ett *program med flera innehavare* är ett program som accepterar inloggningar från användare från alla företag eller organisationer som har Azure AD utan att kräva en separat instans, konfiguration eller distribution. AppSource rekommenderar att program implementerar flera innehavare för att möjliggöra enstaka *klick* gratis provperiod.

Så här aktiverar du flerrättersinnehavare i ditt program:
1. Ange `Multi-Tenanted` egenskapen till `Yes` på programregistreringens information i [Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Som standard konfigureras program som skapas i *[Azure-portalen](#single-tenant-applications)* som en enda klient .
1. Uppdatera koden för att `common` skicka begäranden till slutpunkten. Det gör du genom att `https://login.microsoftonline.com/{yourtenant}` `https://login.microsoftonline.com/common*`uppdatera slutpunkten från till .
1. För vissa plattformar, till exempel ASP .NET, måste du också uppdatera koden för att acceptera flera utfärdare.

Mer information om flerargsinnehavare finns i Så här loggar du [in alla Azure Active Directory -användare (Azure AD) med hjälp av programmönstret för flera innehavare](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Program med en klient

Ett program med en enda klient är ett program som bara accepterar inloggningar från användare av en definierad Azure *AD-instans.* Externa användare (inklusive arbets- eller skolkonton från andra organisationer eller personliga konton) kan logga in på ett program med en enda klient efter att ha lagt till varje användare som gästkonto i Azure AD-instansen som programmet är registrerat. 

Du kan lägga till användare som gästkonton i Azure AD via [Azure AD B2B-samarbetet](../b2b/what-is-b2b.md) och du kan göra det [programmässigt](../../active-directory-b2c/code-samples.md). När du använder B2B kan användare skapa en självbetjäningsportal som inte kräver en inbjudan att logga in. Mer information finns i [Självbetjäningsportal för azure AD B2B-samarbete.](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal)

Program med en klient kan aktivera *Contact Me-upplevelsen,* men om du vill aktivera den kostnadsfria provupplevelsen som AppSource rekommenderar aktiverar du flera innehavare i ditt program i stället.

## <a name="appsource-trial-experiences"></a>Utvärderingsupplevelser för AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Gratis provperiod (kundledd provupplevelse)

Den kundledda utvärderingsversionen är den upplevelse som AppSource rekommenderar eftersom den ger en åtkomst med ett enda klick till ditt program. Följande exempel visar hur den här upplevelsen ser ut:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Användaren hittar ditt program på AppSource-webbplatsen</li><li>Väljer alternativet "Fri utvärderingsversion"</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource omdirigerar användaren till en URL på webbplatsen</li><li>Webbplatsen startar processen <i>med enkel inloggning</i> automatiskt (vid sidinläsning)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Användaren omdirigeras till Microsofts inloggningssida</li><li>Användaren tillhandahåller autentiseringsuppgifter för att logga in</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Användaren ger sitt samtycke till din ansökan</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Inloggningen slutförs och användaren omdirigeras tillbaka till din webbplats</li><li>Användaren startar den kostnadsfria provperioden</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontakta mig (partnerledd provupplevelse)

Du kan använda partnertestupplevelsen när en manuell eller en långsiktig åtgärd måste hända för att etablera användaren/företaget – till exempel måste ditt program etablera virtuella datorer, databasinstanser eller åtgärder som tar mycket tid att slutföra. I det här fallet, när användaren har valt knappen **Begär utvärdering** och fyller i ett formulär, skickar AppSource användarens kontaktinformation. När du får den här informationen etablerar du sedan miljön och skickar instruktionerna till användaren om hur du får tillgång till utvärderingsversionen:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Användaren hittar ditt program på AppSource-webbplatsen</li><li>Väljer alternativet Kontakta mig</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Fyller i ett formulär med kontaktinformation</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Du får användarinformation</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Konfigurera miljön</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Kontakta användaren med utvärderingsinformation</td>
        </tr>
        </table><br/><br/>
        <ul><li>Du får användarens information och installationstestinstans</li><li>Du skickar hyperlänken för att komma åt ditt program till användaren</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Användaren kommer åt ditt program och slutför en inloggningsprocessen</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Användaren ger sitt samtycke till din ansökan</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Inloggningen slutförs och användaren omdirigeras tillbaka till din webbplats</li><li>Användaren startar den kostnadsfria provperioden</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Mer information

Mer information om testupplevelsen för AppSource finns i [den här videon](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Efterföljande moment

- Mer information om hur du skapar program som stöder Azure AD-inloggningar finns i [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/v1-authentication-scenarios).
- Information om hur du listar ditt SaaS-program i AppSource finns i [Information om Partner för AppSource](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Få support

För Azure AD-integrering använder vi [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource) med communityn för att ge support.

Vi rekommenderar starkt att du ställer dina frågor om Stack Overflow först och bläddra bland befintliga problem för att se om någon har ställt din fråga tidigare. Kontrollera att dina frågor eller kommentarer [ `[azure-active-directory]` `[appsource]` ](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource)är taggade med och .

Använd följande kommentarsavsnitt för att ge feedback och hjälpa oss att förfina och forma vårt innehåll.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
