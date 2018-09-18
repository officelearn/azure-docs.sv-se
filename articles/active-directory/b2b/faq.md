---
title: Azure Active Directory B2B-samarbete vanliga frågor och svar | Microsoft Docs
description: Få svar på vanliga frågor och svar om Azure Active Directory B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: reference
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: b4407ac6b7a0d9fdbf52b84fb94223c32868f0c5
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983860"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B-samarbete vanliga frågor och svar

Dessa vanliga frågor (FAQ) om Azure Active Directory (Azure AD) business-to-business (B2B) samarbete uppdateras regelbundet för att inkludera nya artiklar.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Kan vi anpassa vår inloggningssida, så är det enklare för våra gästanvändare för B2B-samarbete?
Absolut! Se våra [blogginlägget om den här funktionen](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Läs mer om hur du anpassar din organisations inloggningssida [Lägg till företagsanpassning för att logga in och åtkomstpanelsidor](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Kan användare i B2B-samarbetet komma åt SharePoint Online och OneDrive?
Ja. Möjligheten att söka efter befintliga gästanvändare i SharePoint Online med hjälp av Personväljaren är dock **av** som standard. Om du vill aktivera alternativet att söka efter befintliga gästanvändare, ange **ShowPeoplePickerSuggestionsForGuestUsers** till **på**. Du kan aktivera den här inställningen antingen på klientnivån eller på webbplatssamlingsnivå. Du kan ändra den här inställningen med hjälp av cmdlet: Set-SPOTenant och Set-SPOSite. Med dessa cmdlets kan medlemmar söka alla befintliga gästanvändare i katalogen. Ändringar i klientorganisationsområdet påverkar inte SharePoint Online-platser som redan har etablerats.

### <a name="is-the-csv-upload-feature-still-supported"></a>CSV-överföringsfunktionen fortfarande stöds?
Ja. Mer information om hur du använder CSV-filuppladdningsfunktionen finns i [det här PowerShell-exemplet](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hur kan jag Anpassa min postinbjudningar?
Du kan anpassa nästan allt om hur du bjuder in med hjälp av den [B2B inbjudan API: er](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Gästanvändare kan återställa sina flerfunktionsautentisering?
Ja. Gästanvändare kan återställa sina flerfunktionsautentisering på samma sätt som vanliga användare göra.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Vilken organisation ansvarar för multifaktorautentisering licenser?
Organisationen som bjuder in utför multifaktorautentisering. Organisationen som bjuder in måste se till att organisationen har tillräckligt med licenser för sina B2B-användare som använder multifaktorautentisering.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Vad händer om en partnerorganisation redan har multifaktorautentisering ställa in? Kan vi lita på sina multifaktorautentisering och inte använda vår egen multifaktorautentisering?
Den här funktionen är planerat för en senare version kommer så som och du kan välja specifika partner ska undantas från din (bjuder in organisationens) multifaktorautentisering.

### <a name="how-can-i-use-delayed-invitations"></a>Hur kan jag använda fördröjd inbjudningar?
En organisation kanske vill lägga till användare i B2B-samarbetet, etablera dem till program efter behov och sedan skicka inbjudningar. Du kan använda B2B-samarbetsinbjudan API för att anpassa onboarding-arbetsflödet.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag visa gästanvändare i den globala adresslistan Exchange?
Ja. Gästen objekt visas inte i din organisations globala adresslistan som standard, men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Mer information finns i **kan jag göra gäst objekt visas i den globala adresslistan?** i [gäståtkomst i Office 365-grupper](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ).

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kan jag göra en gästanvändare en begränsad administratör?
Absolut. Mer information finns i [att lägga till gästanvändare till en roll](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Tillåter att Azure AD B2B-samarbete B2B-användare åtkomst till Azure-portalen?
Om en användare har tilldelats rollen begränsad administratör eller global administratör, behöver inte användare i B2B-samarbetet någon åtkomst till Azure-portalen. B2B-samarbete användare som har tilldelats rollen begränsad administratör eller global administratör kan dock åtkomst till portalen. Även om en gästanvändare som inte tilldelats någon av dessa administrativa roller har åtkomst till portalen, kanske användaren kan komma åt vissa delar av upplevelsen. Gästrollen har vissa behörigheter i katalogen.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kan jag blockera åtkomst till Azure-portalen för gästanvändare?
Visst! När du konfigurerar den här principen kan vara noga med att undvika att av misstag blockerar åtkomsten till medlemmar och administratörer.
Att blockera en gästanvändare åtkomst till den [Azure-portalen](https://portal.azure.com), använda en princip för villkorlig åtkomst i Windows Azure klassiska modellen API:
1. Ändra den **alla användare** gruppen så att den innehåller bara medlemmar.
  ![ändra grupp-skärmbild](media/faq/modify-all-users-group.png)
2. Skapa en dynamisk grupp som innehåller gästanvändare.
  ![Skapa grupp skärmbild](media/faq/group-with-guest-users.png)
3. Konfigurera en princip för villkorlig åtkomst blockera gästanvändare från att komma åt portalen, som visas i följande video:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Stöder Azure AD B2B-samarbete multifaktorautentisering och konsumenten e-postkonton?
Ja. Multi-Factor authentication och konsument e-postkonton stöds både för Azure AD B2B-samarbete.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Planerar du att stöd för återställning av lösenord för användare i Azure AD B2B-samarbetet?
Ja. Här följer viktig information för lösenordsåterställning via självbetjäning (SSPR) för en B2B-användare som bjuds in från en partnerorganisation:
 
* SSPR sker endast i identity-klient för B2B-användare.
* Om innehavaren identitet är ett Microsoft-konto, används SSPR mekanism för Microsoft-konto.
* Om identitets-klient är en just-in-time (JIT) eller ”viral” klient, skickas ett e-postmeddelande för återställning av lösenord.
* För andra klienter följs standardprocessen för SSPR för B2B-användare. Innehavare som medlem SSPR för B2B-användare, i samband med resursen är blockerad. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Lösenordet återställs för gästanvändare i en just-in-time (JIT) eller ”viral” klient som godkänt inbjudningar med ett arbets eller skolans e-postadress, men som inte har en befintlig Azure AD-konto?
Ja. Ett lösenord för återställning av e-postmeddelande kan skickas som gör att användaren återställer sitt lösenord i JIT-innehavare.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Tillhandahåller Microsoft Dynamics 365 support på nätet för Azure AD B2B-samarbete?
Ja, Dynamics 365 (online) har stöd för Azure AD B2B-samarbete. Mer information finns i Dynamics 365-artikeln [bjuda in användare med Azure AD B2B-samarbete](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Vad är livslängden för ett initialt lösenord för en nyligen skapade användare i B2B-samarbete?
Azure AD har en fast uppsättning tecken, lösenordssäkerhet och molnbaserade användarkonton för kontoutelåsning krav som gäller lika för alla Azure AD-kontot. Cloud-användarkonton är konton som inte är federerat med någon annan identitetsprovider som 
* Microsoft-konto
* Facebook
* Active Directory Federation Services
* En annan molnklient (för B2B-samarbete)

För federerade konton beror lösenordsprincip på den princip som tillämpas i den lokala innehavare och användarens inställningar för Microsoft-konto.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>En organisation kanske vill ha olika upplevelser i sina program för klientanvändare och gästanvändare. Finns det standard riktlinjer för detta? Är förekomsten av identitetsprovidern anspråk rätt modell ska använda?
 Gästanvändare kan använda alla identitetsleverantör för autentisering. Mer information finns i [egenskaperna för en användare för B2B-samarbete](user-properties.md). Använd den **UserType** som bestämmer användarupplevelsen. Den **UserType** anspråk är för närvarande inte ingår i token. Program ska använda Graph API för att fråga katalogen för användaren och för att få UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Var hittar jag en B2B-samarbete community kan dela lösningar och skicka in idéer?
Vi lyssnar hela tiden till din feedback för att förbättra B2B-samarbete. Vi erbjuder dig att dela din användare scenarier, metodtips och vad du tycker om Azure AD B2B-samarbete. Gå med i diskussionen den [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Vi inbjuder dig också att skicka in dina idéer och rösta på framtida funktioner på [B2B-samarbete idéer](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Kan vi skicka en inbjudan som automatiskt in, så att användaren är bara ”klart”? Eller användaren har alltid att klicka dig igenom till URL: en för inlösen?
En inbjudaren kan bjuda in andra användare i partnerorganisationen med hjälp av Användargränssnittet, PowerShell-skript eller API: er. Sedan kan avsändaren skicka gästanvändaren en direktlänk till en delad app. I de flesta fall är det inte längre behöver öppna din e-postinbjudan och klicka på en URL för inlösen. Mer information finns i [inlösning av inbjudan för Azure Active Directory B2B-samarbete](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hur fungerar B2B-samarbete när den inbjudna partnern använder federation att lägga till egna lokal autentisering?
Om partnern har en Azure AD-klient som är federerat till lokal autentisering infrastruktur, uppnås lokala enkel inloggning (SSO) automatiskt. Om partnern inte har en Azure AD-klient, skapas en Azure AD-konto för nya användare. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Jag tror att Azure AD B2B inte godkände gmail.com och outlook.com-e-postadresser och att B2C har använts för dessa typer av konton?
Vi tar bort skillnaderna mellan B2B och företag till konsument (B2C) samarbete enligt vilken identiteter stöds. Den identitet som används är inte ett bra skäl att välja mellan att använda B2B eller med hjälp av B2C. Information om hur du väljer ett samarbetsalternativ finns i [jämför B2B-samarbete och B2C i Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Vilka program och tjänster stöder Azure B2B-gästanvändare?
Alla Azure AD-integrerade program stöder Azure B2B-gästanvändare. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kan vi tvinga Multi-Factor authentication för B2B-gästanvändare om våra partners inte har multifaktorautentisering?
Ja. Mer information finns i [villkorlig åtkomst för användare i B2B-samarbetet](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>I SharePoint, kan du definiera en lista för ”Tillåt” eller ”neka” för externa användare. Kan vi göra det i Azure?
Ja. Azure AD B2B-samarbete stöder tillåta listor och neka listor. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Vilka licenser måste vi använda Azure AD B2B?
Information om vilka licenser i din organisation måste använda Azure AD B2B finns i [Azure Active Directory B2B-samarbete och licenser vägledning](licensing-guidance.md).

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)

