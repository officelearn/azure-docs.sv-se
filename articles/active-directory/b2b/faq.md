---
title: Vanliga frågor om B2B-samarbete – Azure Active Directory | Microsoft-dokument
description: Få svar på vanliga frågor om Azure Active Directory B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92c3e0d77a26db406e24d6d2fa07e96349613634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050825"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Vanliga frågor om Azure Active Directory B2B-samarbete

Dessa vanliga frågor och svar (Vanliga frågor) om Azure Active Directory (Azure AD) B2B-samarbete (Business To Business) uppdateras regelbundet för att inkludera nya ämnen.

   > [!IMPORTANT]
   > **Från och med den 31 mars 2021**stöder Microsoft inte längre inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbetsscenarier. Som förberedelse uppmuntrar vi kunder att välja [e-post engångskodautentisering.](one-time-passcode.md) Vi välkomnar din feedback på den här offentliga förhandsversionen och är glada över att skapa ännu fler sätt att samarbeta.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Kan vi anpassa vår inloggningssida så att det blir mer intuitivt för våra B2B-samarbetsgäster?
Absolut! Se vårt [blogginlägg om den här funktionen](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Mer information om hur du anpassar organisationens inloggningssida finns i [Lägga till företagsprofilering för att logga in och komma åt panelsidor](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Kan B2B-samarbetsanvändare komma åt SharePoint Online och OneDrive?
Ja. Möjligheten att söka efter befintliga gästanvändare i SharePoint Online med hjälp av personväljaren är dock **Av** som standard. Om du vill aktivera alternativet att söka efter befintliga gästanvändare anger du **ShowPeoplePickerSuggestionsForGuestUsers** **till På**. Du kan aktivera den här inställningen antingen på klientnivå eller på webbplatssamlingsnivå. Du kan ändra den här inställningen med hjälp av cmdlets set-spotenant och set-sposite. Med dessa cmdlets kan medlemmar söka i alla befintliga gästanvändare i katalogen. Ändringar i klientomfånget påverkar inte SharePoint Online-webbplatser som redan har etablerats.

### <a name="is-the-csv-upload-feature-still-supported"></a>Stöds csv-uppladdningsfunktionen fortfarande?
Ja. Mer information om hur du använder funktionen för uppladdning av CSV-filer finns i [det här PowerShell-exemplet](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hur kan jag anpassa mina e-postmeddelanden för inbjudan?
Du kan anpassa nästan allt om inbjudna processen med hjälp av [B2B inbjudan API: er](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Kan gästanvändare återställa sin multifaktorautentiseringsmetod?
Ja. Gästanvändare kan återställa sin multifaktorautentiseringsmetod på samma sätt som vanliga användare gör.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Vilken organisation ansvarar för multifaktorautentiseringslicenser?
Den inbjudande organisationen utför multifaktorautentisering. Den inbjudande organisationen måste se till att organisationen har tillräckligt med licenser för sina B2B-användare som använder multifaktorautentisering.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Vad händer om en partnerorganisation redan har konfigurerat multifaktorautentisering? Kan vi lita på deras multifaktorautentisering och inte använda vår egen multifaktorautentisering?
Den här funktionen stöds för närvarande inte. Om åtkomst till organisationens resurser kräver multifaktorautentisering måste partnerorganisationen registrera sig för multifaktorautentisering i din (den inbjudande) organisationen.

### <a name="how-can-i-use-delayed-invitations"></a>Hur kan jag använda fördröjda inbjudningar?
En organisation kanske vill lägga till B2B-samarbetsanvändare, etablera dem i program efter behov och sedan skicka inbjudningar. Du kan använda API:et för inbjudning av samarbete i B2B för att anpassa introduktionsarbetsflödet.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag göra gästanvändare synliga i Exchange Global Address List?
Ja. Gästobjekt visas inte som standard i organisationens globala adresslista (GAL), men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Se [Kan jag göra gästobjekt synliga i den globala adresslistan?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kan jag göra en gästanvändare till en begränsad administratör?
Absolut. Mer information finns i [Lägga till gästanvändare i en roll](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Tillåter Azure AD B2B-samarbete B2B-användare att komma åt Azure-portalen?
Om inte en användare tilldelas rollen som begränsad administratör, kräver B2B-samarbetsanvändare inte åtkomst till Azure-portalen. B2B-samarbetsanvändare som har tilldelats rollen som begränsad administratör kan dock komma åt portalen. Om en gästanvändare som inte har tilldelats någon av dessa administratörsroller har åtkomst till portalen kan användaren komma åt vissa delar av upplevelsen. Gästanvändarrollen har vissa behörigheter i katalogen.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kan jag blockera åtkomst till Azure-portalen för gästanvändare?

Visst! Du kan skapa en princip för villkorlig åtkomst som blockerar alla gäst- och externa användare från att komma åt Azure-portalen. När du konfigurerar den här principen bör du undvika att oavsiktligt blockera åtkomsten till medlemmar och administratörer.

1. Logga in på din [Azure-portal](https://portal.azure.com/) som säkerhetsadministratör eller administratör för villkorlig åtkomst.
2. Välj **Azure Active Directory** i Azure Portal. 
3. Välj **Säkerhet**under **Hantera**.
4. Under **Skydda**väljer du **Villkorlig åtkomst**. Välj **Ny princip**.
5. Ange ett namn för principen i textrutan **Namn** på sidan **Nytt** (till exempel "Blockera gäster från att komma åt portalen").
6. Under **Tilldelningar** väljer du **Användare och grupper**.
7. På fliken **Inkludera** väljer du **Välj användare och grupper**och väljer sedan Alla **gäst- och externa användare (Förhandsgranska).**
9. Välj **Done** (Klar).
10. På sidan **Nytt** väljer du **Molnappar eller -åtgärder**i avsnittet **Tilldelningar** .
11. På sidan **Molnappar eller åtgärder** väljer du **Välj appar**och väljer sedan **Välj**.
12. Välj **Microsoft Azure-hantering** på sidan **Välj** och välj sedan **Välj**.
13. På sidan **Molnappar eller åtgärder** väljer du **Klar**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Stöder Azure AD B2B-samarbete multifaktorautentisering och e-postkonton för konsumenter?
Ja. Multifaktorautentisering och e-postkonton för konsumenter stöds båda för Azure AD B2B-samarbete.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Stöder du lösenordsåterställning för Azure AD B2B-samarbetsanvändare?
Om din Azure AD-klient är en användares arbetskatalog kan du [återställa användarens lösenord](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) från Azure-portalen. Men du kan inte direkt återställa ett lösenord för en gästanvändare som loggar in med ett konto som hanteras av en annan Azure AD-katalog eller extern identitetsprovider. Endast gästanvändaren eller administratören i användarens arbetskatalog kan återställa lösenordet. Här är några exempel på hur återställning av lösenord fungerar för gästanvändare:
 
* Gästanvändare som loggar in med ett guestuser@live.comMicrosoft-konto (till exempel) kan återställa sina egna lösenord med hjälp av Microsoft-konto självbetjäning lösenordsåterställning (SSPR). Se [Så här återställer du lösenordet för ditt Microsoft-konto](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Gästanvändare som loggar in med ett Google-konto eller en annan extern identitetsleverantör kan återställa sina egna lösenord med hjälp av identitetsleverantörens SSPR-metod. En gästanvändare med Google-kontot guestuser@gmail.com kan till exempel återställa sitt lösenord genom att följa instruktionerna i [Ändra eller återställa lösenordet](https://support.google.com/accounts/answer/41078).
* Om identitetsklienten är en JIT-klient (just-in-time) eller "viral" (vilket innebär att det är en separat, ohanterad Azure-klient) kan bara gästanvändaren återställa sitt lösenord. Ibland tar en organisation [över hanteringen av virala klienter](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) som skapas när medarbetare använder sina e-postadresser för arbete för att registrera sig för tjänster. När organisationen tar över en viral klientorganisation kan endast en administratör i den organisationen återställa användarens lösenord eller aktivera SSPR. Om det behövs kan du som inbjudande organisation ta bort gästanvändarkontot från katalogen och skicka en inbjudan igen.

* Om gästanvändarens arbetskatalog är din Azure AD-klient kan du återställa användarens lösenord. Du kan till exempel ha skapat en användare eller synkroniserat en användare från den lokala Active Directory och angett att deras UserType ska gästas. Eftersom den här användaren är hemd i din katalog kan du återställa deras lösenord från Azure-portalen.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Tillhandahåller Microsoft Dynamics 365 onlinesupport för Azure AD B2B-samarbete?
Ja, Dynamics 365 (online) stöder Azure AD B2B-samarbete. Mer information finns i Dynamics 365-artikeln [Bjud in användare med Azure AD B2B-samarbete](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Vad är livslängden för ett första lösenord för en nyskapade B2B-samarbetsanvändare?
Azure AD har en fast uppsättning tecken, lösenordsstyrka och kontoutelåsningskrav som gäller lika för alla Azure AD-molnanvändarkonton. Molnanvändarkonton är konton som inte är federerade med en annan identitetsleverantör, till exempel 
* Microsoft-konto
* Facebook
* Active Directory Federation Services
* En annan molnklient (för B2B-samarbete)

För federerade konton beror lösenordsprincipen på vilken princip som tillämpas i den lokala hyresrättsinnehavaren och användarens Microsoft-kontoinställningar.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>En organisation kanske vill ha olika upplevelser i sina program för klientanvändare och gästanvändare. Finns det standardvägledning för detta? Gör identitetsleverantörens närvaro anspråk på rätt modell att använda?
En gästanvändare kan använda vilken identitetsleverantör som helst för att autentisera. Mer information finns i [Egenskaper för en B2B-samarbetsanvändare](user-properties.md). Använd egenskapen **UserType** för att fastställa användarupplevelsen. **UserType-anspråket** ingår för närvarande inte i token. Program bör använda Microsoft Graph API för att fråga katalogen för användaren och för att hämta UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Var kan jag hitta en B2B-samarbetsgemenskap för att dela lösningar och skicka in idéer?
Vi lyssnar ständigt på din feedback för att förbättra B2B-samarbetet. Dela dina användarscenarier, metodtips och vad du gillar med Azure AD B2B-samarbete. Delta i diskussionen i [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Vi inbjuder dig också att lämna in dina idéer och rösta för framtida funktioner på [B2B Collaboration Ideas.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas)

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Kan vi skicka en inbjudan som automatiskt löses in, så att användaren bara är "redo att gå"? Eller måste användaren alltid klicka sig vidare till inlösen-url:en?
Du kan bjuda in andra användare i partnerorganisationen med hjälp av användargränssnittet, PowerShell-skript eller API:er. Du kan sedan skicka en direktlänk till en delad app till gästanvändaren. I de flesta fall behöver du inte längre öppna e-postinbjudan och klicka på en url för inlösen. Se [Azure Active Directory B2B-inbjudningsinbjudan](redemption-experience.md)för samarbete .

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hur fungerar B2B-samarbete när den inbjudna partnern använder federationen för att lägga till sin egen lokala autentisering?
Om partnern har en Azure AD-klient som är federerad till den lokala autentiseringsinfrastrukturen, uppnås lokal enkel inloggning (SSO) automatiskt. Om partnern inte har en Azure AD-klient skapas ett Azure AD-konto för nya användare. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Jag trodde att Azure AD B2B inte accepterade gmail.com och outlook.com e-postadresser och att B2C användes för den typen av konton?
Vi tar bort skillnaderna mellan B2B och business-to-consumer (B2C) samarbete när det gäller vilka identiteter som stöds. Den identitet som används är inte en bra anledning att välja mellan att använda B2B eller använda B2C. Information om hur du väljer samarbetsalternativ finns i [Jämför B2B-samarbete och B2C i Azure Active Directory](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Vilka program och tjänster stöder Azure B2B-gästanvändare?
Alla Azure AD-integrerade program kan stödja Azure B2B-gästanvändare, men de måste använda en slutpunkt som konfigureras som klient för att autentisera gästanvändare. Du kan också behöva [anpassa anspråken](claims-mapping.md) i SAML-token som utfärdas när en gästanvändare autentiserar till appen. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kan vi tvinga multifaktorautentisering för B2B gästanvändare om våra partners inte har multifaktorautentisering?
Ja. Mer information finns i [Villkorlig åtkomst för B2B-samarbetsanvändare](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>I SharePoint kan du definiera en "tillåt" eller "neka"-lista för externa användare. Kan vi göra detta i Azure?
Ja. Azure AD B2B-samarbete stöder tillåta listor och neka listor. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Vilka licenser behöver vi för att använda Azure AD B2B?
Information om vilka licenser din organisation behöver för att använda Azure AD B2B finns i [Azure Active Directory B2B-licensieringsvägledning](licensing-guidance.md).

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)

