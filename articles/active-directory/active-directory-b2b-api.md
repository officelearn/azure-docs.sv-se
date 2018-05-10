---
title: Azure Active Directory B2B-samarbete API och anpassning | Microsoft Docs
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f1bd93ac2ef6aa75e07eeec3e3cb2222b6febc1c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-samarbete API och anpassning

Vi har fått många kunder berätta för oss att de vill anpassa inbjudan processen på ett sätt som passar bäst för deras organisationer. Du kan göra precis som med vårt API. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funktionerna i inbjudan API
API: et finns följande funktioner:

1. Bjud in en extern användare med *alla* e-postadress.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Anpassa där du vill att användarna ska hamna när de accepterar deras inbjudan.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Välj att skicka e-post för standard inbjudan via oss

    ```
    "sendInvitationMessage": true
    ```

  ett meddelande som mottagaren som du kan anpassa

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Och välj att skicka en kopia: personer som du vill behålla i loop om att din bjuda in deltagare i den här.

5. Eller helt anpassa din inbjudan och onboarding arbetsflöde genom att välja att inte skicka meddelanden via Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  I så fall måste få du tillbaka en inlösning URL från API: et som du kan bädda in i en postmall för e-, Snabbmeddelanden eller andra distributionsmetod du väljer.

6. Om du är administratör kan välja du dessutom att bjuda in användare som medlem.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Auktoriseringsmodellen
API: et kan köras i följande tillstånd lägen:

### <a name="app--user-mode"></a>Appen + användarläge
I detta läge gäller den som använder API: N måste ha behörighet att skapa B2B inbjudningar.

### <a name="app-only-mode"></a>Appen läge
Appen måste User.Invite.All omfång för inbjudan ska lyckas i appen endast sammanhang.

För mer information, se: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Nu är det möjligt att använda PowerShell för att lägga till och bjuda in externa användare till en organisation enkelt. Skapa en inbjudan med hjälp av cmdlet:

```
New-AzureADMSInvitation
```

Du kan använda följande alternativ:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Du kan också kontrollera ut inbjudan API-referens i [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
- [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
- [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)

