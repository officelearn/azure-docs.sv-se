---
title: Felsökning av Azure Active Directory B2B-samarbete | Microsoft Docs
description: Friskrivning från ansvar för vanliga problem med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: ca339319a370fd56cf8493418ae311b39f8fa59e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648377"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Felsökning av Azure Active Directory B2B-samarbete

Här finns några lösningar för vanliga problem med Azure Active Directory (Azure AD) B2B-samarbete.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Jag har lagt till en extern användare men ser inte dem i den globala adressboken eller i Personväljaren

Objektet kan ta några minuter att replikera i fall där externa användare inte fylls i listan.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>En B2B-gästanvändare inte visas i SharePoint Online/OneDrive Personväljaren 
 
Möjligheten att söka efter befintliga gästanvändare i Personväljaren SharePoint Online (SPO) har värdet OFF som standard så att de matchar äldre beteende.

Du kan aktivera den här funktionen med hjälp av inställningen ”ShowPeoplePickerSuggestionsForGuestUsers” på samlingsnivå klient- och plats. Du kan ange funktionen med hjälp av Set-SPOTenant och Set-SPOSite cmdletar som ger möjlighet att söka efter alla befintliga gästanvändare i katalogen. Ändringar i klientorganisationsområdet påverkar inte redan etablerade SPO-webbplatser.

## <a name="invitations-have-been-disabled-for-directory"></a>Inbjudningar har inaktiverats för katalog

Om du får ett meddelande om att du inte har behörighet att bjuda in användare, verifiera att ditt användarkonto har behörighet att bjuda in extern användare under användarinställningar:

![](media/troubleshoot/external-user-settings.png)

Om du nyligen har ändrat dessa inställningar eller tilldelat rollen Gästinbjudare till en användare kan det finnas en fördröjning på 15 – 60 minuter innan ändringarna träder i kraft.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Användaren som jag får ett fel under inlösning

Vanliga fel är:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>När en användare administratör inte tillåter EmailVerified användare skapas i deras klienter

När du bjuder in användare vars organisation använder Azure Active Directory, men där användarens konto inte finns (till exempel användaren finns inte i Azure AD-contoso.com). Administratören för contoso.com kan ha en princip på plats som förhindrar att användare håller på att skapas. Du måste kontrollera med administratören för att bestämma om externa användare. Den externa användaren administratör kan behöva tillåta e-verifierad användare inom sin domän (finns i den här [artikeln](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) på e-verifierat användarna).

![](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Extern användare finns inte redan i en federerad domän

Om du använder federation-autentisering och användaren inte redan finns i Azure Active Directory, kan användaren inte bjudas in.

Den externa användaren administratören måste synkronisera användarens konto till Azure Active Directory för att lösa problemet.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hur gör\#”, vilket är vanligtvis inte ett giltigt tecken synkronisering med Azure AD?

”\#” är ett reserverat tecken i UPN-namn för Azure AD B2B-samarbete eller externa användare, eftersom det inbjudna kontot user@contoso.com blir user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Därför \# i UPN-namn som kommer från en lokal tillåts inte att logga in på Azure-portalen. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Jag får ett felmeddelande när du lägger till externa användare till en synkroniserad grupp

Externa användare kan läggas endast till ”tilldelats” eller ”säkerhetsgrupper” och inte till grupper som är hanterad lokalt.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Min extern användare tog inte emot ett e-postmeddelande för att lösa in

När en användare bör kontrollera med sina ISP: N eller skräppost filtret så att du får följande adress: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Jag har sett att det anpassade meddelandet inte får ingår i inbjudan meddelanden ibland

För att uppfylla sekretesslagar våra API: er inte inkluderar anpassade meddelanden i din e-postinbjudan när:

- Avsändaren har inte en e-postadress i bjuder in klienten
- När ett huvudnamn för appservice skickar inbjudan

Om det här scenariot är viktiga för dig kan du utelämna e-postinbjudan vårt API och skicka den via den e-postmekanism. Kontakta din organisations är jurist att kontrollera att alla e-postmeddelanden som du skickar detta sätt uppfyller gällande sekretesslagstiftning.

## <a name="next-steps"></a>Nästa steg

- [Få support för B2B-samarbete](get-support.md)