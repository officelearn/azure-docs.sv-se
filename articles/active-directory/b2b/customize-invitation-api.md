---
title: Azure Active Directory B2B-samarbete API och anpassning | Microsoft Docs
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: a41eadaecc203f9371da3eee05367a4f77747253
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647165"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-samarbete API och anpassning

Vi har haft många kunder berätta för oss att de vill anpassa inbjudningsprocessen på ett sätt som passar bäst för deras organisationer. Med vårt API kan du göra just detta. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funktionerna i inbjudan API
API: et erbjuder följande funktioner:

1. Bjud in en extern användare med *alla* e-postadress.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Anpassa där du vill att användarna ska få när de har accepterat sin inbjudan.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Välj att skicka e-post för standard inbjudan via oss

    ```
    "sendInvitationMessage": true
    ```

  med ett meddelande till den mottagare som du kan anpassa

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Och väljer att skicka en kopia: personer som du vill behålla uppdaterad om dina bjuda in den här medarbetare.

5. Eller helt anpassa dina inbjudan och onboarding arbetsflöde genom att välja att inte skicka meddelanden via Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  I det här fallet, du kan få en inlösen URL från API: et som du kan bädda in i en e-postmall, Snabbmeddelanden eller andra distributionsmetod du väljer.

6. Slutligen, om du är administratör kan du bjuda in användaren som medlem.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Auktoriseringsmodellen
API: et kan köras i följande lägen för auktorisering:

### <a name="app--user-mode"></a>App- + användarläge
I det här läget kan den använder API-behov har behörigheter att skapa B2B inbjudningar.

### <a name="app-only-mode"></a>Endast appläge
Appen måste i appen endast sammanhanget User.Invite.All omfånget för inbjudan ska lyckas.

Mer information finns: https://graph.microsoft.io/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell
Nu är det möjligt att använda PowerShell för att lägga till och bjuda in extern användare till en organisation enkelt. Skapa en inbjudan med hjälp av cmdleten:

```
New-AzureADMSInvitation
```

Du kan använda följande alternativ:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

Du kan också besöka våra API-referens för inbjudan i [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Element i e-postinbjudan B2B-samarbete](invitation-email-elements.md)
- [B2B-samarbete inlösning av inbjudan](redemption-experience.md)
- [Lägga till B2B-samarbete användare utan inbjudan](add-user-without-invite.md)

