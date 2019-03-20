---
title: Vad är MyApps-portalen – Azure Active Directory? | Microsoft Docs
description: Lär dig hur du använder varianter av MyApps-portalen (webbläsare, Android-app, iPhone och iPad-app) för att få åtkomst till SaaS-appar.
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47c624a9f1d3989e9146f7c32745ca892f6467e0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58113812"
---
# <a name="what-is-the-myapps-portal"></a>Vad är MyApps-portalen?

Om du har ett arbets eller skolkonto i Azure Active Directory (AD Azure), kan du kan använda Mina appar webbaserad portal för att visa och starta molnbaserade program som en Azure AD-administratör har gett dig åtkomst till. Du kan också använda självbetjäning och apphanteringsfunktionerna via MyApps-portalen.

MyApps-portalen är separat från Azure-portalen. Det kräver inte du har en Azure-prenumeration.

![MyApps-portalen][1] genom att använda MyApps-portalen kan du redigera vissa av dina profilinställningar och gör följande:

- Ändra lösenordet som associeras med ett arbets- eller skolkonto.

- Redigera inställningar för återställning av lösenord.

- Redigera inställningar för kontakt- och inställningar som rör Multi-Factor authentication (för konton som har krävs för att använda den av en administratör).

- Visa information, till exempel användar-ID, alternativ e-, mobil- och office telefonnummer och enheter-konto.

- Visa och starta molnbaserade program som Azure AD-administratör har gett dig åtkomst till. 

