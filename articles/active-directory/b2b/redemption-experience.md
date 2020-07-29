---
title: Inbjudan om inlösning i B2B-samarbete – Azure AD
description: Beskriver Azure AD B2B-samarbets Inbjudnings upplevelsen för slutanvändare, inklusive avtalets Sekretess villkor.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6bf5d40262c5991504d3dc62490fb50f6a20592
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83826102"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B-samarbete för inbjudan

I den här artikeln beskrivs de olika sätt som gäst användare kan komma åt resurser och medgivande processen som de stöter på. Om du skickar ett e-postmeddelande med inbjudan till gästen innehåller inbjudan en länk som gästen kan lösa in för att få åtkomst till din app eller Portal. E-postinbjudan är bara ett av de sätt som gäster kan få åtkomst till dina resurser. Alternativt kan du lägga till gäster till katalogen och ge dem en direkt länk till portalen eller appen som du vill dela. Oavsett vilken metod de använder, vägleds gästerna genom en process för godkännande av första gången. Den här processen säkerställer att dina gäster samtycker till sekretess villkoren och accepterar de [användnings villkor](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) som du har konfigurerat.

När du lägger till en gäst användare i din katalog har gäst användar kontot en medgivande status (synlig i PowerShell) som inlednings vis är inställt på **PendingAcceptance**. Den här inställningen är kvar tills gästen accepterar din inbjudan och accepterar din sekretess policy och användnings villkor. Därefter ändras medgivande statusen till **godkänd**och medgivande sidorna visas inte längre för gästen.

   > [!IMPORTANT]
   > Från och med den **31 mars 2021**kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

## <a name="redemption-through-the-invitation-email"></a>Inlösen via e-postinbjudan

När du lägger till en gäst användare till din katalog med [hjälp av Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)skickas ett e-postmeddelande med inbjudan till gästen i processen. Du kan också välja att skicka e-postinbjudningar när du [använder PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) för att lägga till gäst användare i din katalog. Här är en beskrivning av gästens upplevelse när de löser in länken i e-postmeddelandet.

