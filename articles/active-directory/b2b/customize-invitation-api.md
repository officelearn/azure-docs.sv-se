---
title: API och anpassning av B2B-samarbete – Azure Active Directory
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a090ee3f9588ff6bff01e12db469bf04407a7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263470"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Api och anpassning av Azure Active Directory B2B-samarbete

Vi har haft många kunder berätta för oss att de vill anpassa inbjudan processen på ett sätt som fungerar bäst för sina organisationer. Med vårt API kan du göra just det. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funktioner för api:et för inbjudan

API:et erbjuder följande funktioner:

1. Bjud in en extern användare *med valfri* e-postadress.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Anpassa var du vill att användarna ska landa när de har accepterat sin inbjudan.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Välj att skicka standardtjudan via oss

    ```
    "sendInvitationMessage": true
    ```

   med ett meddelande till mottagaren som du kan anpassa

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Och välj att cc: personer som du vill hålla i slingan om din inbjudande denna medarbetare.

5. Eller helt anpassa din inbjudan och introduktion arbetsflöde genom att välja att inte skicka meddelanden via Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   I det här fallet får du tillbaka en url för inlösen från API:et som du kan bädda in i en e-postmall, snabbmeddelande eller annan distributionsmetod som du väljer.

6. Slutligen, om du är administratör, kan du välja att bjuda in användaren som medlem.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Auktoriseringsmodell

API:et kan köras i följande auktoriseringslägen:

### <a name="app--user-mode"></a>App + användarläge

I det här läget måste den som använder API:et ha behörighet att skapa B2B-inbjudningar.

### <a name="app-only-mode"></a>Endast appläge

I appkontexten behöver appen appen programmet User.Invite.All för att inbjudan ska lyckas.

Mer information finns i:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Du kan enkelt lägga till och bjuda in externa användare till en organisation. Skapa en inbjudan med cmdlet:

```powershell
New-AzureADMSInvitation
```

Du kan använda följande alternativ:

* -InvitedUserDisplayName
* -InbjudenAnvändarEmailAdress
* -SendInvitationMessage
* -InbjudenUserMessageInfo

### <a name="invitation-status"></a>Status för inbjudan

När du har skickat en extern användare en inbjudan kan du använda cmdleten **Get-AzureADUser** för att se om de har accepterat den. Följande egenskaper för Get-AzureADUser fylls i när en extern användare skickas en inbjudan:

* **UserState** anger om inbjudan är **Väntande accepterad** eller **accepterad**.
* **UserStateChangedOn** visar tidsstämpeln för den senaste ändringen av **egenskapen UserState.**

Du kan använda alternativet **Filter** för att filtrera resultaten efter **UserState**. Exemplet nedan visar hur du filtrerar resultat så att endast användare som har en väntande inbjudan visas. I exemplet visas också alternativet **Formatera lista,** där du kan ange vilka egenskaper som ska visas. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Kontrollera att du har den senaste versionen av AzureAD PowerShell-modulen eller AzureADPreview PowerShell-modulen. 

## <a name="see-also"></a>Se även

Kolla in api-referensen för inbjudan i [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [De delar av B2B samarbete inbjudan e-post](invitation-email-elements.md)
- [B2B samarbete inbjudan inlösen](redemption-experience.md)
- [Lägga till B2B-samarbetsanvändare utan inbjudan](add-user-without-invite.md)