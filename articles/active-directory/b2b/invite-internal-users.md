---
title: Bjud in interna användare till B2B-samarbete – Azure AD
description: Om du har interna användarkonton för partners, distributörer, leverantörer, leverantörer och andra gäster kan du ändra till Azure AD B2B-samarbete genom att bjuda in dem att logga in med sina egna externa autentiseringsuppgifter eller inloggning. Använd antingen PowerShell eller Microsoft Graph-inbjudnings-API:et.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783fc0fa6f6c4e6c918fa3ff5fe0b53a71fa0178
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680180"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Bjud in interna användare till B2B-samarbete

|     |
| --- |
| Att bjuda in interna användare att använda B2B-samarbete är en offentlig förhandsversion av Azure Active Directory. Mer information om förhandsgranskningar finns i [Tilläggsvillkor för microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). |
|     |

Innan azure AD B2B-samarbetet är tillgänglig kan organisationer samarbeta med distributörer, leverantörer, leverantörer och andra gästanvändare genom att konfigurera interna autentiseringsuppgifter för dem. Om du har interna gästanvändare som detta kan du bjuda in dem att använda B2B-samarbete så att du kan dra nytta av Azure AD B2B-förmåner. Dina B2B-gästanvändare kan använda sina egna identiteter och autentiseringsuppgifter för att logga in, och du behöver inte underhålla lösenord eller hantera kontolivscykler.

Genom att skicka en inbjudan till ett befintligt internt konto kan du behålla användarens objekt-ID, UPN, gruppmedlemskap och apptilldelningar. Du behöver inte ta bort och bjuda in användaren manuellt eller tilldela om resurser. Om du vill bjuda in användaren använder du inbjudnings-API:et för att skicka både det interna användarobjektet och gästanvändarens e-postadress tillsammans med inbjudan. När användaren accepterar inbjudan ändrar B2B-tjänsten det befintliga interna användarobjektet till en B2B-användare. Framöver måste användaren logga in på molnresurstjänster med hjälp av sina B2B-autentiseringsuppgifter. De kan fortfarande använda sina interna autentiseringsuppgifter för att komma åt lokala resurser, men du kan förhindra detta genom att återställa eller ändra lösenordet på det interna kontot.

> [!NOTE]
> Inbjudan är enkelriktad. Du kan bjuda in interna användare att använda B2B-samarbete, men du kan inte ta bort B2B-autentiseringsuppgifterna när de har lagts till. Om du vill ändra tillbaka användaren till en internanvändare måste du ta bort användarobjektet och skapa ett nytt.

I den offentliga förhandsversionen kan den metod som beskrivs i den här artikeln för att bjuda in interna användare till B2B-samarbete inte användas i följande fall:

- Den interna användaren har redan tilldelats en Exchange-licens.
- Användaren kommer från en domän som har konfigurerats för direkt federation i katalogen.
- Den interna användaren är ett molnkonto och deras huvudkonto finns inte i Azure AD.

I dessa fall, om den interna användaren måste ändras till en B2B-användare, bör du ta bort det interna kontot och skicka användaren en inbjudan till B2B-samarbete.

**Lokala synkroniserade användare**: För användarkonton som synkroniseras mellan lokala och molnet förblir den lokala katalogen auktoritetskällan efter att de har bjudits in att använda B2B-samarbete. Alla ändringar du gör i det lokala kontot synkroniseras med molnkontot, inklusive att inaktivera eller ta bort kontot. Därför kan du inte hindra användaren från att logga in på sitt lokala konto samtidigt som du behåller sitt molnkonto genom att helt enkelt ta bort det lokala kontot. I stället kan du ange det lokala kontolösenordet till ett slumpmässigt GUID eller annat okänt värde.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Så här bjuder du in interna användare till B2B-samarbete

Du kan använda PowerShell eller inbjudnings-API:et för att skicka en B2B-inbjudan till den interna användaren. Kontrollera att den e-postadress som du vill använda för inbjudan anges som den externa e-postadressen för det interna användarobjektet.

- För en användare med endast molnet använder du e-postadressen i egenskapen User.OtherMails för inbjudan.
- För en lokal synkroniserad användare måste du använda värdet i egenskapen User.Mail för inbjudan.
- Domänen i egenskapen Mail måste matcha kontot de använder för att logga in. Annars kan vissa tjänster som Teams inte autentisera användaren.

Som standard skickar inbjudan ett e-postmeddelande till användaren om att de har bjudits in, men du kan undertrycka det här e-postmeddelandet och skicka ett eget i stället.

> [!NOTE]
> Om du vill skicka din egen e-post eller annan kommunikation kan du använda Ny AzureADMSInvitation med -SendInvitationMessage:$false för att bjuda in användare tyst och sedan skicka ditt eget e-postmeddelande till den konverterade användaren. Se [Azure AD B2B-samarbets-API och anpassning](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Använda PowerShell för att skicka en B2B-inbjudan

Använd följande kommando för att bjuda in användaren till B2B-samarbete:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Använda inbjudnings-API:et för att skicka en B2B-inbjudan

Exemplet nedan visar hur du anropar inbjudnings-API:et för att bjuda in en intern användare som B2B-användare.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Svaret på API:et är samma svar som du får när du bjuder in en ny gästanvändare till katalogen.

## <a name="next-steps"></a>Nästa steg

- [B2B samarbete inbjudan inlösen](redemption-experience.md)