1. Gästen får ett [e-postmeddelande om inbjudan](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) som skickas från **Microsofts inbjudningar**.
2. Gästen väljer **acceptera inbjudan** i e-postmeddelandet.
3. Gästen kommer att använda sina egna autentiseringsuppgifter för att logga in i din katalog. Om gästen inte har ett konto som kan sammanställas till din katalog och funktionen [eng ång slö sen](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) ord för e-post inte är aktive rad. gästen uppmanas att skapa en personlig [MSA](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) eller ett självbetjänings [konto för Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup). Mer information finns i [inlösnings flödet för inbjudan](#invitation-redemption-flow) .
4. Gästen vägleds genom den [godkännande upplevelse](#consent-experience-for-the-guest) som beskrivs nedan.

## <a name="redemption-through-a-direct-link"></a>Inlösen via en direkt länk

Som ett alternativ till e-postinbjudan kan du ge en gäst en direkt länk till din app eller Portal. Du måste först lägga till gäst användaren till din katalog via [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) eller [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Sedan kan du använda något av de [anpassningsbara sätten för att distribuera program till användare](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), inklusive direkt inloggnings länkar. När en gäst använder en direkt länk i stället för e-postinbjudan, kommer de fortfarande att guidas genom den första gången.

> [!IMPORTANT]
> Den direkta länken måste vara klient-/regionsspecifika. Med andra ord måste det innehålla ett klient-ID eller verifierad domän så att gästen kan autentiseras i din klient, där den delade appen finns. En vanlig URL som https://myapps.microsoft.com inte fungerar för en gäst, eftersom den kommer att omdirigeras till sin hem klient för autentisering. Här följer några exempel på direkta länkar med klient kontext:
 > - Åtkomst panel för appar:`https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Åtkomst panel för appar för en verifierad domän:`https://myapps.microsoft.com/<;verified domain>`
 > - Azure-portalen: `https://portal.azure.com/<tenant id>`
 > - Enskild app: se så här använder du en [direkt inloggnings länk](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Det finns vissa fall där e-postinbjudan rekommenderas över en direkt länk. Om dessa specialfall är viktiga för din organisation rekommenderar vi att du bjuder in användare genom att använda metoder som fortfarande skickar e-postinbjudan:
 - Användaren har inget Azure AD-konto, ett MSA eller ett e-postkonto i en federerad organisation. Om du inte använder funktionen för eng ång slö sen ord måste gästen lösa in inbjudan via e-post för att vägleda dig genom stegen för att skapa en MSA.
 - Ibland kanske det inbjudna användarobjektet inte har en e-postadress på grund av en konflikt med ett kontakt objekt (till exempel ett Outlook-kontakt objekt). I det här fallet måste användaren klicka på inlösnings-URL: en i e-postinbjudan.
 - Användaren kan logga in med ett alias för den e-postadress som bjudits in. (Ett alias är en ytterligare e-postadress som associeras med ett e-postkonto.) I det här fallet måste användaren klicka på inlösnings-URL: en i e-postinbjudan.

## <a name="invitation-redemption-flow"></a>Inlösnings flöde för inbjudan

När en användare klickar på länken **acceptera inbjudan** i ett [e-postmeddelande](invitation-email-elements.md), löses Azure AD automatiskt om inbjudan utifrån inlösnings flödet enligt nedan:

![Skärm bild som visar flödes diagrammet för inlösen](media/redemption-experience/invitation-redemption-flow.png)

**Om användarens användar princip namn (UPN) matchar både ett befintligt Azure AD-och personal MSA-konto uppmanas användaren att välja vilket konto de vill lösa in med.*

1. Azure AD utför användarbaserad identifiering för att avgöra om användaren finns i en [befintlig Azure AD-klient](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b#easily-add-guest-users-in-the-azure-ad-portal).

2. Om en administratör har aktiverat [direkt Federation](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)kontrollerar Azure AD om användarens domänsuffix matchar domänen för en konfigurerad SAML/WS-utfodras identitets leverantör och omdirigerar användaren till den förkonfigurerade identitets leverantören.

3. Om en administratör har aktiverat [Google Federation](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)kontrollerar Azure AD om användarens domänsuffix är gmail.com eller googlemail.com och omdirigerar användaren till Google.

4. Inlösnings processen kontrollerar om användaren har en befintlig personlig [Microsoft-konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

5. När användarens **Hem Katalog** identifieras skickas användaren till motsvarande identitets leverantör för att logga in.  

6. Om steg 1 till 4 inte kan hitta en arbets katalog för den inbjudna användaren, avgör Azure AD om den bjudande klienten har aktiverat funktionen [eng ång slö sen](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) ord för gäster i ett e-postmeddelande.

7. Om [e-post med eng ång slö sen ord för gäster har Aktiver ATS](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode)skickas ett lösen ord till användaren via det inbjudna e-postmeddelandet. Användaren kommer att hämta och ange lösen ordet på inloggnings sidan för Azure AD.

8. Om e-postlösenord med eng ång slö sen ord för gäster är inaktiverat, kontrollerar Azure AD domänsuffix för att avgöra om det tillhör ett konsument konto. I så fall uppmanas användaren att skapa en personlig [Microsoft-konto](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create). Annars uppmanas användaren att skapa ett självbetjänings [konto för Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).

9. Azure AD försöker skapa ett självbetjänings [konto för Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup) genom att kontrol lera åtkomsten till e-postmeddelandet. Verifierar att kontot görs genom att skicka en kod till e-postmeddelandet och låta användaren hämta och skicka det till Azure AD. Men om den inbjudna användarens klient är federerad eller om fältet AllowEmailVerifiedUsers är inställt på false i den inbjudna användarens klient organisation, kan inte användaren slutföra inlösningen och flödet resulterar i ett fel. Mer information finns i [felsöka Azure Active Directory B2B-samarbete](https://docs.microsoft.com/azure/active-directory/b2b/troubleshoot#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. Användaren uppmanas att skapa en personlig [Microsoft-konto (MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create).

11. Efter autentiseringen till den rätta identitets leverantören omdirigeras användaren till Azure AD för att slutföra [medgivande upplevelsen](https://docs.microsoft.com/azure/active-directory/b2b/redemption-experience#consent-experience-for-the-guest).  

För just-in-Time (JIT)-inlösen, där inlösen sker via en klient kopplings länk, är steg 8 till 10 inte tillgängliga. Om en användare når steg 6 och funktionen email eng ång slö sen ord inte är aktive rad, får användaren ett fel meddelande och kan inte lösa in inbjudan. För att förhindra det här felet bör administratörer antingen [Aktivera eng ång slö sen ord eng ång slö sen ord](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode#when-does-a-guest-user-get-a-one-time-passcode) eller se till att användaren klickar på länken inbjudan.

## <a name="consent-experience-for-the-guest"></a>Godkännande upplevelse för gästen

När en gäst loggar in för att komma åt resurser i en partner organisation för första gången, går de igenom följande sidor. 

1. Gästen granskar sidan **gransknings behörigheter** som beskriver den bjudande organisationens sekretess policy. En användare måste **acceptera** användningen av informationen i enlighet med den bjudande organisationens sekretess policy för att kunna fortsätta.

   ![Skärm bild som visar sidan gransknings behörigheter](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Information om hur du som klient organisations administratör kan länka till din organisations sekretess policy finns i [How-to: Lägg till din organisations sekretess information i Azure Active Directory](https://aka.ms/adprivacystatement).

2. Om användnings villkoren har kon figurer ATS öppnas gästen och granskar användnings villkoren och väljer sedan **acceptera**. 

   ![Skärm bild som visar nya användnings villkor](media/redemption-experience/terms-of-use-accept.png) 

   Du kan konfigurera användnings [villkoren](../governance/active-directory-tou.md) i **externa identiteter**  >  **användningsvillkor**.

3. Om inget annat anges omdirigeras gästen till appens åtkomst panel, som innehåller en lista över de program som gästen har åtkomst till.

   ![Skärm bild som visar åtkomst panelen för appar](media/redemption-experience/myapps.png) 

I din katalog har gästens **inbjudan godkänt** värde ändrats till **Ja**. Om en MSA skapades, visar gästens **källa** Microsoft- **konto**. Mer information om egenskaper för gäst användar konton finns i [Egenskaper för en Azure AD B2B-samarbets användare](user-properties.md). 

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Lägg till Azure Active Directory B2B-samarbets användare i Azure Portal](add-users-administrator.md)
- [Hur kan informations arbetare lägga till B2B-samarbets användare för att Azure Active Directory?](add-users-information-worker.md)
- [Lägga till Azure Active Directory B2B-samarbets användare med hjälp av PowerShell](customize-invitation-api.md#powershell)
- [Lämna en organisation som gäst användare](leave-the-organization.md)
