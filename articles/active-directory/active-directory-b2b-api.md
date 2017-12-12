---
title: Azure Active Directory B2B-samarbete API och anpassning | Microsoft Docs
description: "Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: 9e1dcd38e7c05a234e4b8d7a6190770d813a227f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-samarbete API och anpassning

Vi har fått många kunder berätta för oss att de vill anpassa inbjudan processen på ett sätt som passar bäst för deras organisationer. Du kan göra precis som med vårt API. [https://Developer.microsoft.com/Graph/docs/API-Reference/v1.0/Resources/Invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

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
Appen måste User.ReadWrite.All eller Directory.ReadWrite.All omfång för inbjudan ska lyckas i appen endast sammanhang.

Mer information finns på: https://graph.microsoft.io/docs/authorization/permission_scopes


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

Läs andra artiklar om Azure AD B2B-samarbete:

* [Vad är Azure AD B2B-samarbete?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hur lägger Azure Active Directory-administratörer till B2B-samarbete användare?](active-directory-b2b-admin-add-users.md)
* [Hur lägger informationsarbetare till B2B-samarbete användare?](active-directory-b2b-iw-add-users.md)
* [Elementen i e-postinbjudan B2B-samarbete](active-directory-b2b-invitation-email.md)
* [B2B-samarbete inbjudan inlösning](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B-samarbete och licensiering](active-directory-b2b-licensing.md)
* [Felsökning av Azure Active Directory B2B-samarbete](active-directory-b2b-troubleshooting.md)
* [Vanliga och frågor svar om Azure Active Directory B2B-samarbete](active-directory-b2b-faq.md)
* [Multi-Factor Authentication för användare av B2B-samarbete](active-directory-b2b-mfa-instructions.md)
* [Lägg till B2B-samarbete användare utan inbjudan](active-directory-b2b-add-user-without-invite.md)
* [Artikelindex för programhantering i Azure Active Directory](active-directory-apps-index.md)
