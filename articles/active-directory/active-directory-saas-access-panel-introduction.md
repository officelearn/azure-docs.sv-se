---
title: "Vad är åtkomstpanelen i Azure Active Directory? | Microsoft Docs"
description: "Lär dig hur du använder variationer av åtkomstpanelen (webbläsare, Android-app, iPhone och iPad-appen) för att få åtkomst till SaaS-appar."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c9069cb0b46ddc1155c64bd63a7fcd8a685abbad
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="what-is-the-access-panel"></a>Vad är åtkomstpanelen?

Åtkomstpanelen är en webbaserad portal. Om du har ett arbets- eller skolkonto i Azure Active Directory (Azure AD) kan du använda åtkomstpanelen visa och starta molnbaserade program som en Azure AD-administratör har gett dig åtkomst till. Du kan också använda självbetjäning och funktioner för hantering av appen via åtkomstpanelen.

Åtkomstpanelen är separat från Azure-portalen. Det behöver du inte ha en Azure-prenumeration.

![Åtkomstpanelen][1] med åtkomstpanelen kan du redigera vissa av dina profilinställningar och gör följande:

- Ändra lösenordet associerat med ett arbets- eller skolkonto.

- Redigera inställningar för återställning av lösenord.

- Redigera inställningar för kontakt och inställningar som rör multifaktorautentisering (för konton som har krävs för att använda den av en administratör).

- Visa konto information, till exempel användar-ID, alternativa e-post, mobile och office telefonnummer och enheter.

- Visa och starta molnbaserade program som Azure AD-administratör har gett dig åtkomst till. 

