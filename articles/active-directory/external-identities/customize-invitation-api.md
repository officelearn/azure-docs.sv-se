---
title: API för B2B-samarbete och anpassning – Azure Active Directory
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f62ece12732c45037bcad328eec162bebb4e6a
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87910190"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>API för Azure Active Directory B2B-samarbete och anpassning

Vi har haft många kunder som säger oss att de vill anpassa processen för inbjudan på ett sätt som passar bäst för deras organisationer. Med vårt API kan du bara göra det. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funktioner i API för inbjudan

API: et erbjuder följande funktioner:

1. Bjud in en extern användare med *en* e-postadress.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Anpassa var du vill att användarna ska landa efter att de har accepterat sin inbjudan.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Välj att skicka standard inbjudan via e-post via oss

    ```
    "sendInvitationMessage": true
    ```

   med ett meddelande till mottagaren som du kan anpassa

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Och välj att cc: personer som du vill behålla i slingan om att bjuda in den här samarbets partnern.

5. Du kan också helt anpassa din Inbjudnings-och onboarding-arbetsflöde genom att välja att inte skicka meddelanden via Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   I det här fallet får du tillbaka en inlösnings-URL från API: et som du kan bädda in i en e-postmall, ett snabb meddelande eller en annan distributions metod.

6. Slutligen, om du är administratör, kan du välja att bjuda in användaren som medlem.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Auktoriserings modell

API: et kan köras i följande lägen:

### <a name="app--user-mode"></a>App + User Mode

I det här läget måste alla som använder API: n ha behörighet att skapa B2B-inbjudningar.

### <a name="app-only-mode"></a>Läge för endast appar

Appen behöver användaren. Bjud in en app för att det ska gå att utföra den här kontexten.

Mer information finns i:https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>PowerShell

Du kan enkelt använda PowerShell för att lägga till och bjuda in externa användare till en organisation. Skapa en inbjudan med hjälp av cmdleten:

```powershell
New-AzureADMSInvitation
```

Du kan använda följande alternativ:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Status för inbjudan

När du har skickat en extern användare en inbjudan kan du använda cmdleten **Get-AzureADUser** för att se om de har accepterat den. Följande egenskaper för Get-AzureADUser fylls när en extern användare skickar en inbjudan:

* **UserState** anger om inbjudan är **PendingAcceptance** eller **accepterad**.
* **UserStateChangedOn** Visar tidsstämpeln för den senaste ändringen av egenskapen **userState** .

Du kan använda **filter** alternativet för att filtrera resultaten efter **userState**. Exemplet nedan visar hur du filtrerar resultat för att endast visa användare som har en väntande inbjudan. I exemplet visas också alternativet **format-List** , som gör att du kan ange vilka egenskaper som ska visas. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Kontrol lera att du har den senaste versionen av AzureAD PowerShell-modulen eller AzureADPreview PowerShell-modulen. 

## <a name="see-also"></a>Se även

Kolla in API-referens för inbjudan i [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) .

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Elementen i e-postinbjudanen B2B-samarbete](invitation-email-elements.md)
- [Inlösen av B2B-samarbets inbjudningar](redemption-experience.md)
- [Lägg till B2B-samarbets användare utan inbjudan](add-user-without-invite.md)