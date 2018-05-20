---
title: Felsökning av Azure Active Directory B2B-samarbete | Microsoft Docs
description: Kompensation för vanliga problem med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/25/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 4347b846b0ff91f606615abc2c94253c0ae70587
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Felsökning av Azure Active Directory B2B-samarbete

Här följer några lösningar på vanliga problem med Azure Active Directory (AD Azure) B2B-samarbete.


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Jag har lagt till en extern användare men se inte dem i den globala adressboken eller i Personväljaren

Objektet kan ta några minuter att replikera i fall där externa användare inte fylls i listan.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Gästanvändare B2B syns inte i SharePoint Online/OneDrive personer väljare 
 
Möjligheten att söka efter befintliga gästanvändare i Personväljaren SharePoint Online (SPO) är OFF som standard så att den matchar äldre beteende.

Du kan aktivera den här funktionen med hjälp av inställningen 'ShowPeoplePickerSuggestionsForGuestUsers' på samlingsnivå klient och plats. Du kan ange funktionen med hjälp av Set-SPOTenant och Set-SPOSite cmdlets som gör att medlemmar för att söka efter alla befintliga gästanvändare i katalogen. Ändringar i klientorganisationsområdet påverkar inte redan etablerade SPO platser.

## <a name="invitations-have-been-disabled-for-directory"></a>Inbjudningar har inaktiverats för katalogen

Om du får ett meddelande om att du inte har behörighet att bjuda in användare kontrollerar du att ditt användarkonto har behörighet att bjuda in externa användare under användarinställningar:

![](media/troubleshoot/external-user-settings.png)

Om du nyligen har ändrat inställningarna eller tilldelas en användare med rollen gäst bjuder in, det kan finnas en fördröjning 15 – 60 minuter innan ändringarna börjar gälla.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Användaren som jag inbjuden tar emot ett fel under inlösning

Vanliga fel är:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>När en användare Admin har otillåten EmailVerified användare skapas i klientorganisationen

När användarna vars organisation använder Azure Active Directory, men där användarens konto inte finns (till exempel användaren finns inte i Azure AD contoso.com). Administratören för contoso.com kan har en princip för att förhindra att användare skapas. Användaren måste kontrollera med administratören för att avgöra om externa användare tillåts. Den externa användaren administratör kan behöva användarna verifierad e-post i sin domän (se den här [artikel](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) på e-post verifieras användare).

![](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Externa användare finns inte redan i en federerad domän

Om du använder federation-autentisering och användaren inte redan finns i Azure Active Directory, kan användaren inte bjudas in.

Den externa användaren administratör måste synkronisera användarens konto i Azure Active Directory för att lösa problemet.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hur fungerar\#', som inte är vanligtvis ett giltigt tecken synkronisering med Azure AD?

”\#” är ett reserverat tecken i UPN-namnen för Azure AD B2B-samarbete eller externa användare eftersom kontot inbjudna user@contoso.com blir user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Därför \# i UPN-namn som kommer från lokala tillåts inte att logga in på Azure-portalen. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Ett felmeddelande när du lägger till externa användare till en synkroniserade grupp

Externa användare kan läggas till bara ”har tilldelats” eller ”-” säkerhetsgrupper och inte grupper som är hanterat lokalt.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Min extern användare tog inte emot ett e-postmeddelande för att lösa in

När en användare bör kontrollera med deras Internet- eller skräppost filter så att du får följande adress: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Jag Lägg märke till att det anpassade meddelandet inte får ingå i inbjudan meddelanden vid tillfällen

Om du vill följa sekretesslagar våra API: er inte inkluderar anpassade meddelanden i e-postinbjudan när:

- Avsändaren av inbjudan har inte en e-postadress i bjuda in klienten
- När en apptjänst-principal skickar inbjudan

Om det här scenariot är viktigt att du kan du ignorera e-postinbjudan våra API och skicka den via e-mekanismen för ditt val. Kontakta din organisations juridiskt biträde kontrollera e-postmeddelandet du skickar det här sättet även uppfyller sekretesslagar.

## <a name="next-steps"></a>Nästa steg

- [Få support för B2B-samarbete](get-support.md)