- Själva hantera grupper. Administratörer kan skapa och hantera säkerhetsgrupper och begära medlemskap i säkerhetsgrupper i Azure AD. Mer information finns i [grupphantering via Självbetjäning för användare i Azure AD](active-directory-accessmanagement-self-service-group-management.md) och [hantera grupper](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>Komma åt åtkomstpanelen

Du kan komma åt åtkomstpanelen genom att gå till `http://myapps.microsoft.com`.

Om du har anpassning som konfigurerats för sidan logga in, kan du läsa in anpassning genom att lägga till organisationens domän till URL (till exempel `http://myapps.microsoft.com/<your domain>.com`).

Du kan använda alla aktiva eller verifierat domännamn som har konfigurerats i Azure-portalen, enligt nedan:

![Wingtip Toys domännamn][2]  

Distribuera URL för alla användare som loggar in på program som är integrerade med Azure AD.

## <a name="authentication"></a>Autentisering

Om du vill nå åtkomstpanelen, måste du autentiseras via ett arbets- eller skolkonto konto i Azure AD. Du kan autentiseras till Azure AD direkt. Alternativt, om en organisation har konfigurerat federation med hjälp av Active Directory Federation Services (AD FS) eller andra tekniker, du kan autentiseras av Windows Server Active Directory.

Om du har en prenumeration på Azure eller Office 365 och du har använt Azure-portalen eller ett Office 365-program, kan du visa listan med program utan att logga in igen. Om du inte har autentiserats, uppmanas du att logga in med användarnamnet och lösenordet för ditt konto i Azure AD. Om din organisation har konfigurerat federation, räcker att ange användarnamnet.

Du kan interagera med de program som administratören har integrerat med katalogen när du är autentiserad. Information om hur du integrerar program med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Webbläsarkrav

Minst åtkomstpanelen kräver en webbläsare som stöder JavaScript och har aktiverat för CSS. Om du vill logga in på program via lösenordsbaserade enkel inloggning (SSO), måste du ha åtkomst till panelen tillägget installeras i webbläsaren. Tillägget laddas ned automatiskt när du väljer ett program som har konfigurerats för lösenordsbaserad enkel inloggning.

Tillägget för åtkomst-panelen är tillgängligt för:
- **Edge**: på Windows 10 årsdagar Edition eller senare. 
- **Chrome**: på Windows 7 eller senare, och i MacOS X eller senare.
- **Firefox 26.0 eller senare**: Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare.
- **Internet Explorer 8, 9, 10, 11**: i Windows 7 eller senare (begränsat stöd).

## <a name="my-apps-secure-sign-in-extension"></a>Mina appar skyddat inloggningstillägg
Om du vill logga in på lösenordsbaserade enkel inloggning, måste du använda tillägget. När tillägget har installerats kan du logga in till den att aktivera ytterligare funktioner genom att välja **logga in att komma igång**. 

- Du kan logga in på en app direkt med hjälp av appen **inloggnings-URL**. När du använder den Webbadress tillägget åtgärden identifieras och ger dig möjlighet att logga in från tillägget.
- Du kan starta en av dina appar från åtkomstpanelen med hjälp av den *Snabbsökning* funktion i tillägget. 
- Tillägget innehåller de sista tre program som du startade i **nyligen använda** avsnitt.

> [!NOTE]
> Ytterligare funktioner är endast tillgängligt för gräns, Chrome och Firefox.
>

Om du använder en URL för Mina appar än `https://myapps.microsoft.com`, konfigurera standard-URL genom att göra följande:
1. När du är *inte* inloggad-tillägget, högerklicka på ikonen för tillägget.
2. Välj på menyn **Mina appar URL**.
3. Välj standard-URL.
4. Välj ikonen tillägg.
5. Välj **logga in att komma igång**.

## <a name="mobile-app-support"></a>Stöd för mobila appar

Azure Active Directory-teamet publicerar mobilappen Mina appar. När du installerar appen måste logga du in på lösenordsbaserade SSO-program för iOS och Android-enheter.

> [!NOTE]
> Du kan logga in på program som stöder federation med Azure AD (inklusive Salesforce, Google Apps, Dropbox, rutan, Concur, Workday, Office 365 och mycket mer än 70 andra) på valfri webbläsare på alla enheter utan att behöva en plugin-program eller mobil app. Ska användas på en mobil enhet, den andra [åtkomst till panelen upplevelser](https://myapps.microsoft.com/) även kräver inte Mina appar mobilappen.
>
>

### <a name="my-apps-for-android"></a>Mina appar för Android

Mina appar för Android stöds på en Android-enhet som kör Android version 4.1 eller senare.  

Det är tillgängligt på den [Google Play store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Mina appar för Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Mina appar för iPhone och iPad

Mina appar för iOS stöds på en iPhone eller iPad som kör iOS version 7 eller senare.  

Det är tillgängligt på den [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Mina appar för iOS][4]    


## <a name="managed-browser-for-my-apps"></a>Hanterad webbläsare för Mina appar

Mina appar är även integrerad med Intune Managed Browser. Intune Managed Browser för iOS och Android-enheter spelar en viktig roll i att hjälpa se till att data på mobila enheter förblir säkra. Webbläsaren kan du säkert visa och navigera på webbsidor som kan innehålla företagsinformation, och kan ge en säker webbsurfning upplevelse.  

Du får snabb åtkomst till Mina appar på din startsida för hanterad webbläsare och i dina bokmärken så att färre klick krävs för att nå alla program som du vill komma åt.

Intune Managed Browser finns på den [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) och [Google Play-butiken](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Hanterad webbläsare för Mina appar][5]    


## <a name="tips-for-testing-the-user-experience"></a>Tips om att testa användarupplevelsen

Om du är administratör för Azure och du är inloggad på Azure-portalen med ett konto i katalogen, är du automatiskt inloggad till åtkomstpanelen som ditt aktuella konto. Den här vyn visar alla program som har tilldelats.

Testa i en *olika* användarens konto måste du göra följande:

1. Längst upp till höger i Azure-portalen eller åtkomstpanelen, Välj **logga ut**. 
2. Gå till den [åtkomstpanelen](http://myapps.microsoft.com).
3. Ange användarnamn och lösenord för kontot på sidan logga in i din katalog som du vill testa.


## <a name="starting-applications"></a>Start av program

Det här avsnittet beskrivs flera typer av program som kan visas på åtkomstpanelen.

### <a name="office-365-applications"></a>Office 365-program

Om din organisation använder Office 365-program och du är licensierad för dem, visas Office 365-program på din åtkomstpanelen.

När du väljer ett program sida vid sida för ett Office 365-program kan du omdirigeras till programmet och loggas in automatiskt.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Microsoft och tredje parts program som har konfigurerats med federationsbaserat enkel inloggning

Din administratör kan lägga till program i Active Directory-avsnittet i Azure-portalen inställd för SSO på **Azure AD enkel inloggning**. Du kan se dessa program bara om administratören uttryckligen har gett dig åtkomst till dem.

När du väljer en panel för ett program kan du omdirigeras och loggas in automatiskt till den.

### <a name="password-based-sso-without-identity-provisioning"></a>Lösenordsbaserade SSO utan identitet etablering

Din administratör kan lägga till program i Active Directory-avsnittet i Azure-portalen inställd för SSO på **lösenordsbaserade enkel inloggning**. Alla användare i katalogen kan se alla program som har konfigurerats i det här läget.

Första gången du väljer en ikonen för programmet, uppmanas du att installera lösenord SSO plugin-program för Internet Explorer eller Chrome. Installationen kräver att du starta om webbläsaren. När du tillbaka till åtkomstpanelen och välj panelen program igen och du uppmanas att ange ett användarnamn och lösenord för programmet. När du har angett ditt användarnamn och lösenord, autentiseringsuppgifterna lagras på ett säkert sätt och kopplad till ditt konto i Azure AD.

Nästa gång du väljer panelen programmet loggas du automatiskt in till programmet.  

Du behöver inte ange dina autentiseringsuppgifter igen och eller installera lösenord SSO plugin-programmet.

Om dina autentiseringsuppgifter har ändrats i målprogrammet från tredje part, måste du uppdatera dina autentiseringsuppgifter som lagras i Azure AD. 

Uppdatera dina autentiseringsuppgifter genom att göra följande:

1. Välj ikonen på ikonen för programmet.
2. Välj **uppdatera autentiseringsuppgifterna** att ange användarnamn och lösenord för programmet.


### <a name="password-based-sso-with-identity-provisioning"></a>Lösenordsbaserade enkel inloggning med identiteten etablering

Din administratör kan lägga till program i Active Directory-avsnittet i Azure-portalen inställd för SSO på **lösenordsbaserade enkel inloggning**, tillsammans med identiteten etablering.

Första gången du väljer en ikonen för programmet, uppmanas du att installera lösenord SSO plugin-program för Internet Explorer eller Chrome. Installationen kräver att du starta om webbläsaren.  

När du tillbaka till åtkomstpanelen och välj panelen program igen och du loggas automatiskt in till programmet.

Vissa program kan kräva att du kan ändra ditt lösenord vid den första inloggningen. Om dina autentiseringsuppgifter har ändrats i målprogrammet från tredje part, måste du även uppdatera de autentiseringsuppgifter som lagras i Azure AD. 

Uppdatera dina autentiseringsuppgifter genom att göra följande:

1. Välj ikonen på ikonen för programmet.
2. Välj **uppdatera autentiseringsuppgifterna** att ange användarnamn och lösenord för programmet.


### <a name="application-with-existing-sso-solutions"></a>Program med befintliga lösningar för enkel inloggning

Om du vill konfigurera enkel inloggning för ett program, innehåller Azure-portalen ett tredje alternativ som heter befintliga enkel inloggning. Det här alternativet gör det möjligt för administratören att skapa en länk till ett program och placera den på åtkomstpanelen för valda användare.

Om ett program har konfigurerats för att autentisera användare med hjälp av AD FS 2.0 kan administratören till exempel använda befintliga Single Sign-On-alternativet för att skapa en länk till den på åtkomstpanelen. När du använder länken kan autentiseras via AD FS 2.0 eller det befintliga lösning för enkel inloggning i programmet.


## <a name="next-steps"></a>Nästa steg

- Om du vill visa en lista över alla avsnitt som är relaterade till hantering av program finns i [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md).
 
- Information om hur du integrerar en SaaS-app med Azure AD finns i [lista över självstudier om hur du integrerar SaaS-appar](active-directory-saas-tutorial-list.md).
 
- Mer information om hur du hanterar appar med Azure AD finns i [introduktion till enkel inloggning och hantera appåtkomst med Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Läs mer om användaretablering i [automatisera användaretablering och avetablering för SaaS-program](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