- Lokal hantera grupper. Administratörer kan skapa och hantera säkerhetsgrupper och begära medlemskap i säkerhetsgrupper i Azure AD. Mer information finns i [grupphantering för användare i Azure AD](../users-groups-roles/groups-self-service-management.md) och [hantera dina grupper](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Få åtkomst till MyApps-portalen

Du kan komma åt MyApps-portalen genom att gå till `https://myapps.microsoft.com`.

Om du har anpassade anpassning som konfigurerats för din inloggningssida kan du läsa in anpassning genom att lägga till organisationens domän i URL: en (till exempel `https://myapps.microsoft.com/<your domain>.com`).

Du kan använda alla aktiva eller verifierat domännamn som har konfigurerats i din Azure-portalen enligt nedan: ![Wingtip Toys domännamn][2]  

Distribuera URL: en för alla användare som loggar in till program som är integrerade med Azure AD.

## <a name="authentication"></a>Authentication

För att nå MyApps-portalen, måste du autentiseras via ett arbets- eller skolkonto konto i Azure AD. Du kan autentiseras till Azure AD direkt. Du kan också om en organisation har konfigurerat federation med hjälp av Active Directory Federation Services (AD FS) eller andra tekniker, kan du autentiseras av Windows Server Active Directory.

Om du har en prenumeration på Azure eller Office 365 och du har använt Azure-portalen eller ett Office 365-program, kan du visa listan med program utan att logga in igen. Om du inte har autentiserats, uppmanas du att logga in med användarnamnet och lösenordet för ditt konto i Azure AD. Om din organisation har konfigurerat federation, räcker att skriva användarnamnet.

När du har autentiserats kan interagera du med de program som din administratör har integrerats med katalogen. Läs hur du integrerar program med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Webbläsarkrav

Minst MyApps-portalen kräver en webbläsare som stöder JavaScript och har aktiverat CSS. Om du vill logga in på program via lösenordsbaserad enkel inloggning (SSO), måste du ha MyApps portaltillägg som installerats i din webbläsare. Tillägget laddas ned automatiskt när du väljer ett program som har konfigurerats för lösenordsbaserad SSO.

Installationsprogrammet är arkitektur-specifika. Om du klickar på länken kan hämta du bara installationsprogrammet för OS-arkitektur som du för närvarande körs på. Om du är administratör för distribution av program, kontrollera att du besöker hämtningslänken från ett 64-bitars och 32-bitars enhet för att hämta båda installationsprogram.


MyApps portaltillägg är nu tillgänglig för:
- **Microsoft Edge**: på Windows 10 Anniversary Edition eller senare. 
- **Chrome**: på Windows 7 eller senare, och i Mac OS X eller senare.
- **Firefox 26.0 eller senare**: på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.
- **Internet Explorer 11**: på Windows 7 eller senare (begränsat stöd).

## <a name="my-apps-secure-sign-in-extension"></a>Mina appar skyddat inloggningstillägg
Om du vill logga in på lösenordsbaserad enkel inloggning, måste du använda tillägget. När tillägget har installerats kan du logga in till den att tillhandahålla ytterligare funktioner genom att välja **logga in att komma igång**. 

- Du kan logga in på en app direkt med hjälp av appens **inloggnings-URL**. När du använder appens Webbadress tillägget åtgärden identifieras och ger dig alternativet att logga in från tillägget.
- Du kan starta en av dina appar från MyApps-portalen med hjälp av den *Snabbsökning* funktion i tillägget. 
- Tillägget visar de senaste tre program som du startade i **nyligen använda** avsnittet.
- Du kan använda interna företagets URL: er samtidigt remote via [Application Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Ytterligare funktioner är endast tillgängligt för Microsoft Edge, Chrome och Firefox.
> 
> Du kan hämta tillägget direkt från följande platser:
> - [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
> - [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
> - [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Om du använder en Mina appar-URL än `https://myapps.microsoft.com`, konfigurera standard-URL genom att göra följande:
1. När du arbetar *inte* inloggad på tillägget, högerklicka på tilläggsikonen.
2. På menyn, Välj **Mina appar URL**.
3. Välj standard-URL.
4. Välj tillägg.
5. Välj **logga in att komma igång**.

För att använda företagets interna URL: er samtidigt remote med hjälp av tillägget måste du göra följande:
1. [Konfigurera programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) på din klient.
2. [Publicera programmet](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) och URL: en via programproxy.
3. Installera tillägget och logga in till den genom att välja inloggning att komma igång.
4. Nu kan du bläddra till interna företagets URL även under remote.

> [!NOTE]
> Du kan också stänga av automatisk omdirigering till företagets URL: er genom att välja kugghjulet på huvudmenyn och välja **av** för företagets interna URL: en omdirigering av alternativet.


## <a name="mobile-app-support"></a>Stöd för mobila appar

Azure Active Directory-teamet publicerar den mobila appen för Mina appar. När du installerar appen kan logga du in på lösenordsbaserad SSO-program i iOS och Android-enheter.

> [!NOTE]
> Du kan logga in på program som stöder federation med Azure AD (inklusive Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 och mycket mer än 70 andra) på i stort sett alla webbläsare, på alla enheter, utan att behöva en plugin-programmet eller mobila app. Som ska användas på en mobil enhet, den andra [MyApps-portalen inträffar](https://myapps.microsoft.com/) även kräver inte den mobila appen för Mina appar.

### <a name="my-apps-for-iphone-and-ipad"></a>Mina appar för iPhone och iPad

Mina appar för iOS stöds på en iPhone eller iPad med iOS version 7 eller senare.  

Det är tillgängligt på den [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Mina appar för iOS][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Intune Managed Browser för Mina appar

Mina appar är också integrerat med Intune Managed Browser. Intune Managed Browser för iOS och Android-enheter hjälper dig att säkrare och navigerar webbsidor som kan innehålla företagsinformation, vilket ger en säkrare webbsurfning upplevelse.  

Du kan komma till Mina appar både på startsidan för Managed Browser från dina bokmärken, vilket innebär att det finns färre klick för att uppnå dina appar.

Intune Managed Browser finns på den [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) och [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Hanterad webbläsare för Mina appar][5]    


## <a name="tips-for-testing-the-user-experience"></a>Tips för att testa användarupplevelsen

Om du är administratör för Azure och du har loggat in på Azure Portal med ett konto i katalogen, är du automatiskt inloggad MyApps-portalen som ditt aktuella konto. Den här vyn visas alla program som har tilldelats dig.

Testa i en *olika* användare konto, gör du följande:

1. Längst upp till höger på Azure portal eller MyApps-portalen, Välj **logga ut**. 
2. Gå till den [MyApps-portalen](https://myapps.microsoft.com).
3. På sidan logga in skriver du användarnamnet och lösenordet för kontot i din katalog som du vill testa.


## <a name="starting-applications"></a>Start av program

Det här avsnittet beskrivs flera typer av program som kan visas på MyApps-portalen.

### <a name="office-365-applications"></a>Office 365-program

Om din organisation använder Office 365-program och du har licenser för dessa, Office 365-program visas på din MyApps-portalen.

När du väljer ett program sida vid sida för ett Office 365-program, är du omdirigeras till programmet och loggas in automatiskt.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Microsoft och tredje parts program som har konfigurerats med federation-baserad enkel inloggning

Administratören kan lägga till program i avsnittet Active Directory i Azure-portalen inställd för enkel inloggning på **Azure AD enkel inloggning**. Du kan se dessa program bara om administratören uttryckligen har gett dig åtkomst till dem.

När du väljer en panel för ett program kan du omdirigeras och loggas in automatiskt till den.

### <a name="password-based-sso-without-identity-provisioning"></a>Lösenordsbaserad SSO utan att behöva etablera identitet

Administratören kan lägga till program i avsnittet Active Directory i Azure-portalen inställd för enkel inloggning på **lösenordsbaserad enkel inloggning**. Alla användare i katalogen kan se alla program som har konfigurerats i det här läget.

Första gången du väljer en program-panel uppmanas du att installera plugin-programmet lösenord SSO för Internet Explorer eller Chrome. Installationen kräver att du starta om webbläsaren. När du återgår till MyApps-portalen och välj panelen programmet igen uppmanas du att ange ett användarnamn och lösenord för programmet. När du har angett ditt användarnamn och lösenord kan autentiseringsuppgifterna som lagras på ett säkert sätt och kopplat till ditt konto i Azure AD.

Nästa gång du väljer panelen program har du automatiskt loggat in till programmet.  

Du behöver inte ange dina autentiseringsuppgifter igen och eller installera lösenord SSO-plugin-programmet.

Om dina autentiseringsuppgifter har ändrats i målprogrammet från tredje part, måste du även uppdatera dina autentiseringsuppgifter som lagras i Azure AD. 

Du kan uppdatera dina autentiseringsuppgifter genom att göra följande:

1. Välj ikonen på panelen program.
2. Välj **uppdatera autentiseringsuppgifterna** att ange användarnamnet och lösenordet för programmet.


### <a name="password-based-sso-with-identity-provisioning"></a>Lösenordsbaserad enkel inloggning med identiteten etablering

Administratören kan lägga till program i avsnittet Active Directory i Azure-portalen inställd för enkel inloggning på **lösenordsbaserad enkel inloggning**, tillsammans med identity etablering.

Första gången du väljer en program-panel uppmanas du att installera plugin-programmet lösenord SSO för Internet Explorer eller Chrome. Installationen kräver att du starta om webbläsaren.  

När du gå tillbaka till MyApps-portalen och välj panelen programmet igen, du har loggat in automatiskt till programmet.

Vissa program kan kräva att du kan ändra lösenord vid första inloggningen. Om dina autentiseringsuppgifter har ändrats i målprogrammet från tredje part, måste du även uppdatera de autentiseringsuppgifter som lagras i Azure AD. 

Du kan uppdatera dina autentiseringsuppgifter genom att göra följande:

1. Välj ikonen på panelen program.
2. Välj **uppdatera autentiseringsuppgifterna** att ange användarnamnet och lösenordet för programmet.


### <a name="application-with-existing-sso-solutions"></a>Program med befintliga lösningar för enkel inloggning

Om du vill konfigurera enkel inloggning för ett program, tillhandahåller Azure-portalen ett tredje alternativ som heter befintlig enkel inloggning. Det här alternativet kan din administratör för att skapa en länk till ett program och placera den på MyApps-portalen för valda användare.

Om ett program är konfigurerad för att autentisera användare med hjälp av AD FS 2.0, kan administratören till exempel använda alternativet befintlig enkel inloggning för att skapa en länk till den på MyApps-portalen. När du använder länken kan autentiseras via AD FS 2.0 eller den befintliga SSO-lösning programmet tillhandahåller.


## <a name="next-steps"></a>Nästa steg

- Mer om programhantering finns i [programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md).
 
- Läs hur du integrerar en SaaS-app med Azure AD i den [lista över guider om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md).
 
- Mer information om hur du hanterar appar med Azure AD finns i [introduktion till enkel inloggning och hantera appåtkomst med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
 
- Läs mer om etableringen av användare i [automatisera användaretablering och avetablering för SaaS-program](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
