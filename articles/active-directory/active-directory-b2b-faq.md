---
title: "Azure Active Directory B2B-samarbete vanliga frågor och svar | Microsoft Docs"
description: "Få svar på vanliga frågor och svar om Azure Active Directory B2B-samarbete."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: 0e3ae7f176f41b4b0984dbef0748451c7ebff9e7
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B-samarbete vanliga frågor och svar

Dessa vanliga frågor och svar om Azure Active Directory (AD Azure) business-to-business (B2B) samarbete uppdateras regelbundet för att inkludera nya artiklar.

### <a name="is-azure-ad-b2b-collaboration-available-in-the-azure-classic-portal"></a>Är Azure AD B2B-samarbete tillgängliga i den klassiska Azure-portalen?
Nej. Azure AD B2B-samarbetsfunktioner är endast tillgängliga i den [Azure-portalen](https://portal.azure.com) och i den [åtkomstpanelen](https://myapps.microsoft.com/). 

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Kan vi anpassa vår inloggningssida så att det är mer intuitiv för gästanvändare våra B2B-samarbete?
Absolut! Se vår [blogginlägget om den här funktionen](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Mer information om hur du anpassar din organisations inloggningssida finns [lägga till företagsanpassning för att logga in och åtkomstpanelsidan](customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Har B2B-samarbete användare åtkomst till SharePoint Online och OneDrive?
Ja. Möjligheten att söka efter befintliga gästanvändare i SharePoint Online med hjälp av Personväljaren är dock **av** som standard. Om du vill aktivera alternativet för att söka efter befintliga gästanvändare ange **ShowPeoplePickerSuggestionsForGuestUsers** till **på**. Du kan aktivera den här inställningen antingen på klienten eller på webbplatssamlingsnivå. Du kan ändra den här inställningen med hjälp av Set-SPOTenant och Set-SPOSite cmdlets. Med dessa cmdlets söka medlemmar alla befintliga gästanvändare i katalogen. Ändringar i klientorganisationsområdet påverkar inte SharePoint Online-platserna som redan har etablerats.

### <a name="is-the-csv-upload-feature-still-supported"></a>Funktionen Överför CSV stöds fortfarande?
Ja. Mer information om hur du använder funktionen överför CSV-fil finns [PowerShell-exempel](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hur kan jag Anpassa min inbjudan e-post?
Du kan anpassa nästan allt om bjuder in processen med hjälp av den [B2B inbjudan API: er](active-directory-b2b-api.md).

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>Kan en extern inbjudna användare lämnar organisationen när du har fått en inbjudan?
Bjuda in organisationens administratör kan ta bort en B2B-samarbete gästanvändare från sina kataloger, men gästanvändaren kan inte lämna bjuda in organisationskatalog sig själva. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Gästanvändare kan återställa sina multifaktorautentisering metoden?
Ja. Gästanvändare kan återställa sina multifaktorautentisering på samma sätt som vanliga användare gör.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Vilken organisation ansvarar för multifaktorautentisering licenser?
Bjuda in organisationen utför multifaktorautentisering. Bjuda in organisationen måste se till att organisationen har tillräckligt många licenser för sina B2B-användare som använder multifaktorautentisering.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Vad händer om en organisation redan har multifaktorautentisering konfigurera? Kan vi förtroende för sina multifaktorautentisering och inte använda egna multifaktorautentisering?
Den här funktionen är planerad för en framtida version så som sedan du kan välja specifika partner ska undantas från din (bjuda in organisationens) multifaktorautentisering.

### <a name="how-can-i-use-delayed-invitations"></a>Hur kan jag använda fördröjd inbjudningar?
En organisation kanske vill lägga till användare för B2B-samarbete dem till program efter behov etablera och skicka inbjudningar. Du kan använda B2B-samarbetsinbjudan API för att anpassa onboarding-arbetsflöde.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kan jag göra en gästanvändare en begränsad administratör?
Absolut. Mer information finns i [lägger till gästanvändare i en roll](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Tillåter att Azure AD B2B-samarbete B2B-användare åtkomst till Azure portal?
Om en användare har tilldelats rollen för begränsad administratör eller global administratör, behöver inte B2B-samarbete användare åtkomst till Azure-portalen. Dock B2B-samarbete användare som har tilldelats rollen begränsad administratör eller global administratör kan komma åt portalen. Även om en gästanvändare som inte har tilldelats någon av administratörsrollerna använder portalen kan kanske användaren kan komma åt vissa delar av. Gästrollen har några behörigheter i katalogen.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kan jag blockera åtkomst till Azure portal för gästanvändare?
Visst! När du konfigurerar den här principen var noga med att undvika att av misstag blockera åtkomst till medlemmar och administratörer.
Att blockera en gästanvändare åtkomst till den [Azure-portalen](https://portal.azure.com), använda en princip för villkorlig åtkomst i Windows Azure klassisk distribution modellen API:
1. Ändra den **alla användare** gruppen så att den innehåller bara medlemmar.
  ![ändra grupp skärmbild](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Skapa en dynamisk grupp som innehåller gästanvändare.
  ![Skapa grupp skärmbild](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Konfigurera en princip för villkorlig åtkomst till block gästanvändare från åtkomst till portalen, som visas i följande video:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Stöder Azure AD B2B-samarbete multifaktorautentisering och konsumenten e-postkonton?
Ja. Multi-Factor authentication och konsumenten e-postkonton stöds både för Azure AD B2B-samarbete.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Du planerar att stödja återställning av lösenord för Azure AD B2B-samarbete användare?
Ja. Här följer viktig information för lösenordsåterställning via självbetjäning (SSPR) för en B2B-användare som är inbjuden från en partnerorganisation:
 
* SSPR uppstår endast i identity-innehavare för B2B-användare.
* Om innehavaren identitet är ett Microsoft-konto, används SSPR mekanism för Microsoft-konto.
* Om innehavaren identitet är en just-in-time (JIT) eller ”viral” klient, skickas ett e-postmeddelande för återställning av lösenord.
* För andra klienter följs standardprocessen för SSPR för B2B-användare. Som member SSPR för B2B-användare i samband med resursen, blockeras innehavare. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Lösenordet återställs tillgängliga för gästanvändare i en just-in-time (JIT) eller ”viral” klient som har accepterat inbjudan med ett arbets eller skolans e-postadress, men som inte har en befintlig Azure AD-kontot?
Ja. Ett lösenord för återställning av e-post kan skickas som gör det möjligt för en användare att återställa sina lösenord i JIT-innehavare.

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>Tillhandahåller Microsoft Dynamics CRM online-support för Azure AD B2B-samarbete?
Microsoft Dynamics CRM ger för närvarande inte support online för Azure AD B2B-samarbete. Men planerar vi att stödja detta i framtiden.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Vad är livslängden för en inledande lösenordet för en nyligen skapade B2B-samarbete användare?
Azure AD har en fast uppsättning tecken och lösenordssäkerhet konto kontoutelåsning krav som gäller lika för alla Azure AD cloud användarkonton. Användarkonton i molnet är konton som inte är federerat med en annan identitetsleverantör som 
* Microsoft-konto
* Facebook
* Active Directory Federation Services
* Ett annat moln-klient (för B2B-samarbete)

För federerade konton beror lösenordsprincip på den princip som tillämpas i lokala innehavare och användarens inställningar för Microsoft-konto.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>En organisation kanske vill ha olika kanaler i sina program för klient-användare och gästanvändare. Är det standard riktlinjer för detta? Är förekomsten av identitetsleverantören anspråk att använda rätt modell?
 Gästanvändare kan använda valfri identitetsprovider som för att autentisera. Mer information finns i [egenskaperna för en B2B-samarbete användare](active-directory-b2b-user-properties.md). Använd den **UserType** egenskapen fastställa användarupplevelsen. Den **UserType** anspråk är för närvarande inte ingår i token. Program ska använda Graph API för att fråga katalogen för användaren och för att få UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Var hittar jag en B2B-samarbete community dela lösningar och skicka uppslag
Vi lyssnar hela tiden till din feedback för att förbättra B2B-samarbete. Vi ber dig att dela din användare scenarier, bästa praxis och vad du tycker om Azure AD B2B-samarbete. Anslut diskussionen den [Microsoft teknisk Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Också gärna skicka dina idéer och rösta på framtida funktioner på [B2B-samarbete idéer](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Kan vi skicka en inbjudan som automatiskt Inlöst så att användaren är bara ”klar”? Eller användaren har alltid klicka dig igenom till inlösning URL?
Inbjudningar som skickas av en användare i organisationen bjuda in som även är medlem i resurspartnerns organisation behöver inte inlösning av B2B-användare.

Vi rekommenderar att du bjuda in en användare från partnerorganisationen att ansluta till bjuda in organisationen. [Lägg till användaren till gästrollen bjuder in i resursorganisationen](active-directory-b2b-add-guest-to-role.md). Den här användaren kan erbjuda andra användare i resurspartnerns organisation med hjälp av inloggning Användargränssnittet, PowerShell-skript eller API: er. Sedan behöver B2B-samarbete användare från den organisationen inte lösa deras inbjudan.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hur fungerar B2B-samarbete när inbjuden partnern använder federation för att lägga till egna lokal autentisering?
Om partnern har en Azure AD-klient som är federerat till lokal autentisering infrastruktur, uppnås lokalt enkel inloggning (SSO) automatiskt. Om partnern som inte har en Azure AD-klient, skapas en Azure AD-kontot för nya användare. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Jag tror Azure AD B2B accepterade gmail.com och outlook.com-e-postadresser och att B2C användes för dessa typer av konton?
Vi tar bort skillnaderna mellan B2B och företag att företagets (B2C) samarbete enligt vilken identiteter stöds. Den identitet som används är inte en bra anledning att välja mellan att använda B2B eller med hjälp av B2C. Information om hur du väljer ett samarbetsalternativ finns [jämför B2B-samarbete och B2C i Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Vilka program och tjänster stöder Azure B2B gästanvändare?
Alla Azure AD-integrerade program stöder Azure B2B gästanvändare. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kan vi framtvinga multifaktorautentisering för B2B gästanvändare om våra samarbetspartners inte har multifaktorautentisering?
Ja. Mer information finns i [villkorlig åtkomst för användare för B2B-samarbete](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>I SharePoint, kan du definiera en lista för ”Tillåt” eller ”neka” för externa användare. Kan vi göra detta i Azure?
Ja. Azure AD B2B-samarbete stöder listor Tillåt och neka listor. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Vilka licenser vi behöver använda Azure AD B2B?
Information om vad licenser organisationen behöver använda Azure AD B2B finns [Azure Active Directory B2B-samarbete licensiering vägledning](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>Nästa steg

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hur lägger Azure AD-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure AD B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Azure AD B2B-samarbete API och anpassning](active-directory-b2b-api.md)
* [Multi-Factor Authentication för användare av B2B-samarbete](active-directory-b2b-mfa-instructions.md)
* [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex för programhantering i Azure AD](active-directory-apps-index.md)
